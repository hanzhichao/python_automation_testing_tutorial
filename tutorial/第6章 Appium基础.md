
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
1．应用层
应用层主要是用户使用的各种应用，包括系统应用和各种三方应用。
2．应用框架层
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
3．运行库
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
4．内核层
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
1．常见布局
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
2．基本控件类型
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

| 类型 | 属性 | 说明 |
| ---- | ---- | ---- |
| 标识 | index | 索引 |
| text | 显示文本 |  |
| resource-id | 资源id |  |
| class | 控件类型(文本/按钮/输入框等) |  |
| content-desc | 控件描述 |  |
| package | 所属包(一个包就是一个apk) |  |
| password | 是否密码框 |  |
| 可用性 | checkable | 是否可选中 |
| clickable | 是否可点击 |  |
| focusable | 是否可聚焦 |  |
| long-clickable | 是否可以长按 |  |
| 状态 | enabled | 是否可用 |
| checked | 是否已勾选 |  |
| focused | 是否聚焦状态 |  |
| selected | 是否已选中 |  |
| 坐标 | bounds | 元素左上角和右下角的坐标 |
## 5.2  ADB命令
ADB（Android Debug Bridge)，即安卓调试桥，用于通过PC来连接和控制Android设备（设备需要开启“开发者模式”，并授权允许连接）。ADB功能非常强大，不仅可以安装、卸载APP，上传、下载文件，操作手机触控点击，输入文字，按键，滑动屏幕，截图等，还可以监控设备日志，性能，进行功能和稳定性测试等。
### 5.2.1  ADB基础命令
1．启动、关闭服务
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
2．连接、查看设备状态、断开连接
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
3．安装、卸载APP
使用adb命令还可以安装和卸载apk应用，安装时需要指定应用apk安装包的路径，卸载时则需要指定应用的PackageName，即包名，安装和卸载命令如下：
- adb install apk路径，-r 保留数据和缓存重新安装，-s 安装到SD卡下
- adb uninstall 包名：卸载app，-k 保留app数据及缓存文件
4．上传、下载文件
通过adb push和adb pull命令可以上传下载文件，使用方法如下：
- adb push 本地文件 设备目录：上传本地文档到设备相应目录
- adb pull 设备文件路径 本地目录：下载设备文件到本地
5．重启设备
使用adb reboot命令可以重启设备，除了正常重启外，还可以重启到刷机模式或恢复模式，常用的命令如下：
- adb reboot：重启设备。
- adb reboot bootloader：重启到Bootloader，即刷机模式。
- adb reboot recovery：重启到Recovery，即恢复模式。
### 5.2.2  ADB Shell命令
由于Android是基于Linux内核的，因此ADB Shell支持大部分Linux Shell基础命令，如ls、cd、cat等，同时ADB Shell还具备Package包管理、Activity活动管理以及Input屏幕输入操作等。
1．目录、文件操作
ADB Shell支持ls、cd、rm、mv等Linux基础命令，可以在adb shell中使用这些命令在设备中查看或修改设备中的目录及文件。常用命令如下：
- adb shell ls：列出所有文件及目录
- adb shell cd：进入目录
- adb shell rm：删除，rm -r 删除目录
- adb shell mv：移动或重命名文件及目录
- adb shell chmod：设置文件及目录权限
- adb shell touch：创建空文件
- adb shell mkdir：创建目录
- adb shell cat：查看文件内容
2．获取设备信息
可以通过adb shell cat 指定系统文件或adb shell getprop命令来获取设备相应的信息，常用的命令如下：
- adb shell cat /sys/class/net/wlan0/address：获取设备MAC地址
- adb shell cat /proc/cpuinfo：获取CPU序列号
- adb shell cat /proc/memeinfo：查看当前内存占用
- adb shell cat /proc/iomem：查看IO内存分区
- adb shell getprop ro.build.version.release：获取Android系统版本
3.    查看进程及内存占用
同在Linux中一样，我们还可以使用top、ps等命令查看内存及进程状态，常用命令如下：
- adb shell top：查看设备CPU及内存占用情况，-m 6查看内存前6的APP，-n 1，刷新一次内存信息然后返回
- adb shell procrank：查询各进程内存使用情况
- adb shell ps：查看进程列表，-x 进程PID，查看指定进程状态
- adb shell kill 进程PID： 杀死一个进程
- adb shell service list：查看后台服务信息
4.截图、录屏
使用adb shell screencap和adb shell screenrecord命令
- adb shell screencap /sdcard/1.png：截图
- adb shell screenrecord /sdcard/test01.mp4：录屏
录屏时支持使用以下参数，--size：录制尺寸，宽*高；--bit-rate：比特率，默认4000000，即4M；--time-limit: 录制时间；--verbose：显示详细信息；--bug-report：添加额外信息。按Ctrl+C停止录屏。
4.    打开关闭Wi-Fi
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

