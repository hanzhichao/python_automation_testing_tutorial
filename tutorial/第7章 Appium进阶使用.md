

在学习完如何使用Appium启动App、定位和操作后，本章继续讲解Appium的进阶使用，主要包括：

- 需求配置详解
- 元素定位及基本操作
- 设备相关操作
- 应用相关操作
- 触控及动作链
- 按键及剪贴板操作
- 移动浏览器和混合App操作

## 6.1  需求配置

由于Appium 支持不同的平台、版本及设备，代码（Appium客户端）在连接Appium服务时需要提供所需操作平台、设备及应用相关的信息。这种信息称之为Desired Capabilities，即期望配置，或Appium启动项配置。
Appium提供非常多的配置项，除了指定平台、设备及应用外，还包括Web、键盘、超时时间、初始化设置等各种配置。其中常用配置项如表6.18所示。

### 6.1.1  Android常用配置

Appium使用UiAutomator及UiAutomator2引擎操作Android设备，常用配置如下表6.18所示：
表6.18  常用Desired Capabilities需求配置项

| 分类                                          | 配置项                                          | 说明                         |
| ------------------------------------------- | -------------------------------------------- | -------------------------- |
| 平台                                          | platformName                                 | 必须，指定平台名称，如Android、iOS等    |
| platformVersion                             | 指定平台版本，如7.1.2                                |                            |
| automationName                              | 指定自动化引擎，如UiAutomator2，不区分大小写                 |                            |
| 设备                                          | deviceName                                   | 指定设备或模拟器名称                 |
| udid                                        | 指定真机设备号                                      |                            |
| orientation                                 | 指定设备朝向，如LANDSCAPE(横屏)、PORTRAIT(竖屏)           |                            |
| networkSpeed                                | 指定网速                                         |                            |
| gpsEnabled                                  | 启用GPS定位                                      |                            |
| unicodeKeyboard                             | 是否切换到Unicode键盘（支持中文输入)，默认为False              |                            |
| resetKeyboard                               | 结束时是否重置键盘，一般和unicodeKeyboard搭配使用，默认为False    |                            |
| settings[settingsKey]                       | 更新设置                                         |                            |
| App                                         | app                                          | 指定应用的apk安装包路径              |
| appPackage                                  | 指定已安装的应用包名                                   |                            |
| appActivity                                 | 指定以验证的应用入口活动名                                |                            |
| autoLaunch                                  | 是否自动启动App，默认为True，测试框架中可以设置为False并手动启动和关闭App |                            |
| autoGrantPermissions                        | 是否自动授权，默认为False                              |                            |
| otherApps                                   | 运行前需要安装的其他应用列表（JSON格式）                       |                            |
| noReset                                     | 不重置App，保留应用当前状态，默认为False                     |                            |
| fullReset                                   | 完全重置App，删除所有应用数据，默认为False                    |                            |
| dontStopAppOnReset                          | 手动重置App时不退出应用，默认为False                       |                            |
| clearSystemFiles                            | 结束时清空产生的系统文件，默认为False                        |                            |
| Web及WebView                                 | browserName                                  | 指定要操作浏览器名称，如Chrome、Firefox |
| autoWebView                                 | 自动切换到WebView上下文，适用于纯H5应用                     |                            |
| ChromeOptions                               | Chrome浏览器选项                                  |                            |
| nativeWebScreenshot                         | 使用设备截图取代浏览器自带的截图功能，more为True                 |                            |
| 超时                                          | newCommandTimeout                            | 等待脚本新命令的超时时间               |
| deviceReadyTimeout                          | 等待设备就绪的超时时间                                  |                            |
| androidDeviceReadyTimeout                   | 等待Android设备启动应用后就绪的超时时间                      |                            |
| androidInstallTimeout                       | 等待Android设备安装应用的超时时间                         |                            |
| 初始化                                         | skipUnlock                                   | 跳过解锁屏幕                     |
| skipDeviceInitialization                    | 跳过设备初始化（安装和启动Appium Settings）                |                            |
| skipServerInstallation                      | 跳过UiAutomator2服务安装                           |                            |
| skipLogcatCapture                           | 跳过Logcat日志捕获设置                               |                            |
| noSign                                      | 跳过检查应用debug签名                                |                            |

### 6.1.1  常见使用场景                                                                                                    
常见使用场景如下：                                                                                                           
1．自动选择设备，不启动App                                                                                                     
```json
{                                                                                                                   
    "platformName"："Android"                                                                                        
}        
```                                                                                                             
 1．自动安装（或重新安装）应用并启动                                                                                                    
 需求配置中通过app指定应用apk安装路径时，将上传apk到设备，安装或重新安装应用。                                                                           
```json
 {                                                                                                                     
     "platformName"："Android",                                                                                         
     "platformVersion"："7.1.2",                                                                                        
     "deviceName"："127.0.0.1:62001",                                                                                   
     "app": "D:\xiaomi.apk",                                                                                           
     "automationName": "UiAutomator2"                                                                                  
 }      
```                                                                                                               
 2．手动安装应用，指定要启动的App                                                                                                    
 ```json                                                                                                               
 {                                                                                                                     
     "platformName"："Android",                                                                                         
     "platformVersion"："7.1.2",                                                                                        
     "deviceName"："127.0.0.1:62001",                                                                                   
     "appPackage": " com.android.settings",                                                                            
     "appActivity": ".Settings",                                                                                       
     "automationName": "UiAutomator2"                                                                                  
 }                                                                                                                     
 ```                                                                                                                   
 3．启动移动浏览器                                                                                                             
 ```json                                                                                                               
 {                                                                                                                     
     "platformName"："Android",                                                                                         
     "platformVersion"："7.1.2",                                                                                        
     "deviceName"："127.0.0.1:62001",                                                                                   
     "browserName": "Chrome"                                                                                           
 }                                                                                                                     
 ```                                                                                                                   
 4．不重置应用，保持当前应用授权、登录等状态                                                                                                
 ```json                                                                                                               
 {                                                                                                                     
   "platformName":"Android",                                                                                         
   "platformVersion":"7.1.2",                                                                                            
   "deviceName": "127.0.0.1:62001",                                                                                   
   "appPackage": " com.android.settings",                                                                                
   "appActivity": ".Settings",                                                                                           
   "noReset": true                                                                                                       
 }                                                                                                                     
 ```                                                                                                                   
 
 5．不卸载应用模拟应用首次安装并自动授权                                                                                                  
 ```json                                                                                                               
{                                                                                                                     
  "platformName":"Android",
  "platformVersion":"7.1.2",
  "deviceName":"127.0.0.1:62001",
  "appPackage": "com.android.settings",
  "appActivity": ".Settings",
  "fullReset": true,
  "autoGrantPermissions": true
}
````

6．非首次安装-开启中文输入支持，跳过初始化操作，不自动启动App
```json
{
    "platformName"："Android",
	"platformVersion"："7.1.2",
    "deviceName"："127.0.0.1:62001",
	"appPackage": " com.android.settings",
	"appActivity": ".Settings",
	"unicodeKeyborad": true,
	"resetKeyborad": true,
	"skipDeviceInitialization": true,
	"skipServerInstallation": true,
	"skipUnlock": true,
	"skipLogcatCapture": true,
	"noSign": true,
	"autoLaunch": false
}
````

