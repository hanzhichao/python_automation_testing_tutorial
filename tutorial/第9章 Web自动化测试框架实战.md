
测试框架的设计有两种思路，一种则是自顶向下，直接设计框架结构和选取各种问题的解决方案，并逐步完善，这种适合有较多框架事件经验的人。另一种是自底向上，从脚本逐步演变完善成框架，这种适合新手了解框架的演变过程。本章沿用Moncia这个项目，演示从测试脚本逐步演化成测试框架的过程及测试框架中各种问题的处理方案。
## 9.1  从脚本到用例
相比于一堆测试脚本，使用规范化的测试用例格式会方便我们灵活的执行和管理用例。一个完整的自动化测试用例应包含：
- 测试准备（setup）：测试准备步骤、用例辅助方法或工具，可以共用；
- 测试步骤（test steps）：核心测试步骤；
- 断言（assertions）：期望结果于实际结果的比对，用例可以报告不止一个断言；
- 测试清理（teardown）：对执行测试造成的影响进行清理和还原，以免影响后续执行，可以共用。
### 9.1.1  编写测试函数
将测试脚本转化为Pytest测试用例的方法非常简单，只要将测试过程编写为test开头的测试函数即可，我这里以使用Selenium自动登录Monica并添加一个联系人的过程为例，演示测试脚本的完善过程。
1.在测试脚本中编写整个流程
有时候我们为了快速实现一个功能，会直接把代码按步骤写到模块里，如下例：

> 代码test_monica_contract_add_v1_0.py内容
```python
from time import sleep
from selenium import webdriver
from selenium.common import NoSuchElementException
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select

print('打开浏览器')
driver = webdriver.Chrome()
print('登录Monica')
driver.get('http://localhost:8080/')
driver.find_element(By.ID, 'email').send_keys('<你的Monica账户的邮箱>')
driver.find_element(By.ID, 'password').send_keys('<你的Monica账户的密码>')
driver.find_element(By.CSS_SELECTOR, 'button[type=submit]').click()
sleep(1)
if driver.current_url == 'http://localhost:8080/dashboard':
    print('登录成功')
else:
    print('登录失败')
print('添加联系人')
try:
    driver.find_element(By.LINK_TEXT, '添加某人').click()
except NoSuchElementException:
    # 第一次添加显示的按钮是"添加您的第一个联系人"
    driver.find_element(By.LINK_TEXT, '添加您的第一个联系人').click()
driver.find_element(By.NAME, 'last_name').send_keys('张')
driver.find_element(By.NAME, 'first_name').send_keys('三')
select = Select(driver.find_element(By.NAME, 'gender'))
select.select_by_visible_text('男')
driver.find_element(By.NAME, 'save').click()
if 'http://localhost:8080/people/' in driver.current_url:
    print('添加成功')
else:
    print('添加失败')
print('关闭浏览器')
driver.quit()
```

2.使用函数封装每个测试步骤
上面的脚本包含4个步骤，我们可以把每个测试步骤封装成一个函数。这样做的好处一是使测试步骤更清晰，另外封装的步骤通过（如登录）设置一些参数使得可以进行复用（可以切换任意用户）。修改版本代码如下：

> 代码test_monica_contract_add_v1_1.py内容
```python
from time import sleep
from selenium import webdriver
from selenium.common import NoSuchElementException
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select
def open_browser():
    print('打开浏览器')
    driver = webdriver.Chrome()
    return driver
def close_browser(driver):
    print('关闭浏览器')
    driver.close()
def login(driver, email, password):
    print('登录Monica')
    driver.get('http://localhost:8080/')
    driver.find_element(By.ID, 'email').send_keys(email)
    driver.find_element(By.ID, 'password').send_keys(password)
    driver.find_element(By.CSS_SELECTOR, 'button[type=submit]').click()
    sleep(1)
    if driver.current_url == 'http://localhost:8080/dashboard':
        print('登录成功')
    else:
        print('登录失败')
def add_contract(driver, username, gender='男'):
    # username包含姓名，第一个是姓，后面的是名
    # 添加联系人时的email等输入项可以稍后补充
    print(f'添加联系人 {username}')
    last_name, first_name = username[0], username[1:]
    try:
        driver.find_element(By.LINK_TEXT, '添加某人').click()
    except NoSuchElementException:
        # 第一次添加显示的按钮是"添加您的第一个联系人"
        driver.find_element(By.LINK_TEXT, '添加您的第一个联系人').click()
    driver.find_element(By.NAME, 'last_name').send_keys(last_name)
    driver.find_element(By.NAME, 'first_name').send_keys(first_name)
    select = Select(driver.find_element(By.NAME, 'gender'))
    select.select_by_visible_text(gender)
    driver.find_element(By.NAME, 'save').click()
    if 'http://localhost:8080/people/' in driver.current_url:
        print('添加成功')
    else:
        print('添加失败')
if __name__ == '__main__':
    # 测试主流程
    driver = open_browser()
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    add_contract(driver, '张三', '男')
    close_browser(driver)
```
这里我们封装了打开浏览器open_browser、关闭浏览器close_browser、登录login、添加联系add_contract这4个函数，不同于v1.0将driver作为全局变量，这里使用了函数返回值及函数参数来传递driver。读者需要理解下每个函数需要哪些参数，以及参数从哪个函数返回，即整个过程中参数的传递。以上代码中，if __name__ == '__main__':中编写了测试的主流程，即函数的调用步骤。
3.编写标准的Pytest测试用例
在将测试步骤封装成函数的基础上，我们可以将测试主流程编写成标准的Pytest测试用例，我们可以将v1_1中测试主流程编写为标准的Pytest测试函数，复制test_monica_contract_add_v1_1.py文件，删除掉`if __name__ == '__main__':`及以下内容，并添加代码如下：

> 代码test_monica_contract_add_v2.0.py部分内容
```python
...
def test_monica_contract_add():
    driver = open_browser()
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    add_contract(driver, '张三', '男')
    close_browser(driver)
```

