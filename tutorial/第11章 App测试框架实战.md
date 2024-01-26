

# 第10章  App测试框架实战

## 10.1.1  项目简介

页面基础类

编写Fixtures

## 11.2  目录结构

## 12.3  安装依赖

## 12.4  处理Appium Server及Caps配置

基于Appium的自动化项目需要确定Appium Server地址及Caps配置，我们将所有配置都集中到pytest.ini文件中。对于只有一个变量的Appium Server我们使用Pytest添加配置项add_ini方法在[pytest]段添加appium_server配置。对于包含多个变量的caps配置，我们在pytest中新增一个段[caps]来添加。

#### Appium Server配置

在pytest.ini中添加配置如下：

```ini
[pytest]
# ...
appium_server = localhost:4723
在根目录conftest.py添加自定义配置项：
def pytest_addoption(parser):
    # ...
    parser.addini('appium_server', help="appium server url eg. localhost:4723")
编写Fixture函数来获取完整的appium_server地址，在根目录conftest.py中添加：
@pytest.fixture()
def appium_server(request):
    appium_server = request.config.getini('appium_server')
    if not appium_server:
        raise ValueError('pytest.ini中[pytest]缺少appium_server配置')
    if not appium_server.startswith('http'):
        appium_server = 'http://%s' % appium_server

    if not appium_server.endswith('/wd/hub'):
        appium_server = '%s/wd/hub' % appium_server
    return appium_server
```

如果缺少appium_server配置项则抛出异常。这里还对appium_server地址进行了补全，如果没有以http开头，则前面补充http://，如果没有以/wd/hub结尾，则后面补充完整服务接口地址。

#### Caps配置

在pytest.ini中添加配置如下：

```ini
[caps]
platformName = Android
deviceName = MuMu
udid = emulator-5554
app = apps/mishop.apk
uiautomationName = uiautomator2
autoLaunch = False
```

这里约定，如果app地址以“/”开头则视为绝对路径，否则则视为data目录下的路径。
在根目录conftest.py中添加Fixture函数如下：

```python
@pytest.fixture()
def caps(ini, data_dir):
    if not ini.has_section('caps'):
        raise ValueError('pytest.ini中缺少[caps]段配置')
    caps = dict(ini.items('caps'))
    app = caps.get('app')
    if app and not app.startswith('/'):  # 处理相对路径(相对于data目录)
        caps['app'] = str(data_dir / app)
    return caps
```

其中ini是pytest-ini提供的Fixture函数，是pytest.ini文件的ConfigParser对象，可以用来读取pytest.ini中的配置数据。data_dir是我们自己写的Fixture函数，表示项目中data目录。
以上代码中首先判断项目中是否有[caps]段配置，如果没有则抛出异常。然后获取caps中app项的配置，如果存在app项配置并且不是绝对路径，则将路径和data_dir目录进行组装得到app的绝对路径。

#### Fixture函数driver的实现

在实现了appium_server和caps两个Fixture后，我们边可以实现用例所需要实现driver这个Fixture。在根目录conftest.py中添加：

```python
import logging
import appium

@pytest.fixture()
def driver(appium_server, caps):
    logging.info('连接服务 %s caps: %s' % (appium_server, caps))
    return webdriver.Remote(appium_server, caps)
```

## 11.5  编写页面基类

1.基础元素定位操作封装
元素定位是App自动化操作的核心步骤，在原定位方法的基础上，这里主要实现以下功能：

- 增加通过text属性定位元素的方式；
- 全部使用主动等待，并支持设置超时时间；
- 定位不到元素自动截图，并添加到Allure报告中；
- 支持尝试定位偶现元素，并忽略定位不到元素异常；

2.基础元素操作封装