> 注：以上示例使用JSON格式表示，在Python脚本中使用需要将其中的true和false改为True及False。

## 6.2  元素定位及操作

在Appium中有两种操作方式，一种是基于坐标的点击（tap）、滑动（swipe）等操作，一种是基于元素的点击（click）、输入(send keys)。基于元素的操作在易读性和屏幕兼容性上较好，因此一般推荐使用这种方式。想要操作元素，则需要先定位元素。
定位元素时自动化脚本中非常重要的一环。我们一般采用元素的一些不同于其他的属性进行定位，如元素的资源ID、显示文字、控件类型等等。
由于Appium融合了多种测试工具，因此也集成了Selenium、UiAutomator2、Espresso以及iOS、Mac、WinApp甚至基于OpenCV的图像识别等各种定位方式。
Android自动化中常用的定位方式如下：

- driver.find_element('id', ' ')：通过resource-id属性定位；
- driver.find_element('class name', ' ')：通过class属性定位；
- driver.find_element('xpath', ' ')：通过XPath定位；
- driver.find_element_by_uiautomator：通过UiAutomator原始UiSelector API进行定位；
- driver.find_elment_by_image：通过图片Base64编码定位。

每种定位方式都支持单数和复数`find_elements_by…`两种方式，单数方法定位到元素时返回元素对象，定位不到时抛出异常。复数方法返回定位到的元素列表，定位不到元素时返回空列表，不会抛出异常，因此也可以用于判断元素存在或处理偶现元素。
Appium中同样支持逐层定位。即定位到元素后，继续向下（子节点）中使用定位方法继续查找。

> 注：由于元素定位的基本原理是基于当前屏幕生成XML格式的布局文件，通过指定属性查找到指定元素并获取期bounds屏幕坐标值，计算出中心点坐标进行点击等操作。
因此每次只能定位当前屏幕中显示的元素，不在当前屏幕中的元素需要滑动页面使得元素显示才能定位到。

### 6.2.1  通过元素资源ID定位

在Android应用中，当元素有resource-id属性时，可以通过该属性定位元素，使用示例如下：

```python
element = driver.find_element_by_id(‘’)
```

或使用通用的定位方式

```python
from appium.webdriver.common import MobileBy

element = driver.find_element(MobileBy.ID, ‘’)
```

> 注：MobileBy.ID即字符串“id”，也可以不导入MobileBy，直接使用driver.find_element(‘id’, ‘’)

### 6.2.2  通过显示文字定位

从用户角度来看，显示文字是识别元素的最明显特征之一。如登录界面的登录按钮、对话框中的确认、取消按钮，弹出的提示（Toast）消息，以及输入框中的提示文字等。所显示的文字一般通过元素的text属性设定，因此我们可以使用XPath语法来描述，如：
```
//*[@text=”登录”]
```
即当前界面，任意位置，任意类型（Tag），text属性为指定文字的节点。
如果想要限定元素类型，比如按钮，可以使用：
```
//android.widget.Button[@text=”登录”]
```
通过显示文本定位，我们甚至可以不借助uiautomatorviewer、Appium Inspector等元素审查工具来快速编写脚本。这种方式除了可以定位普通文字、按钮以外，还可以直接定位弹出对话框（包括授权对话框）中的按钮、弹出Toast消息以及带提示文字的输入框。

> 注：定位Toast元素必须使用UiAutomator2引擎，UiAutomator1不支持

如果元素没有显示文字，如输入框，可以使用元素类型定位，详见下一小节。
使用XPath定位Toast元素示例如下：

> 代码appium_toast.py内容
```python
from appium import webdriver
from selenium.common.exceptions import NoSuchElementException

caps = {
'platformName': 'Android',
    'platformVersion': '7',
    'appPackage': 'com.android.settings',
    'appActivity': '.Settings',
}

print('连接服务-启动设置')
driver = webdriver.Remote('http://localhost:4723/wd/hub', caps)
driver.implicitly_wait(10)  # 智能等待

print('点击声音')
driver.find_element_by_xpath('//*[@text="声音"]').click()

print('点击勿扰')
driver.find_element_by_xpath('//*[@text="勿扰"]').click()

print('点击自动规则')
driver.find_element_by_xpath('//*[@text="自动规则"]').click()

print('点击Weekend')
driver.find_element_by_xpath('//*[@text="Weekend"]').click()

print('点击第一个Switch按钮')
driver.find_element_by_xpath('//android.widget.Switch[1]').click()

toast_text = '将自动规则设置为在指定时间段内开启勿扰模式'
try:
    driver.find_element_by_xpath(f'//*[@text="{toast_text}"]')
except NoSuchElementException:
    print('未定位到指定Toast位到元素')
else:
    print('定位到了指定Toast元素')
driver.quit()
```

使用XPath不仅可以通过显示文本text属性定位，还可以通过控件类型（对应标签名）结合位置定位（从1开始）。

### 6.2.3  通过控件类型定位

元素的class属性用于表示元素的控件类型，如android.widget.Button表示按钮，android.widget.EditText表示输入框等。当元素没有resource-id、text等属性时，我们可以通过控件类型结合find_elements方法，得到所有该类型元素的列表，然后通过索引获取指定的元素，如定位当前页面第二个输入框：
```python
ipt_list = driver.find_element_by_class_name(‘android.widght.EditText’)
password_ipt = ipt_list[1]
```