不同于v1_0、v1_1版Python脚本的运行方法（命令行使用python <脚步路径>），Pytest用例脚本使用pytest <脚本路径>或python -m pytest <脚本路径>来执行。如果使用PyCharm，可以直接点击用例左侧的绿色三角图标或者右键进行执行该测试用例。
或者，我们也可以在Pytest用例脚本下面加上以下语句，
```python
if __name__ == '__main__':
    pytest.main([__file__])
```
这样便可以像Python脚本一样直接运行。其中__file__指当前脚本，也可以添加其他运行参数，如-qs等。
### 9.1.2  使用断言
测试用例中必须包含期望结果来验证执行的通过与否。不同于“调试”，需要有人值守来人工判断没个执行过程是否通过，自动化“测试”往往需要批量运行，并自动判断用例是否通过。断言即是执行过程中的实际结果与期望结果的自动对比。
Pytest中使用标准的assert语句来进行断言。assert断言语句在用例执行失败时（和期望结果不一致）会抛出AssertionError异常，测试框架会自动捕获该异常，并将用例标记为执行失败状态，并且不会因为异常导致执行中断而影响其他用例的执行。
assert语句的基本格式如下：
assert <判断表达式>, '断言失败消息'
其中断言失败信息可以省略，断言表达式可以是任何Python表达式，常用的全例如：
假设a = 10
- 判断相等：assert a == 10
- 判断大于小于等：assert a > 5
- 判断包含：assert a in [10, 15, 20]
- 判断为True：assert a is True
- 判断不为None：assert a is not None
断言通过时不会有任何额外的输入和提示，断言失败则抛出AssertinError异常，导致用例失败。
注：在用例中也可以使用if判断配合pytest.fail()或者手动抛出AsserionError异常来将用例设置为失败状态，示例如下：
    if driver.current_url != 'http://localhost:8080/dashboard':
        # rasie AssersionError('登录失败')
        pytest.fail('登录失败')
Web UI自动化测试过程中常用的断言策略有以下几种：
- 流程成功执行视为通过：按确定的元素操作步骤，可以正常完成整个流程视为通过；
- 通过标题断言：通过当前网页标题driver.title来判断处于某一页面上；
- 通过URL断言：通过当前URL，driver.current_url来判断处于某一页面上；
- 通过页面源码断言：通过网页源代码driver.page_source中包含特定信息来判断处于某一页面上；
- 通过存在特定元素断言：通过存在某个特定元素来判断处于某一页面上。
我们可以将v2_0中login及add_contract两个函数中的if判断改为使用标准的assert断言，一般，我们也可将断言放到用例中，已兼容期望某些步骤失败的场景。删除login即add_contract函数中的if判断，在用例中增加assert断言，修改后部分代码如下：

> 代码test_monica_contract_add_v2_1.py部分内容
```python
...
def test_monica_contract_add(driver):
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
    add_contract(driver, '张三', '男')
    assert 'http://localhost:8080/people/' in driver.current_url
```
当登录或添加联系人任意一个步骤失败，测试用例会失败并停止执行后面的步骤。
### 9.1.3  分离测试准备及清理方法
在测试用例中，我们要尽可能的分离核心测试步骤，将可以共用的测试准备及测试清理步骤单独提取出来，以方便复用。
在上例中，我们核心的测试步骤是从打开百度网站到断言网页标题，而启动浏览器和关闭浏览器可以视为测试准备和测试清理方法。
测试准备和测试清理方法我们可以使用Pytest中的setup_function()及teardown_function()方法，也可以使用自定义的Fixture方法来吧两个方法集中的一个函数中。将v2_1版本中的open_browser及close_browser函数，修改为setup_function()及teardown_function()函数，这时我们可以在测试函数test_monica_contract_add中去掉打开浏览器、关闭浏览器这两个步骤。在执行用例前、后会自动调用setup_function及teardown_function，自动打开、关闭浏览器。修改代码如下：

> 注意：由于我们无法手动调用setup_function及teardown_function（由Pytest框架自动调用）并获取其返回值，这里driver必须设置为全局变量。

> 代码test_monica_contract_add_v2_2.py部分内容
```python
...
def setup_function():
    global driver
    print('打开浏览器')
    driver = webdriver.Chrome()
def teardown_function():
    print('关闭浏览器')
    driver.quit()
...
def test_monica_contract_add(driver):
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
    add_contract(driver, '张三', '男')
    assert 'http://localhost:8080/people/' in driver.current_url
```
此时，测试步骤只包含登录和添加联系人，更加精简清晰。
### 9.1.4  使用自定义Fixture方法
我们也可以使用自定义的Fixture方法来包含setup_function及teardown_fucntion两个过程，同时使用自定义Fixture，用例还可以方便的获取Fixture函数的返回值。删除掉setup_function及teardown_fucntion修改代码如下：

> 代码test_monica_contract_add_v2_3.py部分内容
```python
...
import pytest
@pytest.fixture()
def driver():
    print('打开浏览器')
    dr = webdriver.Chrome()
    yield dr
    print('关闭浏览器')
    driver.close()
...
def test_monica_contract_add(driver):
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
    add_contract(driver, '张三', '男')
    assert 'http://localhost:8080/people/' in driver.current_url
```

> 注意：由于没有将浏览器驱动对象设置为全局变量，用例中需要使用Fixture函数的名称来获取其返回的浏览器驱动对象。

上例中我们自定义了一个名为driver的Fixture方法。yield上面对的所有语句属于测试准，这里创建了一个浏览器驱动对象dr。yield语句将dr对象交给用例执行，并等待用例执行完毕，再执行下面的测试清理语句，退出浏览器。
用例中使用Fixture函数名driver作为参数来注入测试准备和测试清理方法，用例中使用的driver即Fixture函数yield返回的dr，浏览器驱动对象。
### 9.1.5  使用pytest-selenium插件
Pytest框架的优点之一是，拥有很多功能丰富的插件。使用这些插件可以省略我们自己编写Fixture方法的过程，直接安装使用。
上例中我们自己编写了一个名为driver的Fixture方法，我们也可以直接使用Pytest-Selenium插件，该插件提供了一个全局的driver（或selenium）Fixture方法，可以直接使用，并且支持切换使用的浏览器。使用pip install pytest-selenium安装Pytest-Selenium插件，并删除掉v2_2中自定义的Fixture函数driver，其他保持不变。

> 代码test_monica_contract_add_v2_4.py内容
```python
from time import sleep
import pytest
from selenium.common import NoSuchElementException
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select
def login(driver, email, password):
    print('登录Monica')
    driver.get('http://localhost:8080/')
    driver.find_element(By.ID, 'email').send_keys(email)
    driver.find_element(By.ID, 'password').send_keys(password)
    driver.find_element(By.CSS_SELECTOR, 'button[type=submit]').click()
def add_contract(driver, username, gender='男'):
    # username包含姓名，第一个是姓，后面的是名
    # 添加联系人时的email等输入项可以稍后补充
    print(f'添加联系人 {username}')
    last_name, first_name = username[0], username[1:]
    try:
        driver.find_element(By.LINK_TEXT, '添加某人').click()
    except NoSuchElementException:
        # 第一次添加显示的按钮是"添加您的第一个联系人"
        driver.find_element(By.LINK_TEXT, '添加您的第一个联系人').click()
    driver.find_element(By.NAME, 'last_name').send_keys(last_name)
    driver.find_element(By.NAME, 'first_name').send_keys(first_name)
    select = Select(driver.find_element(By.NAME, 'gender'))
    select.select_by_visible_text(gender)
    driver.find_element(By.NAME, 'save').click()
def test_monica_contract_add(driver):
    login(driver, '<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
    add_contract(driver, '张三', '男')
    assert 'http://localhost:8080/people/' in driver.current_url
```
不同于我们之前自己写的Fixture函数driver，固定使用Chrome浏览器，Pytest-Selenium是支持使用不同的浏览器的，因此使用Pytest-Selenium时，必须在运行时使用--driver来指定浏览器。此时直接运行上面的用例会报错。可以在命令行使用以下命令运行：