|命令|说明|
|---|---|
|pm list packages|打印所有安装的包|
|pm list permission-groups|打印全部已知权限组|
|pm list permissions|打印所有已知权限，可选选项为|
|pm list instrumentation|用于列出所有测试包|
|pm list features|打印所有系统功能|
|pm list libraries|打印所有系统库|
|pm list users|打印所有系统用户|
|pm path|打印指定包的.apk文件路径|
|pm dump|打印指定包相关的系统状态|
|pm install|安装单个应用|
|pm install-create|创建一个安装会话|
|pm install-write|将包写入已知安装会话|
|pm install-commit|执行暂存的安装会话|
|pm install-abandon|丢弃安装会话|
|pm uninstall|在系统中卸载指定包|
|pm set-installer|设置包安装器名称|
|pm move-package|移动包|
|pm move-primary-storage|移动主要存储|
|pm clear|清空包|
|pm enable|启用包或组件|
|pm disable|禁用包或组件|
|pm disable-user|禁用用户|
|pm disable-until-used|禁用包或组件直到使用|
|pm hide|隐藏包|
|pm unhide|显示隐藏包|
|pm grant|应用授权|
|pm revoke|撤销授权|
|pm reset-permissions|重置所有权限|
|pm set-app-link|设置app安装包连接|
|pm get-app-link|获取app安装包连接|
|pm set-install-location|设置应用默认安装位置|
|pm get-install-location|获取应用默认安装位置|
|pm set-permission-enforced|强制设置权限|
|pm trim-caches|消减缓存已到达指定可用空间大小|
|pm create-user|使用指定用户名创建新用户，打印新用户标识|
|pm remove-user|删除指定用户标识用户，删除用户所有关联数据|
|pm get-max-users|获取最大用户数|
### 5.2.4  活动管理命令
AM即Activity Manage，活动管理，使用adb shell am命令可以启动和操作App中的活动（页面），例如：
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

|命令|说明|
|---|---|
|am start|启动一个Activity|
|am startservice|启动一个服务|
|am stopservice|停止一个服务|
|am force-stop|杀死指定包关联所有进程|
|am kill|杀死指定进程|
|am kill-all|杀死所有后台进程|
|am broadcast|发送一个广播，需要接受者具备的权限|
|am instrument|启动一个Instrumentation|
|am profile start|在进程中启动一个性能衡量器|
|am profile stop|在进程中停止一个性能衡量器，`<PORCESS>`参数可以为进程名或PID|
|am dumpheap|显示一个进程的堆信息|
|am set-debug-app|设置App为调试模式|
|am clear-debug-app|清空对App调试模式的设置|
|am monitor|启动监控，监控应用Crash崩溃及ANR无响应|
|am hang|挂起活动/服务|
|am restart|重启活动/服务|
|am screen-compat|开启或关闭指定App的屏幕兼容模式|
|am to-uri|将Intent打印为URI格式|
|am to-intent-uri|将Intent打印为标准的intent:URI格式|
|am switch-user|切换用户|
|am send-trim-memory|向活动/服务发送收缩内存指令|

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

|分类|按键|说明|值|
|---|---|---|---|
|电话键|KEYCODE_CALL|拨号键|5|
|KEYCODE_ENDCALL|挂机键|6|
|KEYCODE_HOME|按键Home|3|
|KEYCODE_MENU|菜单键|82|
|KEYCODE_BACK|返回键|4|
|KEYCODE_SEARCH|搜索键|84|
|KEYCODE_CAMERA|拍照键|27|
|KEYCODE_FOCUS|拍照对焦键|80|
|KEYCODE_POWER|电源键|26|
|KEYCODE_NOTIFICATION|通知键|83|
|KEYCODE_MUTE|话筒静音键|91|
|KEYCODE_VOLUME_MUTE|扬声器静音键|164|
|KEYCODE_VOLUME_UP|音量增加键|24|
|KEYCODE_VOLUME_DOWN|音量减小键|25|
|控制键|KEYCODE_ENTER|回车键|66|
|KEYCODE_ESCAPE|ESC键|111|
|KEYCODE_DPAD_CENTER|导航键 确定键|23|
|KEYCODE_DPAD_UP|导航键 向上|19|
|KEYCODE_DPAD_DOWN|导航键 向下|20|
|KEYCODE_DPAD_LEFT|导航键 向左|21|
|KEYCODE_DPAD_RIGHT|导航键 向右|22|
|KEYCODE_MOVE_HOME|光标移动到开始键|122|
|KEYCODE_MOVE_END|光标移动到末尾键|123|
|KEYCODE_PAGE_UP|向上翻页键|92|
|KEYCODE_PAGE_DOWN|向下翻页键|93|
|KEYCODE_DEL|退格键|67|
|KEYCODE_FORWARD_DEL|删除键|112|
|KEYCODE_INSERT|插入键|124|
|KEYCODE_TAB|Tab键|61|
|KEYCODE_NUM_LOCK|小键盘锁|143|
|KEYCODE_CAPS_LOCK|大写锁定键|115|
|KEYCODE_BREAK|Break/Pause键|121|
|KEYCODE_SCROLL_LOCK|滚动锁定键|116|
|KEYCODE_ZOOM_IN|放大键|168|
|KEYCODE_ZOOM_OUT|缩小键|169|
|组合键|KEYCODE_ALT_LEFT|Alt+Left||
|KEYCODE_ALT_RIGHT|Alt+Right||
|KEYCODE_CTRL_LEFT|Control+Left||
|KEYCODE_CTRL_RIGHT|Control+Right||
|KEYCODE_SHIFT_LEFT|Shift+Left||
|KEYCODE_SHIFT_RIGHT|Shift+Right||
|基本|KEYCODE_0|按键'0'|7|
|KEYCODE_1|按键'1'|8|
|KEYCODE_2|按键'2'|9|
|KEYCODE_3|按键'3'|10|
|KEYCODE_4|按键'4'|11|
|KEYCODE_5|按键'5'|12|
|KEYCODE_6|按键'6'|13|
|KEYCODE_7|按键'7'|14|
|KEYCODE_8|按键'8'|15|
|KEYCODE_9|按键'9'|16|
|KEYCODE_A|按键'A'|29|
|KEYCODE_B|按键'B'|30|
|KEYCODE_C|按键'C'|31|
|KEYCODE_D|按键'D'|32|
|KEYCODE_E|按键'E'|33|
|KEYCODE_F|按键'F'|34|
|KEYCODE_G|按键'G'|35|
|KEYCODE_H|按键'H'|36|
|KEYCODE_I|按键'I'|37|
|KEYCODE_J|按键'J'|38|
|KEYCODE_K|按键'K'|39|
|KEYCODE_L|按键'L'|40|
|KEYCODE_M|按键'M'|41|
|KEYCODE_N|按键'N'|42|
|KEYCODE_O|按键'O'|43|
|KEYCODE_P|按键'P'|44|
|KEYCODE_Q|按键'Q'|45|
|KEYCODE_R|按键'R'|46|
|KEYCODE_S|按键'S'|47|
|KEYCODE_T|按键'T'|48|
|KEYCODE_U|按键'U'|49|
|KEYCODE_V|按键'V'|50|
|KEYCODE_W|按键'W'|51|
|KEYCODE_X|按键'X'|52|
|KEYCODE_Y|按键'Y'|53|
|KEYCODE_Z|按键'Z'|54|
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
在滑动屏幕是，需要确定起始点和终止点的坐标，假设模拟器的分辨率为1280\*720，从屏幕正中间划过，则两个点的y坐标都是屏幕高度的一半，即1280/2=720。
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