### 6.2.4  使用UiSelector定位

UiSelector是UIAutomtor中的原始元素选择API接口，使用Java语法格式。
UiSelector中包含了通过资源ID（resourceId）、显示文本（text）、控件类型（className）等多种方法，并提供了包含（Contains）、正则匹配（Matches）、开头匹配（StartsWith）、结尾匹配（EndWith）等多种匹配方法。常用的定位方式如下：

#### 通过资源ID定位
```python
driver.find_element_by_android_uiautomator('new UiSelector().resourceId(“id”)').click()
```

> 注：不同于XML布局文件中的resource-id格式，UiSelector API中属性采用驼峰法命名，因此是resourceId。

#### 通过显示文字定位

```python
# Text定位
driver.find_element_by_android_UiAutomator('new UiSelector().text("textstr")')
# 文字包含
driver.find_element_by_android_UiAutomator('new UiSelector().textContains(textStr)')
# 以哪个字符开始
driver.find_element_by_android_UiAutomator('new UiSelector().textStartsWith(textStr)')
```

#### 通过控件类型定位

```python
driver.find_element_by_android_UiAutomator('new UiSelector().className("class属性")')
```

> 注：className属性为驼峰法格式

#### 多属性结合定位
```python
driver.find_element_by_android_UiAutomator('new UiSelector().className("android.widget.TextView").text("Custom View")')
```
其他常用的定位方式如表6.20所示：
表6.20  UiSelector常用定位方式

| 定位方式                                                                                                                                                                   | 说明            |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| UiSelector.text                                                                                                                                                        | 通过文本定位        |
| UiSelector.textContains                                                                                                                                                | 文本包含字符串       |
| UiSelector.textStartsWith                                                                                                                                              | 文本以指定字符串开头    |
| UiSelector.textMatches                                                                                                                                                 | 文本匹配指定正则表达式   |
| UiSelector.resourceId                                                                                                                                                  | 通过资源ID定位      |
| UiSelector.resourceIdMatches                                                                                                                                           | 资源ID匹配指定正则表达式 |
| UiSelector.className                                                                                                                                                   | 通过类名定位        |
| UiSelector.classNameMatches                                                                                                                                            | 类型匹配指定正则表达式   |
| UiSelector.description                                                                                                                                                 | 通过内容描述定位      |
| UiSelector.descriptionStartWith                                                                                                                                        | 内容描述以指定字符串开头  |
| UiSelector.descriptionMatches                                                                                                                                          | 内容描述匹配指定正则表达式 |
| UiSelector和XPath两种语法都可以定位到任意一种元素，可以根据自己的偏好选择。                                                                                                                          |               |


### 6.2.5  元素存在判断                                                                                                                                                                     
在App自动化流程中，有时候会出现首页广告、升级提升、授权提升等，有时候又没有。这种元素称为偶现元素。对于偶现元素，我们首先要判断元素是否存在，再进行操作。                                                                                                       
另外，在自动化流程中往往需要通过判断页面上是否有特定元素来断言步骤是否成功。                                                                                                                                               
由于单数的定位方法find_element在定位不到元素时会抛出异常，中断脚本运行，因此在判断元素存在时可以使用try … except语句示例如下：                                                                                                          

> 代码appium_check_element.py内容                                                                                                                                                          
```python                                                                                                                                                                            
from appium import webdriver                                                                                                                                                         
from selenium.common.exceptions import NoSuchElementException                                                                                                                        
caps = {                                                                                                                                                                             
    'platformName': 'Android',                                                                                                                                                       
    'platformVersion': '7',                                                                                                                                                          
    'appPackage': 'com.android.settings',                                                                                                                                            
    'appActivity': '.Settings',                                                                                                                                                      
}                                                                                                                                                                                    
driver = webdriver.Remote('<http://127.0.0.1:4723/wd/hub>', caps)                                                                                                                    
try:                                                                                                                                                                                 
    driver.find_element_by_xpath('//*[@text="关于平板电脑"]').click()                                                                                                                      
except NoSuchElementException:                                                                                                                                                       
    print('不存在 关于平板电脑')                                                                                                                                                              
driver.quit()                                                                                                                                                                        
```                                                                                                                                                                                  
由于复数形式的定位方法，定位不到元素时返回空列表，不抛出异常，我们也可以使用fInd_elements来判断元素存在，示例如下：                                                                                                                     
```python                                                                                                                                                                            
…                                                                                                                                                                                    
elm_list = driver. find_elements_by_xpath('//*[@text="关于平板电脑"]')                                                                                                                     
if len(elm_list) > 1:                                                                                                                                                                
    print('存在 关于平板电脑')                                                                                                                                                               
    about = elm_list[0]  # 取列表第一个                                                                                                                                                    
    about.click()  # 点击                                                                                                                                                              
 else:                                                                                                                                                                                 
     print('不存在 关于平板电脑')                                                                                                                                                               
 ```                                                                                                                                                                                   
 ### 6.2.6  操作元素                                                                                                                                                                       
 点击和输入是元素最常见的操作。                                                                                                                                                                       
 点击可以适用于当前屏幕上任何可点击的按钮、切换按钮、文字、图片、输入框、日期框、下拉选项等各种控件。                                                                                                                                    
 输入一般用于输入框包括原生应用和WebView中的输入框等。在输入时，有两种方式。一种是使用send_keys方法以模拟按键的方式输入（输入中文时需要启用unicode键盘），另一种则是直接修改属性来设置文本，对应原生Android应用可以使用set_text方法，对应WebView网页中的表单元素可以使用set_value方法。                
 Appium常用的元素操作方法如下：                                                                                                                                                                    

- click()：点击元素；
- send_keys('文本')：以模拟按键的方式输入文本；
- clear()：清空输入；
- set_text()：设置元素的text属性，仅支持Android；
- set_value('文本')：设置Web元素的属性；

示例：启动设置，并搜索“关于”，如图6.10所示。
图6.10  设置右上角搜索按钮
代码如下：

