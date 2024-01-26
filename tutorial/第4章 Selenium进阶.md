在学习完基础的Selenium操作后，本章继续讲解Selenium的一些进阶操作，包括使用浏览器选项无界面启动浏览器，XPath及CSS Selector语法，模拟键盘鼠标操作，等待及使用JavaScript等。

## 4.1  浏览器选项
除了以常规的样式启动浏览器外，还可以通过Chrome Options浏览器选项，添加一些参数，对浏览器进行特定的设置，如无界面模式启动、全屏启动等等。
以Chrome浏览器为例，首先需要实例化一个ChromeOptions类的对象，代码如下：
from selenium import webdriver
options = webdriver.ChromeOptions()
options对象常用属性和方法如下：
- binary_location：用于设置浏览器地址。
- debugger_address：远程调试地址。
- headless：设置是否无界面模式。
- add_argument()：添加启动参数。
- add_extension()：添加插件。
- add_encoded_extension()：添加编码后的插件。
- add_experimental_option()：添加实验选项。
- to_capabilities()：转为desired capabilities格式。
当有多个版本的浏览器或浏览器不在系统默认安装位置时可以通过binary_location属性指定浏览器的安装位置。headless属性设置为True时，浏览器以无界面形式启动。add_argument()可以添加启动参数。一些简单的使用示例如下：

### 4.1.1  无界面运行（Headless模式）
无界面模式只需要设置headless属性为True即可，代码如下：

> 代码selenium_with_headless_chrome.py内容
```python
from selenium import webdriver

options = webdriver.ChromeOptions()  # 自定义浏览器选项
options.headless = True
driver = webdriver.Chrome(options=options)
driver.get('https://www.baidu.com/')
print(driver.title)
driver.quit()
```
### 4.1.2  使用Chromium内核的360浏览器

国产的360安全浏览器，360急速浏览器，QQ浏览器，遨游浏览器甚至新版还未上市的Edge浏览器都是基于Chrome浏览器的开源版本Chromium开发来的。所以360浏览器可以理解为一个定制的Chrome浏览器，最新360安全浏览器是基于Chromium 63版本的，如图5.1所示。

图5.1 360安全浏览器内核版本

下载对应的2.35版本chromedriver驱动并放置到D盘。在启动浏览器时，可以指定驱动的位置，代码如下：
```python
driver = webdriver.Chrome('D:\\chromedriver.exe') 
```

使用360浏览器的完整示例代码如下：

> 代码selenium_with_360safe_browser.py内容

```python
from selenium import webdriver

options = webdriver.ChromeOptions()
options.binary_location = 'C:\\Users\\Cactus\\AppData\\Roaming\\360se6\\Application\\360se.exe'
driver = webdriver.Chrome('D:\\chromedriver.exe', options=options)
driver.get('https://www.baidu.com')
print(driver.title)
driver.quit()
```
### 4.1.3  Selenium模拟手机浏览器
可以通过添加实验选项mobileEmulation来实现模拟浏览器，代码如下：
代码selenium_mobile_emulation.py内容
```python
from selenium import webdriver

options = webdriver.ChromeOptions()
options.add_experimental_option('mobileEmulation', {'deviceName': 'iPhone X'})
driver = webdriver.Chrome(options=options)
driver.get('https://www.baidu.com')
```
实现效果如图5.2所示。
图5.2  Selenium模拟手机浏览器
另外也可以通过设置User-Agent参数来伪装使用的是手机浏览器，代码如下：
代码selenium_with_user_agent.py内容
```python
from selenium import webdriver
options = webdriver.ChromeOptions()
options.add_argument('user-agent="MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; '
                     'CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1"')
driver = webdriver.Chrome(options=options)
driver.get('https://www.baidu.com')
driver.quit()
```
### 4.1.4  使用用户真实环境
Selenium每次打开Chrome浏览器时，每次都是一个初始的全新环境，有时候，我们需要一些当前用户的一些设置和插件等，便可以通过ChromeOptions参数，通过指定用户数据目录--user-data-dir来设置成真实用户目录来实现。
浏览器地址栏访问chrome://version/查看个人资料路径，如图5.3所示。
图5.3  Chrome 查看用户真实路径
复制出个人资料路径，去掉最后的/Default，得到用户数据目录为：/Users/superhin/Library/Application Support/Google/Chrome
新建脚本，使用ChromeOptions()，添加--user-data-dir参数

> 代码selenium_user_data_dir.py内容
```python
from selenium import webdriver
from time import sleep

option = webdriver.ChromeOptions()
option.add_argument('--user-data-dir=C:\\Users\\Secoo\\AppData\\Local\\Google\\Chrome\\User Data')
driver = webdriver.Chrome(options=option)
driver.get('https://www.baidu.com')
sleep(5)
driver.quit()
```