```python
import logging
import time

import allure
from appium import webdriver
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException

DEFAULT_TIMEOUT = 30  # 默认查找元素超时时间30秒

from utils.path_utils import REPORTS_DIR  # 截图默认放到reports目录下

class BasePage:
    """App页面对象基础类"""
    def __init__(self, driver: webdriver.Remote, timeout=None):
        self.driver = driver
        self.timeout = timeout or DEFAULT_TIMEOUT

    def find(self, by, value, ignore_error=False, timeout=None):
        timeout = timeout or self.timeout  # 如果未指定则使用对象timeout属性设置
        if by == 'text':  # 扩展一种通过文本定位的方式，实际转为xpath进行定位
            by, value = 'xpath', '//*[@text="%s"]' % value
        try:
            return WebDriverWait(self.driver, timeout).until(
                EC.presence_of_element_located((by, value))
            )
        except TimeoutException:
            if ignore_error is False:
                snapshot_path = REPORTS_DIR / ('snapshot_%s.png' % int(time.time()))
                self.driver.save_screenshot(snapshot_path)
                allure.attach.file(snapshot_path,
                                   attachment_type=allure.attachment_type.PNG)
                raise NoSuchElementException('超时 %ss后 找不到元素 %s=%s'
                                             % (timeout, by, value))

    def find_all(self, by, value):
        if by == 'text':
            by, value = 'xpath', '//*[@text="%s"]' % value
        return self.driver.find_elements(by, value)
```

另外，补充一个判断元素是否存在的方法，代码如下：

```python
    def exists(self, by, value, timeout=None):
        logging.info('检查元素 %s=%s 是否存在% (by, value))
        return self.find(by, value, ignore_error=True, timeout=timeout) is not None
```

#### 元素常见操作封装

点击和输入是最常见的两种元素操作，封装方法如下：

```python
	def click(self, by, value, ignore_error=False, timeout=None):
		logging.info("点击元素 %s=%s" % (by, value))
		with allure.step("点击元素 %s=%s 超时时间 %ss" % (by, value, timeout)):
			self.find(by, value, ignore_error, timeout).click()
		return self
	
	def input_text(self, by, value, text, timeout=None):
		logging.debug("向元素 %s=%s 输入文本 %s" % (by, value, text) )
		with allure.step("向元素 %s=%s 输入文本 %s" % (by, value, text)):
			elm = self.find(by, value, timeout=timeout)
			elm.clear()
			elm.send_keys(text)
		return self
```

#### 等待及常用按键封装

除元素操作外，等待一定时间、按返回键、按Home键等也是常用操作，封装方法如下：

```python
	def wait(self, secs=1):
		logging.info('等待 %ss' % secs)
		with allure.step('等待 %ss' % secs):
			time.sleep(secs)
		return self
	
	def press_back(self):
		logging.info('按返回键')
		with allure.step("按返回键"):
			self.driver.press_keycode(4)
		return self
	
	def press_home(self):
		logging.info('按Home键')
		with allure.step("按Home键"):
			self.driver.press_keycode(3)
		return self
```

#### 滑动操作封装

屏幕滑动是App常用操作，这里分别封装上滑、下滑、左滑、右滑4种方法，并支持指定滑动距屏幕左侧或上方百分比，实现代码如下：

```python
	def swipe_up(self, percent=0.5):
		logging.info('在距屏幕左侧 {percent*100}% 处上滑')
		with allure.step('在距屏幕左侧 {percent*100}% 处上滑'):
			size = self.driver.get_window_size()
			x1, x2 = size['width'] * percent
			y1, y2 = size['height'] * 0.9, size['height'] * 0.1
			self.driver.swipe(x1, y1, x2, y2)
		return self
	
	def swipe_down(self, percent=0.5):
		logging.info('在距屏幕左侧 {percent*100}% 处下滑')
		with allure.step('在距屏幕左侧 {percent*100}% 处下滑'):
			size = self.driver.get_window_size()
			x1, x2 = size['width'] * percent
			y1, y2 = size['height'] * 0.1, size['height'] * 0.9
			self.driver.swipe(x1, y1, x2, y2)
		return self
	
	def swipe_left(self, percent=0.5):
		logging.info('在距屏幕上方 {percent*100}% 处左滑')
		with allure.step('在距屏幕上方 {percent*100}% 处左滑'):
			size = self.driver.get_window_size()
			x1, x2 = size['width'] * 0.9, size['width'] * 0.1
			y1 = y2 = size['height'] * percent
			self.driver.swipe(x1, y1, x2, y2)
		return self
	
	def swipe_right(self, percent=0.5):
		logging.info('在距屏幕上方 {percent*100}% 处右滑')
		with allure.step('在距屏幕上方 {percent*100}% 处右滑'):
			size = self.driver.get_window_size()
			x1, x2 = size['width'] * 0.1, size['width'] * 0.9
			y1 = y2 = size['height'] * percent
			self.driver.swipe(x1, y1, x2, y2)
		return self
```

