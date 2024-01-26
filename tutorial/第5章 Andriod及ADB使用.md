---
date created: 2024-01-26 21:39
---

随着移动互联网的兴起，App应用逐级称为很多公司的主力产品。相比于桌面及Web应用，App产品往往迭代更快，这便带来了大量的回归工作。
App应用往往是直面客户的，并且包含了大量的样式和前端交互，同时由于App中技术框架的不同和设备的多样性，单纯的接口服务层的回归是不足以覆盖App功能的。因此App UI层的自动化测试是非常重要的，可以用来减轻客户端人员的大量回归工作。
Appium是目前比较流行的App UI自动化测试工具，兼容Android和iOS，一套代码便可完成多种设备的自动化测试。同时Appium支持Java、Python、Ruby、JavaScript等各种主流语言，API上继承Selenium的使用方式，上手非常方便。
本章将介绍Appium的基本原理、环境搭建、Android基本知识、ADB命令以及Monkey命令的基本使用方法。
由于iOS设备的App只能使用MacOS系统的电脑进行测试，本章只讲解Appium基于Android设备的自动化测试方法，Appium iOS设备的操作方式与其类似。

## 5.1  Android基本介绍

在使用Appium进行Android设备的自动化之前，了解Android的一些基本知识有助于我们了解Appium工作的原理和定位问题。
Android是一个基于Linux内核的开源移动设备操作系统，最初由Andy Rubin于2003年10月创立，并于2005年被Google收购并于2007年11月，与84家硬件制造商、软件开发商及电信营运商组建开放手机联盟共同研发改良Android系统，并对其开源。
Android的开源极大的降低了手机厂商的准入门槛，设备厂商只需要安装指定的设备方案采购组装各种零部件，对Android系统进行定制和界面美化即可快速生产出流行的智能手机。厂商的疯狂涌入给Android带来了快速的发展，同时由于不同厂商设备尺寸、硬件架构、性能以及所定制内容的不同，也为Android带来严重的碎片化问题及对应用的兼容性挑战。

### 5.1.1  Android系统框架

Android系统本质是在Linux内核上使用Dalvik虚拟机来运行Java应用程序的一套框架。如图6.1所示，Android系统整个架构分为以下4层。

图6.1 Android 系统架构
#### 应用层
应用层主要是用户使用的各种应用，包括系统应用和各种三方应用。
#### 应用框架层
应用框架层包含了应用中所使用的一些组件及模块，主要组件及介绍如下：

- 包管理器（Package Manager）：提供App安装、卸载、清理数据等应用包操作。
- 活动管理器（Activity Manager）：启动、切换活动页面等，并提供返回和导航。
- 内容提供者（Content Provider）：为App提供数据，在不同App之间共享内容。
- 窗口管理器（Window Manager）：活动页面窗口（显示）管理。
- 视图系统（View System）：页面控件及布局管理。
- 资源管理器（Resource Manager）：程序使用的图片、布局文件等资源管理。
- 通知管理器（Notification Manager）：App通知栏提示信息管理。
- 通话管理器（Telephony Manager）：来电、去电及通话管理。
- 位置管理器（Location Manager）：定位及位置服务管理。
- XMPP服务（XMPP Service）：基于XML的一种实时消息服务。

#### 运行库
运行库包含Android运行的核心库，Dalvik虚拟机以及界面管理、以及一些由C、C++、Java等开发的为应用框架层提供支持的基础库，主要的库及介绍如下：

- 界面管理（Surface Manager）：负责界面显示是基础库。
- 多媒体框架（Media Framework）：多种格式的音视频编码解码，录制回放等。
- SQLite数据库（SQLite）：轻量型的关系型数据库。
- 字体库（FreeType）：点阵及向量字描绘及显示。
- 向量图形引擎（SGL）：2D绘图引擎。
- 图形处理库（OpenGL | ES）：根据Open GL ES标准实现的3D绘图引擎。
- 浏览器内核（Webkit）：为移动浏览器和嵌入式WebView提供支持。
- 会话加密库（SSL）：用于HTTPS通信握手及证书验证等。
- 标准C系统函数库（libc）：标准的C语言函数库，可用于操作底层Linux内核。

#### 内核层
内核层基于Linux2.6，为Android移动设备各种硬件提供底层驱动，如显示驱动、USB驱动、键盘驱动等。

### 5.1.2  Android基本组件及常见概念

Android包含以下4大基本组件。

- Activity：活动（页面）
- Service：后台服务
- Content Provider：内容提供者
- Broadcast Receiver：广播接收器

Android应用及调试中有以下常见概念。
1．Package
Package即包，一个包对应一个App应用。Android应用在构建并签名后会生成一个.apk的安装文件，可以进行分发和发布。每个包有在安装到设备后包名Package Name是App应用的唯一标志。
2．Activity
Activity及活动，一个Activity可以理解为一个页面，Activity可以调用其他Activity，同时一般一个应用有一个主Activity：Launchable Activity即App入口页。
3．Intent
Indent可以理解为调用，常用于一个Activity调用其他的Activity页面，Service后台服务或传递广播。
图6.2 Indent示意图
4．ADB
ADB即Android Debug Bridge安卓设备调试桥，用于PC端通过命令，连接并调试Android设备。ADB是一个非常强大的命令行工具，可以进入手机系统后台执行Linux命令，安装卸载应用，启动和操作App，上传下载文件、监控设备日志性能等等。