> 注：运行脚本时需要退出你的Chrome浏览器，不然会显示，用户数据目录已经被使用。
selenium.common.exceptions.InvalidArgumentException: Message: invalid argument: user data directory is already in use, please specify a unique value for --user-data-dir argument, or don't use --user-data-dir

如果你浏览器设置了打开历史网页，启动浏览器时会打开之前未关闭的网页。
### 4.1.5  常用浏览器启动参数
一些常用的启动参数如下：
- 禁用图片加载：options.add_argument('blink-settings=imagesEnabled=false')
- 自动打开开发者工具：options.add_argument("auto-open-devtools-for-tabs")
- 设置窗口尺寸：options.add_argument('window-size=300,600')
- 设置窗口启动位置：options.add_argument('window-position=120,0')
- 禁用GPU渲染：options.add_argument('disable-gpu')
- 全屏启动：options.add_argument('start-fullscreen')
- 全屏启动，无地址栏：options.add_argument('kiosk')
- 启动时，不激活（前置）窗口：options.add_argument('no-startup-window')
注：由于chromedriver版本的更新，有些启动参数可能会有失效的现象。
## 4.2  万能的XPath
XPath即XML路径语言，支持HTML，用于查找节点。CSS Selector即CSS路径选择器，是CSS在渲染页面是查找节点的一种语法。
XPath和CSS Selector是两种高级的定位语法。掌握XPath和CSS Selector语法有助于我们方便的定位到各种元素。即使元素没有特定的属性时，仍可以通过文本或父级或祖先级节点定位到该节点。
相对来说XPath语法比CSS Selector更强大，支持节点文本，向上查找等等，而CSS Selector则更快，通常情况下建议优先采用CSS Selector定位。
XPath以其强大从查找能力被称为万能的查询方式。XPath支持绝对路径、相对路局、模糊匹配、位置、文本、包含及轴等多功能。
### 4.2.1  绝对路径结合位置索引定位
在XPath中使用/代表使用绝对路径从根节点逐层进行查找，如果不是第一个子节，可以使用[n]来取第n个节点，如图5.3所示，百度首页搜新闻链接。

图5.3  百度首页搜新闻链接
根据状态栏上的路径层次信息结合元素层级，可以写出搜索框元素的XPath绝对路径为：
/html/body/div/div/div[3]/a
XPath中的位置索引从1开始，div[3]代表第三个div子元素。在Elements面板中可以按Ctrl+F搜索框，搜索框支持使用字符串、XPass和CSS Selector进行搜索，搜索框右边1 of 6显示的结果数量为6个，当前取第一个。
### 4.2.2  使用相对路径结合属性定位
由于页面层次繁多，并且有时会动态插入删除节点，使用绝对路径会不太稳定。一般最常用的是使用相对路径结合特定属性进行定位。
在XPath中使用//表示从任意路径开始匹配，如//div会匹配所有div标签，也可以//*匹配任意路径任意节点。
在XPath中使用[]添加过滤条件，使用@取当前节点的属性，常用的方式如下：
- `//div[@id]`：查询所有拥有id属性的div节点。
- `//div[@id="wrapper"]`：查询id属性为wrapper的div节点。
- `//input[@id="kw" and @name="wd"]`：查询id属性为kw并且，name属性为wd的input节点。除and外，也可以是使用or、not，结合多个属性进行判断。
### 4.2.3  使用XPath函数结合节点文本查找
当使用属性不太好定位节点时，也可以通过节点文本定位节点，如定位百度首页新闻链接。新闻链接实际是一个文本为“新闻”的a标签节点，使用XPath可以表示为：
`//a[text()="新闻"]`
此处的text()是XPath计算节点文本的函数，其他常用的函数如下：
- contains()：包含，如`//a[contains(text(), "新")]`，表示文本中包含“新”的链接
- starts-with()：以特定字符串开头，如`//div[starts-with(@id, "wa")]`，表示id属性以“wa”开头的div节点
- last()：最后一个子元素，如`//div[@id="warpper"]/div[last()]`，表示id为“warpper”的最后一个div子元素。
- `normalize-space()`：去除左右空白字符，如`//a[normalize-space(text())="新闻"]`，表示去除左右空白字符后，文本等于“新闻”的链接。
### 4.2.4  向上查找
XPath中支持使用“..”返回上级节点查找，如图5.5所示，查找李四所在行第4个单元格中的查看链接。
图5.5  使用XPath向上查找
首先找到文本为“李四”的单元格//td[text()="李四"]，然后向上查找到所在行tr标签，然后再找到第4个单元格中的链接，完整XPath如下：
```
//td[text()="李四"]/../td[4]/a[text()="查看"]
```