> 代码appium_element_operation.py内容
```python
from appium import webdriver
from time import sleep

caps = {
    'platformName': 'Android',  # 必填
    'platformVersion': '7',
    'appPackage': 'com.android.settings',
    'appActivity': '.Settings',
}
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)  # 智能等待
print('点击右上角搜索按钮')
driver.find_element_by_accessibility_id('搜索设置').click()
print('定位第一个输入框')
search_ipt = driver.find_element_by_class_name('android.widget.EditText')
print('输入 关于')
search_ipt.send_keys('关于')
sleep(1)
print('清空输入')
search_ipt.clear()
sleep(1)
print('设置文字 关于')
search_ipt.set_text('关于')
print('点击关于平板电脑')
driver.find_element_by_xpath('//*[@text="关于平板电脑"]').click()
sleep(3)
driver.quit()
```

当元素只需要操作一次的时，可以定位完直接操作，如
driver.find_element_by_accessibility_id('搜索设置').click()
当元素需要操作两次以上时，建议先赋值给变量，再进行操作，这样可以减少元素定位时间。

> 注：不能操作后再赋值变量，如：

search_btn = driver.find_element_by_accessibility_id('搜索设置').click()
由于click()操作返回None，因此得到的search_btn是None，而不是定位到的元素。

### 6.2.7  获取元素属性

在自动化流程中，有时我们需要获取元素状态、规格、文本或其他属性来进行判断或断言。Appium中，元素对象常用的属性及方法如下：

- text：元素文本；
- tag_name：元素内容描述
- size：元素尺寸；
- location：元素位置；
- rect：元素规格，包含尺寸和位置信息；
- is_enabled()：是否可用；
- is_displayed()：是否显示；
- is_selected()：是否已选中；
- get_attribute('属性名')：获取元素指定属性；
- screenshot('保存路径')：保存元素截图到指定位置。

get_attribute方法支持的元素属性如表6.3所示：
表6.3  get_attribute方法支持的属性