| 分类 | 参数 | 说明 |
| ---- | ---- | ---- |
| 约束 | -p <包名> | 限定操作的App，可以使用多个-p指定多个包。 |
| -c <分类名> | 限定操作的App类型，可以使用多个-c指定多个分类。 |  |
| 日志 | -v | 指定日志级别。  <br>-v：默认，只显示启动通知、测试完成和最终结果；  <br>-vv：显示较详细日志, 包含每个Activity事件信息；  <br>-vvv：显示最详细的日志，包含测试中选中/未选中的Activity信息 |
| 事件 | -s <事件序列号> | 使用之前的时间序列号，以相同的事件序列运行，常用于复现问题。 |
| --throttle <毫秒> | 指定指令间隔时间。 |  |
| --pct-touch <百分比> | 调整触摸事件（点击、长按等）的百分比。 |  |
| --pct-motion <百分比> | 调整运动事件（滑动等）的百分比。 |  |
| --pct-trackball <百分比> | 调整轨迹球事件(移动光标)的百分比。 |  |
| --pct-nav <百分比> | 调整基本导航事件（上、下、左、右）的百分比。 |  |
| --pct-majornav <百分比> | 调整主要导航事件（主页、后台、菜单）的百分比。 |  |
| --pct-syskeys <百分比> | 调整系统关键事件的（电话、音量等）百分比。 |  |
| --pct-appswitch <百分比> | 调整活动启动的百分比。 |  |
| --pct-anyevent <百分比> | 调整其他类型事件（如按键等）的百分比。 |  |
| 调试 | --dbg-no-events | 执行打开初始程序，不再进行下一步操作 |
| --hprof | 将在Monkey生成事件序列前后生成profilling报告。 |  |
| --ignore-crashes | 忽略崩溃事件 |  |
| --ignore-timeouts | 忽略超时事件 |  |
| --ignore-security-exceptions | 忽略证书或认证异常 |  |
| --ignore-native-crashes | 忽略本地代码崩溃事件 |  |
| --kill-process-after-error | 发生错误后杀死进程 |  |
| --monitor-native-crashes | 监控并报告本地代码崩溃事件 |  |
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
LIO-AN00:/ # ps | grep monkey
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