>注：如果td中有span等标签，要根据实际网页源码逐级向上或向下查找。
### 4.2.5  使用XPath轴向前和向后查找
轴是通过当前节点相对位置查找的一种方式，常用的如：
- following：选取当前节点后的所有节点。
- following-sibling：选取当前节点后的所有同级节点。
- preceding：选取当前近节点前的所有节点。
- preceding-sibling：选取当前节点前的所有同级节点。
如，查找“百度一下”按钮前面的最近的一个input标签，如图5.6所示。


图5.6 使用XPath轴查找附件节点
XPath语法如下：
```
//input[@value="百度一下"] /preceding::input[1]
```
### 4.2.6  万能的XPath
使用XPath可以完全替代使用id、name、class_name、tag_name、link_text、partial_link_text，以及逐层定位等方式，转换关系如下：
- find_element(By.ID, 'kw')：相当于find_element(By.XPATH, '//*[@id="kw"]')
- find_element(By.NAME, 'wd')：相当于find_element(By.XPATH, '//*[@name="wd"]')
- find_element(By.CLASS_NAME, 's_ipt')：相当于find_element(By.XPATH, '//*[contains(@class, "s_ipt")]')
- find_element(By.TAG_NAME, 'input')：相当于find_element(By.XPATH, '//input')
- find_element(By.LINK_TEXT, '新闻')：相当于find_element(By.XPATH, '//a[text()="新闻"]')
- find_element(By.PARTIAL_LINK_TEXT, '新')：相当于find_element(By.XPATH, '//a[contains(text()="新")]')
- find_element(By.ID, 'wrapper').find_element(By.ID, 'kw')：相当于find_element(By.XPATH，'//*[@id="wrapper"]//*[@id="kw"]')

在使用XPath时有以下注意点：
 1. XPath查询速度较慢，因此推荐优先使用CSS Selector。
 2. 代码中使用XPath之前先在浏览器Elements面板中进行验证。
 3. 由于兼容性的问题，Elements中验证过的XPath脚本不一定在Selenium能找到元素，建议修改其他表示语法。
 4. 在Selenium中对于使用XPath定位复合类属性支持不好，如，使用//div[@class="s-top-left s-isindex-wrap"]可能定位不到相应元素，复合类建议使用CSS Selector定位。
## 4.3  犀利的CSS Selector
CSS Selector是CSS定位HTML元素的一种语法，主要结合标签名、属性及子元素位置进行定位，语法简介，速度较快，不支持节点文本和向上查找。Selenium中的通过id、name、class_name三种定位方式底层实际使用的便是CSS Selector。
如图5.7，CSS Selector同样可以在Chrome开发者工具，Elements面板的Ctrl+F中验证。

图 5.7  使用Elements面板验证CSS Selector
### 4.3.1  通过标签结合属性定位
CSS Selector中没有绝对路径，相对路径不要//，取属性也不需要@，字符串也不需要加引号，常用方式如下：
- `input[name]`：查找拥有name属性的input节点。
- `input[name=wd]`：查找name属性为wd的input节点。
- `input#kw`：查找id属性为kw的input节点。
- `input.s_ipt`：查找class属性包含s_ipt的input节点。
- `div.s-top-left.s-isindex-wrap`：查找class属性包含s-top-left和s-isindex-wrap的div节点。
在CSS Selector中id和class可以分别使用#和.代替，其他属性可以使用`[属性名=属性值]`查找。
### 4.3.2  向下及子元素索引
CSS Selector中使用空格或>向下级查找，查找第一个子元素使用:first-child，查找第n个子元素使用:nth-child(n)，示例如下：
- div.s-top-left.s-isindex-wrap a：class属性包含s-top-left和s-isindex-wrap的div节点下的链接
- div.s-top-left.s-isindex-wrap a:first-child：div下的第1个链接。
- div.s-top-left.s-isindex-wrap a:nth-child(3)：div下的第3个链接。
## 4.4  模拟键盘鼠标
在网页的操作中，除了常用的点击、输入操作外，有可能还会需要用到模拟键盘鼠标操作，如按Tab键、回车键、复制粘贴、鼠标悬浮、拖动元素等等等。
### 4.4.1  模拟键盘操作
模拟键盘也是一种输入操作，使用send_keys()方法，不同于输入字符串，这里输入的是Keys模块中的一个指令，如，send_keys(Keys.RETURN)表示输入回车键，示例如下：
代码selenium_keys.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

