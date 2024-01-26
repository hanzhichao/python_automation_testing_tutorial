# Selenium基础
## Selenium简介

Selenium是一个开源的浏览器自动化测试框架，广泛应用于Web UI自动化测试和爬虫领域。
Selenium支持Chrome、Chromium、Firefox、Safari、Opera、Edge、IE7至IE11等多种浏览器，使用不同WebDriver驱动来控制浏览器完成各种操作。
同时，Selenium支持Java、Python、C#、Ruby、JavaScript等绝大多数主流编程语言。
加上Selenium简单易用上手快的特点，使得Selenium成为最流行的自动化测试工具之一。

## Selenium简介及WebDriver原理

到目前为止，Selenium经历1.0、2.0、3.0三个主要版本。
- Selenium 1.0的核心是Selenium RC，即Selenium远程服务器，通过注入脚本的方式来操作浏览器。
- Selenium 2.0的核心是WebDriver，通过不同的驱动直接操作各种浏览器。
- Selenium 3.0在Selenium 2.0的基础上舍弃了Selenium RC，优化了部分API，并增加了更多的浏览器支持。


### Selenium3大组件
Selenium包含Selenium WebDriver、Selenium IDE及Selenium Grid三大组件，如图3.1所示。

图3.1  Selenium3大组件

![[第3章 Selenium基础_image_1.png]]

- Selenium WebDriver是Selenium的核心，用于使用不同的编程语言编写脚本来驱动浏览器自动化执行。
- Selenium IDE是浏览器的一个插件，可以录制页面操作并进行回放，上手简单，可以快速录制生成很多自动脚本，稳定性略差。
- Selenium Grid是一种分布式多浏览器兼容性测试方案，可以注册多个浏览器并选择执行。


### WebDriver原理
WebDriver本质上是一套基于HTTP的API协议，称作The WebDriver Wire Protocol或JSON Wire Protocol。不同的浏览器通过各自的驱动程序来实现对WebDriver协议的支持。
Selenium操作浏览器的过程为，如图3.2所示。

图3.2  WebDriver原理

![[第3章 Selenium基础_image_2.png]]

1. 使用不同的浏览器驱动在本地或远程启动一套WebDriver服务（默认绑定9515端口）。
2. 测试脚本调用不同的Selenium SDK的方法将代码转化为API请求发送到驱动的WebDriver服务。
3. 驱动将收到的API请求转化为浏览器操作指令，控制浏览器完成操作。

## Selenium环境搭建
Selenium是一个浏览器自动化测试框架，因此首先需要安装有浏览器，本书以Chrome浏览器为主进行讲解，另外还需要下载浏览器对应版本的驱动程序，然后安装Selenium编写测试脚本。

### 安装Chrome浏览器
百度或使用360软件工具箱，搜索下载最新版本的Google Chrome浏览器，安装即可。
打开浏览器，点击地址栏后的更多按钮（3个竖点），在下拉菜单中，点击帮助，关于Google Chrome查看安装的浏览器版本，如图4.1所示。

图3.1 查看浏览器版本
![[第3章 Selenium基础_image_3.png]]

### 下载对应版本chromedriver
浏览器中打开：https://npm.taobao.org/mirrors/chromedriver/，并下载对应版本的浏览器驱动，一般只需要大版本对应即可，如图4.2所示。

图4.2 下载chromedriver

![[第3章 Selenium基础_image_4.png]]

下载后解压，将chromedriver.exe放在配置了环境变量的目录中（如Python安装目录的Scripts中）。

> 注：chromedriver.exe所在目录配置到环境变量Path中是为了可以通过chromedriver命令在命令行任意位置启动chromedriver服务。

### 安装Selenium
Selenium是Python的一个三方包，在命令行使用pip命令安装即可。
```bash
pip install selenium
```

> 注：pip install命令应在命令行中运行，而不是Python交互环境（标志是>>>）中运行。
### 验证是否安装成功
打开命令行，输入Python进入Python命令交互界面，输入以下代码。
```
>>> from selenium import webdriver
```
不报错说明selenium安装成功
```
>>> driver = webdriver.Chrome() 
```
能自动打开Chrome浏览器说明chrome驱动安装正确。
## 浏览器基础操作
本节讲授如何使用Selenium打开浏览器、最大化窗口、访问指定网址、前进、后退、刷新等基本操作。