2．测试结果分析
运行完怎么知道有没有问题呢，Android常见的问题有CRASH崩溃、ANR无响应以及Exception运行异常等，我们可以再Monkey运行日志中搜索CRASH、ANR及Exception字段。查看App在运行过程中是否出现了崩溃、无响应和应用程序异常等问题。
常见引起App崩溃的原因一般有设备兼容性、网络原因、内存泄露和程序逻辑错误（如数组越界、堆栈溢出、并发访问、逻辑出错）等。
常见引起App无响应一般是主线程执行了耗时操作（如硬件、网络、数据库等大量读写操作）或其他线程崩溃或持有锁导致主线程等待超时。
搜索到CRASH、ANR或Exception时，可以根据详细的异常信息进行分析。或者向上查找Switch看异常发生在哪个Activity活动中，以及发生异常的操作，可以尝试手工打开Activity指定页面，模拟指定操作看是否能复现问题，或重新运行Monkey使用-s 1609999764904来尝试复现问题。
## 5.4  Appium简介及环境搭建
Appium是一款开源的App自动化测试框架。它既可以测试原生（Native）应用，又可以测试混合（Hybrid）和移动Web（如H5，小程序等）应用程序。Appium使用WebDriver协议兼容了iOS、Android以及Windows移动应用。
Appium测试App并不需要SDK或重新编译应用，在开发语言上，Appium支持Python、JavaScript、Java、PHP、Ruby、C#等主流语言进行开发。Appium继承了Selenium WebDriver的API和使用方式，对于有Selenium基础的人，Appium脚本编写会非常容易上手。
Appium的主要特点包括：
- 开源免费
- 跨平台：支持iOS、Android、H5、混合App、Win App
- 跨语言：支持Win、Linux、Mac，支持Java、Python、Ruby等各种语言
### 5.4.1  App自动化测试工具对比
结合目前比较流行的一些App自动化测试工具，从支持系统、测试类型、支持语言、是否支持H5、是否可跨应用、工具的稳定性及是否开源几个方面进行衡量，如表5.1所示：
表 5.1  App常见自动化测试工具对比

|名称|支持系统|测试类型|支持语言|H5|跨应用|
|---|---|---|---|---|---|
|Monkey Runner|Android|功能|Python|支持|否|
|Monkey|Android|稳定性|Java|否|否|
|UiAutomator|Android|功能|Java|支持|支持|
|Espresso|Android|功能、白盒|Java|支持|支持|
|UiAutomation|iOS|功能|JavaScript|支持|支持|
|Selendroid|Android|功能|Java、Python等|支持|支持|
|UiAutomator2|Android|功能|Python|支持|支持|
|Calabash|Android、iOS|功能|Ruby|支持|支持|
|Appium|Android、iOS|功能|任意语言|支持|支持|
|Macaca|Android、iOS|功能|任意语言|支持|支持|
|Airtest|Android、iOS|功能|Python|支持|支持|
|Soloπ|Android、iOS|性能|-|否|否|
|Maxim|iOS|稳定性|-|支持|支持|
根据对比我们可以看出，Appium虽然相比Android SDK和XCode自带的工具稳定性一般，但是胜在跨平台，支持原生、混合、H5各种应用类型，支持跨应该，而且支持几乎各种主流开发语言。学习和使用Appium作为App测试工具是一个不错的选择。
### 5.4.2  Appium基本原理
在学习Appium之前，我们要先了解下Appium的工作原理。Appium是基于客户端/服务器的模式的，工作原理和Selenium非常相似，使用一套HTTP接口服务来在测试脚本和移动设备之间传递操作指令和操作结果。由于Java、Python等主流的编程语言基本都支持发送HTTP接口，这使得你几乎可以使用任何编程语言甚至接口请求工具来操作移动设备App。
Appium Server的接口继承至Selenium WebDriver的JSON Wire Protocol协议，有一套完整的标准，称之为Mobile JSON Wire Protocol，即移动端JSON报文协议，该协议使用JSON格式发送请求和返回响应，定义了从建立与设备的会话（Session）到定位及操作元素等一系列的接口。
由于Appium Server支持不同的平台和设备，在使用Appium Server接口服务时我们需要发送一个称之为Desired Capabilities（即期望能力）的配置，来告诉Appium我们需要操作什么类型的平台、平台版本、设备名称以及要操作的App相关信息等。
在驱动移动设备操作的引擎上，Appium并没有自己重新设计一套兼容各种设备的引擎，而是通过整合不同平台的官方自动化框架来完成。对于Android2.3-4.3版本，Appium使用谷歌的Instrumentations引擎，Android 4.3版本以上则采用谷歌的UiAutomator和UiAutomator2引擎。对应iOS系统，9.3版本以下使用苹果的UiAutomation引擎，9.3以上版本统一使用苹果的XCUITest引擎。
Android4.3-5.1版本推荐使用UiAutomator引擎，Android6.0以上推荐使用UiAutomator2引擎。Appium分别使用UiAutomator和UiAutomator2来驱动Android设备的原理和过程，如图5.1和5.2所示。

图5.1  Appium使用UiAutomator驱动Android设备流程