另外补充一个边上滑边查找的方法，实现如下：

```python
~    def swipe_up_find(self, by, value, percent=0.5, timeout=None):
        timeout = timeout or self.timeout
        logging.info('上滑查找 %s=%s 超时时间 %ss' % (by, value, timeout))
        with allure.step('上滑查找 %s=%s 超时时间 %ss' % (by, value, timeout)):
            if by == 'text':
                by, value = 'xpath', '//*[@text="%s"]' % value
            start = time.time()
            while time.time() - start < timeout:
                try:
                    return self.driver.find_element(by, value)
                except NoSuchElementException:
                    self.swipe_up(percent)
        raise NoSuchElementException('上滑查找 超时 %ss后 找不到元素 %s=%s' %(timeout, by, value))~
```

其他常用的App操作方法可以根据需求自行补充。

## 11.6  编写页面对象类

有了页面对象基础类，我们便可以编写具体的页面对象类。页面对象类主要包含以下部分：

- 使用到的元素：可以使用类属性列出所有使用到元素的定位方式及对应的值；
- 单个元素操作：每个元素封装一个方法，操作完仍然在当前页面的返回self，跳转到新页面的，返回对应的页面对象；
- 组合流程操作：调用封装的单个元素操作进行组合，同样，操作完仍然在当前页面的返回self，跳转到新页面的，返回对应的页面对象。

以小米商城App，首页、分类、服务、购物车、我的几个主页面为例，参考封装如下。

图11.3 小米商城常用页面关系
在pages创建对应的页面类如下：

- pages/main_page.py：主导航页
- pages/home_page.py：首页
- pages/category_page.py：分类页
- pages/service_page.py：服务页
- pages/cart_page.py：购物车页
- pages/my_page.py：我的页面
- pages/login_page.py：登录页
- pages/search_page.py：搜索页
- ...

每个模块中继承BasePage并创建同名的页面对象类，参考格式如下：

> 文件pages/main_page.py内容

```python
from .base_page import BasePage

class MainPage(BasePage):
    pass
```

#### 1.避免循环引用

由于页面对象操作后直接返回另一个页面对象，而另一个页面对象某个操作后也有可能返回该页面对象，这就造成了循环引用，例如，我的页面pages/my_page.py：

```python
from .base_page import BasePage
from .login_page import LoginPage

class MyPage(BasePage):
    def go_to_login_page(self):
        # ...
        return LoginPage(self.driver, self.timeout)
登录页面pages/login_page.py：
from .base_page import BasePage
from .my_page import MyPage

class LoginPage(BasePage):
    def login_with_password(self, username, password):
        # ...
        return MyPage(self.driver, self.timeout)
```