###  启动浏览器
启动浏览器只需要从selenium中导入webdriver模块，然后使用webdriver.Chrome()启动浏览器即可，代码如下：
代码selenium_start_chrome.py内容
```python
from selenium import webdriver

driver = webdriver.Chrome()
```
这里的driver是我们通过Webdriver服务和浏览器交互的一个会话，简单可以理解为driver就是一个浏览器对象，我们可以通过driver来控制浏览器。
如果之前下载的chromedriver所在目录没有配置到环境变量Path中，在启动浏览器时也可以手动指定驱动的位置，代码如下：
#TODO 废弃
```python
driver = webdriver.Chrome('D:\\chromedriver.exe')
```

>注：如果没有指定驱动的位置，Selenium查找驱动的顺序为，首先查找脚本所在目录，然后查找所环境变量Path中的目录。

webdriver模块支持启动多种浏览器，前提时需要下载各种浏览器所需的驱动，向chromedriver.exe一样，并放置在配置了环境变量的目录中，然后可以webdriver对应的方法启动不同的浏览器，代码如下：
代码selenium_start_browsers.py内容
```python
from selenium import webdriver

driver = webdriver.Firefox()
driver = webdriver.Edge()
driver = webdriver.Ie()
```

### 浏览器基础操作
Selenium常用的浏览器操作有，打开网址、前进、后退、刷新、最大化等等，对应driver的方法如下：
- driver.get(url)：打开网址。
- driver.forward()：前进。
- driver.back()：后退。
- driver.refresh()：刷新页面。
- driver.maximize_window()：最大化窗口。
- driver.set_window_size(800, 600)：设置窗口大小。
- driver.save_screenshot("D:\\1.png")：保存页面截图。
- driver.close()：关闭当前页面，当只有一个页面是会自动退出浏览器。
- driver.quit()：退出浏览器。

使用示例如下：

> 代码selenium_page_navigation.py内容
```python
from selenium import webdriver
from time import sleep

driver = webdriver.Chrome()
driver.get("https://www.baidu.com/") # 打开网页
driver.maximize_window()  # 窗口最大化
sleep(1)  # 等待1s
driver..get("https://www.qq.com")
sleep(1)
driver.back()  # 后退
sleep(1)
driver.set_window_size(800,600)
driver.save_screenshot('D:\\baidu.png')  # 截图
driver.quit()
```
为了使读者能观察到每一步操作，这里导入了sleep函数用于等待一定的时间再继续操作。
以上示例中先打开了百度首页，最大化窗口，然后再打开腾讯首页，后退到百度页面后设置窗口尺寸并截图，最后退出浏览器。


### 获取页面属性
通过driver变量也可以获取页面的标题、网址、源码等属性，常判断是不是在某一个页面上。driver常用的页面属性如下：
- driver.title：页面标题
- driver.current_url：当前网址
- driver.page_source：网页源代码，多用于爬虫解析

使用示例如下：
> 代码selenium_get_title_and_page_source.py内容
```python
from selenium import webdriver
driver = webdriver.Chrome()
driver.get("https://www.baidu.com/") # 打开网页
assert '百度一下' in driver.title
```
assert是Python自带的断言语句，期望结果是网页标题中包含“百度一下”。断言通过不会有任何输出，断言失败则会抛出AssertionError异常。
## 元素定位基础
除了对浏览器的操作外，我们还需要对具体的页面元素（如输入框、按钮、链接等）进行操作。在操作一个指定的元素之前，首先应该先找到它，这便是元素定位，然后才能对元素进行操作。


### 查看页面元素属性
要想定位元素首先需要知道元素的属性。通过Chrome的开发者工具，可以很方便的查看页面元素对应的节点代码。
首先，使用Chrome打开百度首页，按Ctrl+Shift+I打开开发者工具，切换到元素（Elements）面板。点击开发者工具，左上角的定位元素的图标或按Ctrl+Shift+C，然后点击页面元素即可快速定位到响应的节点代码，如图4-2所示。

图4.2  查看元素属性