```bash
pytest test_monica_contract_add_v3_0.py --driver=chrome
```

我们也可以使用Pytest的配置文件pytest.ini来设置默认的参数，在脚本同级目录，新建pytest.ini，内容如下：
文件pytest.ini内容

```ini
[pytest]
addopts = --driver=chrome
```

此时，在PyCharm便可以直接运行用例。
Pytest-Selenium还支持配置浏览器选项及配合pytest-html失败自动截图等功能，详细可以参考其官方使用文档https://pytest-selenium.readthedocs.io/en/latest/。

> 注：pytest-selenium默认会拦截所有接口请求，可以在pytest.ini中配置sensitive_url = ''来设置无敏感url。
### 9.1.6  生成测试报告
使用Pytest框架生成测试报告最常用的插件有pytest-html和allure-pytest两种，前者简单，可以生成单文件测试报告。后者华丽，功能强大，使用较为复杂。本章我们使用pytest-html来生成报告，allure-pytest的具体使用下章讲解。
pytest-html的使用方式非常简单，使用pip install pytest-html命令安装pytest-html并使用--html来生成报告即可，修改pytest.ini文件内容如下：
文件pytest.ini内容

```ini
[pytest]
addopts = --driver=chrome --html=report.html --self-contained-html
```
运行测试用例后，打开生成的report.html文件，效果如图9.1：

图9.1 HTML测试报告

> 注：如果想自己生成HTML测试报告，可以在conftest.py文件中通过pytest_terminal_summary钩子方法terminalreporter参数对象的stats属性结合三方库Jinjia2来自定义生成报告。
## 9.2  增加易维护性
众所周知，UI的变动导致Web自动化用例的维护成本非常高，当一个元素变动时（如登录按钮），所有使用到这个元素的用例都将因此而失败，逐个修改每一条用例的成本是非常高的。
最好的做法就是使用模块封装的方式来隔离变动，隔离变动旨在隔离易变的和稳定的，常用的策略为：
- 代码：隔离易变（如元素定位）和稳定的（页面操作），可以使用模块封装的方式对易变的操作进行封装；
- 数据：变动较频繁，建议与代码隔离，以降低代码的修改；
- 配置：配置也是数据的一种，主要用于增加框架使用的灵活性，配置变动也较频繁，讲义与代码隔离。
另外使用数据驱动、添加日志和失败自动截图也是快速定位问题、降低维护成本的有效方法。
### 9.2.1  分层-使用PageObject
分层，是将页面操作及业务逻辑进行封装，来应对底层页面元素的变动。Page Object模式即Page Object Module，页面对象模型模式，是一种基于模块的框架结构。以页面为对象来统一管理页面上元素的定位及操作。
分析添加联系人的过程，需要登录页、后台主页（dashboard页）、添加联系人页，三个页面的操作，我们可以为每一个页面编写一个页面类，同时其中的每个需要的元素操作封装一个方法，以及将常用流程封装一些组合操作方法，如登录页，代码实现如下：

> 代码test_monica_contract_add_v3_0.py部分内容
```python
...
from selenium.webdriver.common.by import By
class LoginPage(object):
    def __init__(self, driver):
        self.driver = driver
    email_ipt_loc = (By.ID, 'email')
    password_ipt_loc = (By.ID, 'password')
    login_btn_loc = (By.CSS_SELECTOR, 'button[type=submit]')
    def open(self):
        self.driver.get('http://localhost:8080/')
    def input_email(self, email):
        self.driver.find_element(*self.email_ipt_loc).send_keys(email)
    def input_password(self, password):
        self.driver.find_element(*self.password_ipt_loc).send_keys(password)
    def click_login_btn(self):
        self.driver.find_element(*self.login_btn_loc).click()
    def login(self, email, password):
        print('登录Monica')
        self.open()
        self.input_email(email)
        self.input_password(password)
        self.click_login_btn()
        sleep(1)
        assert self.driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
...
```

> 注：以上代码中定位元素，如self.driver.find_element(*self.email_ipt_loc)中的*是将类属性email_ipt_loc元祖类型的变量拆开成两个变量，也叫解包。

页面类的基本模板为：
- 在类属性中，列出所有要操作的元素的定位方式；
- 每个元素操作封装一个方法，并暴露相关参数（如email、password等）；
- 封装一些常用的组合操作（如login方法）。
由于元素的定位及操作等都需要用到浏览器对象driver，因此我们需要在页面类对象的初始化方法中，将driver作为一个参数传入，并绑定给self即对象本身，这样页面对象所有的方法便可以使用self.driver来定位和操作元素。
后台主页类，实现代码如下：

> 代码test_monica_contract_add_v3_0.py部分内容
```python
...
from selenium.common import NoSuchElementException
class DashboardPage(object):
    def __init__(self, driver):
        self.driver = driver
    add_contract_lnk_loc = (By.LINK_TEXT, '添加某人')
    add_first_contract_lnk_loc = (By.LINK_TEXT, '添加您的第一个联系人')
    def click_add_contract_link(self):
        try:
            self.driver.find_element(*self.add_contract_lnk_loc).click()
        except NoSuchElementException:
            self.driver.find_element(*self.add_first_contract_lnk_loc).click()
```
添加联系人页面类，实现代码如下：

> 代码test_monica_contract_add_v3_0.py部分内容
```python
...
from selenium.webdriver.support.select import Select

class ContractAddPage(object):
    def __init__(self, driver):
        self.driver = driver
    last_name_ipt_loc = (By.NAME, 'last_name')
    first_name_ipt_loc = (By.NAME, 'last_name')
    gender_sel_loc = (By.NAME, 'gender')
    save_btn_loc = (By.NAME, 'save')
    def input_last_name(self, last_name):
        self.driver.find_element(*self.last_name_ipt_loc).send_keys(last_name)
    def input_first_name(self, first_name):
        self. driver.find_element(*self.first_name_ipt_loc).send_keys(first_name)
    def select_gender(self, gender):
        select = Select(self.driver.find_element(*self.gender_sel_loc))
        select.select_by_visible_text(gender)
    def click_save_btn(self):
        self.driver.find_element(*self.save_btn_loc).click()
    def add_contract(self, username, gender='男'):
        print(f'添加联系人 {username}')
        last_name, first_name = username[0], username[1:]
        self.input_last_name(last_name)
        self.input_first_name(first_name)
        self.select_gender(gender)
        self.click_save_btn()
```