图5.2  Appium使用UiAutomator2驱动Android设备流程
Appium Server作为一套用于中转指令接口服务，首先需要启动它。你可以使用Nodejs版或者Appium桌面版来启动Appium Server。Appium Server启动时需要绑定两个端口，默认是4723和4724端口，用于分别于脚本和移动设备进行通讯。
在自动化操作Android设备时，Appium Server还需要借助Android SDK中的adb工具来连接移动设备，部署测试环境和获取设备信息等。
在Appium Server每次操作时会在移动设备中安装一个Appium Setting的应用并后台启动一个bootstrap.jar或UiAutomator2 Server的服务来监听Appium Server的4723端口，以获得操作指令。收到的指令会转化为UiAutomator或UiAutomator2的自动化脚本来操作设备和其他App应用。
在iOS端，流程相识，测试脚本通过对应的Appium Client SDK按照Mobile JSON Wire Protocol协议，发送HTTP请求将操作命令发给Appium Server，Appium Server通过Instruments.js在iOS手机上安装bootstrap.js，监听4723端口，并接收Appium Server发送过啦的命令。然后bootstrap.js调用UiAutomation或XCUITest执行相关命令，来操作手机及App，并返回结果。
### 5.4.3  Windows系统Appium测试环境搭建
Appium的使用环境搭建起来相对较为麻烦，需要安装Java JDK，Android SDK，Appium Server以及模拟器或者使用真机。Appium Server可以通过Nodejs安装，也可以使用集合了Nodejs环境的Appium桌面版。
使用Appium桌面进行安装相对比较简单，本教程使用此方式安装。
由于Appium在操作移动设备时需要用到Android SDK自动工具中的adb等工具，因此搭建Android自动化环境需要先安装Android SDK。
Android SDK基于Java开发，首先需要先安装Java jdk1.8。
在测试设备上可以使用真机也可以使用模拟器。相对于不同厂商的真机，模拟器界面较为统一，拥有Root权限，支持多种Android版本并且方便PC端展示。由于Android SDK自带模拟器启动比较慢，因此一般采用三方模拟器。夜神模拟器、逍遥模拟器、网易MuMu、Genymotion等。本文采用夜神模拟器5.1.1。
使用Python编写Appium自动化脚本还需要安装相应的三方包Appium-Python-Client，安装方式很简单，使用pip安装即可。
```bash
pip install Appium-Python-Client
```

1．安装Java jdk1.8运行环境
搜索或在Oracle官网https://www.oracle.com/cn/java/technologies/javase/javase-jdk8-downloads.html，下载Java jdk1.8安装包，下载完成后，一路下一步进行安装，记住安装的路径。
桌面右键单击计算机图标，选择属性，选择环境管理，进入环境管理选项卡。
新建JAVA_HOME环境变量，变量值是自己安装jdk的路径，比如，“D:\Java\jdk1.8.0”。
```
JAVA_HOME=D:\Java\jdk1.8.0
```
新建CLASSPATH环境变量，值为：
```
CLASSPATH=%JAVA_HOME%/lib/dt.jar;%JAVA_HOME%/lib/tools.jar;
```
找到PATH变量，追加如下目录：
```
%JAVA_HOME%/bin;%JAVA_HOME%/jre/bin;
```
安装完成验证方法。打开cmd命令行窗口，在命令行窗口中输入java -version,可以正常显示出Java版本表示安装成功。
2．安装Android SDK并配置环境变量
搜索下载Android SDK安装包，并解压（建议其所在路径中不要存在中文字符）。在系统环境变量中设置ANDROID_HOME系统变量为你的Android SDK路径，把tools和platform-tools两个目录加入到系统的Path路径里，如图6.3及6.4所示。
图6.3  配置ANDORID_HOME
图6.4  配置tools、platform-tools到环境变量中
验证方法，打开cmd，输入adb devices不报错，表示安装成功，如图6.5所示。
图6.5  检查adb
3．下载安装夜神模拟器
搜索或在官网https://www.yeshen.com/下载夜神模拟器，并安装，安装时注意安装目录。

> 注：由于夜神模拟器自带一套adb并且会自动将其所在路径添加到系统变量Path中，当夜神模拟器中的adb和Android SDK中的adb版本不一致时可能会有问题。解决方法为，将
Android SDK platform-tools中的adb.exe替换夜神模拟器Nox/bin目录中的adb.exe，及nox_adb.exe或将系统环境变量中的夜神目录删掉。