![[第3章 Selenium基础_image_5.png]]


如上图，百度搜索框这个元素对应`<input type="text" class="s_ipt" name="wd" id="kw" maxlength="100" autocomplete="off">`这个节点，标签名为input，id属性为kw，name属性为wd，class属性为s_ipt。

>注：有些元素的class中会包含多个类名（class name），以空格隔开，这种称为复合类。


### 8种基本的定位方式
Selenium有8种基本的定位方式，分别是通过通用的标签名tag定位，通过元素的3种基本属性id、name、某一个类名（class name）定位，通过链接文字或部分链接文字定位，以及两种高级的元素查找语法CSS Selector和XPath进行定位。

以下为常用的8种基本定位方式。
- find_element(By.ID,"")：通过id属性定位
- find_element(By.NAME, "")：通过name属性定位
- find_element(By.CLASS_NAME, "")：通过某一class name定位
- find_element(By.TAG_NAME, "")：通过标签名定位
- find_element(By.LINK_TEXT, "")：通过链接文字定位
- find_element(By.PARTIAL_LINK_TEXT, "")：通过部分链接文字定位
- find_element(By.XPATH, "")：通过XPath定位
- find_element(By.CSS_SELECTOR, "")：通过CSS选择器定位。


#### 1．通过id属性定位
一般来说元素的id属性是唯一的，通过元素id可以快速定位到这个元素。打开百度首页，通过开发者工具我们可以看到搜索框的id为kw，百度一下按钮的id为su。我们可以使用id来定位并操作这两个元素，示例如下：

> 代码selenium_find_element_by_id.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://www.baidu.com/")
driver.find_element(By.ID, 'kw').send_keys('Selenium')
driver.find_element(By.ID, 'su').click()
driver.quit()
```
以上示例中，首先启动Chrome浏览器，并访问百度首页，然后通过kw这个id定位到输入框，使用send_keys()输入Selenium这个单词。然后使用su这个id定位到百度一下按钮，使用click()方法进行点击，最后退出浏览器。

> 注：有些网站的元素的id是动态变化的，这种情况下不建议使用id定位。

#### 通过name属性定位
如果节点有name属性，也可以使用name来定位，百度首页，搜索框的元素name为wd，因此也可以使用如下语句定位并操作输入框。

```python
driver.find_element(By.NAME, 'wd').send_keys('Selenium')
```

#### 通过某个class名定位
元素的class属性比较特殊，可以有多个class name，称为复合class，以空格隔开。
打开hao123.com，使用开发者工具定位搜索框和百度一下按钮，如图4.3所示。

图4.3  复合class元素

![[第3章 Selenium基础_image_6.png]]

在定位元素时，可以选择一个不重复的class name进行定位，示例如下：

> 代码selenium_find_element_by_class_name.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://www.hao123.com/")
driver.find_element(By.CLASS_NAME, 'textInput').send_keys('Selenium')
driver.find_element(By.CLASS_NAME, 'submitInput').click()
driver.quit()
```

> 注：定位方式为driver.find_element_by_class_name()，而不是by_class()，class_name指某一个class名，而class指整个class属性。

#### 通过链接文字定位
链接实际上就是一个`<a>`标签的节点，当遇到链接时，一般首选通过链接文字定位，链接文字一般就是链接在页面上的显示文字。如百度首页顶部的新闻链接，可以使用以下方法进行定位。

```python
driver.find_element(By.LINK_TEXT, "新闻")
```

> 注：有时候为了布局，链接文字中会插入一些空格、换行符等空白字符，find_element_by_link_text()，会自动去除链接文字左右的空白字符，但如果链接文字中间有空白字符，则需要写上。

#### 通过部分链接文字定位
当链接文字较长或有动态变化时，可以使用部分链接文字进行定位。如百度首页的新闻链接也可以使用以下方式定位。

```python
driver.find_element(By.PARTIAL_LINK_TEXT, "新")
```

#### 通过标签名定位
由于标签名重复度很高，直接通过标签名很难唯一识别一个元素。通过标签名定位一般结合定位一组元素使用。当定位到多个元素时，使用find_element方法默认返回第一个。
以下示例会定位到百度页面的第一个链接（a标签）。