### 5.1.3  Android SDK简介

SDK即Software Development Kit，软件开发工具包，是被软件开发工程师用于为特定的软件包、软件框架、硬件平台、操作系统等建立应用软件的开发工具的集合。
Android SDK提供了Android API库和开发、构建、调试以及测试工具。Android SDK的目录如图6.2所示。
图6.2  Android SDK目录
各个目录的解释如下：

- add-ons：附加库。
- docs：参考文档。
- build-tools：编译及反编译工具，如aapt等。
- extras：扩展开发包。
- platforms：各版本代码
- tools：工具，如uiautomatorviewer等。
- platforms-tools：版本统统工具，如adb、sqlite3等。
- AVD Manager：Android Virtual Device虚拟机管理器，可以创建各种版本的模拟器，启动教慢。
- SDK Manager：SDK管理器，可以联网下载各个版本的Android API及相关工具。如果你的Android SDK中缺少相关的工具便可以在SDK Manager中下载。

### 5.1.4  Android SDK常用工具

- aapt：使用aapt获取apk包名及入口活动名
- adb：使用adb连接设备
- uiautomatorviewer：查看活动页面元素布局

### 5.1.5  Android常见布局和基本控件

Android中的元素分为ViewGroup视图组和View视图，ViewGroup负责组织层次分组关系，ViewGroup可以包含子的ViewGroup和View。View则负责具体显示。
图6.3  Android中的ViewGroup和View
#### 常见布局
布局属于ViewGroup视图组，用来表示其中子元素的排列方式，常见的布局方式有以下几种，如图6.4所示。
图6.4  Android中常见的布局
对应解释如下：

- LinnerLayout：线性布局，水平或垂直依次排列
- RelativeLayout：相对布局，根据上一个元素、布局的相对位置进行布局
- FrameLayout：帧布局，相同位置叠放布局
- TableLayout：表格布局
- GridLayout：网格布局
- ConstrainLayout：约束布局，如水平对齐等

在实际App中，为了达到期望的排列效果，布局往往是混合嵌套使用的，如图6.5所示。
6.5  使用Uiautomatorviewer查看设置中元素的布局
#### 基本控件类型
元素控件是我们主要的交互对象，Android中常见的控件有以下几种。

- android.widget.TextView：显示文字
- android.widget.EditText：输入框
- android.widget.ImageView：图片
- android.widget.Button：按钮
- android.widget.RadioButton：单选框
- android.widget.CheckBox：复选框
- android.widget.Spinner：旋钮
- android.widget.Switch：开关按钮
- android.widget.Alert：警告对话框
- android.widget.Toast：弹出提示消息
- android.widget.SeekBar：滑块
- android.widget.ListView：列表视图
- android.widget.ScrollView：滚动视图
- android.widget.RecyclerView：循环视图
- android.widget.WebView：网页视图

### 5.1.6  Android控件属性列表

元素使用XML进行布局的，每个元素都是一个node节点，每个节点都拥有固定格式的属性，如图6.6所示。

图6.6  使用adb shell uiautumator dump 导出的XML布局文件
每个节点的属性及说明如表6.1所示。
表6.1  Android元素属性

| 类型                                                                                                                                                               | 属性               | 说明           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------ |
| 标识                                                                                                                                                               | index            | 索引           |
| text                                                                                                                                                             | 显示文本             |              |
| resource-id                                                                                                                                                      | 资源id             |              |
| class                                                                                                                                                            | 控件类型(文本/按钮/输入框等) |              |
| content-desc                                                                                                                                                     | 控件描述             |              |
| package                                                                                                                                                          | 所属包(一个包就是一个apk)  |              |
| password                                                                                                                                                         | 是否密码框            |              |
| 可用性                                                                                                                                                              | checkable        | 是否可选中        |
| clickable                                                                                                                                                        | 是否可点击            |              |
| focusable                                                                                                                                                        | 是否可聚焦            |              |
| long-clickable                                                                                                                                                   | 是否可以长按           |              |
| 状态                                                                                                                                                               | enabled          | 是否可用         |
| checked                                                                                                                                                          | 是否已勾选            |              |
| focused                                                                                                                                                          | 是否聚焦状态           |              |
| selected                                                                                                                                                         | 是否已选中            |              |
| 坐标                                                                                                                                                               | bounds           | 元素左上角和右下角的坐标 |
 ## 5.2  ADB命令                                                                                                                                                                                    
 ADB（Android Debug Bridge)，即安卓调试桥，用于通过PC来连接和控制Android设备（设备需要开启“开发者模式”，并授权允许连接）。ADB功能非常强大，不仅可以安装、卸载APP，上传、下载文件，操作手机触控点击，输入文字，按键，滑动屏幕，截图等，还可以监控设备日志，性能，进行功能和稳定性测试等。                                 