此时，用例中要使用页面对象的方法，必须先实例化页面对象，再进行操作，修改用例如下：

> 代码test_monica_contract_add_v3_0.py部分内容
```python
def test_monica_contract_add(driver):
    login_page = LoginPage(driver)
    login_page.login('<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert driver.current_url == 'http://localhost:8080/dashboard', '登录失败'
    dashboard_page = DashboardPage(driver)
    dashboard_page.click_add_contract_link()
    contract_add_page = ContractAddPage(driver)
    contract_add_page.add_contract('张三', '男')
    assert 'http://localhost:8080/people/' in driver.current_url
```
在v2_4版本中，
### 9.2.2  使用页面基类-封装常用操作
除了用例的编写外，页面模型也是需要用户进行编辑和新增的，如何使页面模型的编写更简单呢？比如每个页面模型都要编写初始化方法传入driver，比如常用的通过节点文本定位、鼠标悬浮、强制等待、主动等待、偶现元素处理等。我们可以编写一个页面基础类作为所有所有页面对象的父类，在页面基础类中实现这些操作。一个简单的页面基础类，实现如下：

> 代码test_monica_contract_add_v3_1.py部分内容
```python
...
from time import sleep
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
class BasePage(object):
    def __init__(self, driver):  # 初始化传入driver
        self.driver = driver  # 绑定页面对象
    @property
    def title(self):
        return self.driver.title
    @property
    def url(self):
        return self.driver.current_url
    def wait(self, secs=1):
        print(f"等待 {secs}s")
        sleep(secs)
    def find(self, by, value, timeout=None):
        if timeout:
            wait = WebDriverWait(self.driver, timeout)
            wait.until(EC.presence_of_element_located((by, value)))
        return self.driver.find_element(by, value)
    def click(self, by, value, timeout=None):
        print(f'点击元素 {by}={value}')
        self.find(by, value, timeout).click()
    def input_to(self, by, value, text, timeout=None):
        print(f'向元素 {by}={value} 输入文本 {text}')
        elm = self.find(by, value, timeout)
        elm.clear()
        elm.send_keys(text)
   def select(self, by, value, text, timeout=None):
        print(f'元素 {by}={value} 选择 {text}')
        elm = self.find(by, value, timeout)
        select = Select(elm)
        select.select_by_visible_text(text)
```
除页面对象通用的初始化方法传人driver参数外，这里简单封装了页面标题、页面当前url两个动态属性及等待、查找元素、点击元素、向元素输入文本及下拉选择，5个常用的操作方法。find、click、input_to及select操作时都支持传人timeout超时时间来使用主动等待的方式定位元素。
此外读者可以根据自己项目的实际需求增加常见的操作方法的封装（如切换Frame、关闭alert、移除隐藏属性、判断元素是否存在等）。
此时，我们具体的页面对象类LoginPage、DashboadPage及ContractAddPage可以继承页面基类，并使用其中封装的操作，修改代码如下：

> 代码test_monica_contract_add_v3_1.py部分内容
```python
...
class LoginPage(BasePage):
    email_ipt_loc = (By.ID, 'email')
    password_ipt_loc = (By.ID, 'password')
    login_btn_loc = (By.CSS_SELECTOR, 'button[type=submit]')
    def open(self):
        self.driver.get('http://localhost:8080/')
    def input_email(self, email):
        self.input_to(*self.email_ipt_loc, email)
    def input_password(self, password):
        self.input_to(*self.password_ipt_loc, password)
    def click_login_btn(self):
        self.click(*self.login_btn_loc)
    def login(self, email, password):
        print('登录Monica')
        self.open()
        self.input_email(email)
        self.input_password(password)
        self.click_login_btn()
        sleep(1)
        
class DashboardPage(BasePage):
    add_contract_lnk_loc = (By.LINK_TEXT, '添加某人')
    add_first_contract_lnk_loc = (By.LINK_TEXT, '添加您的第一个联系人')
    def click_add_contract_link(self):
        try:
            self.click(*self.add_contract_lnk_loc)
        except NoSuchElementException:
            self.click(*self.add_first_contract_lnk_loc)
class ContractAddPage(BasePage):
    last_name_ipt_loc = (By.NAME, 'last_name')
    first_name_ipt_loc = (By.NAME, 'last_name')
    gender_sel_loc = (By.NAME, 'gender')
    save_btn_loc = (By.NAME, 'save')
    def input_last_name(self, last_name):
        self.input_to(*self.last_name_ipt_loc, last_name)
    def input_first_name(self, first_name):
        self.input_to(*self.first_name_ipt_loc, first_name)
    def select_gender(self, gender):
        self.select(*self.gender_sel_loc, gender)
    def click_save_btn(self):
        self.click(*self.save_btn_loc)
    def add_contract(self, username, gender='男'):
        print(f'添加联系人 {username}')
        last_name, first_name = username[0], username[1:]
        self.input_last_name(last_name)
        self.input_first_name(first_name)
        self.select_gender(gender)
        self.click_save_btn()
...
```
### 9.2.3  页面对象关联
在用例中，每用到一个页面对象，我们需要导入并实例化这个页面对象类，再进行操作，以及每个操作后，我们都不知道现在哪个页面。
我们可以在页面操作方法中，某些会进行页面跳转的方法中，直接返回跳转到的页面对象，例如：
- 登录页面，登录操作后，返回后台主页对象；
- 后台主页对象，点击添加联系人链接后，返回添加联系人页面对象；
- 添加联系人页面，操作添加联系人后，返回联系人详情页面对象；
这里我们需要一个新的页面对象，联系人详情页面，我们先建立这个页面类，操作可以先留空。修改LoginPage的login方法、DashboardPage的click_add_contract_link方法及ContractAddPage的add_contract方法，增加返回值，代码修改如下：

> 代码test_monica_contract_add_v3_2.py部分内容
```python
...
class LoginPage(BasePage):
    ...
    def login(self, email, password):
        ...
        return DashboardPage(self.driver)
class DashboardPage(BasePage):
     ...
    def click_add_contract_link(self):
        ...
        return ContractAddPage(self.driver)
class ContractAddPage(BasePage):
    ...
    def add_contract(self, username, gender='男'):
        ...
        return ContractDetailPage(self.driver)
class ContractDetailPage(BasePage):
    pass
```
此时用例可以修改为：

> 代码test_monica_contract_add_v3_2.py部分内容
```python
def test_monica_contract_add(driver):
    page = LoginPage(driver)
    page = page.login('<你的Monica账户邮箱>', '<你的Monica账户密码>')
    assert page.url == 'http://localhost:8080/dashboard', '登录失败'
    page = page.click_add_contract_link()
    page.add_contract('张三', '男')
    assert 'http://localhost:8080/people/' in page.url
```