input_loc = (By.ID, 'kw')
driver = webdriver.Chrome()
driver.get("https://www.baidu.com/")
driver.maximize_window()
elm = driver.find_element(*input_loc)
elm.send_keys("百度你好")
elm.send_keys(Keys.BACK_SPACE)
elm.send_keys(Keys.BACK_SPACE)
elm.send_keys(Keys.CONTROL, 'A')
elm.send_keys(Keys.CONTROL, 'C')
driver.click()  # 空白处点击，取消全选状态
elm.send_keys(Keys.CONTROL, 'V')
elm.send_keys(Keys.RETURN)
driver.quit()
```
示例中，input_loc代表输入框元素的定位器，这里使用通用的定位方法find_elmenent()来定位元素，使用\*号，将元素定位器的元祖变量解包成两个变量。

在上例中，首先定位到百度首页的搜索框，输入“百度你好”，然后按退格两次，全选，复制，点击空白处、粘贴，并按回车键搜索，搜索的文字为“百度百度”，最后退出。
常用的Keys指令如表5.1所示。

表5.1 Selenium Keys按键指令

|指令 |说明 |
|---|---|
|Keys.BACKSPACE|退格键|
|Keys.TAB|Tab键|
|Keys.ENTER|回车键|
|Keys.CONTROL|Ctrl键|
|Keys.SHIFT|Shift键|
|Keys.ALT|Alt键|
|Keys.ESCAPE|ESC键|
|Keys.SPACE|空格键|
|Keys.HOME|HOME键|
|Keys.END|END键|
|Keys.INSERT|插入键|
|Keys.LEFT/Keys.RIGHT/Keys.UP/Keys.DOWN|向左、向右、向上、向下键|
|Keys.NUMPAD0-Keys.NUMPAD9|数字键0-9|
|Keys.F1-Keys.F12|功能键F1-F12|
完整的按键列表，可以查看Keys模块源码。
### 4.4.2  模拟鼠标操作

在网页中有些事件是通过鼠标悬浮、双击甚至右键单击触发的，如图5.7，百度首页的设置选项，需要鼠标悬浮才能显示。
图5.7  百度首页设置菜单
模拟鼠标操作要使用Selenium中的ActionChains动作链。操作步骤如下：
 1. 从selenium.webdriver.common包的action_chains模块导入ActionChains类。
 2. 使用driver实例化一个ActionChains对象。
 3. 使用ActionChains对象，设计操作流程，并perform()执行。
ActionChains对象支持以下元素操作。
- click()：点击。
- send_keys()：输入。
- send_keys_to_element()：向元素输入。
- double_click()：双击。
- context_click()：右键单击。
- click_and_hold()：点击并按住不放。
- drag_and_drop()：拖动元素1到元素2位置。
- move_to_element()：移动到元素。
- pause()：暂停等待。
- key_down()：按下键。
- key_up()：松开键。
- release()：松开鼠标。
- perform()：执行操作。
- 
使用示例如下：

> 代码selenium_action_chains.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.by import By
from time import sleep

driver = webdriver.Chrome()
driver.get("https://www.baidu.com/")
driver.implicitly_wait(10)
setting = driver.find_element(By.LINK_TEXT, '设置')
ActionChains(driver).move_to_element(setting).perform()
sleep(0.5)
driver.find_element_by_link_text('高级搜索').click()
driver.quit()
```
由于下拉菜单从隐藏到显示需要一点时间，所以一般要等待后再继续操作。
>注：新版百度页面“设置”元素可能有所变动，请根据实际情况定位。
## 4.5 等待与期望条件
在处理页面流程的过程中，由于页面的加载、JavaScript脚本的执行或者业务流程的需要。由于元素有可能还没有加载出来，此时直接定位会定位不到而抛出异常，因此，往往需要等待一定的时间再进行定位及操作。
### 4.5.1  三种等待方式
Selenium常用的等待策略有三种，强制等待、智能等待（隐性等待）及显性等待。
1．强制等待
强制等待，是指使用time.sleep(秒数)，使程序强制休眠一定的时间再继续执行。一般强制等待只能休眠固定的时间，不能根据页面元素的实时加载状态来判断继续或结束等待。如图5.8，百度首页登录流程。


图5.8  百度首页登录流程
在点击登录后，弹出对话框，这种DIV对话框是直接写在HTML中的，默认是隐藏状态，添加登录时触发JavaScript执行使期由隐藏变为显示，由于JavaScript执行需要一定的时间，因此常常需要进行等待。
有些页面元素状态切换比较快，也可以不使用等待，如在点击用户名登录时，登录对话框里由二维码变为用户名密码输入框。示例代码如下：

> 代码selenium_sleep.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
import time

driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.find_element(By.LINK_TEXT, '登录').click()
time.sleep(0.5)
driver.find_element(By.XPATH, '//*[text()="用户名登录"]').click()
time.sleep(0.2)  # 实测可以省略
driver.find_element(By.CSS_SELECTOR, 'input[placeholder="手机/邮箱/用户名"]').send_keys('superhin001')
driver.find_element(By.CSS_SELECTOR, 'input[placeholder="密码"]').send_keys('*******')
driver.find_element(By.CSS_SELECTOR, 'input[value="登录"]').click()
driver.quit()
```
强制等待常用与有新页面加载或弹出对话框及页面元素变换的场景。在定位元素报错时，尝试在定位之前添加等待也是解决问题的常用思路之一。

2．智能等待
智能等待，智能等待是使用driver.implictly_wait(最大等待时间）。每次会话（一次运行为一次会话）只需要使用一次。使用智能等待后，在任何元素定位不到时，会自动间隔一定的时间再次定位，直到定位到元素或者超时。使用智能等待示例如下：

> 代码selenium_implicitly_wait.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
driver = webdriver.Chrome()

driver.implicitly_wait(10)
driver.get('https://www.baidu.com')
driver.find_element(By.LINK_TEXT, '登录').click()
driver.find_element(By.XPATH, '//*[text()="用户名登录"]').click()
driver.find_element(By.CSS_SELECTOR, 'input[placeholder="密码"]').send_keys('********')
driver.find_element(By.CSS_SELECTOR, 'input[value="登录"]').click()
driver.quit()
```
在实际运行时，由于页面元素加载时间的不确定，使用智能等待是一种靠谱的增加脚本稳定性的一种方式。
由于智能等待在任何元素定位不到时都会等待，会延长报错的时间，因此在调试时不建议使用。
智能等待不一定能完全解决所有的页面元素定位的问题，有时候还需要配合time.sleep()使用。

3．显性等待
显性等待，显性等待是使用WebDriverWait,对明显需要等待的特定元素进行循环等待。实现逻辑和智能等待相似。在定位不到元素时可以自动忽略定位不到元素异常，并间隔一定时间再次定位，直到定位到元素或者超时。如图5.9，王五后面的查看链接需要的等待不确定等待时间才能出现，此时便可以使用显性等待。
图 5.9 需要显性等待的场景
使用WebDriverWait的步骤如下：
 1. 实例化一个WebDriverWait对象，参数分别是driver，最大超时时间（秒），轮询间隔（秒）。
 2. 使用该对象的until方法，until方法接受一个函数，通常是我们结束等的期望条件。until方法会每隔一定间隔调用该函数，并自动忽略元素定位异常，直到该函数返回真（有效结果）或者超时。当函数返回真（有效结果）时，until返回函数的结果。
示例如下：

> 代码selenium_webdriver_wait_v1.py内容
```python
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('http://127.0.0.1/control.html')
def find_view(driver):
    return driver.find_element(By.XPATH, '//td[text()="王五"]/../td[4]/a')
wait = WebDriverWait(driver, 30, 1)
element = wait.until(find_view)
element.click()
driver.quit()
```
我们这里定义了一个find_view期望函数，期望函数一般包含一个参数driver。find_view中，我们尝试使用XPath，通过文本查找到王五所在单元格，并向上查找到所在行，然后取第4个单元格的连接标签。由于表格中，王五所在行往往是动态变化的，而表格列则比较固定。这种查找王五所在行的查看链接是非常有用的一种方式。
wait是实例化出来的WebDriverWait对象，执行间隔是1秒，最大超时时间是30秒。使用wait.until(find_view)会每隔1秒传递driver并调用find_view方法。如果find_view中定位不到指定元素，并抛出异常。until方法会忽略异常，间隔1秒继续调用，直到其定位并返回元素（有效结果）或者超时抛出超时异常。
find_view函数通常也可以使用lambda表达式（匿名函数）代替。上例也可以修改为如下代码。
代码selenium_webdriver_wait_v2.py内容
```python
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('http://127.0.01/control.html')
WebDriverWait(driver, 30, 1).until(
    lambda driver: driver.find_element(By.XPATH, '//td[text()="王五"]/../td[4]/a')
).click()
driver.quit()
```

由于wait对象只使用一次，也可以不不赋值变量，直接使用实例化出来的对象进行操作。
### 4.5.2  期望条件
Selenium中内置了很多期望条件，我们可以直接使用，如元素出现并被定位到。示例如下：
代码selenium_excepted_conditions.py内容
```python
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('http://12.7.0.01/control.html')
element_loc = (By.XPATH, '//td[text()="王五"]/../td[4]/a')
WebDriverWait(driver, 30, 1).until(
    EC.presence_of_element_located(element_loc)
).click()
driver.quit()
```
由于expected_conditions模块名字较长，这里为其赋予了别名EC。expected_conditions模块中包含了非常多对的期望条件方法，presence_of_element_located是指元素出现并被定位到。该方法参数接受一个包含定位方式和定位表达式的元祖（即元素定位器），并返回定位到的元素。
常用的期望条件如下：
- title_is：标题是指定字符串。
- title_contains：标题包含指定字符串。
- presence_of_element_located：元素出现并被定位到。
- url_to_be：url为指定url。
- url_contains：url包含指定字符串。
- url_matches：url匹配指定正则表达式。
- url_changes：url有变化。
- visibility_of：元素可见。
- visibility_of_element_located：元素可见并被定位到。
- text_to_be_present_in_element：元素文本包含指定字符串。
- text_to_be_presend_in_element_value：元素value属性中包含指定字符串。
- frame_to_be_available_and_switch_to_it：框架元素可以被切换到。
- element_to_be_clickable：元素支持点击操作。
- element_located_to_be_selected：定位到的元素为选中状态。
- new_window_is_opened：打开了新窗口。
- num_of_windows_to_be：当前打开的窗口为指定数量。
- alart_is_present：弹出了警告框。
除了用于WebDriverWait外，期望条件也可以于断言及条件判断。示例如下：