### 5.2.1  ADB基础命令                                                                                                                                              
#### 启动、关闭服务                                                                                                                                                       
启动ADB服务的命令为：                                                                                                                                                                                  
```bash                                                                                                                                                                                   
 $ adb start-server                                                                                                                                                                        
 ```                                                                                                                                                                                       
 一般来说，在使用ADB连接或查看设备时会自动启动服务，本条命令一般不需要使用。                                                                                                                                                   
 关闭ADB服务的命令为：                                                                                                                                                                              
 ```bash                                                                                                                                                                                   
 $ adb kill-server                                                                                                                                                                         
 ```                                                                                                                                                                                       
 当ADB设备连接出现异常或想中断连接时，可以使用该命令关闭ADB服务，然后可以使用连接或查看设备命令重新启动服务。                                                                                                                                 
 $ adb root：以root权限重启adb服务                                                                                                                                                                 
#### 连接、查看设备状态、断开连接                                                                                                                                                                          
 Android虚拟机一般需要我们手动连接，夜神模拟器默认版本5.1.1默认绑定本机127.0.0.1的62001端口，我们可以使用以下命令连接夜神模拟器5.1.1。                                                                                                        
 ```bash                                                                                                                                                                                     
 $ adb connect 127.0.0.1:62001                                                                                                                                                                 
 ```                                                                                                                                                                                           
 当看到“connected：127.0.0.1:62001”的返回结果时，表明连接成功。                                                                                                                                                  
 断开连接命令：                                                                                                                                                                                       
 ```bash                                                                                                                                                                                       
 $ adb disconnect 127.0.0.1:62001                                                                                                                                                              
 ```                                                                                                                                                                                            
 我们可以通过adb devices命令查看设备连接状态：                                                                                                                                                                     
 ```bash                                                                                                                                                                                          
 $ adb devices                                                                                                                                                                                    
 ```                                                                                                                                                                                              
 通过 adb get-serialno获取设备和ID和序列号:                                                                                                                                                                  
 ```bash                                                                                                                                                                                          
 $ adb get-serialno                                                                                                                                                                               
 ```                                                                                                                                                                                              

#### 安装、卸载APP                                                                                                                                                                                       
 使用adb命令还可以安装和卸载apk应用，安装时需要指定应用apk安装包的路径，卸载时则需要指定应用的PackageName，即包名，安装和卸载命令如下：                                                                                                                    

- adb install apk路径，-r 保留数据和缓存重新安装，-s 安装到SD卡下
- adb uninstall 包名：卸载app，-k 保留app数据及缓存文件

#### 上传、下载文件
通过adb push和adb pull命令可以上传下载文件，使用方法如下：

- adb push 本地文件 设备目录：上传本地文档到设备相应目录
- adb pull 设备文件路径 本地目录：下载设备文件到本地
#### 重启设备
使用adb reboot命令可以重启设备，除了正常重启外，还可以重启到刷机模式或恢复模式，常用的命令如下：

- adb reboot：重启设备。
- adb reboot bootloader：重启到Bootloader，即刷机模式。
- adb reboot recovery：重启到Recovery，即恢复模式。

### 5.2.2  ADB Shell命令

由于Android是基于Linux内核的，因此ADB Shell支持大部分Linux Shell基础命令，如ls、cd、cat等，同时ADB Shell还具备Package包管理、Activity活动管理以及Input屏幕输入操作等。
#### 目录、文件操作
ADB Shell支持ls、cd、rm、mv等Linux基础命令，可以在adb shell中使用这些命令在设备中查看或修改设备中的目录及文件。常用命令如下：

- adb shell ls：列出所有文件及目录
- adb shell cd：进入目录
- adb shell rm：删除，rm -r 删除目录
- adb shell mv：移动或重命名文件及目录
- adb shell chmod：设置文件及目录权限
- adb shell touch：创建空文件
- adb shell mkdir：创建目录
- adb shell cat：查看文件内容

#### 获取设备信息
可以通过adb shell cat 指定系统文件或adb shell getprop命令来获取设备相应的信息，常用的命令如下：

- adb shell cat /sys/class/net/wlan0/address：获取设备MAC地址
- adb shell cat /proc/cpuinfo：获取CPU序列号
- adb shell cat /proc/memeinfo：查看当前内存占用
- adb shell cat /proc/iomem：查看IO内存分区
- adb shell getprop ro.build.version.release：获取Android系统版本

#### 查看进程及内存占用
同在Linux中一样，我们还可以使用top、ps等命令查看内存及进程状态，常用命令如下：

- adb shell top：查看设备CPU及内存占用情况，-m 6查看内存前6的APP，-n 1，刷新一次内存信息然后返回
- adb shell procrank：查询各进程内存使用情况
- adb shell ps：查看进程列表，-x 进程PID，查看指定进程状态
- adb shell kill 进程PID： 杀死一个进程
- adb shell service list：查看后台服务信息

#### 截图、录屏
使用adb shell screencap和adb shell screenrecord命令

- adb shell screencap /sdcard/1.png：截图
- adb shell screenrecord /sdcard/test01.mp4：录屏

录屏时支持使用以下参数，--size：录制尺寸，宽*高；--bit-rate：比特率，默认4000000，即4M；--time-limit: 录制时间；--verbose：显示详细信息；--bug-report：添加额外信息。按Ctrl+C停止录屏。

#### 打开关闭Wi-Fi
可以使用以下命令开启或禁用Wi-Fi。

- adb shell svc wifi enable：打开Wi-Fi。
- adb shell svc wifi disable：禁用Wi-Fi。