这样就只需要一个入口页面（LoginPage）进行操作即可，如果去掉中间的断言方法，也可以使用链式操作，实现整个操作流程，用例代码如下：
```python
def test_monica_contract_add(driver):
    page = LoginPage(driver)
    page.login('<你的Monica账户邮箱>', '<你的Monica账户密码>')\
        .click_add_contract_link()\
        .add_contract('张三', '男')
    assert 'http://localhost:8080/people/' in page.url
```

这样用例显得更加简洁紧凑。同时我们也可以使用Fixture函数，提前将入口页实例化好，这样所有用例无需导入，即可直接使用开展测试。实现代码如下：

> 代码test_monica_contract_add_v3_2.py部分内容
```python
@pytest.fixture
def page(driver):
    return LoginPage(driver)
def test_monica_contract_add(page):
    page.login('<你的Monica账户邮箱>', '<你的Monica账户密码>')\
        .click_add_contract_link()\
        .add_contract('张三', '男')
    assert 'http://localhost:8080/people/' in page.url
```
    
这样，用例中无需直接使用driver这个Pytest-Selenium提供的Fixture函数，直接使用上层的页面对象（登录页对象）开始操作即可。
### 9.2.4  使用日志
在项目中必要的输出信息可以帮助我们显示测试步骤的一些中间结果和快速的定位问题，虽然Pytest框架可以自动捕获print信息并输出屏幕或报告中，当时更规范的应使用logging的记录和输出日志。 相比print, logging模块可以分等级记录信息。
我们可以在BasePage封装的方法中输出DEBUG级别的日志、在具体页面对象的操作，如login、add_contract中使用INFO级别的日志，代码修改如下：

> 代码test_monica_contract_add_v3_3.py部分内容
```python
import logging
...
class BasePage(object):
    ...
    def wait(self, secs=1):
        logging.debug(f"等待 {secs}s")
        ...
    def click(self, by, value, timeout=None):
        logging.debug(f'点击元素 {by}={value}')
        ...
    def input_to(self, by, value, text, timeout=None):
        logging.debug(f'向元素 {by}={value} 输入文本 {text}')
        ...
    def select(self, by, value, text, timeout=None):
        logging.debug(f'元素 {by}={value} 选择 {text}')
        ...
class LoginPage(BasePage):
    ...
    def login(self, email, password):
        logging.info('登录Monica')
        ...
class ContractAddPage(BasePage):
    ...
    def add_contract(self, username, gender='男'):
        logging.info(f'添加联系人 {username}')
        ...
```
同时，我们需要在pytest.ini中添加命令行日志及日志文件相关的配置，修改pytest.ini，内容如下：
文件pytest.ini内容
```ini
[pytest]
addopts = --driver=chrome --html=report.html --self-contained-html
log_cli=True
log_cli_level=INFO
log_cli_format=%(asctime)s %(levelname)s %(message)s
log_cli_date_format=%Y-%m-%d %H:%M:%S
log_file = run.log
log_file_level = DEBUG
log_file_format = %(asctime)s %(levelname)s %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S
```
这里，命令行日志及日志文件采用了不同的日志级别，命令行输出INFO级别的日志（较为简洁），日志文件中输出DEBUG级别的日志（更加详细）。
### 9.3.4  元素定位失败时自动截图
由于自动化往往需要无值守运行，因此当用例执行失败时需要提供尽量丰富的现场信息，除了日志外，在元素定位失败时报错当前页面的截图有助于辅助判断是否已经切换到指定页面，目标元素是否已加载或是否被遮挡。
由于我们在页面基类中封装了统一的定位方法find，在规范使用情况下，所有的页面对象在定位操作元素时都通过该方法进行定位元素，因此，我们只需要在find中增加异常判断及截图逻辑即可，实现代码如下：

> 代码test_monica_contract_add_v3_4.py部分内容
```python
from time import sleep, time
from selenium.common import NoSuchElementException
...
class BasePage(object):
    ...
    def find(self, by, value, timeout=None):
        if timeout:
            wait = WebDriverWait(self.driver, timeout)
            try:
                wait.until(EC.presence_of_element_located((by, value)))
            except TimeoutError:
                self.driver.save_screenshot(f'{int(time())}.png')
        try:
            return self.driver.find_element(by, value)
        except NoSuchElementException:
            self.driver.save_screenshot(f'{int(time())}.png')
```
由于使用主动等待（WebDriverWait）和正常定位元素时抛出的异常不一样，这里需要分别进行处理，默认保存的页面截图文件以出错时的时间戳进行命名。
## 9.3  增加灵活性
框架的灵活性是指框架可以快速识别不同的环境及配置，满足用户不同的需求，例如：
- 不同的测试环境的基础URL可能不同；
- 不同的用户或测试环境使用的用户可能不同；
- 在挑选用例运行时，可能需要挑选不通特性（标记）的用例；
- 在挑选用例运行时，可能要选择不同优先级（level）的用例；
- 有时候可能需要用例按指定的顺序运行；
以下通过不同的小节来讲解各种灵活性需求的实现。
### 9.3.2  实现可配置
对于环境基础URL、测试使用的用户、密码等经常需要更改的项，我们需要实现可配置，使用通过简单修改配置便可快速切换测试环境及用户、密码等。
最简单的，我们可以使用Fixture函数来实现不同的配置项。
### 9.3.2  实现多环境切换
对于自动化测试框架来说，希望能一套用例来可以跑多套环境。不同环境的执行流程基本是一样的，不一样的是服务器地址（base_url）和所使用的数据。
我们可以使用pytest-base-url插件，配合pytest-variables插件来实现服务器地址和测试数据的切换。示例如下：

```python
from time import sleep
from selenium import webdriver
import pytest

def test_baidu_search_01(driver, base_url, variables):
    url = base_url + '/'
    keyword = variables['keywords'][0]
    driver.get(url)
    driver.find_element_by_id('kw').send_keys(keyword)
    driver.find_element_by_id('su').click()
    sleep(1)
    assert '韩志超' in driver.title
    
if __name__ == '__main__':
    # --html需要pip install pytest-html
    # --driver 需要pip install pytest-selenium
    # --base-url 需要pip install pytest-base-url
    # --variables 需要安装 pip install pytest-variables
    pytest.main([__file__, '--driver=chrome', '--html=report.html','--self-contained-html', '--base-url=https://www.baidu.com', '--variables=test.json'])
```
测试环境数据test.json内容如下：
```
{
  "keywords":
    ["博客园 韩志超"，"临渊",  "简书 韩志超"]
}
```
由于pytest-selenium默认把所有url当作敏感url，我们需要在pytest.ini中通过配置进行关闭，即设置无敏感url。具体设置方法如下：
```
[pytest]
sensitive_url = None
```
### 9.3.2  用例标记
除了使用目录来按模块来整理用例外，我们也可以通过规范用例命令规则及自定义标签来组织用例。除@pytest.mark.skip、@pytest.mark.skipIf、@pytest.mark.xfail、@pytest.mark.paramitrize等系统标记外，我们可以自定义任何标记来使用，如使用smoke标记冒烟用例，使用destructive标记破坏性用例（有修改操作未还原的），使用abnormal标记异常用例，使用flaky标记不稳定用例，使用h5标记H5相关用例。
在严格模式下，可用标签需要在pytest.ini例出来，以防止随意使用任意标签导致的标签混乱问题。在pytest.ini文件注册标签如下：
```
[pytest]
markers =
    smoke: smoke test case
    destructive: destructive test case
    abnormal: abnormal test case
    flaky: flaky test case
    h5: h5 test case
    hzc: testcase by hzc
```
用例标记方式如下
```python
@pytest.mark.smoke
def test_baidu_search_01(driver, base_url, variables):
    ...
```
用例可以添加多个标记，运行时可以使用pytest -m 命令挑选标签执行，如