>  代码selenium_find_element_by_tag_name.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
driver = webdriver.Chrome()
driver.get("https://www.baidu.com/")
first_link = driver.find_element(By.TAG_NAME, 'a')
print(first_link.get_attribute('href'))
driver.quit()
```
以上代码通过标签名a定位到第一个a标签元素，然后使用get_attribute()方法获取该元素的“href”属性，及链接地址。


#### 通过XPath定位
XPath是XML路径选择语言，支持HTML。XPath有一套完整的元素定位语法。下一章我们会详细阐述。当元素没有id、name、class等属性时，我们可以通过Chrome开发者工具复制元素XPath进行定位，如下图所示。
图4.4  复制元素XPath

![[第3章 Selenium基础_image_7.png]]

首先打开Chrome开发者工具，定位到元素，在元素代码上点击右键，选择Copy，选择Copy XPath，然后粘贴使用即可。
百度首页搜索框使用XPath方式定位示例如下：
```python
driver.find_element(By.XPATH, "//*[@id="kw"]")
```


#### 通过CSS选择器定位
CSS选择器是CSS语言的一套元素选择方法，速度较快。我们同样可以通过开发者工具复制出元素的CSS Selector进行定位使用，操作方式如上例，在元素代码上点击右键，选择Copy，选择Copy selector即可。
百度首页搜索框使用CSS Selector方式定位示例如下，CSS Selector详细语法，下章会进行阐述。

```python
driver.find_element(By.CSS_SELECTOR, "#kw")
```

> 注：定位方式选择顺序（参考）
> 1. 链接元素：link text > partial link text
> 2. 表单元素：id > name > class_name > tag_name
> 3. 如果熟悉CSS Selector或XPath语法：css_selector > xpath

### 定位一组元素
当元素拥有相同的id、name、class_name、tag_name等时，可以定位到所有的元素，然后按顺序取得具体某个元素，这里使用find_elements_by_...，注意对一个s。
不同于find_element方法，find_elements方法返回一个列表，当定位不到时不会报NoSuchElementException错误，而是返回一个空列表。因此find_elements方法可以用于以下场景。
- 定位重复元素。
- 检查元素是否存在。
- 处理偶现元素。
#### 定位重复元素
有时网页中会包含多个同名的连接，如图4.3所示，百度首页包含两个以上的登录连接。

图4.3  百度首页登录连接
使用`driver.find_element_by_link_text('登录')`只能定位到一个满足要求的元素，如果我们需要定位第二个登录便可以使用find_elements方法，示例如下：

>  代码selenium_find_elements.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
elm_list = driver.find_elements(By.LINK_TEXT, '登录')
print(elm_list)  # 打印定位到的元素列表
elm_list[1].click()  # 点击第二个元素
```
`find_elements(By.LINK_TEXT, '登录')`返回所有连接文字为“登录”的页面元素列表。然后通过索引取第二个元素进行操作。
#### 检查元素是否存在
在测试步骤中有时需要检查某个元素是否存在来确定是否成功进入到了某个页面。由于find_element方法当检查的元素不存在时会抛出异常，而find_elements不会。因此我们可以通过find_elements来检查元素是否存在，代码如下：

>  代码selenium_check_element.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
elm_list = driver.find_elements(By.ID, 'kw')  # 关键字输入框
assert len(elm_list) > 0  # 断言结果不为空
```
由于find_elements找不到元素时会返回空列表，可以通过列表长度来判断是否有结果。
#### 处理偶现元素
偶现元素指流程中有些元素有时出现，有时不出现，此时便可以通过find_elements检查元素是否存在，然后进行操作，示例如下：

>  代码selenium_handle_occasional_element.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
elm_list = driver.find_elements(By.ID, 'su')  # 百度一下按钮
if len(elm_list) > 0  # 如果元素存在
    elm_list[0].click()  # 取第一个元素点击
```

此处加上百度首页“百度一下”按钮为偶现元素，先检查原始是否存在，如果存在则点击，如果不存在则不做处理。

> 注：如果偶现元素时JavaScript弹出框等，需要使用其他的方式检查及处理。