### 5.2.3  包管理命令

PM即Package Manage，包管理。除Linux基础名另外，ADB Shell还具备了额外的包管理、活动管理及屏幕输入操作等命令，使用adb shell pm相关命令可以查看、管理设备中安装的应用，例如：

- adb shell pm install：安装一个包。
- adb shell pm clear：清除某个包的数据。
- adb shell pm list package：显示所有的安装包，-f：显示相关文件；-d：显示禁用包；-e：显示启用包；-s：显示系统包；-3：显示三方包。

使用示例如下：

```bash
# 查看系统中所有三方包
$ adb shell list package -3
# 清除小米商城App数据
$ adb shell pm clear com.xiaomi.shop
# 查看所有权限
$ adb shell pm list permissions -s
# 对小米商城App授权获取位置权限
$ adb shell pm grant com.mishop.shop android.permission.ACCESS_FINE_LOCATION
```

PM常用命令如表6.1所示。
表 6.1  PM包管理相关命令

| 命令                                                             | 说明                    |
| -------------------------------------------------------------- | --------------------- |
| pm list packages                                               | 打印所有安装的包              |
| pm list permission-groups                                      | 打印全部已知权限组             |
| pm list permissions                                            | 打印所有已知权限，可选选项为        |
| pm list instrumentation                                        | 用于列出所有测试包             |
| pm list features                                               | 打印所有系统功能              |
| pm list libraries                                              | 打印所有系统库               |
| pm list users                                                  | 打印所有系统用户              |
| pm path                                                        | 打印指定包的.apk文件路径        |
| pm dump                                                        | 打印指定包相关的系统状态          |
| pm install                                                     | 安装单个应用                |
| pm install-create                                              | 创建一个安装会话              |
| pm install-write                                               | 将包写入已知安装会话            |
| pm install-commit                                              | 执行暂存的安装会话             |
| pm install-abandon                                             | 丢弃安装会话                |
| pm uninstall                                                   | 在系统中卸载指定包             |
| pm set-installer                                               | 设置包安装器名称              |
| pm move-package                                                | 移动包                   |
| pm move-primary-storage                                        | 移动主要存储                |
| pm clear                                                       | 清空包                   |
| pm enable                                                      | 启用包或组件                |
| pm disable                                                     | 禁用包或组件                |
| pm disable-user                                                | 禁用用户                  |
| pm disable-until-used                                          | 禁用包或组件直到使用            |
| pm hide                                                        | 隐藏包                   |
| pm unhide                                                      | 显示隐藏包                 |
| pm grant                                                       | 应用授权                  |
| pm revoke                                                      | 撤销授权                  |
| pm reset-permissions                                           | 重置所有权限                |
| pm set-app-link                                                | 设置app安装包连接            |
| pm get-app-link                                                | 获取app安装包连接            |
| pm set-install-location                                        | 设置应用默认安装位置            |
| pm get-install-location                                        | 获取应用默认安装位置            |
| pm set-permission-enforced                                     | 强制设置权限                |
| pm trim-caches                                                 | 消减缓存已到达指定可用空间大小       |
| pm create-user                                                 | 使用指定用户名创建新用户，打印新用户标识  |
| pm remove-user                                                 | 删除指定用户标识用户，删除用户所有关联数据 |
| pm get-max-users                                               | 获取最大用户数               |

### 5.2.4  活动管理命令                                                                     
 AM即Activity Manage，活动管理，使用adb shell am命令可以启动和操作App中的活动（页面），例如： |                       |

- adb shell am start -n：启动App指定Activity活动（页面）
- adb shell am start -a: 启动指定组件

使用示例如下：

```
# 启动并拨打电话
$ adb shell am start -a android.intent.action.CALL -d tel:10086

# 启动小米商城
$ adb shell am start -n com.xiaomi.shop/.activity.MainTabActivity
```

AM常用命令如表6.2所示。
表 6.2  AM活动管理相关命令

| 命令                  | 说明                                    |
| ------------------- | ------------------------------------- |
| am start            | 启动一个Activity                          |
| am startservice     | 启动一个服务                                |
| am stopservice      | 停止一个服务                                |
| am force-stop       | 杀死指定包关联所有进程                           |
| am kill             | 杀死指定进程                                |
| am kill-all         | 杀死所有后台进程                              |
| am broadcast        | 发送一个广播，需要接受者具备的权限                     |
| am instrument       | 启动一个Instrumentation                   |
| am profile start    | 在进程中启动一个性能衡量器                         |
| am profile stop     | 在进程中停止一个性能衡量器，`<PORCESS>`参数可以为进程名或PID |
| am dumpheap         | 显示一个进程的堆信息                            |
| am set-debug-app    | 设置App为调试模式                            |
| am clear-debug-app  | 清空对App调试模式的设置                         |
| am monitor          | 启动监控，监控应用Crash崩溃及ANR无响应               |
| am hang             | 挂起活动/服务                               |
| am restart          | 重启活动/服务                               |
| am screen-compat    | 开启或关闭指定App的屏幕兼容模式                     |
| am to-uri           | 将Intent打印为URI格式                       |
| am to-intent-uri    | 将Intent打印为标准的intent:URI格式             |
| am switch-user      | 切换用户                                  |
| am send-trim-memory | 向活动/服务发送收缩内存指令                        |