```bash
pytest -m "smoke and h5"
```

即运行带例smoke和h5两个标签的用例，另外也支持or，not等多个标签的逻辑判断。
在规划标记是，也可以按维护人添加标记，以方便运行某人负责的所有用例。
### 9.3.3  用例等级
除了自定义用例标记外，我们可以对用例重要性进行评级，来快速回归不同优先级的用例。对用例进行标记等级，我们可以使用三方插件pytest-level。
安装方式
```bash
$ pip install pytest-level
```
标记用例
```python
@pytest.mark.smoke
@pytest.mark.level(1)
def test_baidu_search_01(driver, base_url, variables):
    ...```
运行方式
```
$ pytest --level=1
```
### 9.3.4  用例顺序
在某些情况下我们如何希望用例有序，可以使用pytest-order插件实现。
安装方法
```
$ pip install pytest-order
```
标记用例
```python
import pytest

@pytest.mark.order(1)
def test_login():
    pass
@pytest.mark.order(2)
def test_add_goods():
    pass
@pytest.mark.order(3)
def test_query_goods():
    pass
@pytest.mark.order(4)
def test_del_goods():
    pass
```
运行时用例便可按数字从小到大的顺序运行。
一般情况下，不建议用例之间有顺序依赖。每条用例应该可以独立执行的，有依赖的测试用例建议作为测试步骤放到一个大的场景用例中去，这样可以确保执行的有序，如：
```python
def login(username, password):
    pass
def add_goods(goods_name, *args):
    pass
def query_goods(goods_name):
    pass
def del_goods(goods_name):
    pass
def test_login():
    login('user', 'pwd')
    # ... 断言，结果判断
def test_add_goods():
    login('user', 'pwd')
    add_goods('...')
    # ... 断言，结果判断
def test_query_goods():
    login('user', 'pwd')
    add_goods('...')
    query_goods('...')
    # ... 断言，结果判断
def test_del_goods():
    login('user', 'pwd')
    add_goods('...')
    query_goods('...')
    del_goods('...')
    # ... 断言，结果判断
```
也可以写一个大的场景用例，包含4个测试点点验证：
这看起来有很多冗余，并且在一个用例中，如test_del_goods中，登录、添加商品、查询商品应该被视为是测试准备（setup），只保留核心的del_goods('...')作为测试步骤。
```python
def test_login_add_query_del_goods():
    login('user', 'pwd')
    # ... 断言，结果判断
    add_goods('...')
    # ... 断言，结果判断
    query_goods('...')
    # ... 断言，结果判断
    del_goods('...')
    # ... 断言，结果判断
```
这样步骤永远是有序的，一个步骤失败，后续步骤将中断，不再执行。
另外，针对上面每个验证点分开的用例形式，我们可以使用Fixture模块化的特性，采用步骤渐进的方式来编写每一个带依赖的步骤，示例如下：
```python
import pytest
@pytest.fixture
def login():
    # fixture一般不使用普通参数，默认用户名密码需要确定并写在函数中
    useranme, password = 'user', 'pwd'
    # ... 业务逻辑
@pytest.fixture
def add_goods(login):  # 依赖login步骤
    pass
@pytest.fixture
def query_goods(add_goods):  # 依赖add_goods步骤
    pass
def test_login():   # 作为参数引用login步骤
    login('user', 'pwd')
    # ... 断言，结果判断
def test_add_goods(login):
add_goods('...')
    # ... 断言，结果判断
def test_query_goods(add_goods):
    query_goods('...')
    # ... 断言，结果判断
def test_del_goods(query_goods):
    del_goods('...')
    # ... 断言，结果判断
```
这样做的好处是，任何一个用例都可以单独执行。缺点是一起执行时，登录、添加商品等会执行不止一遍。
## 9.4  不稳定用例处理
不稳定用例（flaky tests）是UI自动化测试过程中一个典型的问题。主要的策略有：
- 暂时跳过用例，等环境或用例稳定后再运行
- 为用例设置超时时间防止卡死
- 用例失败后自动重试
以下为3种策略的具体实现方式。
### 9.4.1  标记跳过用例
对于不稳定的用例，暂时跳过用例是最常用的方法之一。
在用例上使用`@pytest.mark.skip()`、`@pytest.mark.skipIf()`或在Fixture函数、测试用例中使用pytest.skip()方法即可跳过该用例。
### 9.4.2  使用超时时间
未避免用例卡死（长时间未结束），我们可以使用pytest-timeout为用例统一或分别添加超时时间。
安装方法：
```bash
pip install pytest-timeout
```
使用方法如下：
全局使用

```bash
pytest --timeout=300
```

配置方法：

```ini
[pytest]
timeout = 300
```

单独使用：

```python
@pytest.mark.timeout(60)
def test_foo():
    pass
```

### 9.4.3  用例失败重跑
对于不稳定用例，失败后立即重试可以应对一些环境或UI不稳定导致的一些用例失败的问题。我们可以很方便的借助pytest-rerunfailures这个插件来实现这个功能。
安装方法：
```bash
pip install pytest-rerunfailures
```
使用方法：
```bash
pytest -rerun 3 rerun-delay 1
```
即每次失败后，延迟1秒进行重试，最多重试3次，有一次成功，则视为成功。3次都失败则视为失败。
## 9.5  从面向过程到面向对象
### 9.5.1  按页面归类元素操作
Page Object模式即Page Object Module，页面对象模型模式，是一种基于模块的框架结构。以页面为对象来统一管理页面上元素的定位及操作，修改后示例如下：