### 逐层定位
除了driver支持各种find_element方法外，定位到的元素可以继续使用各种find_element，在其子孙元素中查找相应元素。
当一个元素没有任何属性，不方便定位时，可以先定位到容易定位的父级或祖先级元素，然后使用父级或祖先级元素继续定位。
如图4.4所示，百度首页，假设input搜索框，没有id等属性，我们可以先定位到其上上级span元素，再通过span元素继续查找。

图4.4 百度首页搜索框元素上上级span标签

![[第3章 Selenium基础_image_8.png]]


逐层定位代码如下：

>  代码selenium_continue_find_element.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
span = driver.find_element(By.CLASS_NAME, 's_ipt_wr')
span.find_element_by_tag_name('input').send_keys('Selenium')
```
以上示例中，使用span的一个类名先定位到span元素，然后使用span元素继续向下通过签名查找input的元素，并进行输入操作。
如果span中有多个input标签，可以使用find_elements结合索引取相应位置的元素进行操作。


## 元素操作基础
当定位到元素后，即可对元素进行操作，常用的元素操作有点击、输入、清空等。也可以获取元素的熟悉及状态。

### 元素常用操作及属性
元素常见的操作有点击、输入和清空等，对应方法如下：
- click()：点击元素，支持链接、按钮、单选框、复选框、文本框、下拉菜单等。
- send_keys()：输入文本，支持文本框、密码框、多行文本框、上传文件框等。
- clear()：清空输入。

> 注：有些普通元素由于绑定了Click事件，同样支持点击操作。

除了操作元素外，还可以获取其标签名、文本、元素属性及一些可用状态等，对应方法如下：
- tag_name：元素标签名
- text：元素文本
- get_attribute('属性名')：获取元素属性
- is_displayed()：元素是否显示
- is_enabled()：元素是否可用（如按钮是否可点击）
- is_selected()：元素是否已选中

使用示例如下：

>  代码selenium_element_operations.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
search_ipt = driver.find_element(By.ID, 'kw')
print(search_ipt.tag_name)  # 打印搜索框标签名
print(search_ipt.is_displayed(), search_ipt.is_enabled())  # 检查元素是否显示及可用
search_ipt.send_keys('Selenium')
print(search_ipt.get_attribute('value'))  # 打印搜索框当前value属性
search_ipt.clear()
driver.quit()
```

当一个元素需要多次使用时，为避免重复定位，一般将元素赋值给一个变量，如这里的search_ipt代表定位到的输入框元素。
以上示例中定位到搜索框，然后打印了其标签名和是否显示、是否可用，然后输入Selenium并打印元素的value属性，然后清空输入。运行后打印结果如下：
```
input
True True
Selenium
```

### 下拉框操作
在网页中，标准下拉框是一种`<select><option></option></select>`的结构，如：
```html
<select name='city'>
    <option value="beijing">北京</option>
    <option value="shanghai">上海</option>
    <option value="chongqing">重庆</option>
</select>
```
在Selenium的支持类中有专门处理下拉框的类，导入使用即可，操作步骤如下：
1. 从selenium.webdriver.support.select模块导入Select类。
2. 使用定位到的select元素，实例化一个Select对象。
3. 通过选项索引、值或显示文字选择选项。

示例如下：

>  代码selenium_operate_select_element.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select