### 5.2.5  设备设置命令

adb shell settings相关命令可以用于获取、更新system系统、secure安全及global全局相关的设置，常用的命令有：

- adb shell settings list：获取系统、安全或所有设置项
- adb shell settings get：获取指定设置
- adb shell settings put：更新指定设置

一些常用示例如下：

```bash
# 获取亮度是否自动调节设置
$ adb shell settings get system screen brightness_mode
# 获取当前亮度值
$ adb shell settings get system screen_brightness
# 设置屏幕亮度
$ adb shell settings put system screen_brightness 150
# 获取屏幕休眠时间
$ adb shell settings get system screen_off_timeout
# 更改休眠时间为10分钟
$ adb shell settings put system screen_off_timeout 600000
# 设置输入法为Sogou拼音
$ adb shell settings put secure default_input_method：com.sohu.inputmenthod.sogouoem/.SogouIME
```

### 5.2.6  屏幕输入命令

使用adb shell input相关命令可以对设备进行点击、输入文字、模拟按键、滑动等操作，常用命令如下：

- adb shell input tap：触点指定坐标操作。
- adb shell input text：输入指定文本，仅支持ASCII码字符。
- adb shell input swipe：从起始点坐标滑动到终止点坐标。
- adb shell input keyevent：模拟按键，对应KeyCode如表6.3所示。

表6.3 ADB KeyCode对应值

| 分类                                                                                                                                                      | 按键               | 说明       | 值  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | -------- | -- |
| 电话键                                                                                                                                                     | KEYCODE_CALL     | 拨号键      | 5  |
| KEYCODE_ENDCALL                                                                                                                                         | 挂机键              | 6        |    |
| KEYCODE_HOME                                                                                                                                            | 按键Home           | 3        |    |
| KEYCODE_MENU                                                                                                                                            | 菜单键              | 82       |    |
| KEYCODE_BACK                                                                                                                                            | 返回键              | 4        |    |
| KEYCODE_SEARCH                                                                                                                                          | 搜索键              | 84       |    |
| KEYCODE_CAMERA                                                                                                                                          | 拍照键              | 27       |    |
| KEYCODE_FOCUS                                                                                                                                           | 拍照对焦键            | 80       |    |
| KEYCODE_POWER                                                                                                                                           | 电源键              | 26       |    |
| KEYCODE_NOTIFICATION                                                                                                                                    | 通知键              | 83       |    |
| KEYCODE_MUTE                                                                                                                                            | 话筒静音键            | 91       |    |
| KEYCODE_VOLUME_MUTE                                                                                                                                     | 扬声器静音键           | 164      |    |
| KEYCODE_VOLUME_UP                                                                                                                                       | 音量增加键            | 24       |    |
| KEYCODE_VOLUME_DOWN                                                                                                                                     | 音量减小键            | 25       |    |
| 控制键                                                                                                                                                     | KEYCODE_ENTER    | 回车键      | 66 |
| KEYCODE_ESCAPE                                                                                                                                          | ESC键             | 111      |    |
| KEYCODE_DPAD_CENTER                                                                                                                                     | 导航键 确定键          | 23       |    |
| KEYCODE_DPAD_UP                                                                                                                                         | 导航键 向上           | 19       |    |
| KEYCODE_DPAD_DOWN                                                                                                                                       | 导航键 向下           | 20       |    |
| KEYCODE_DPAD_LEFT                                                                                                                                       | 导航键 向左           | 21       |    |
| KEYCODE_DPAD_RIGHT                                                                                                                                      | 导航键 向右           | 22       |    |
| KEYCODE_MOVE_HOME                                                                                                                                       | 光标移动到开始键         | 122      |    |
| KEYCODE_MOVE_END                                                                                                                                        | 光标移动到末尾键         | 123      |    |
| KEYCODE_PAGE_UP                                                                                                                                         | 向上翻页键            | 92       |    |
| KEYCODE_PAGE_DOWN                                                                                                                                       | 向下翻页键            | 93       |    |
| KEYCODE_DEL                                                                                                                                             | 退格键              | 67       |    |
| KEYCODE_FORWARD_DEL                                                                                                                                     | 删除键              | 112      |    |
| KEYCODE_INSERT                                                                                                                                          | 插入键              | 124      |    |
| KEYCODE_TAB                                                                                                                                             | Tab键             | 61       |    |
| KEYCODE_NUM_LOCK                                                                                                                                        | 小键盘锁             | 143      |    |
| KEYCODE_CAPS_LOCK                                                                                                                                       | 大写锁定键            | 115      |    |
| KEYCODE_BREAK                                                                                                                                           | Break/Pause键     | 121      |    |
| KEYCODE_SCROLL_LOCK                                                                                                                                     | 滚动锁定键            | 116      |    |
| KEYCODE_ZOOM_IN                                                                                                                                         | 放大键              | 168      |    |
| KEYCODE_ZOOM_OUT                                                                                                                                        | 缩小键              | 169      |    |
| 组合键                                                                                                                                                     | KEYCODE_ALT_LEFT | Alt+Left |    |
| KEYCODE_ALT_RIGHT                                                                                                                                       | Alt+Right        |          |    |
| KEYCODE_CTRL_LEFT                                                                                                                                       | Control+Left     |          |    |
| KEYCODE_CTRL_RIGHT                                                                                                                                      | Control+Right    |          |    |
| KEYCODE_SHIFT_LEFT                                                                                                                                      | Shift+Left       |          |    |
| KEYCODE_SHIFT_RIGHT                                                                                                                                     | Shift+Right      |          |    |
| 基本                                                                                                                                                      | KEYCODE_0        | 按键'0'    | 7  |
| KEYCODE_1                                                                                                                                               | 按键'1'            | 8        |    |
| KEYCODE_2                                                                                                                                               | 按键'2'            | 9        |    |
| KEYCODE_3                                                                                                                                               | 按键'3'            | 10       |    |
| KEYCODE_4                                                                                                                                               | 按键'4'            | 11       |    |
| KEYCODE_5                                                                                                                                               | 按键'5'            | 12       |    |
| KEYCODE_6                                                                                                                                               | 按键'6'            | 13       |    |
| KEYCODE_7                                                                                                                                               | 按键'7'            | 14       |    |
| KEYCODE_8                                                                                                                                               | 按键'8'            | 15       |    |
| KEYCODE_9                                                                                                                                               | 按键'9'            | 16       |    |
| KEYCODE_A                                                                                                                                               | 按键'A'            | 29       |    |
| KEYCODE_B                                                                                                                                               | 按键'B'            | 30       |    |
| KEYCODE_C                                                                                                                                               | 按键'C'            | 31       |    |
| KEYCODE_D                                                                                                                                               | 按键'D'            | 32       |    |
| KEYCODE_E                                                                                                                                               | 按键'E'            | 33       |    |
| KEYCODE_F                                                                                                                                               | 按键'F'            | 34       |    |
| KEYCODE_G                                                                                                                                               | 按键'G'            | 35       |    |
| KEYCODE_H                                                                                                                                               | 按键'H'            | 36       |    |
| KEYCODE_I                                                                                                                                               | 按键'I'            | 37       |    |
| KEYCODE_J                                                                                                                                               | 按键'J'            | 38       |    |
| KEYCODE_K                                                                                                                                               | 按键'K'            | 39       |    |
| KEYCODE_L                                                                                                                                               | 按键'L'            | 40       |    |
| KEYCODE_M                                                                                                                                               | 按键'M'            | 41       |    |
| KEYCODE_N                                                                                                                                               | 按键'N'            | 42       |    |
| KEYCODE_O                                                                                                                                               | 按键'O'            | 43       |    |
| KEYCODE_P                                                                                                                                               | 按键'P'            | 44       |    |
| KEYCODE_Q                                                                                                                                               | 按键'Q'            | 45       |    |
| KEYCODE_R                                                                                                                                               | 按键'R'            | 46       |    |
| KEYCODE_S                                                                                                                                               | 按键'S'            | 47       |    |
| KEYCODE_T                                                                                                                                               | 按键'T'            | 48       |    |
| KEYCODE_U                                                                                                                                               | 按键'U'            | 49       |    |
| KEYCODE_V                                                                                                                                               | 按键'V'            | 50       |    |
| KEYCODE_W                                                                                                                                               | 按键'W'            | 51       |    |
| KEYCODE_X                                                                                                                                               | 按键'X'            | 52       |    |
| KEYCODE_Y                                                                                                                                               | 按键'Y'            | 53       |    |
| KEYCODE_Z                                                                                                                                               | 按键'Z'            | 54       |    |