启动夜神模拟器，如果默认不是Android 5.1.1版本，可以通过夜神多开器下载5.1.1版本的模拟器启动，等启动完成后，在模拟器的（非手机系统的）设置中将其由平板模式设置为手机模式，并重启。
验证方法，打开cmd命令行，输入以下命令。
```bash
$ adb connect 127.0.0.1:62001
$ adb devices
```
能看到设备为成功。
```
List of devices attached
127.0.0.1:62001 device
```
注：如果62001端口无法连接，可以尝试使用adb connect 127.0.0.1:62025端口。
4．下载并安装Appium桌面版
搜索或在GitHub上https://github.com/appium/appium-desktop/releases/
下载Appium桌面版，并安装。Appium桌面版集成了Node.js环境和各种依赖包，提供界面化配置和日志显示，并提供了Appium Inspector元素检查器，使用起来较为方便。
下载后双击安装，安装完启动，并在Configuration中配置JDK和ANDORID_HOME路径，和系统变量中的路径保持一致。
5．安装Appium Python客户端
打开cmd命令行，使用pip安装appium-python-client。
```python
$ pip install appium-python-client
```
验证方法，在Python交互环境下使用输入from appium import webdriver不报错即为安装成功。
```python
>>> from appium import webdriver
>>>
```
这样Appium + Android的测试环境就搭建好了。
### 5.4.4  MacOS系统Appium测试环境搭建
MacOS系统很多包使用brew工具安装非常方便，因此我们先安装brew，然后在安装Java jdk1.8和Python3，已经安装好的便可以跳过。
1．安装Brew
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
2．安装Java jdk1.8并配置环境变量
```bash
$ brew cask install caskroom/versions/java8
```
安装完成后配置环境变量
```bash
$ vim ~/.zshrc
```
在文件末尾添加以下环境变量，并保存。
```
export JAVA_HOME=$(/usr/libexec/java_home)
export PATH=${JAVA_HOME}/bin:${PATH}
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
```
source配置文件是环境变量生效。
验证方法，在终端输入java -version能显示Java版本表示安装成功。
$ source ~/.zshrc
3．下载Android SDK并并配置环境变量
搜索下载Mac版，Android SDK并解压到/usr/local目录，如android-sdk-macosx。
配置环境变量
```bash
$ vim ~/.zshrc
```
在文件末尾添加以下环境变量，并保存。
```
export ANDROID_HOME=/usr/local/android-sdk-macosx
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools
export PATH=${PATH}:${ANDROID_HOME}/build-tools/28.0.3
```
source配置文件使环境变量生效。
$ source ~/.zshrc
验证方法，在终端输入adb不报错则表示配置成功。
4．下载安装网易MuMu模拟器
由于Mac上的夜神模拟器版本比较单一，并且不太稳定，因此建议使用网易MuMu或者Genymotion模拟器。在网易MuMu官网http://mumu.163.com/下载模拟器，安装并启动。
注：网易MuMu模拟器不需要使用adb connect命令进行连接，模拟器启动后自动连接。
验证方法，输入adb devices查看设备是否连接正常。
5．下载安装Appium桌面版
搜索或在GitHub上https://github.com/appium/appium-desktop/releases/
下载对应Appium桌面版，并安装
6．安装Appium Python客户端
同样在终端使用pip命令安装即可。
```bash
$ pip3 install Appium-Python-Client
```
这样MacOS系统上的Appium + Android环境就搭建好了。
## 5.5  Appium基础使用
在搭建好Appium环境后，使用Appium启动应用一般需要以下几步。
 1. 启动adb并连接模拟器或手机
 2. 启动Appium服务
 3. 获取应用apk安装包或应用信息（包名、入口活动名）
 4. 编写脚本，配置相关信息并连接Appium服务
 5. 定位并操作App中的元素
### 5.5.1  启动adb并连接模拟器
启动夜神模拟器，在命令行使用以下命令连接模拟器，并查看设备，确保设备状态正常。
```bash
$ adb connect 127.0.0.1:62001
connected to 127.0.0.1:62001
$ adb devices
List of devices attached
127.0.0.1:62001 device
```
注：如果adb devices查看不到设备，或者设备是offline状态，一般可能是Android SDK下和夜神模拟器安装目录中的adb版本不一致导致的，可以尝试尝试adb kill-server重新连接或者尝试将Android SDK下的adb.exe替换夜神安装目录中的adb.exe及nox_adb.exe再次连接。
### 5.5.2  启动Appium服务
启动Appium Desktop，第一次启动时，需要点击Edit Configurations按钮，配置好ANDROID_HOME和JAVA_HOME。如图6.7所示。
图6.7  首次启动编辑Appium配置
然后点击Start Server 1.8.3按钮，启动Appium Desktop。如图6.8所示。
图6.8  启动Appium Desktop
### 5.5.3  获取应用apk安装包或应用信息
在使用Appium启动应用时，有以下两种方式：
 1. 指定应用apk安装包路径，自动安装应用并启动
 2. 手动安装应用，指定应用包名和入口活动名
由于第一种每次运行需要重新上传安装包到手机并重新安装应用，速度较慢，因此在调试时一般推荐使用第二种方式。
第二种方式需要我们手动获得应用的包名（Package）及入口活动名（Launchable Activity），获取应用包名和入口活动名的方式很多，有apk安装包我们可以使用Android SDK目录build-tools中的aapt工具进行探查，没有apk安装包则可以使用adb shell logcat命令探查。
1．有apk包的获取方法
首先在命令行进入Android SDK所在目录的build-tools目录下，看有没有某个版本如28.0.3的构建工具，没有的话需要使用SDK Manager下载一个版本的构建工具，如果有，cd到该目录，然后使用aapt工具查看apk包的信息。
```bash
$ cd /usr/local/android-sdk-macosx/build-tools/28.0.3
$ ./aapt dump badging <apk包路径>
```
从命令执行结果中可以找到package: name包名和launchable-activity: name相关信息，如图6.9及6.10所示。
图6.9  使用aapt命令查看包名称
图6.10 使用aapt命令查看启动页名称
2．无apk包已安装的APP包的获取方法
如果App以及安装到手机上，没有安装包。可以使用logcat日志查看到应用的包名和启动页名。
首先启动logcat如下：
```bash
$ adb shell logcat | findstr “cmp="
```
然后启动App，查看日志输出，“cmp=”后即为包名/启动页名称，如图6.11所示。
图6.11 使用logcat查看已安装应用的报名和启动页名称