> 代码test_baidu_search_v4内容
```python
from time import sleep
from selenium import webdriver
import pytest

class BaiduHomePage:
    url = 'https://www.baidu.com'
    search_ipt_loc = ('id', 'kw')  # 百度搜索框
    search_btn_loc = ('id', 'su')  # 百度一下按钮
    def __init__(self, driver):  # 初始化传入driver
        self.driver = driver     # 绑定页面对象
    def open(self):
        print('打开百度页面')
        self.driver.get(self.url)
    def input_search_keyword(self, keyword):
        print(f'输入搜索关键词 {keyword}')
        self.driver.find_element(*self.search_ipt_loc).send_keys(keyword)
    def click_search_button(self):
        print('点击百度一下按钮')
        self.driver.find_element(*self.search_btn_loc).click()
    def search(self, keyword):  # 页面常用组合操作
        print(f'搜索关键字 {keyword}')
        self.open()
        self.input_search_keyword(keyword)
        self.click_search_button()
        sleep(0.5)
@pytest.fixture
def baidu_home(driver):   # 自定义一个fixture方法方便多个用例共享
    page_obj = BaiduHomePage(driver)
    return page_obj
def test_baidu_search_01(driver, baidu_home):
    baidu_home.seach('博客园 韩志超')
    assert '韩志超' in driver.title
if __name__ == '__main__':
    # --html需要pip install pytest-html
    # --driver 需要pip install pytest-selenium
    pytest.main([__file__, '--driver=chrome', '--html=report.html','--self-contained-html'])
```
上例BaiduHomePage中除了封装了每个元素的单独操作外，还封装了组合的search操作，这样既可以灵活使用（如，只输入搜索词，不点击搜索按钮），也方便用例中快速使用组合操作。
上例中没有把页面对象baidu_home的实例化放到用例中，而是单独封装了一个Fixture方法，这样的好处是，所有需要用到此页面对象的用例都可以直接使用。
框架封装的一个设计方向就是让用户的使用尽可能简单。用例的编写便是用户的一个高频使用场景，我们通过设计要使的用例的编写尽可能简单。
### 9.5.2  封装常用方法
除了用例的编写外，页面模型也是需要用户进行编辑和新增的，如何使页面模型的编写更简单呢？比如每个页面模型都要编写初始化方法传入driver，比如常用的通过节点文本定位、鼠标悬浮、强制等待、主动等待、偶现元素处理等。我们可以编写一个页面基础类作为所有所有页面对象的父类，在页面基础类中实现这些操作。如下例：

> 代码test_baidu_search_v5.py内容
```python
from time import sleep
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException, NoAlertPresentException
import pytest

class BasePage:
    url = None
    def __init__(self, driver):  # 初始化传入driver
        self.driver = driver     # 绑定页面对象
    @property
    def title(self):
        return self.driver.title
    @property
    def page_source(self):
        return self.driver.page_source
    def open(self, url=None):
        url = url or self.url  # 如果没有指定url则打开页面url
        print(f'打开 {url}')
        if url:
            self.driver.get(self.url)
        return self  # 返回self以支持链式操作如page.open('').click_element('')
    def wait(self, secs=1):
        print(f"等待 {secs}s")
        sleep(secs)
        return self
    def find_element(self, by, value, timeout=None, ignore_error=False):
        """元素定位方法增加显式等待和忽略异常选项（处理偶现元素）"""
        try:
            if timeout is None:
                return self.driver.find_element(by, value)
            else:
                return WebDriverWait(self.driver, timeout).until(EC.presence_of_element_located((by, value)))
        except NoSuchElementException:
            if ignore_error is False:  # 不忽略错误则抛出异常
                raise
    def click_element(self, by, value, timeout=None, ignore_error=False):
        print(f'点击元素 {by}={value} 超时 {timeout} 忽略异常 {ignore_error}')
        self.find_element(by, value, timeout, ignore_error).click()
        return self
    def input_text(self, by, value, text, timeout=None):
        print(f'向元素 {by}={value} 输入文本 {text} 超时 {timeout}')
        elm = self.find_element(by, value, timeout)
        elm.clear()
        elm.send_keys(text)
        return self
    def move_to_element(self, by, value):
        print('移动到元素 {by}={value}')
        elm = self.find_element(by, value)
        ActionChains(self.driver).move_to_element(elm).perform()
        return self
    def switch_to_frame(self, *frames):
        print(f'切换到框架 {" > ".join(frames)}')
        for frame in frames:
            self.driver.switch_to.frame(frame)
    def switch_to_window(self, index):
        print(f'切换到第{index+1}个窗口')
        window_list = self.driver.window_handles
        self.driver.switch_to.window(window_list[index])
        return self
    def dismiss_alert(self, ignore_error=False):
        print("关闭警告弹框")
        try:
            self.driver.switch_to.alert().dissmiss()
        except NoAlertPresentException:
            if ignore_error is False:
                raise
        return self
    def remove_attr(self, by, value, attr):
        print(f'移除元素 {by}={value} {attr}属性')
        elm = self.find_element(by, value)
        js_script = f'arguments[0].removeAttribute("{attr}");'
        self.driver.execute_script(js_script, elm)
        return self
```
我们在BasePage页面基础类里，我们将页面标题driver.title，页面源码driver.page_source绑定给页面对象以方便获取。
我们定义了一个open方法拥有打开指定url或者子类配置的url。重新封装了find_element并扩展了显式等待和对偶现元素的支持（偶现元素定位不到视作未出现，不报错）。除了find_element方法返回元素对象外，其他操作方法都返回self对象本身，这样可以使页面对象支持链式操作，如：
```python
...
page = BasePage(driver)
page.open('https://www.baidu.com/').click_element('id','su).input_text('简书韩志超').wait()
```
此外我们还封装了点击元素、输入文本、鼠标悬浮、切换窗口、框架、关闭警告框、移除元素属性等常用操作。我们在每种基本操作中增加了print信息，使得执行过程更透明易懂。
其他常用的操作读者可以根据需求自行补充其他封装方法，或者直接使用对象.driver来调用driver的原生方法，如定位一组元素：
```python
...
page = BasePage(driver)
elm_list = page.driver.find_elements_by_xpath('//li')
```
有了页面基础类，每个页面对象类写起来遍稍微简略点，BaiduHomePage类修改后代码如下：
```python
class BaiduHomePage(BasePage):
    url = 'https://www.baidu.com'
    search_ipt_loc = ('id', 'kw')  # 百度搜索框
    search_btn_loc = ('id', 'su')  # 百度一下按钮
    def input_search_keyword(self, keyword):
        self.input_text(*self.search_ipt_loc, keyword)
    def click_search_button(self):
        self.click_element(*self.search_btn_loc)
    def search(self, keyword):  # 页面常用组合操作
        print(f'搜索关键字 {keyword}')
        self.open().input_search_keyword(keyword).click_search_button().wait(0.5)
```
首先集成BasePage类，并无须再写__init__初始化方法，直接配置url和页面元素对象即可。这里对单个元素操作不再添加额外打印信息，使用基础方法click_element、input_text自带的打印信息。
在组合操作search方法中，我们使用了链式操作，写起来更简洁。