使用adb命令操作高仿微信App登录的示例如下：          
 我们的操作流程为：                                                                                                                                                                               
  1. 使用坐标点击启动页登录按钮                                                                                                                                                                        
  2. 在登录页默认手机号框输入账号                                                                                                                                                                       
  3. 按Tab键切换到密码输入框                                                                                                                                                                        
  4. 输入密码                                                                                                                                                                                 
  5. 使用坐标点击登录页的登录按钮                                                                                                                                                                       
  6. 左滑到联系人界面                                                                                                                                                                             
 如图6.7和6.8所示，我们可以通过uiautomatorviewer工具通过查看按钮的bounds属性（左上角和右下角坐标）来计算出按钮的中心坐标，如启动页登录按钮中心点坐标为x=(40+300)/2=170，y=(1153+1241)/2=1197。登录页登录按钮中心坐标为x=360,y=496。                                 

图6.7  高仿微信App启动页                                                                                                                                                                        

图6.7  高仿微信App登录页
完整操作命令如下：

```bash
# 重置高仿微信App
$ adb shell pm clear com.lqr.wechat
# 点击启动页登录按钮
$ adb shell input tap 170 1197
# 输入手机号
$ adb shell input text 18010108****
# 按Tab键
$ adb shell input keyevent 61
# 输入密码
$ adb shell input text 123456
# 点击登录页登录按钮
$ adb shell input tap 360 496
# 从屏幕中间左滑
$ adb shell input swipe 700 720 20 720
```

在滑动屏幕是，需要确定起始点和终止点的坐标，假设模拟器的分辨率为1280*720，从屏幕正中间划过，则两个点的y坐标都是屏幕高度的一半，即1280/2=720。
起始点我们从距离屏幕右边界20像素开始滑动，即起始点x坐标为720-20=700。终止点我们以距离做边界20像素结束，即终止点的x坐标值为20。得到起始点，终止点的坐标分别为(700，720)、(20，720)。