...
area = Select(driver.find_element(By.NAME, 'area')
area.select_by_index(0)
area.select_by_value('shanghai')
area.select_by_visiable_text('重庆')
```
以上示例中先将通过name属性定位到的元素实例化得到一个Select对象，Select对象有以下三种选择选项的方式。
- select_by_index()：通过选项索引位置选择
- select_by_value()：通过选项value值选择
- select_by_visible_text()：通过选项显示文字选择

根据实际情况，选择一种即可。

> 注意：本例由于没有网址，无法直接运行，读者可以自行将以上的`<select>...</select>`代码保存为一个本地HTML文件，使用浏览器打开，复制出URL地址，补充到以上示例中使用。

> 注：只有标准的`<select>`标签的下拉框才可以这样操作，下拉菜单或弹出菜单可以通过逐步点击或移动鼠标加逐步点击来实现。
### 上传下载操作

#### 上传操作
标准上传框实际上是一个==type=file==的input输入框，Selenium2.0之后支持使用send_keys()方法向上传框输入文件路径来完成上传操作。百度首页，点击输入框内右侧的照相机图标，查看上传图片元素，如图4.6所示。

图4.5 百度首页上传图片

![[第3章 Selenium基础_image_9.png]]

如果定位上图中的加号图标，会得到标签名为i的元素，要找到真实的`<input type="file">`的元素，这个元素才是真正的上传框，操作示例如下：

>  代码selenium_handle_upload.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
driver = webdriver.Chrome()
driver.get('https://www.baidu.com/')
driver.find_element(By.CLASS_NAME, 'soutu-btn').click()  # 点击输入框中的相机图标
div = driver.find_element(By.CLASS_NAME, 'upload-wrap')  # 逐层定位，先定位到上级div元素
div.find_element(By.TAG_NAME, 'input').send_keys('D:\\300.jpg')  # 输入文件路径完成上传
```
上例中首先点击百度首页搜索框总的相机图标，然后通过逐层定位的方式定位到真实的上传框，通过send_keys(文件路径)完成上传。

> 注：有时候页面美观，会在原本的上传框上叠加一个控件，而将原上传框隐藏。
> 只要使用JavaScript将原本的上传框显示，定位并操作这个上传框即可。对于Flash等其他非标控件上的上传框则要配合其他PC GUI自动化库（如pywin32等）或软件（如AutoIt3）完成。

#### 下载操作
Google Chrome中设置有默认的下载路径，因此下载文件时指需要要点击相应的下载按钮或链接即可，文件会自动下载到相应路径。

## 必要的切换
当页面出现弹出警告框，或者页面通过frame/iframe框架引入了其他页面，以及打开了新窗口时，由于driver还处于老的页面上，因此需要进行必要的切换才能处理警告框、框架页面以及新窗口。

### 警告框切换
JavaScript弹出框是网页中最常见的网页对话框之一。包括Alert（警告框）、Confirm（确认框）、Prompt（征求框）三种，样式如图4.6，4.7，4.8所示。
图4.6 Alert弹出框

![[第3章 Selenium基础_image_10.png]]

图4.7 Confirm弹出框

![[第3章 Selenium基础_image_11.png]]


图4.8 Prompt弹出框

![[第3章 Selenium基础_image_12.png]]

这三种弹出框都属于JavaScript弹框，可以使用driver.switch_to.alert切换到弹框，然后进行确认、取消或输入操作。
上图网页源码如下，可以保存为本地文件alert.html使用。
```html
<input id="alert" value="alert" type="button" onclick="alert('请按确认继续！');">
<input id="confirm" value="confirm" type="button" onclick="confirm('是否确定？');">
<input id="prompt" value="prompt" type="button" onclick="var name = prompt('请输入地址:',''); document.write(name) ">
```
Alert框操作示例如下：

>  代码selenium_swtich_alert.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.maximize_window()
driver.get("file:///Users/apple/Desktop/alert.html")
driver.find_element (By.ID, 'alert').click()
driver.switch_to.alert.accept()
```

driver.switch_to.alert后返回弹出框对象，支持以下三种操作。
- accept()：接受，同点击确定。
- dismiss()：取消或关闭。
- send_keys()：输入内容。

prompt框的输入操作，示例如下：
```python
...
driver.find_element(By.ID, 'prompt').click()
prompt = driver.switch_to.alert
prompt.send_keys('Kevin')
prompt.accept()
```

> 注：只有JavaScript弹框才可以使用switch_to.alert，否则会报错。
> Web常见4种弹出框，各自的处理方式如下：
> 1. JavaScript警告框：使用switch_to.alert.dismiss()关闭弹框
> 2. DOM或div对话框：最常见的弹出对话框，弹出框本身是网页DOM的一部分，只是原本为隐藏的。操作方式为，通过正常操作触发其显示后，稍微等待后直接定位对话框上的元素进行操作。
> 3. 弹出小窗口（新页面）：使用driver.switch_to.window(窗口句柄)，切换到新窗口进行操作。
> 4. Basic Auth授权框：将用户名密码直接放在URL中进行访问以绕过授权弹框。

### 网页窗口切换
在打开了新窗口（新的Tab页）时，driver还停留在原来的页面上，想要操作打开的新页面，就需要进行窗口切换。每个窗口都有一个唯一的标识，称为窗口句柄。通过识别窗口句柄来进行切换到指定窗口，或逐个切换后通过网页标题来判断是否目标窗口（页面）。
在切换窗口前，需要先获取打开网页窗口的句柄信息，driver有以下两个窗口句柄相关的属性。
- window_handles：当前会话所有网页窗口的句柄，列表类型。
- current_window_handle：当前窗口的句柄。

切换到指定窗口的方法。
```python
driver.switch_to.window(指定窗口句柄)
```

1．通过索引切换
默认情况下，Chrome新打开的窗口在后面，window_handles获取到的窗口句柄顺序也按此排序。可以通过索引来切换到指定的第几个窗口，示例如下：
代码selenium_switch_window.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://www.baidu.com/")
driver.find_element(By.LINK_TEXT, "帮助中心").click()
print(driver.title)
tabs = driver.window_handles
print(tabs)
driver.switch_to.window(tabs[1])
print(driver.title)
driver.quit()
```
以上示例中，点击百度首页“帮助中心”连接时，会打开新窗口，显示帮助中心页面。此时打印页面标题时，依然是原页面百度首页的标题。tabs为当前所有窗口的窗口句柄列表，可以通过索引取第二个窗口，并通过其窗口句柄切换到新窗口。此时再打印窗口标题时，可以发现已经切换到了新窗口，此时可以对新页面进行操作，运行打印结果如下：
```
百度一下，你就知道
['CDwindow-F5F21F7213B094985515FFF4D4945E9A', 'CDwindow-0466C23128D82EB57163B35728896939']
百度用户服务中心-首页
```
当有多个页面时，也可以根据先后顺序通过索引进行切换。

### 框架页面切换
框架页面是指在一个页面中引入了一个或多个其他页面。标志是frame或iframe标签。由于每个frame是一套完整的HTML代码结构，因此在操作框架页面时需要切换到对应的框架中才能定位并操作其中的元素。

#### 如何识别框架页面
第一种方法是通过开发者工具，定位到元素，查看元素的路径，如果有frame或iframe标签，则元素在框架页面中，如图4.8所示，FrameA元素在两层框架中。
图 4.8  通过元素路径查看框架页面
另一种方式是，如果页面中发现有多个竖行滚动条，怎很有可能是通过框架由多个页面组成。

#### 框架页面切换
框架页面切换分为切入和切出，切入时应逐层切入，同级之间不能相互切入，必须先切出到父框架，再切入同级框架。Selenium框架切换相关方法如下：
- switch_to.frame('')：切入，支持frame或iframe的id、name、index、和定位到的frame或iframe元素
- switch_to.parent_frame()：切出到父框架
- switch_to.default_content()：切出到原有内容（切出所有框架）
以下示例演示图4.8，分别操作FrameA元素和FrameB元素的示例。先逐层切入到FrameA所在框架，定位FrameA元素，然后再切出到其父框架，再切人FrameB所在框架，定位FrameB元素，代码如下：

>  代码selenium_switch_frame.py内容
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://www.w3school.com.cn/tiy/t.asp?f=html_frame_rows")
driver.switch_to.frame("iframeResult")  # 使用节点id或name,切换到第一层iframe
driver.switch_to.frame(0)   # 根据索引,切换到第二层第1个frame
frame_a = driver.find_element(By.TAG_NAME, "h3")
print(frame_a.text)
driver.switch_to.parent_frame()  # 切换到父框架
driver.switch_to.frame(1)  # 根据索引,切换到第二层第2个frame
frame_b = driver.find_element(By.TAG_NAME, "h3")
print(frame_b.text)
driver.quit()
```
运行后打印结果为：
```
Frame A
Frame B
```

## 小结
Selenium的核心是WebDriver，本章讲解了WebDriver的基本原理，浏览器的一些基本操作。8种基本定位方式，定位一组元素及逐层定位以及元素的点击输入等基本操作。
当遇到弹出警告框、新窗口或框架页面时则需要进行必要的切换，然后进行操作。
学完本章应能完成80%以上自动化流程，下一章将讲述一些更深层次的技巧。