| 分类                                                          | 属性名             | 说明       |
| ----------------------------------------------------------- | --------------- | -------- |
| 基本信息                                                        | package         | 包名       |
| resource-id或resourceId                                      | 资源ID            |          |
| class或className                                             | 控件类型            |          |
| text或name                                                   | 显示文本            |          |
| content-desc或contentDescription                             | 内容描述            |          |
| contentSize                                                 | 可滚动内容大小         |          |
| 可用性                                                         | clickable       | 是否可点击    |
| enabled                                                     | 是否可用            |          |
| focusable                                                   | 是否可聚焦           |          |
| long-clickable或longClickable                                | 是否可长按           |          |
| scrollable                                                  | 是否可滚动           |          |
| 状态                                                          | displayed       | 是否处于显示状态 |
| focused                                                     | 是否处于聚焦状态        |          |
| selected                                                    | 是否已选中           |          |
| password                                                    | 是否密码框           |          |
| 坐标                                                          | selection-start | 选项开始位置   |
| selection-end                                               | 选项结束位置          |          |
| bounds                                                      | 坐标绑定            |          |
| 示例：获取小米商城首页输入框，推荐搜索词元素相关属性，如图6.13所示：[[hzc34]](#_msocom_34)  |                 |          |
| 图6.13  小米商城首页搜索框文本                                          |                 |          |
| 示例代码如下：                                                     |                 |          |
| 代码appium_element_attribute.py内容                             |                 |          |
| ```python                                                   |                 |          |
| from appium import webdriver                                |                 |          |

caps = {
    'platformName': 'Android',  # 必填
    'platformVersion': '7',
    'appPackage': 'com.android.settings',
    'appActivity': '.Settings',
}
driver = webdriver.Remote('<http://127.0.0.1:4723/wd/hub>', caps)
driver.implicitly_wait(20)  # 智能等待
print('点击右上角搜索按钮')
driver.find_element_by_accessibility_id('搜索设置').click()
print('定位第一个输入框')
search_ipt = driver.find_element_by_class_name('android.widget.EditText')
search_ipt.screenshot('search_ipt.png')  # 保存元素截图到当前执行目录
print('元素文本', search_ipt.text)
print('元素内容描述', search_ipt.tag_name)  # 元素内容描述
print('元素控件类型', search_ipt.get_attribute('class'))
print('元素位置', search_ipt.location)
print('元素尺寸', search_ipt.size)
print('元素规格', search_ipt.rect)
print('元素是否可用', search_ipt.is_enabled())
print('元素是否显示', search_ipt.is_displayed())
print('元素是否已选中', search_ipt.is_selected())
print('元素是否可点击', search_ipt.get_attribute('clickable') == 'true')
print('元素是否已聚焦', search_ipt.get_attribute('focused') == 'true')
driver.quit()

```

> 注：通过get_attribute获取的clickable等属性是字符串形式的true、false

执行结果如下：
```

点击右上角搜索按钮
定位第一个输入框
元素文本 搜索…
元素内容描述 None
元素控件类型 android.widget.EditText
元素位置 {'x': 96, 'y': 51}
元素尺寸 {'height': 54, 'width': 456}
元素规格 {'height': 54, 'width': 456, 'x': 96, 'y': 51}
元素是否可用 True
元素是否显示 True
元素是否已选中 False
元素是否可点击 True
元素是否已聚焦 True

## 6.3  设备及应用操作
### 6.3.1  设备状态
在应用操作中有时需要获取设备相关状态，如定位、设备时间、网络状态、网速、移动信号、电量等状态，或者设置网络及位置状态以测试特定场景。
Appium设备状态相关常用属性及操作如表7.2所示：

7.2  Appium 设备相关属性及操作

| 分类 | 属性/方法 | 说明 |
| ---- | ---- | ---- |
| 设备时间 | device_time | 设备时间 |
| get_device_time() | 获取设备时间 |  |
| 位置 | toggle_location_services() | 切换开启、关闭定位服务 |
| set_location() | 设置位置 |  |
| location | 获取位置 |  |
| 网络状态 | network_connection | 获取网络状态值 |
| set_network_connection() | 设置网络状态值 |  |
| toggle_wifi() | 切换开启、关闭Wi-Fi |  |
| set_network_speed() | 设置网速，仅支持Android原生模拟器 |  |
| 电量 | battery_info | 获取电池信息 |
| set_power_capacity | 设置电池电量，仅支持Android原生模拟器 |  |
| set_power_ac() | 设定充电模式，仅支持Android原生模拟器 |  |
| 移动网络 | make_gsm_call() | 拨打电话，仅支持Android原生模拟器 |
| set_gsm_signal() | 设置GSM信号，仅支持Android原生模拟器 |  |
| set_gsm_voice() | 设置GSM音量，仅支持Android原生模拟器 |  |
| 屏幕尺寸 | get_window_size() | 获取屏幕尺寸 |
| get_display_density() | 获取显示密度 |  |
>注：模拟网速、信号状态等仅支持Android原生模拟器。使用示例如下：


> 代码appium_device_state.py内容
```python
from appium import webdriver
caps = {
    'platformName': 'Android'
}
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.set_network_connection(6)  # 设置网络状态 移动网络 + Wi-Fi
driver.set_location(39.91, 116.40, 43.6)  # 设置坐标 维度39.91 经度116.40 海拔43.6
print('当前屏幕尺寸', driver.get_window_size())
print('当前显示密度', driver.get_display_density())
print('当前设备时间', driver.device_time)
print('当前位置', driver.location)
print('当前设备朝向', driver.orientation)
print('当前网络状态', driver.network_connection)
print('当前电量信息', driver.battery_info)
driver.quit()
````

运行结果如下

```
当前屏幕尺寸 {'width': 720, 'height': 1280}
当前显示密度 240
当前设备时间 2021-01-13T14:25:38+08:00
当前位置 {'latitude': 39.91, 'longitude': 116.4, 'altitude': 43.6}
当前设备朝向 PORTRAIT
当前网络状态 6
当前电量信息 {'level': 0.89, 'state': 5}
返回到屏幕尺寸是包含宽度和高度字典格式；设备时间返回的是UTC标准时间；位置信息包含latitude维度、longitude经度和altitude海拔三个维度的信息；屏幕方向PORTRAIT表示竖屏模式；电量信息中level表示电量百分比，state表示充放电状态，5表示已充满（此处不一致是模拟器本身的设置）。
```

### 6.3.2  应用操作

有时我们需要对应用进行单独的操作，Appium中包含了安装、卸载、重置、启动、关闭等各种常用的应用操作方法，常用的方法如下：

- driver.is_app_installed('包名')：判断是否安装了指定应用；
- driver.install_app('apk安装包路径')：安装应用；
- driver.remove_app('包名')：卸载指定应用；
- driver.reset_app('包名')：重置指定应用；
- driver.launch_app()：启动caps中配置的应用；
- driver.start_activity('包名', '活动名', 参数)：启动指定Activity，参数可以为空；
- driver.current_activity：当前Activity名称；
- driver.background_app(3)：将caps中配置的应用置于后台3s然后恢复；
- driver.close_app()；关闭caps指定的应用；

重新安装应用的示例如下：

> 代码appium_reinstall_app.py内容
```python
from appium import webdriver
caps = {
    'platformName': 'Android',  # 必填
}
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)
if driver.is_app_installed('com.xiaomi.shop') is True:
    print('小米商城已安装, 卸载应用')
    driver.remove_app('com.xiaomi.shop')
print('安装App')
driver.install_app('D:\\xiaomi.apk')
print('通过Activity直接跳到登录页')
driver.start_activity('com.xiaomi.shop', 'com.xiaomi.passport.ui.LoginActivity')
driver.quit()
```

> 注：如果不是测试新版本App，可以在caps中使用fullReset: True来模拟App首次安装。

由于caps中没有配置App相关信息，因此不能使用launch_app、close_app以及backgroupd_app等方法。
手动启动和关闭app常用于用例之间的衔接，如：

> 代码test_xiaomi_shop_app.py内容
```python
import unittest
from appium import webdriver
class TestXiaoMiShop(unittest.TestCase):
    @classmethod
    def setUpClass(cls):  # 测试类级测试准备：连接Appium
        caps = {
            'platformName': 'Android',  # 必填
            'appPackage': 'com.xiaomi.shop',
            'appActivity': '.activity.MainTabActivity',
            'autoLaunch': False  # 不自动启动App
        }
        cls.driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
    def setUp(self):  # 每个用例执行前启动App
        self.driver.launch_app()
    def tearDown(self):  # 每个用例执行后关闭App
        self.driver.close_app()
    def test_search(self):
        pass
    def test_login(self):
        pass
    def test_shopping(self):
        pass
```

此处使用了unittest测试框架，setUpClass用于该测试类所有用例执行前的测试准备，即连接Appium服务，并将driver绑定给cls类对象（类的实例self自动继承cls的属性）。
setUp为用例级的测试准备，在每个用例执行前启动用例，这里需要配置autoLaunch: False来禁止连接Appium时便自动启动，不然会导致重复启动App。
tearDown方法在每个用例执行后关闭App，这样能保证用例执行状态衔接，避免因为执行顺序不确定导致的用例执行前的App状态的不确定性。

### 6.3.3  文件操作

有时我们需要向设备上传或者从设备下载一些文件，可以使用driver的push_file和pull_file来实现这一操作。

- driver.push_file()：上传本地文件或文件Base64编码到设备指定位置；
- driver.pull_file()：获取设备文件Base64编码格式内容；
- driver.pull_forder()：获取设备文件夹zip压缩文件的Base64编码内容。

使用示例如下：

> 代码appium_push_and_pull_file.py内容
```python
import os
import base64
import zipfile
from appium import webdriver
from time import sleep

caps = {
    'platformName': 'Android',
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)

print('上传文件')
driver.push_file(source_path='D:\\123.txt', destination_path='/sdcard/123.txt')

print('执行adb命令')
adb_cmd = {'command': 'ls', 'args':['/sdcard', '|', 'grep', '"123.txt"']}
result = driver.execute_script('mobile: shell', adb_cmd)  # 执行adb命令
print(result.strip())  # 去除结尾的空行

print('下载文件')
file_base64 = driver.pull_file('/sdcard/server.log')  # 获取文件base64数据
file_bytes = base64.b64decode(file_base64)  # 解码为二进制数据
with open('D:\\server.log', 'wb') as f:
    f.write(file_bytes)  # 二进制保存文件
    
print('下载目录')
folder_zip_base64 = driver.pull_folder('/sdcard/Android')
folder_zip_bytes = base64.b64decode(folder_zip_base64)
with open('D:\\Android.zip', 'wb') as f:
    f.write(folder_zip_bytes)  # 二进制保存文件
    
print('解压缩zip文件')
os.chdir('D:\\')  # 切换到D盘
zipfile.ZipFile('Android.zip').extractall('Android')  # 解压到Android目录
driver.quit()
```

执行ADB命令可以使用execute_script方法，格式如上例。
由于pull_file和pull_folder得到的是文件的Base64编码，因此需要先解码成二进制数据，然后使用wb方式写入文件。
解压缩zip文件可以使用Python自带的zipfile模块，可以先使用os.chdir切换到指定目录，再解压，也可以不切换直接解压时指定解压的路径。

### 6.3.4  按键、键盘及剪贴板操作

同ADB中的keyevent命令，模拟按键可以使用driver的keyevent或press_keycode方法，同时还支持长按。
在操作流程中还支持判断键盘是否显示并操作隐藏键盘，以及获取可用的输入法等等，常用的操作方法如下：

- keyevent()：模拟按键实际；
- press_keycode()：同上，模拟按键；
- long_press_keycode()：长按指定键；
- is_keyboard_shown()：键盘是否显示；
- hide_keyboard()：隐藏键盘；
- available_ime_engines：获取可用输入法。

### 6.3.5  输入法操作

有时我们可能遇到有些搜索框，没有提供搜索按钮，而是通过输入法中的确认和搜索键来触发的。一般情况下我们可以尝试使用模拟按键输入回车键尝试是否能触发搜索，如果不能可以通过执行脚本来触发输入法的特定操作。
调用输入操作的使用方法为：
```python
driver.execute_script('mobile: performEditorAction', {'action': search})
```
常见输入执行的操作如下：

- normal：常规操作；
- unspecified：未指定操作；
- none：无操作
- go：点击输入发Go按钮；
- search：点击输入法搜索按钮；
- done：点击输入法确认按钮；
- previous：点击输入向前按钮；

使用示例如下：
代码appium_ime_actions.py内容

```python
from appium import webdriver
from time import sleep

caps = {
    'platformName': 'Android',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity',
    'noReset': True
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)
search_text = driver.find_element_by_id('com.xiaomi.shop.plugin.homepage:id/switcher')\
    .find_element_by_class_name('android.widget.TextView')
driver.set_clipboard_text(search_text.text)
search_text.click()

print('键盘是否打开', driver.is_keyboard_shown())
print('可用输入法', driver.available_ime_engines)
search_ipt = driver.find_element_by_class_name('android.widget.EditText')
search_ipt.send_keys(driver.get_clipboard_text())  # 输入剪贴板文本
driver.keyevent(28)  # 按清空键 KEYCODE_CLEAR
driver.press_keycode(50,28672)  # 按Ctrl+V 即 KEYCODE_V, META_CTRL_ON
# driver.keyevent(279)  # 按粘贴键 KEYCODE_PASTE
driver.keyevent(66)  # 按回车键KEYCODE_ENTER，无法触发搜索

print('触发输入法上的搜索')
driver.execute_script('mobile: performEditorAction', {'action': 'search'})
sleep(1)
driver.keyevent(4)  # 按返回键KEYCODE_BACK
sleep(1)
driver.keyevent(3)  # 按主页键KEYCODE_HOME
driver.quit()
```

执行结果如下：
键盘是否打开 False
可用输入法 ['com.android.inputservice/.InputService', 'com.example.android.softkeyboard/.SoftKeyboard', 'io.appium.settings/.AppiumIME', 'io.appium.settings/.UnicodeIME']
触发输入法上的搜索
代码appium_common_actions.py内容

```python
from appium import webdriver
from time import sleep

caps = {
    "platformName": "Android",
    "platformVersion": "5.1.1",
    "deviceName": "127.0.0.1:62001",
    "appPackage": "com.android.androidui",
    "appActivity": "com.android.androidui.MainActivity",
    "autoLaunch": False
}

driver = webdriver.Remote('http://localhost:4723/wd/hub', caps)
driver.launch_app()
driver.open_notifications()
driver.press_keycode(4)
sleep(3)

driver.start_activity("com.android.browser", "com.android.browser.BrowserActivity")
if driver.current_activity == ".BrowserActivity":
    print("一致")
    
driver.save_screenshot(r"D:\123.png")
driver.background_app(5)

print(driver.network_connection)
if driver.is_app_installed("com.lqr.wechat"):
    driver.remove_app("com.lqr.wechat")
else:
    driver.install_app(r"C:\Users\Secoo\Desktop\16期\day09\app-debug.apk")
    
driver.quit()
```

## 6.4  触控操作及动作链

除了基于元素的定位及操作外，有时我们还需要辅助与基于坐标的点击、滑动以及元素滑动、拖动等触控操作，Appium常用的触控操作方法如下。

- tap：触点指定坐标，支持多点触控和设置按下时间；
- swipe：屏幕滑动，从起始点坐标滑动到终止点坐标，支持设定滑动间隔；
- flick：屏幕快滑，从起始点坐标滑动到终止点坐标，无时间间隔；
- scroll：元素滑动，从一个元素滑动到另一个元素，支持滑动间隔；
- drag_and_drop：元素拖动，将一个元素拖动到另一个元素位置。

另外还可以使用TouchAction来设计一系列的动作并执行，或者使用MultiAction来实现多指操作如捏合及放大等等。

### 6.4.1  坐标点击

当元素无法定位或是图片的某个区域时，我们可以基于坐标点击，比如App首页弹出广告，当没有关闭按钮时，我们可以尝试点击空白处（非广告区域）来关闭广告。我们可以使用Appium Inspector工具的坐标点击工具（从左到右第三个按钮），来实时查看要点击的坐标，然后使用driver.tap()方法来进行坐标点击，示例如下：
代码appium_tap.py内容

```python
from appium import webdriver
from selenium.common.exceptions import NoSuchElementException
from time import sleep

caps = {
    'platformName': 'Android',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity',
    'noReset': True
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
sleep(5)
driver.tap[(100, 200)]   # 点击坐标(100,200)
sleep(3)
driver.quit()
```

> 注意：driver.tap()方法支持多点触控，因此其参数是一个列表，列表的每一项是一对坐标。

### 6.4.2  屏幕滑动

滑动屏幕是最常用的操作之一，driver.swipe(start_x, start_y, end_x, end_y, duration)方法来操作屏幕滑动。其中start_x和start_y是起始点坐标，end_x和end_y是结束点坐标，duration是滑动间隔（即滑动速度）。另外还有一个无滑动间隔的快滑操作driver.flick(start_x, start_y, end_x, end_y)。
配合获取屏幕尺寸的driver.get_window_size()方法我们可以封装出常用的上滑、下滑、左滑及右滑等操作，示例如下：
代码appium_swipe.py内容

```python
class SwipeAction:
    def __init__(self, driver):
        self.driver = driver
        size = self.driver.get_window_size()
        self.width = size['width']
        self.height = size['height']
        
    def swipe_up(self, from_left=0.5):
        """上滑，默认距左边0.5个屏幕宽度"""
        start_x = end_x = self.width * from_left
        # 默认从屏幕高度的80%处滑动到屏幕高度的20%处
        start_y, end_y = self.height * 0.8, self.height * 0.2
        self.driver.swipe(start_x, start_y, end_x, end_y)
        
   def swipe_down(self, from_left=0.5):
        """下滑，默认距左边0.5个屏幕宽度"""
        start_x = end_x = self.width * from_left
        # 默认从屏幕高度的20%处滑动到屏幕高度的80%处
        start_y, end_y = self.height * 0.2, self.height * 0.8
        self.driver.swipe(start_x, start_y, end_x, end_y)
        
    def swipe_left(self, from_top=0.5):
        """左滑，默认距上边0.5个屏幕高度"""
        # 默认从屏幕宽度的20%处滑动到屏幕宽度的80%处
        start_x, end_x = self.width * 0.2, self.width * 0.8
        start_y = end_y = self.height * from_top
        self.driver.swipe(start_x, start_y, end_x, end_y)
        
    def swipe_left(self, from_top=0.5):
        """右滑，默认距上边0.5个屏幕高度"""
        # 默认从屏幕宽度的80%处滑动到屏幕宽度的20%处
        start_x, end_x = self.width * 0.28 self.width * 0.2
        start_y = end_y = self.height * from_top
        self.driver.swipe(start_x, start_y, end_x, end_y)
```

### 6.4.3  滑动查找

App中的常常使用滑动操作来完成页面切换等操作，同时，由于定位元素时只能定位到当前屏幕的内容，而无法定位到未显示的元素，因此也需要便滑动变查找。
以下示例中演示了如何进行滑动查找。

> 代码appium_swipe_find_elment.py内容
```python
from appium import webdriver
from selenium.common.exceptions import NoSuchElementException
from time import sleep

caps = {
    'platformName': 'Android',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity',
    'noReset': True
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
sleep(5)
driver.find_element_by_xpath('//*[@text="分类"]').click()
sleep(1)
screen_size = driver.get_window_size()
width, height = screen_size['width'], screen_size['height']
# 起始点x和终止点x都取宽度的10%位置
start_x = end_x = 0.1 * width
# 起始点y取高度的90%，终止点y取高度的10%
start_y, end_y = 0.9 * height, 0.1 * height
for i in range(10):
    try:
        driver.find_element_by_xpath('//*[@text="零售店"]').click()
    except NoSuchElementException:
        print('未找到，上滑')
        driver.swipe(start_x, start_y, end_x, end_y)  # 上滑
driver.quit()
```

在滑动操作时，这里设置了最大的滑动次数为10，读者可以根据实际情况进行修改。

> 注：上滑查时，使用智能等待implicitly_wait，会延长每次找不到元素的判断时间，因此一般不一起使用。

### 6.4.4  元素滑动及拖动

9.13  Android设置字体大小

> 代码appium_touch_action_move_to.py内容
```python
from appium import webdriver
from appium.webdriver.common.touch_action import TouchAction

caps = {
    'platformName': 'Android',
    'appPackage': 'com.android.settings',
    'appActivity': '.Settings'
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)
driver.find_element_by_xpath('//*[@text="显示"]').click()
driver.find_element_by_xpath('//*[@text="字体大小"]').click()

# 使用坐标滑动
# TouchAction(driver).press(x=278, y=1177).wait(500).move_to(x=433, y=1177).release().perform()
# 使用元素滑动
seek_bar = driver.find_element_by_id('com.android.settings:id/seek_bar')
default = seek_bar.find_element_by_accessibility_id('默认')
large = seek_bar.find_element_by_accessibility_id('大')
TouchAction(driver).press(default).wait(500).move_to(large).release().perform()

# driver.scroll(default, large)
# driver.drag_and_drop(default, large)
driver.quit()
```

上例中的拖动使用scroll或drag_and_drop也可以实现。

### 6.4.5  触控动作链

有时我们需要设计一系列的屏幕操作，如连续滑动等，此时我们可以使用TouchAction来设计一条动作链并执行，动作链支持的方法如下：

- tap()：点击指定元素或坐标；
- press()：在指定元素或坐标处按下；
- long_press()：长按指定元素或坐标；
- wait()：等待，单位毫秒；
- move_to()：移动到指定元素或坐标；
- release()：松开；
- perform()：实时操作；

如图9.14自动设置屏幕解锁图案的示例如下：
图9.14  Android设置解锁图案

> 代码appium_touch_action_draw.py内容a
```python
from appium import webdriver
from appium.webdriver.common.touch_action import TouchAction
from time import sleep
caps = {
    'platformName': 'Android',
    'appPackage': 'com.android.settings',
    'appActivity': '.Settings'
}
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)
screen_size = driver.get_window_size()
width, height = screen_size['width'], screen_size['height']
start_x = end_x = 0.5 * width  # 起始点x和终止点x都取宽度的50%位置
start_y, end_y = 0.9 * height, 0.1 * height  # 起始点y取高度的90%，终止点y取高度的10%
driver.swipe(start_x, start_y, end_x, end_y)  # 上滑
driver.find_element_by_xpath('//*[@text="安全"]').click()
driver.find_element_by_xpath('//*[@text="屏幕锁定"]').click()
driver.find_element_by_xpath('//*[@text="图案"]').click()
sleep(1)
TouchAction(driver).press(x=143, y=618).wait(200)\
    .move_to(x=571, y=618).wait(200)\
    .move_to(x=143, y=1045).wait(200)\
    .move_to(x=571, y=1045).wait(200)\
    .release().perform()
driver.quit()
```

## 6.5  其他操作

### 6.5.1  移动浏览器操作

由于Appium的WebDriver继承自Selenium，因此也支持使用chromedriver操作移动浏览器，首先需要在设备上安装Chrome浏览器，在PC上下载对应版本的chromedriver并放置到配置了环境变量中的路径中，在caps中使用browserName指定要启动的浏览器名即可启动浏览器，driver相关的操作和Selenium一致，元素定位可以在PC上使用PC版Chrome打开开发者工具，切换为手机模式使用Elements面板审查和定位元素。
以下是俩演示了使用移动Chrome浏览器，打开百度并搜索appium关键字的示例。

> 代码appium_mobile_chrome.py内容
```python
from appium import webdriver

caps = {
    "platformName": "Android",
    "platformVersion": "5.1.1",
    "deviceName": "127.0.0.1:62001",
    "browserName": "chrome"
}
driver = webdriver.Remote('http://localhost:4723/wd/hub', caps)
driver.get("http://m.baidu.com")
driver.find_element_by_id("index-kw").send_keys("appium")
driver.find_element_by_id("index-bn").click()
driver.quit()
```

和操作不同app不同的是，caps中不用指定app或appPackage和appActivity，而是使用browserName来指定要启动的浏览器名称，常用的为chrome和Firefox。
然后同样使用webdriver的Remote方法连接Appium服务，移动浏览器中页面元素的操作和Selenium完全一致，支持8中定位方式进行定位及操作。

### 6.5.2  混合应用操作

常见的App分为原生（Native）、网页（H5）和混合（Hybrid）App三种。原生App性能较好，使用应用框架，可以获取更多的权限，执行更复杂的操作。缺点是每次更新都需要重新编码、打包及发布，流程较长。而网页（H5）页面则可以通过服务端配置，快速修改，更改方便，适合一些活动页面，由于网页（H5）可获取的系统权限有限，所以出现了混合（Hybrid）App的模式，基础框架通过原生App实现，活动页面、商品列表页、详情页等使用H5实现。兼具了性能和快速变更的能力。
在Appium中使用上下文（Context）来区分当前操作是原生（Native）应用中还是网页(WebView)中，我们可以使用以下方法来查看当前所处的上下文以及切换到指定的杀青下文。

- driver.context：当前所处的上下文，原生显示NATIVE，网页显示WEBVIEW_***
- driver.swtich_to.context()：切换到指定上下文

以下示例演示了，一个包含一个嵌入式浏览器（WebView）的app的上下文切换和在WebView中的定位和操作。
图 x.x

> 代码appium_hybird_app.py内容
```python
from appium import webdriver
from time import sleep

caps = {
    'platformName': 'Android',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity',
    'noReset': True
}

driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
driver.implicitly_wait(20)
driver.find_element_by_xpath('//*[@text="分类"]').click()
sleep(1)

screen_size = driver.get_window_size()
width, height = screen_size['width'], screen_size['height']
start_x = end_x = 0.1 * width  # 起始点x和终止点x都取宽度的10%位置
start_y, end_y = 0.9 * height, 0.1 * height  # 起始点y取高度的90%，终止点y取高度的10%
driver.swipe(start_x, start_y, end_x, end_y)  # 上滑
driver.find_element_by_xpath('//*[@text="小米服务"]').click()
driver.find_element_by_xpath('//*[@text="以旧换新"]').click()
sleep(1)
# xpath = '/hierarchy/android.widget.FrameLayout/android.widget.LinearLayout/' \
#         'android.widget.FrameLayout/android.widget.FrameLayout/' \
#         'android.widget.LinearLayout/android.widget.FrameLayout/android.widget.LinearLayout' \
#         '/android.webkit.WebView/android.webkit.WebView/' \
#         'android.view.View/android.view.View/android.view.View[2]/' \
#         'android.view.View/android.widget.ListView[1]/android.view.View[1]/' \
#         'android.view.View/android.widget.Image'
xpath = '//android.webkit.WebView//android.widget.ListView[1]//android.widget.Image[1]'
driver.find_element_by_xpath(xpath).click()
sleep(3)
driver.quit()
```

Appium Inspector计算出的XPath比较长，并且使用绝对路径会不太稳定，我们可以提前关键特征进行模糊定位（速度稍慢），XPath改为如下：
xpath = '//android.webkit.WebView//android.widget.ListView[1]//android.widget.Image[1]'
即WebView中第一个（XPath索引从1开始）ListView下的第一个Image图片。

### 6.5.3  Appium并发操作多台设备

为了提高测试效率，有时候我们需要使用多台设备来并发执行自动化脚本。首先同一台PC使用ADB可以连接多台设备，真机一般使用使用USB连接并安装驱动以及打开手机USB调试即可，虚拟机（在Windows）上启动多台虚拟机后可能还需要使用adb connect命令手动连接设备。连接多台设备后我们可以使用adb devices命令查看已连接的设备，执行结果如下：

```bash
$ adb devices
List of devices attached
127.0.0.1:62001 device
127.0.0.1:62025 device
```

同时，一台Appium Server也是支持连接多台设备的。当然也可以每一台设备启动一个Appium Server（注意分配好端口好，避免冲突，每个Appium Server需要占用两个端口）。
以一台Appium Server为例，我们可以使用Python的多线程来并发在两个设备上同时启动App进行操作，示例如下：
代码appium_ parallel.py内容

```python
from appium import webdriver
from time import sleep
import threading

def launch_app(platform_version):
    caps = {
        'platformName': 'Android',
        'platformVersion': platform_version,
        'app': 'D:\\xiaomi.apk'
    }
    driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', caps)
    sleep(5)
    driver.quit()
t1 = threading.Thread(target=launch_app, args=('5.1.1',))
t2 = threading.Thread(target=launch_app, args=('7.1.2',))
t1.start()
t2.start()
t1.join()
t2.join()
```

本例中使用线程来实现并发，也可以使用线程池、进程池或者多进程实现。

## 6.6  小结

本章主要讲解了Appium的一些进阶使用技巧，包括设备状态、应用的安装与卸载、文件操作、触控操作、动作链、模拟按键以及混合应用等的操作。