## 5.3  Monkey使用基础

Monkey是Android SDK自带的一款 命令行工具，用于测试App的稳定性。它通过快速执行海量的点击、滑动等随机操作，来测试App是否会出现崩溃、无响应等问题。

### 5.3.1  Monkey基本使用

Monkey通过在命令行adb shell monkey命令使用，可以通过-p参数指定要操作的应用的包名，-v指定显示日志级别，最后指定操作次数，如：

```bash
$ adb shell monkey -p com.lqr.wechat -v 5000
```

Monkey常用参数如表6.4所示。
表6.3 Monkey命令参数

| 分类                                                                                                                                                                                 | 参数                              | 说明                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 约束                                                                                                                                                                                 | -p <包名>                         | 限定操作的App，可以使用多个-p指定多个包。                                                                                             |
| -c <分类名>                                                                                                                                                                           | 限定操作的App类型，可以使用多个-c指定多个分类。      |                                                                                                                     |
| 日志                                                                                                                                                                                 | -v                              | 指定日志级别。  <br>-v：默认，只显示启动通知、测试完成和最终结果；  <br>-vv：显示较详细日志, 包含每个Activity事件信息；  <br>-vvv：显示最详细的日志，包含测试中选中/未选中的Activity信息 |
| 事件                                                                                                                                                                                 | -s <事件序列号>                      | 使用之前的时间序列号，以相同的事件序列运行，常用于复现问题。                                                                                      |
| --throttle <毫秒>                                                                                                                                                                    | 指定指令间隔时间。                       |                                                                                                                     |
| --pct-touch <百分比>                                                                                                                                                                  | 调整触摸事件（点击、长按等）的百分比。             |                                                                                                                     |
| --pct-motion <百分比>                                                                                                                                                                 | 调整运动事件（滑动等）的百分比。                |                                                                                                                     |
| --pct-trackball <百分比>                                                                                                                                                              | 调整轨迹球事件(移动光标)的百分比。              |                                                                                                                     |
| --pct-nav <百分比>                                                                                                                                                                    | 调整基本导航事件（上、下、左、右）的百分比。          |                                                                                                                     |
| --pct-majornav <百分比>                                                                                                                                                               | 调整主要导航事件（主页、后台、菜单）的百分比。         |                                                                                                                     |
| --pct-syskeys <百分比>                                                                                                                                                                | 调整系统关键事件的（电话、音量等）百分比。           |                                                                                                                     |
| --pct-appswitch <百分比>                                                                                                                                                              | 调整活动启动的百分比。                     |                                                                                                                     |
| --pct-anyevent <百分比>                                                                                                                                                               | 调整其他类型事件（如按键等）的百分比。             |                                                                                                                     |
| 调试                                                                                                                                                                                 | --dbg-no-events                 | 执行打开初始程序，不再进行下一步操作                                                                                                  |
| --hprof                                                                                                                                                                            | 将在Monkey生成事件序列前后生成profilling报告。 |                                                                                                                     |
| --ignore-crashes                                                                                                                                                                   | 忽略崩溃事件                          |                                                                                                                     |
| --ignore-timeouts                                                                                                                                                                  | 忽略超时事件                          |                                                                                                                     |
| --ignore-security-exceptions                                                                                                                                                       | 忽略证书或认证异常                       |                                                                                                                     |
| --ignore-native-crashes                                                                                                                                                            | 忽略本地代码崩溃事件                      |                                                                                                                     |
| --kill-process-after-error                                                                                                                                                         | 发生错误后杀死进程                       |                                                                                                                     |
| --monitor-native-crashes                                                                                                                                                           | 监控并报告本地代码崩溃事件                   |                                                                                                                     |