> 注：MacOS或Linux系统findstr要改为grep
### 5.5.3  编写脚本，配置相关信息并连接Appium服务
编写Appium启动App脚本分为以下几步：
 1. 从appium导入webdriver
 2. 构造字典类型的需求配置项caps
 3. 使用webdriver的Remote方法连接Appium Server得到driver
需求内存项配置caps中需要提供必要的平台名称、平台版本、设备名称及应用安装包路径或App包名及App包名加启动页名称，因此常见有以下两种配置。
通过指定apk安装包路径在线安装并启动App方式配置如下：
```python
caps = {
    'platformName': 'Android',
    'platformVersion': '7.1.2',
    'deviceName': '127.0.0.1:62001',
    'app': 'D:\\xiaomi.apk'
}
```
手动安装应用通过指定应用包名和入口页名启动App方式配置如下：
```python
caps = {
    'platformName': 'Android',
    'platformVersion': '7.1.2',
    'deviceName': '127.0.0.1:62001',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity'
}
```
使用App启动小米商城App的完整代码如下：
代码appium_boot_app.py内容
```python
from appium import webdriver
from time import sleep
# 配置项
caps = {
    'platformName'：'Android',
    'platformVersion'：'7.1.2',
    'deviceName'：'127.0.0.1:62001',
    'appPackage': 'com.xiaomi.shop',
    'appActivity': '.activity.MainTabActivity'
}
driver = webdriver.Remote('http://localhost:4723/wd/hub'，caps)
sleep(3)
driver.quit()
```
确保adb设备处于连接状态，Appium服务已启动，运行脚本，可以看到Appium服务日志滚动（脚本连接Appium服务正常），模拟器中会自动安装Appium Settings（Appium测试环境）并启动小米商城App，如图6.12所示 。
图6.12 使用Appium启动小米商城App
### 5.5.4  定位并操作App中的元素
同Selenium相似，Appium在操作元素时需要先定位再操作，我们可以使用Android SDK下的uiautomatorviewer或Appium桌面版集成的Appium Inspector工具审查元素，如图6.13所示。
图6.13  Appium Inspector
Appium中常用的定位方式有一些几种：
- driver.find_element_by_id：通过resource-id属性定位。
- driver.find_element_by_class_name：通过控件类型class属性定位。
- driver.find_element_by_xpath：通过XPath语法定位。
同样可以使用对应的driver.find_elements_by_***来定位一组元素，得到一个元素列表。
如图6.13，使用三种方式，定位小米商城启动时的同意按钮的示例如下：
agree_btn = driver.find_element_by_id(‘com.xiaomi.shop:id/dialog_btn_agree_yes’）
agree_btn = driver.find_element_by_xpath(‘//*[@text=”同意”]’)
agree_btn = driver.find_elements_by_class_name(‘android.widget.Button)[1]
第一种方式使用元素的resource-id属性定位，当元素有resource-id属性时，可以优先考虑通过ID进行定位元素。
当元素text属性（即显示文字）不为空时，可以通过XPath语法结合text属性定位，//*[@text="同意"]，表示任意路径，任意节点的text属性值为“同意”的元素。
当元素resource-id和text属性都为空时，可以通过Class Name结合find_elements根据控件类型定位出所有该类型控件，如行里中定位出所有的按钮元素，并取第二个，即为同意按钮。
定位到元素后，便可对元素进行操作，常见的操作有：
- click()：点击
- send_keys()：输入
- clear()：清空输入
如，上例中的同意按钮点击操作如下：
agree_btn.click()
但元素只需要操作一次时，也可以和定位连起来使用，如：
driver.find_element_by_id(‘com.xiaomi.shop:id/dialog_btn_agree_yes’).click()
更多的元素定位及元素操作可以参见后面的章节。
## 5.6  使用Appium Inspector
定位元素的前提是我们要需要了解元素的属性。在Android应用中，元素以XML格式编写于布局文件里。在没有应用源码的情况下，我们就需要使用元素审查工具来查看元素属性。常用的元素审查器有Uiautomatorviewer、Appium Inspector、ATX WEditor、Macaca app-inspector、Airtest IDE Poco定位器等。
Appium Inspector是Appium桌面版集成的一款元素审查器，支持Android和iOS，除原生App中的元素外，还支持嵌入式WebView（网页）元素的审查，并能自动计算出元素的XPath路径。相比于Android SDK自带的Uiautomatorviewer，其优缺点如下：
优点：
 1. 跨平台，支持Android和iOS；
 2. 支持原生、网页及混合App元素定位；
 3. 支持自动计算XPath；
 4. 支持同步操作App
 5. 支持坐标操作和元素操作两种模式；
 6. 支持录制操作并生成代码；
 7. 支持使用远程及Appium云服务；
 8. 支持执行额外的Appium指令；
 9. 使用是不同退出Appium。
注：由于Appium服务启动时会占用adb端口，因此在使用Uiautomatorviewer审查元素时往往需要关闭并退出Appium服务。
缺点：
 1. 启动时需要填写需求配置，较为麻烦；
 2. 速度较慢；
 3. 定位不够精准，特别是浮动元素；
 4. 稳定性有待提升；
### 5.6.1  启动配置
 确保adb服务启动，设备处于连接状态（adb devices设备状态正常）。打开Appium桌面版，并点击Start Server启动服务。如图6.13点击，点击右上角的搜索图标，即可启动Appium Inspector，进入配置界面，入图6.14所示。
图6.13  启动Appium Inspector按钮
图6.14  Appium Inspector配置界面
 最上方三个Tab分别是自动发现服务（本地Appium服务）、使用自定义服务（远程Appium服务）和使用Appium云服务。在本地调试时，一般使用第一个即可。
左侧需要填写Desired Capabilities期望配置，提供期望操作的平台、设备及应用信息。常用需求配置包括：
- platformName：必须，平台名，Android或iOS等；
- platformVersion：平台版本；
- deviceName：模拟设备名，或使用udid指定真机设备名称；
- app：apk或ipa安装包路径，或使用appPackage和appActivity指定已安装的应用的包名和入口页活动名。
例如，启动小米商城app的配置如下：
```json
{
    "platformName"："Android",
}
```
> 注：Android 6.1版本以上推荐显式指定驱动名称，即"automationName": "UiAutomtor2"。

可以在右侧输入框点击编辑按钮，使用JSON格式直接编写需求配置项，然后点击保存按钮，生效。
需求配置也可以点击下方的Save按钮保存起来，下次直接选择使用。点击右下角的Start Session按钮启动会话。和使用脚本操作Appium一样，Inspector启动时，会自动在设备上部署Appium环境，并（安装）启动App，如图6.15所示。
图6.15  Appium Inspector启动界面
上方工具栏说明如下：
- 选择元素：默认工具，可以用于现在左侧界面上的元素；
- 坐标滑动：滑动屏幕；
- 坐标点击：点击指定坐标，左上角实时显示点击的坐标值；
- 返回：Android返回操作；
- 刷新：同步设备当前屏幕；
- 录制：录制操作并生成代码；
- 查找：根据指定属性查找元素；
- 复制：复制当前页面XML源码到剪贴板；
- 退出：退出当前会话病关闭Appium Inspector。
选中元素时的操作按钮如下：
- 点击：点击当前元素；
- 输入：在当前元素中输入指定值；
- 清空：清空当前元素的输入内容；
### 5.6.2  坐标操作
Appium Inspector中支持坐标点击和滑动操作，并支持实时显示光标点坐标。在设计滑动动作时这非常有用，我们可以通过显示的坐标确定出滑动的起始点和终止点，如图6.16所示。

图6.16  使用Appium Inspector查看图案解锁坐标点

> 注：由于Appium Inspector操作App流程较慢，因此建议手动在App上完成操作，然后点击Appium Inspector上的刷新按钮，完成界面同步。
### 5.6.3  审查元素
在Appium Inspector上方工具栏的查看元素按钮处于激活状态时，鼠标移动到左侧App界面指定元素时，便可以看到元素被高亮选中。界面中间App Source为应用布局源码，右侧为所选元素的属性以及计算出的XPath路径等，如图6.16所示。
图6.16  使用Appium Inspector审查元素[
### 5.6.4  查找元素
查找按钮可用于在当前页面通过ID、XPath等来查找元素，一般可用来验证所写的定位表达式，入托6.17所示：
图6.17  Appium Inspector查找元素
### 5.6.5  元素操作
选中元素后，右侧面板上方的按钮对元素进行Tap点击、Send Keys输入、Clear清空或复制元素全部属性。
使用Appium Inspector操作小米商城的示例如下图6.17及6.18所示。
图6.18  使用Appium Inspector执行点击操作
图6.18 使用Appium Inspector执行输入操作
### 5.6.6  录制代码
Appium Inspector的另一大优势是可以通过录制动作来生成代码，支持不同的语言及模板。
如图6.19所示，使用Appium Inspector录制设置中搜索“关于”并点击“关于平板电脑”代码的操作步骤如下：
 1. 打开设置，并点击Appium Inspector刷新按钮同步界面；
 2. 定位并点击右上角的搜索按钮；
 3. 输入“关于”；
 4. 定位并点击“关于平板电脑”；
 5. 停止录制，点击显示模板代码按钮，复制生成的代码使用。
图6.19  使用Appium Inspector录制小米商城搜索流程
录制完生成的代码如下：
```python
# This sample code uses the Appium python client
# pip install Appium-Python-Client
# Then you can paste this into a file and simply run with Python
from appium import webdriver

caps = {}
caps["platformName"] = "Android"
caps["appPackage"] = "com.android.settings"
caps["appActivity"] = ".Settings"
caps["ensureWebviewsHavePages"] = True
driver = webdriver.Remote("http://localhost:4723/wd/hub", caps)
el1 = driver.find_element_by_accessibility_id("搜索设置")
el1.click()
el2 = driver.find_element_by_id("android:id/search_src_text")
el2.send_keys("关于")
el3 = driver.find_element_by_id("com.android.settings:id/title")
el3.click()
driver.quit()
```
上例中，除注释外，首先是从appium导入webdriver，然后是组装caps期望配置，并连接Appium服务，然后逐个定位并操作元素，最后退出会话。
## 5.7  小结
本章介绍了Appium的基本原理、Android相关基础知识、ADB命令、Monkey的使用以及Appium基本的元素定位及操作。