> 代码selenium_excepted_condictions2.py内容
```python
from selenium import webdriver
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
assert EC.title_is('百度一下，你就知道')(driver)
hao123 = EC.presence_of_element_located((By.LINK_TEXT, 'hao123'))(driver)
hao123.click()
driver.quit()
```
由于每个期望条件实际是一个类，并支持像函数式的调用（接受固定参数driver）。因此其使用方式通常为：
期望条件函数(实例化参数)(driver)
## 4.6  开挂的JavaScript
在页面流程的处理过程中往往会遇到一些包含限制属性，隐藏、只读、置灰的元素。如页面常将一些对话框或者折叠组件的内容隐藏起来，再通过用户操作使其显示，或者为了页面的美观，将原始样式的上传输入框隐藏，并叠加另一个输入框，并绑定操作。另外有些日期输入框会设置为只读，只允许用户点击选择时间日期。或者需要强制操作一些置灰的按钮。由于Selenium不能直接操作这种隐藏、只读、置灰的元素，我们都可以通过执行JavaScript脚本移除其限制属性，然后进行操作。
### 4.6.1  JavaScript查找元素
同Selenium一样，要想操作元素，需要先找到元素，JavaScript原生查找元素的方式有以下几种。
查找单个元素：
- document.getElementById()：使用元素id查找元素。
- document.querySelector()：使用CSS Selector查找元素。
查找多个元素：
- document.getElementsByName()
- document.getElementsByClassName()
- document.getElementsByTagName()
一般我们使用getElementById()或querySelector()即可，如图5.10所示。


图5.10  使用JavaScript脚本定位元素
示例代码如下：
```python
document.getElementById('kw')
document.querySelector('#kw')
```
### 4.6.2  使用Selenium执行JavaScript脚本
Selenium中执行JavaScript脚本的方法如下：
```python
driver.execute_script('JavaScript脚本')
```
JavaScript脚本以字符串形式传入，支持多行多语句，支持参数化和获取脚本执行返回值。
1．执行JavaScript语句
```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
js = 'document.querySelector("#kw").value="临渊";'
driver.execute_script(js)
```
2. 执行多行JavaScript语句
可以使用三引号构造多行脚本并执行，支持声明变量和定义及调用函数。
```python
js = '''var element= 'document.querySelector("#kw");
element.setAttribute('style', 'background: green; border: 2px solid red;');
element.value = '临渊';'''
driver.execute_script(js)
```
3．获取脚本返回值
脚本前加上return可以获取脚本执行返回值。
```python
js = 'return document.querySelector("#kw");'
element = driver.execute_script(js)
element.send_keys('临渊')
```
4．参数化脚本
JavaScript脚本中支持使用`argments[索引]`来获取Selenium执行脚本时传入的参数，示例如下：
```python
element = driver.find_element('id', 'kw')
style='background: green; border: 2px solid red;'
js = 'arguments[0].setAttribute("style", arguments[1]);'
element = driver.execute_script(js,element,style)
element.send_keys('临渊')
```
### 4.6.3  滚动页面
一般来说，Selenium在get页面时，会获取到页面的所有内容。因此非首屏的元素可以直接定位并操作。但是对于流式加载的页面，当滚动条下拉到底部时会触发加载新的内容。这种情况下，就需要通过JavaScript脚本来操作滚动条，以触发内容加载，常用的Javascript脚本如下：
- 获取页面高度：document.documentElement.scrollHeight;
- 上下滚动：document.documentElement.scrollTop=距顶端高度;
- 左右滚动：ducument.documentElement.scrollLeft=距左边宽度;
以下示例演示，hao123网站，通过JavaScript脚本，循环滚动触发内容加载的示例，代码如下：
代码selenium_scroll_page.py内容
```python
from selenium import webdriver
from time import sleep

driver = webdriver.Chrome()
driver.get('https://www.hao123.com/')
height = driver.execute_script('return document.documentElement.scrollHeight;')
from_top = 0
js = 'document.documentElement.scrollTop=arguments[0];'
for i in range(10):
    from_top += height
    driver.execute_script(js, from_top)
    sleep(1)
sleep(3)
driver.quit()
```