使用示例，在模拟器上手工登录高仿微信后，在命令行运行一下命令：                                                                                                                                                                                                                                                                                                          
 ```                                                                                                                                                                                                                                                                                                                                    
adb shell monkey -p com.lqr.wechat -vvv --throttle 200 --ignore-timeouts --ignore-crashes --pct-touch 20 1000000 > monkey.log                                                                                                                                                                                                          
```                                                                                                                                                                                                                                                                                                                                    
上例中对高仿微信进行稳定性测试，指定日志级别为最详细模式，忽略崩溃事件和超时事件，并指定触控事件占比20%，间隔时间200毫秒，共运行100万次，运行结果输出到monkey.log文件中。                                                                                                                                                                                                                                         
### 5.3.2  手动停止Monkey                                                                                                                                                                                                                                                                                                                  
想要中断Monkey运行,除了在命令行Ctrl+C结束掉当前命令外,还要进入到adb shell，手动杀掉手机中的Monkey进程，操作命令如下：                                                                                                                                                                                                                                                              
```bash                                                                                                                                                                                                                                                                                                                                
$ adb shell monkey -p com.lqr.wechat -vvv --throttle 200 --ignore-timeouts --ignore-crashes --pct-touch 20 1000000 > monkey.log                                                                                                                                                                                                        
^C                                                                                                                                                                                                                                                                                                                                     
$ adb shell                                                                                                                                                                                                                                                                                                                            
LIO-AN00:/ # ps                                                                                                                                                                    grep monkey                                                                                                                                         
root      3116  1764  817916 83740            b766ecc0 S com.android.commands.monkey                                                                                                                                                                                                                                                   
LIO-AN00:/ # kill 3116                                                                                                                                                                                                                                                                                                                 
```                                                                                                                                                                                                                                                                                                                                    
### 5.3.3  Monkey日志分析                                                                                                                                                                                                                                                                                                                  
运行Monkey只需要一行命令，运行完的分析才是最重要的。                                                                                                                                                                                                                                                                                                          
1．Monkey日志基本格式                                                                                                                                                                                                                                                                                                                         
打开生成的monkey.log文件，可以看到内容如下：                                                                                                                                                                                                                                                                                                             
 ```                                                                                                                                                                                                                                                                                                                                      
 :Monkey: seed=1609999764904 count=1000000                                                                                                                                                                                                                                                                                                
 :AllowPackage: com.lqr.wechat                                                                                                                                                                                                                                                                                                            
 :IncludeCategory: android.intent.category.LAUNCHER                                                                                                                                                                                                                                                                                       
 :IncludeCategory: android.intent.category.MONKEY                                                                                                                                                                                                                                                                                         
 // Event percentages:                                                                                                                                                                                                                                                                                                                    
 //   0: 20.0%                                                                                                                                                                                                                                                                                                                            
 //   1: 9.411765%                                                                                                                                                                                                                                                                                                                        
 //   2: 1.882353%                                                                                                                                                                                                                                                                                                                        
 //   3: 14.117647%                                                                                                                                                                                                                                                                                                                       
 //   4: -0.0%                                                                                                                                                                                                                                                                                                                            
 //   5: -0.0%                                                                                                                                                                                                                                                                                                                            
 //   6: 23.529411%                                                                                                                                                                                                                                                                                                                       
 //   7: 14.117647%                                                                                                                                                                                                                                                                                                                       
 //   8: 1.882353%                                                                                                                                                                                                                                                                                                                        
 //   9: 1.882353%                                                                                                                                                                                                                                                                                                                        
 //   10: 0.9411765%                                                                                                                                                                                                                                                                                                                      
 //   11: 12.235294%                                                                                                                                                                                                                                                                                                                      
 :Switch: #Intent;action=android.intent.action.MAIN;category=android.intent.category.LAUNCHER;launchFlags=0x10200000;component=com.lqr.wechat/.ui.activity.SplashActivity;end                                                                                                                                                             
     // Allowing start of Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.lqr.wechat/.ui.activity.SplashActivity } in package com.lqr.wechat                                                                                                                                                       
 :Sending Touch (ACTION_DOWN): 0:(413.0,395.0)                                                                                                                                                                                                                                                                                            
 :Sending Touch (ACTION_UP): 0:(420.77084,326.283)                                                                                                                                                                                                                                                                                        
 ...                                                                                                                                                                                                                                                                                                                                      
 Events injected: 1000000                                                                                                                                                                                                                                                                                                                 
 :Sending rotation degree=0, persist=false                                                                                                                                                                                                                                                                                                
 :Dropped: keys=0 pointers=0 trackballs=0 flips=0 rotations=0                                                                                                                                                                                                                                                                             
 ## Network stats: elapsed time=58532ms (0ms mobile, 0ms wifi, 58532ms not connected)                                                                                                                                                                                                                                                     
 // Monkey finished                                                                                                                                                                                                                                                                                                                       
 ```                                                                                                                                                                                                                                                                                                                                      
 第1行表示种子（事件序列号）为1609999764904，随机事件总数为1000000。                                                                                                                                                                                                                                                                                             
 第2-4行为限定的包及应用分类。                                                                                                                                                                                                                                                                                                                         
 第5-17行为各种事件的百分比。                                                                                                                                                                                                                                                                                                                         
 第18行到倒数第6行为切换到指定Activity及发送的各种事件及具体信息。                                                                                                                                                                                                                                                                                                   
 倒数第5-倒数第2行为运行统计信息。                                                                                                                                                                                                                                                                                                                       
 最后一行表示Monkey运行结束。                                                                                                                                                                                                                                                                                                                        

#### 测试结果分析
运行完怎么知道有没有问题呢，Android常见的问题有CRASH崩溃、ANR无响应以及Exception运行异常等，我们可以再Monkey运行日志中搜索CRASH、ANR及Exception字段。查看App在运行过程中是否出现了崩溃、无响应和应用程序异常等问题。
常见引起App崩溃的原因一般有设备兼容性、网络原因、内存泄露和程序逻辑错误（如数组越界、堆栈溢出、并发访问、逻辑出错）等。
常见引起App无响应一般是主线程执行了耗时操作（如硬件、网络、数据库等大量读写操作）或其他线程崩溃或持有锁导致主线程等待超时。
搜索到CRASH、ANR或Exception时，可以根据详细的异常信息进行分析。或者向上查找Switch看异常发生在哪个Activity活动中，以及发生异常的操作，可以尝试手工打开Activity指定页面，模拟指定操作看是否能复现问题，或重新运行Monkey使用-s 1609999764904来尝试复现问题。