循环引用是不被允许的，为了避免循环引用的问题，我们将所有的页面对象都放到pages/**ini**.py中，并都使用pages来引用模块，**init**.py参考内容如下：

> 文件`pages/__init__.py`内容

```python
from .base_page import BasePage
from .main_page import MainPage
from .home_page import HomePage
from .category_page import CategoryPage
from .service_page import ServicePage
from .cart_page import CartPage
from .my_page import MyPage
from .login_page import LoginPage
from .search_page import SearchPage
```

这样，直接pages包来导入页面对象便不会有循环引用问题，例如我的页面pages/my_page.py：

```python
import pages

class MyPage(pages.BasePage):
    def go_to_login_page(self):
        # ...
        return pages.LoginPage(self.driver, self.timeout)
```

> 登录页面pages/login_page.py：

```python
import pages

class LoginPage(pages.BasePage):
    def login_with_password(self, username, password):
        # ...
        return pages.MyPage(self.driver, self.timeout)
```

#### 主导航页封装
主导航页主要负责跳转到首页、分类页、服务页、购物车页、我的页面，及处理启动时各种对话框并启动到首页。主导航页是所有页面对象的入口，参考封装如下：

```python
import logging
import allure
import pages

class MainPage(pages.BasePage):
    """主导航页"""
    agree_btn = ('text', '同意')
    allow_btn = ('text', '允许')
    home_tab = ('text', '首页')      # 底部首页Tab
    category_tab = ('text', '分类')  # 底部分类Tab
    service_tab = ('text', '服务')  # 底部服务Tab
    cart_tab = ('text', '购物车')    # 底部购物车Tab
    mine_tab = ('text', '我的')      # 底部我的Tab

    @allure.step("启动到首页")
    def start(self):
        """启动到首页"""
        logging.info("启动到首页")
        self.click(*self.agree_btn, ignore_error=True)
        self.click(*self.allow_btn, ignore_error=True)
        self.wait(1)
        self.click(*self.allow_btn, ignore_error=True)
        return self

    @allure.step("跳转到首页"):
    def go_to_home_page(self):
        logging.info('跳转到首页')
        self.click(*self.home_tab)
        return pages.HomePage(self.driver, self.timeout)

    @allure.step('跳转到分类页')
    def go_to_category_page(self):
        logging.info('跳转到分类页')
        self.click(*self.category_tab)
        return pages.CategoryPage(self.driver, self.timeout)

    @allure.step('跳转到发现页')
    def go_to_service_page(self):
        logging.info('点击发现Tab')
        self.click(*self.service_tab)
        return pages.ServicePage(self.driver, self.timeout)

    @allure.step('跳转到购物车页')
    def go_to_cart_page(self):
        logging.info('点击购物车Tab')
        self.click(*self.cart_tab)
        return pages.CartPage(self.driver, self.timeout)

    @allure.step('跳转到我的页面')
    def go_to_my_page(self):
        logging.info('点击我的Tab')
        self.click(*self.mine_tab)
        return pages.MyPage(self.driver, self.timeout)
```

同时，在testcases/conftest.py中添加主导航页、首页、分类页、服务页、购物车页及我的页面对象为Fixture函数，参考代码如下：

```python
import pytest
import pages

@pytest.fixture()
def main_page(driver):
    return pages.MainPage(driver).start()

@pytest.fixture()
def home_page(main_page):
    return main_page.go_to_home_page()

@pytest.fixture()
def category_page(main_page):
    return main_page.go_to_category_page()

@pytest.fixture()
def service_page(main_page):
    return main_page.go_to_service_page()

@pytest.fixture()
def cart_page(main_page):
    return main_page.go_to_cart_page()

@pytest.fixture()
def my_page(main_page):
    return main_page.go_to_my_page()
```

#### 首页封装
页面封装，列出所有需要操作的元素，并封装元素及页面跳转操作，示例如下：

```python
import logging
import pages
import allure

class HomePage(pages.BasePage):
    search_ico = ('id', 'com.xiaomi.shop.plugin.homepage:id/searchIcon')
    scan_ico = ('name', '扫一扫')
    msg_btn = ('id', 'com.xiaomi.shop.plugin.homepage:id/msgBtn')
    login_now = ('id', 'com.xiaomi.shop.plugin.homepage:id/login')

    @allure.step('跳转到搜索页')
    def go_to_search_page(self):
        logging.info('点击搜索图标')
        self.click(*self.search_ico)
        return pages.SearchPage(self.driver, self.timeout)
```

同时我们在testcases/conftest.py中补充一个搜索页的Fixture，由首页进行跳转得到，参考代码如下：

```
@pytest.fixture()
def search_page(home_page):
    return home_page.go_to_search_page()
```

#### 我的页面封装
我的页面可以跳转到登录页面，参考封装如下：

```python
import logging
import allure
import pages

class MyPage(pages.BasePage):
    scan_icon = ('id', 'com.xiaomi.shop.plugin.homepage:id/mine_root_icon_scan')
    settings_icon = ('id', 'com.xiaomi.shop.plugin.homepage:id/mine_root_icon_setting')
    msg_icon = ('id', 'com.xiaomi.shop.plugin.homepage:id/mine_root_icon_msg')
    login_register = ('text', '登录/注册')
    xiaomi_id = ('id', 'com.xiaomi.shop.plugin.homepage:id/mine_top_bar_user_mid')

    @allure.step('转到登录页')
    def go_to_login_page(self):
        logging.info('点击登录/注册')
        self.click(*self.login_register)
        return pages.LoginPage(self.driver, self.timeout)

    @allure.step('获取小米id')
    def get_xiaomi_id(self):
        elm = self.find(*self.xiaomi_id, ignore_error=True)
        if elm:
            return elm.get_attribute('text')
```

同时在testcases/conftest.py中补充一个登录页的Fixture函数，由我的页面跳转得到，参考代码如下：

```python
@pytest.fixture()
def login_page(my_page):
    return my_page.go_to_login_page()
```

#### 登录页面封装
登录是常用的操作之一，自动化中一般使用用户名密码进行登录，因此登录前要先切换登录方式。登录后返回我的页面，参考封装如下：

```python
import logging
import allure
import pages

class LoginPage(pages.BasePage):
    help = ('text', '帮助')
    forget_password = ('text', '忘记密码')
    password_login = ('text', '密码登录')
    agree_check_box = ('id', 'com.xiaomi.shop:id/footer_user_agreement_checkbox')
    username_input = ('id', 'com.xiaomi.shop:id/userId')
    password_input = ('id', 'com.xiaomi.shop:id/password')
    login_btn = ('id', 'com.xiaomi.shop:id/sign_in_btn')

    @allure.step('切换到密码登录')
    def switch_to_login_with_password(self):
        logging.info('切换到密码登录')
        self.wait(0.5)
        size = self.driver.get_window_size()
        x1 = x2 = int(size['width'] * 0.5)
        y1, y2 = int(size['height'] * 0.6), int(size['height'] * 0.4)
        self.driver.swipe(x1, y1, x2, y2)
        self.click(*self.password_login)
        self.wait(0.5)

    @allure.step('用户名密码登录')
    def login_with_password(self, username, password):
        logging.info('用户 %s 登录' % username)
        self.switch_to_login_with_password()
        self.input_to(*self.username_input, username)
        self.input_to(*self.password_input, password)
        self.click(*self.agree_check_box)
        self.click(*self.login_btn)
        return pages.MyPage(self.driver, self.timeout)
```

## 11.7  编写App自动化用例
在编写App自动化前我们需要修改pytest.ini配置，加大超时时间，配置环境为prod，并配置对应的用户名密码等。

```ini
[pytest]
timeout = 300
env = prod

[prod]
username = ${XIAOMISHOP_USER}
password = ${XIAOMISHOP_PWD}
```

在.env中添加变量

```
XIAOMISHOP_USER=你的小米商城用户名
XIAOMISHOP_PWD=你的小米商城密码
```

新建用例testcases/test_xiaomishop_login.py

```python
import allure

@allure.feature('小米商城登录')
class TestXiaoMiShopLogin:
    @allure.story('用户名密码登录')
    def test_login_with_password(self, login_page, user, assert_utils):
        username, password = user
        my_page = login_page.login_with_password(username, password)
        xiaomi_id = my_page.get_xiaomi_id()
        assert_utils.assert_is_not_none(xiaomi_id)
```

对于测试用例，建议对于同一特性/功能（Feature）使用测试类来管理，每个测试场景（Story）编写至少一条用例。读者也可以根据自己的需求添加@pytest.mark.level、@pytest.mark.owner等其他标签。
11.8  框架常见问题及解决方案

## 小结

本章以小米商城App项目为示例，演示了如何规划和设计各种页面对象类。