> 注：在页面对象类的元素操作中也可以每个操作都返回self，以使得上层测试用例再使用时支持链式操作。
### 9.5.3  提高运行效率
提高运行效率通常以下两种方式：
- 优化用例执行速度：如使用Headless无界面模式、使用Cookie绕过登录、使用页面URL直达内部页面（而不是通过页面一步步操作）及使用接口、数据库（而不是页面操作）进行测准备等。
- 并行执行：并行是开多个浏览器同时执行多条用例，这就要求我们的用例之间没有运行顺序的依赖（用例可以单独运行）。

使用Headless模式
Headless即无界面模式，可以在一定程度上提高用例的执行速度。pytest-selenium插件提供了chrome_options的Fixture函数，可以添加Chrome浏览器参数。我们只需要自定义一个--headless命令后选项，重写chrome_options参数，通过request这个内置的Fixture方法拿到配置对象config，判断命令行选项是否包含--headless来添加对应的浏览器参数即可。

实现方式如下：
```python
# conftest.py
import pytest
def pytest_addoption(parser):
    parser.addoption('--headless', action='store_true', help='run chrome headless')
    
@pytest.fixture
def chrome_options(request, chrome_options):
    if request.config.getoption('--headless'):
        chrome_options.add_argument('--headless')
    return chrome_options
```

使用--headless运行测试用例
```python
...
if __name__ == '__main__':
    # --html需要pip install pytest-html
    # --driver 需要pip install pytest-selenium
    # --base-url 需要pip install pytest-base-url
    # --variables 需要安装 pip install pytest-variables
    pytest.main([__file__, '--driver=chrome', '--headless',
                 '--html=report.html','--self-contained-html',
                 '--base-url=https://www.baidu.com',
                 '--variables=test.json'])
```
多进程并行测试
使用pytest-xdist可以启动多个进程来平均分发多个用例，安装方法如下：
```bash
$ pip install pytest-xdist
```
使用方法非常简单，命令行中添加参数-n=<进程数>即可。
```bash
$ pytest -n=3
```
即启动3个进程来执行所有用例。
### 9.5.4  发送邮件
添加自定义选项和配置
假设我们要实现一个运行完发送Email的功能。 我们自定义一个命令行参数项--send-email，不需要参数值。当用户带上该参数运行时，我们就发送报告，不带则不发，运行格式如下：
```
pytest test_cases/ --html=report.html --send-email
```
这里，一般应配合--html先生成报告。 由于Pytest本身并没有--send-email这个参数，我们需要通过Hooks方法进行添加。
```python
# conftest.py
def pytest_addoption(parser):
    """Pytest初始化时添加选项的方法"""
    parser.addoption("--send-email", action="store_true", help="send email with test report")
```
另外，发送邮件我们还需要邮件主题、正文、收件人等配置信息。我们可以把这些信息配置到pytest.ini中，如：
```ini
# pytest.ini
...
email_subject = Test Report
email_receivers = superhin@126.com
email_body = Hi,all\n, Please check the attachment for the Test Report.
```
这里需要注意，自定义的配置选项需要先注册才能使用，注册方法如下：
```python
# conftest.py
def pytest_addoption(parser):
    ...
    parser.addini('email_subject', help='test report email subject')
    parser.addini('email_receivers', help='test report email receivers')
    parser.addini('email_body', help='test report email body')
```
实现发送Email功能
前面我们只是添加了运行参数和Email配置，我们在某个生成报告时的Hook方法中，根据参数添加发送Email功能，示例如下：
```python
from utils.notify import Email

# conftest.py
def pytest_terminal_summary(config):
    """Pytest生成报告时的命令行报告运行总结方法"""
    send_email = config.getoption("--send-email")
    email_receivers = config.getini('email_receivers').split(',')
    if send_email is True and email_receivers:
        report_path = config.getoption('htmlpath')
        email_subject = config.getini('email_subject') or 'TestReport'
        email_body = config.getini('email_body') or 'Hi'
        if email_receivers:
            Email().send(email_subject, email_receivers, email_body, report_path)
```
## 9.6  框架整理
### 9.6.1  分类整理
一个好的框架需要清晰的结构，我们使用目录（或包）将不同的脚本进行归类，例如：
- testcases：存放测试用例，可以按模块建立子目录存放fixtures方法集中放在conftest.py中；
- pages：存放页面对象模型，可以按模块建立子目录存放；
- utils：存放常用的工具，方法的封装，如发邮件功能的封装；
另外我们对输入的测试数据（资源），输出的测试报告、日志文件等也需要建立指定的目录存放，如：
- data/：存放测试数据或资源；
- reports/：存放测试报告，运行日志等
另外加上Pytest运行配置pytest.ini和一些说明文件，如：
- pytest.ini：Pytest配置文件；
- requirements.txt：运行依赖的三方包；
- README.md：框架说明文件。
整个框架结构如下：
```
WebAuto/
  | -- data/
    | -- test.json
    | -- prod.json
  | -- pages/
    | -- baidu_page.py
    | -- base_page.py
  | -- reports/
  | -- testcases/
    | -- test_baidu_search.py
  | -- utils/
    | -- send_email.py
  conftest.py
  pytest.ini
  requirements.txt
  README.md
```
### 9.6.2  敏感数据处理
在测试环境中经常会用到一些身份认证信息，如用户名、密码等，这些属于敏感数据，直接写在代码中，有可能会造成敏感信息泄露。最简单的做法是，将这些敏感信息配置到所运行机器（如本机）的环境变量中。如，我们在自己电脑上的环境变量中添加两个变量：
```bash
WEBAUTO_DEFAULT_USER = admin
WEBAUTO_DEFAULT_PWD = 123456
```
然后我们可以在代码中通过os.getenv()来获取指定的环境变量：
```python
import os
username = os.getenv('WEBAUTO_DEFAULT_USER')
password = os.getenv('WEBAUTO_DEFAULT_PWD')
...
```
### 9.6.3  声明依赖文件
一般来说，框架不只是给自己一个人使用的，多数情况下需要大家协作完成用例的补充。这时候我们一般要在项目中新建一个requirements.txt的来列出所有需要安装的三方包，例如：
```txt
selenium
pytest
pytest-selenium
pytest-html
pytest-variables
pytest-timeout
pytest-level
pytest-base-url
pytest-ordering
pytest-rerunfailures
pytest-xdist
```
### 9.6.4  编写使用说明
一个框架最好能有一个使用说明一样的文件，简单阐述下框架的结构、有哪些特性、如何编写维护用例、如何运行等等。一般推荐使用Markdown语法编写。Markdown是一种标记语言，可以通过不同的标记写出层次分明的文档，示例如下：
```markdown
# WebAuto **项目Web自动化测试框架
使用Pytest + Selenium基于POM模式搭建。
## 特性
-
```
## 9.7  小结