上例中，首先执行JavaScript脚本获取到原始页面高度值，然后循环通过脚本设置滚动条距离页面顶端值，每次增加一个原始页面高度，模拟不断下拉，以触发页面新内容加载。
### 4.6.4  去除元素限制属性
元素常见的限制属性有隐藏、只读和置灰等。我们可以通过JavaScript脚本，元素的removeAttribute方法，通过移除相应的限制属性，来使得元素可以操作。元素移除属性的方法如下：
元素.removeAttribute('属性名')
1．隐藏
在HTML中，由于页面美化和用户交互的需求，元素隐藏的使用非常常见，比如下拉菜单、内容折叠、对话框以及上传文件框等。
隐藏常见有以下几种表现形式。
- `<div hidden>`
- `<div style="display: none;">`
- `<div style="visibility: hidden">`
- `<div style="overflow: hidden">`
- `<div style="opacity: 0;">`
多数情况下，隐藏元素时不可以直接操作的。如果想操作隐藏元素，如果不能通过使用正常的页面步骤触发其显示，则可以通过执行JavaScript脚本，通过移除掉元素的hidden或style属性来使得元素显示并可以进行操作。
如路过图床网[https://imgchr.com/](https://imgchr.com/)，如图5.10所示。


图5.10  元素隐藏属性
真实的上传框并不是“开始上传”按钮。而是隐藏在页面的左上角。我们通过在Elements面板Ctrl+F搜索input[type=file]，可以找到实际的上传框。这里有两处，一处是上传图片，一处是通过相机拍照上传，我们选择第一个上传框。该上传框通过一个样式类hidden-visibility为元素添加了sytle属性visibility: hidden属性，来达到隐藏的目的。因此，我们只需要移除掉该元素的style属性即可，然后使用send_keys完成图片上传。代码如下：

> 代码selenium_handle_hidden_element.py内容
```python
from selenium import webdriver
from time import sleep

driver = webdriver.Chrome()
driver.get('https://imgchr.com/')
elm = driver.find_element('id', 'anywhere-upload-input')
js = 'arguments[0].removeAttribute("style");'
driver.execute_script(js, elm)
sleep(0.5)
elm.send_keys('/Users/superhin/Downloads/beida.jpeg')
sleep(3)
driver.quit()
```
以上示例中，先定位到指定的输入框，然后以参数的形式传递给JavaScript脚本并移除其style属性，由于执行脚本往往需要一定的时间，这里等待了0.5秒后再继续操作。上传框支持使用send_keys文件路径来完成上传。读者需要将图片地址改为自己本地的地址。
只读、置灰等限制属性可以通过类似的方式，移除disable等限制属性

2．只读
有时页面为了防止用户修改指定的属性或者输入错误的格式，会对某些输入框设置readonly只读属性。一些日期选择框为了避免用户输入错误的格式，将输入框设置为只读，用户只能通过点击日期控件进行选择日期，如图5.11，网址为：http://www.miniui.com/demo/datepicker/datepicker.html。


图5.11  元素只读属性

在使用日期控件通过点击选择日期时，如果涉及到年份和月份的更改，往往需要多次的点击翻页，操作十分麻烦。此时可以通过手工操作，了解输入框日期的输入格式，然后一次其只读属性，按相应格式直接输入需要选择的日期，代码如下：
代码selenium_handle_readonly_element.py内容
```python
from selenium import webdriver
from time import sleep
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('http://www.miniui.com/demo/datepicker/datepicker.html')
elm = driver.find_element(By.CSS_SELECTOR, 'span#date3>span>input')
js = 'arguments[0].removeAttribute("readonly");'
driver.execute_script(js, elm)
sleep(0.5)
elm.send_keys('1999-01-01')
sleep(3)
driver.quit()
```
在定位输入框时，使用了CSS选择器，先定位到id为date3的span元素，然后向下两层找到该input元素。

3．置灰
置灰即禁用，为防止用户修改或禁止用户在数据为准备好或不具备权限时进行操作。一般通过原单disable属性设置。同样，如果需要绕过前端的限制，强行进行操作，可以通过执行脚本移除元素的disable属性然后再进行操作，基本代码如下：
```python
...
elm = driver.find_element('', '')
js = 'arguments[0].removeAttribute("disable");'
driver.execute_script(js, elm)
sleep(0.5)
elm.click()
```
其他如果影响到元素操作的属性或元素，如一个元素被其他元素遮盖，也可以通过执行JavaScript脚本移除相应的熟悉或指定元素然后进行元素操作。
### 4.6.5  富文本框输入
富文本框，指支持各种HTML丰富样式的一种输入框，常见于文章编辑。如图5.12所示。网址为：http://www.vemmis.com/bjq/index.html。


图5.12  富文本框

富文本框一般是通过iframe在原始页面中嵌入了一个完整的网页，通过JavaScript脚本，实时响应用户的输入和排版操作，转换为HTML，注入到框架页面的body中。
因此我们如果想要完成输入的话，也需要通过JavaScript的方式向框架页面的body中注入内容。示例代码如下：
代码selenium_input_to_editor.py内容
```python
from selenium import webdriver
from time import sleep

driver = webdriver.Chrome()
driver.get('http://www.vemmis.com/bjq/index.html')
js = "document.querySelector('#ueditor_0').contentDocument.querySelector('body').innerHTML='<h1>Hello</h1>'"
driver.execute_script(js)
sleep(3)
driver.quit()
```
以上代码的JavaScript脚本中，首先使用CSS选择器通过id定位到富文本框所在的iframe元素，通过contentDocument获取到其内部HTML，然后通过CSS选择器定位到内部页面的body元素并修改其元素内部的HTML代码，以达到注入HTML的目的。
## 4. 7  Page Object模式
Web自动化面临的最大问题是页面元素变动带来的测试脚本维护问题，如果脚本中直接定位并操作元素，随着脚本数量的增加，用例和页面元素之间的引用关系会变得非常难以梳理。一旦元素变动，从众多的脚本中筛选出使用该元素的脚本，并逐个调试将是一个非常痛苦的过程。因此引入Page Object模式对页面元素进行分组管理是一种非常好的实践方式。
Page Object即页面对象。该模式为每个网页编写一个类，来集中管理当前页面所有元素的定位及操作。而测试脚本不直接定位元素，而是通过页面对象来进行页面操作。这样当页面元素变动时，只要修复所在的页面对象对该元素的操作即可，如图5.13所示。

图5.13  脚本直接操作元素和Page Object模式对比

编写Page Object通常步骤如为：
 1. 为每个操作的页面编写一个类。
 2. 在初始化方法中传入driver并绑定对象，已使得对象方法可以使用driver定位并操作元素。
 3. 在类中列出所有需要操作元素的定位器，每个元素的定位器由定位方式和定位表达式组成，如(By.ID, 'kw')。
 4. 为每个元素的每种操作封装一个方法，在该方法中定位并操作元素。
 5. 如果本页面有些常用的元素组合操作，也可以在元素操作的基础上，封装一些组合操作的方法。
以百度首页为例，假设我们要暂时只需要操作搜索框和搜索按钮，可以编写页面对象如下：

> 代码selenium_page_object.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

# 1. 每个页面写一个类
class BaiduPage(object):
    # 2. 传入共用的driver，并绑定对象
    def __init__(self, driver: webdriver.Chrome):
        self.driver = driver
    # 3. 列出所有要操作的元素的定位器
    search_ipt_loc = (By.NAME, 'wd')  # 类属性会自动绑定对象
    search_btn_loc = (By.ID, 'su')
    # 4. 每个元素每种需要的操作，封装一个方法
    def input_keyword(self, keyword):
        print('输入搜索关键字', keyword)
        search_ipt = self.driver.find_element(*self.search_ipt_loc)
        search_ipt.clear()
        search_ipt.send_keys(keyword)
    def click_search(self):
        print('点击搜索')
        self.driver.find_element(*self.search_btn_loc).click()
    # 5. 当前页面的一些组合操作
    def search(self, keyword):
        self.input_keyword(keyword)
        self.click_search()
        
if __name__ == '__main__':
    driver = webdriver.Chrome()
    driver.maximize_window()
    driver.get('https://www.baidu.com/')
    baidu = BaiduPage(driver)
    baidu.search('博客园 韩志超')
```
由于我们需要多个页面对象，每个页面对象都需要driver来定位并操作元素，但却并不需要每个页面都启动一个浏览器。因此我们在初始化对象时传入公用的driver。
此处driver后面使用类型声明，声明其为webdriver.Chrome对象，这样PyCharm可以识别其对象属性和方法，给出代码提示。
上例中列出了两个元素的定位器，因此至少要封装两个方法来实现每个元素的操作。为每个元素的每种操作封装一个方法可以增加页面操作的灵活性，比如，如果脚本需要只输入搜索词而不点击搜索按钮，便可以只调用input_keyword方法。
search_ipt_loc和search_btn_loc两个元素定位器是BaiduPage的类属性，类属性会自动绑定self对象，因此元素操作方法在引用元素定位器时应加上self.search_ipt_loc。由于driver.find_element()方法接受两个参数，而定位器是一个元祖变量（一个参数），因此需要前面加上\*号将元祖变量解包成多个变量（元祖中几个元素，解包成几个变量）。
## 4.8  小结
本章主要介绍了Selenium的运行选项，两种高级的定位语法XPath和CSS Selector，模拟键盘鼠标操作，各种等待策略以及使用JavaScript脚本绕过前端的一些限制进行操作元素。
