
随着自动化脚本数量的增加，用例及测试数据的组织和维护，公共模块的复用，用例挑选及执行控制，或者团队协作及用例编写规范化，我们便需要引入自动化测试框架。
框架是应用的组织架构，一般包含代码、配置、数据、日志、依赖的组织，可复用模块的抽取以及运行控制等。就像从一盘散沙的武装人员，到一个军队。框架是由脚本集合发展到应用（包含测试项目）的一种必然选择。
框架的基本功能一般包括：
- 代码、配置文件、数据文件等的分类组织。
- 依赖管理。
- 公共模块的复用。
- 运行流程及控制。
另外，从设计目标来看，框架应该具有易用、健壮稳定、良好的性能，通过配置或参数提供灵活的使用方式，以及易调试和维护（如提供运行日志）等特性。
测试框架是为测试而设计的框架。测试框架一般还要包含用例编写、丰富的断言方法、用例组织、测试准备和清理方法、执行测试、生成测试报告等功能。
相比于一堆脚本集合，测试框架一般具有以下优点。
- 用例执行互相独立，一个用例失败不影响其他用例执行和验证。
- 清晰的用例执行状态，如成功、失败、出错、跳过等以及系统的测试报告。
- 灵活的用例挑选及批量运行。
- 提供丰富的断言（期望结果与实际结果的对比）方法。
- 提供不同范围的测试准备和清理方法。
- 在特定环境不满足条件时，跳过用例。
Python中常用的测试框架有Unittest、Nose、Pytest以及Robot Framework等。其中Unittest是Python自带的测试框架，提供基础的用例管理和测试控制功能，使用灵活易于定制。本章以Pytest及Selenium为基础来讲解Web自动化测试框架的搭建流程。Pytest和Robot Framework将在其他章进行讲解。
## 7.1  框架及测试框架
框架指为解决一类问题而开发的一套架构或程序，如Web应用开发框架，测试框架等等。框架使用特定的格式约定，提供一些配套工具库支持，并完成整个流程的控制。
测试框架是为解决单元测试或自动化测试问题设计的一套程序，用户按照特定的格式（约定）来编写用例，使用测试框架提供的命令行或API工具来控制测试用例的执行。
测试框架一般包含TestCase测试用例、TestSuite测试套件（用例集）、Test Fixture测试装置（测试准备及清理方法）等概念。
不同于测试工具及测试平台，测试框架的搭建及参与者（编写用例者）一般需要一定的代码基础，并懂得框架的运转流程。
### 7.1.1  测试框架的种类
测试框架作为一套程序，在设计时可以采用不同结构，或以不同的角度来解决测试用例编写和执行的问题，常见的测试框架的种类有线性测试框架、基于模块的测试框架、库结构的测试框架、数据驱动测试框架、关键字驱动测试框架、行为驱动开发框架及混合框架等。
1．线性测试框架
线性测试框架（Liner Automation Framework），基于录制回放型的框架，如Web自动化测试中的UFT（QTP）、Selenium IDE、Katalon Studio、LoadRunner、Badboy、Postman、Appium、HttpRunner等
- 优点：上手容易，甚至无需编码，用例产出快。
- 缺点：数据耦合性高，模块复用度低，抗变动能力较弱。
- 适用：稳定小型项目，或需要快速产出大量用例的场景。
2．基于模块、库结构测试框架
基于模块的测试框架（Modular Based Testing Framework），对被测产品各个模块的操作进行封装，用例调用模块进行操作，如Page Object Module模式。
库结构测试框架（Library Architecture Testing Framework）在模块型框架的基础上扩展了跨模块的业务操作，即常见的业务场景，使常用的业务流程可以被复用。
- 优点：简化用例操作（直接调用模块封装方法，操作应用），易扩展（编写新的模块），后期维护成本较低（通过修复模块操作来修复失效方法）；
- 缺点：框架前期工作量较高（需要编写大量的模块），数据耦合在用例中，未做分离。
- 适用：海量用例的大型项目，特别是需要考虑后期维护成本的项目；
3．数据驱动型框架
数据驱动型测试框架（Data-Driven Testing Framework），将数据和测试用例分离，使用文本、CSV、Excel、JSON、YAML或来自于数据的数据来批量测试某个场景，如HttpRunner。
- 优点：使用者（执行者）无须编码，仅需按格式提供数据；针对同一场景快速产出大量用例；
- 缺点：每个场景需要单独开发，所有的数据的场景要求一致；
- 适用：高频场景，正向场景（异常场景的处理流程各有不同）。
4．关键字驱动型框架
关键字驱动型测试框架（Keyword Driven Testing Framework），以表格或其他形式编写用例，将文本格式的特定关键字转为函数进行执行的框架，如Robot Framework。
表10.1  关键字驱动框架测试用例

|Test Cases| | | |
|---|---|---|---|
|测试百度搜索||||
||Open Browser|https://www.baidu.com/|chrome|
||Input Element|id=kw|临渊|
||Click Element|id=su||
||Close Browser|||

- 优点：使用者编写用例上手容易，关键字（函数）可以复用；
- 缺点：关键字开发需要一定的工作量，数据处理灵活性不如直接使用代码灵活。
- 适用：Web、App等界面自动化。
5．行为驱动开发型框架
行为驱动开发型框架（Behavior Driven Development Framework），简称BDD。BDD是ATDD验收测试驱动开发的一种升级版，根据明确的预期行为（一组场景）来进行开发和测试。
如一个后台登录功能，可以描述如下：
Feature: 登录功能
  Scenario: 正常登录
    Given 用户名 admin 密码 123456
    When 打开后台页面
    And  输入用户名
    And  输入密码
    And  点击登录按钮
    Then 页面中应不包含 您输入的帐号信息不正确
 Scenario: 账号为空登录
    Given 用户名为空 密码 123456
    When 打开后台页面
    And  输入用户名
    And  输入密码
    And  点击登录按钮
    Then 页面中应包含 您输入的帐号信息不正确
这种预期行为使用一种特定的规范格式进行描述，旨在消除需求从客户，到产品经理，再到开发/测试时的信息失真问题。
- 优点：场景明确、上手简单；
- 缺点：每个场景步骤需要使用脚本实现，步骤和场景复用度低。
- 适用：面向客户的产品，Web、App等界面自动化。
6．混合型框架
混合型框架（Hybrid Testing Framework），混合使用各种模式搭建的框架。
- 优点：融合了各种框架的优点、灵活型较高。
- 缺点：搭建测试框架的工作量较大。
- 适用：被测场景种类较多，需要各种灵活的模式。
### 7.1.2  Python常见测试框架
Python中常用的测试框架有Unittest、Nose、Pytest以及Robot Framework等。其中
#### Unittest
Unittest是Python自带的测试框架，提供基础的用例管理和测试控制功能，使用灵活易于定制。Unittest中主要包含TestCase测试用例、TestSuite测试套件、TestFixture测试准备及清理方法和TestRunner测试运行器等主要概念，另外还包含TestLoader用于批量加载用例生成测试套件，TestResult用于在TestRunner中记录测试结果。Unittest的模型关系及运作流程如图9.1所示。

图9.1  Unittest模型关系及运作流程

Unittest的用例及运行方法如下：
代码test_unittest.py内容
```python
import unittest
from selenium import webdriver

class TestBaidu(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.driver = webdriver.Chrome()
    @classmethod
    def tearDownClass(cls):
        cls.driver.quit()
    def test_baidu_search(self):
        """测试百度搜索"""
        self.driver.get('https://www.baidu.com')
        self.driver.find_element('id', 'kw').send_keys('简书 韩志超')
        self.driver.find_element('id', 'su').click()
        self.assertIn('韩志超', self.driver.title)
if __name__ == '__main__':
    unittest.main()
```
测试类需要继承unittest.TestCase，测试类中使用setUpClass及tearDownClass来在该类所有用例执行前和所有用例执行后启动及关闭浏览器。Unittest中封装了很多断言方法，assertIn用来断言页面标题包含特定的字符串。
#### Nose
Nose是一款基于Unittest并致力于是测试更简单的框架。新版的Nose2则支持通过插件来扩展功能。使用Nose2需要先安装
```bash
$ pip install nose2
```
编写用例如下：

> 代码test_nose2.py内容
```python
import unittest

class TestStrings(unittest.TestCase):
    def test_upper(self):
        self.assertEqual("spam".upper(), "SPAM")
```
在命令行使用nose2来运行，上例运行结果如下：
```bash
You can then run this test like so:
$ nose2 -v
test_upper (test_simple.TestStrings) ... ok
----------------------------------------------------------------------
Ran 1 test in 0.000s
OK
```
其中-v是指定详细显示模式。
#### Pytest
Pytest是一个易用、强大、灵活的功能测试框架，并且兼容Unittest和Nose的测试用例，插件非常丰富，是目前最流行的Python测试框架，不仅可以用作单元测试，还广泛应用于接口自动化和UI自动化测试框架中。
Pytest的安装方式如下：
```
pip install pytest
```
Pytest的用例可以使用函数式写法也可以使用类式写法，示例如下：

> 代码test_pytest.py内容
```python
import pytest
from selenium import webdriver
@pytest.fixture(scope='session')
def driver():
    driver = webdriver.Chrome()
    yield driver
    driver.quit()
def test_baidu_search(driver):
    """测试百度搜索"""
    driver.get('https://www.baidu.com')
    driver.find_element('id', 'kw').send_keys('简书 韩志超')
    driver.find_element('id', 'su').click()
assert '韩志超' in driver.title
```
上例中自定义了一个fixture方法，作用范围是整个运行会话，yield上是setup测试准备，yield下是teardown测试清理。Pytest使用Python标准的assert语句进行断言。
#### Robot Framework
Robot Framework是一款关键字驱动框架，使用特定的格式和语法通过文本或表格来编写用例，支持接口、Web、App、数据库等测试，同时可以使用Python或Java编写扩展。
Robot Framework的安装方式如下，不同的测试还需要安装对应的关键字库。
```bash
pip install robotframework
pip install robotframework-selenium2library
```
用例格式如下：

> 文件test_robotframework.robot内容
```robot
*** Settings ***
Library         Selenium2Library
Suite Setup     Open Browser      https://www.baidu.com      chrome
Suite Teardown  Close Browser
*** Test Cases ***
test_baidu_search
    [Documentation]   测试百度搜索
    Input Text        id=kw                      简书 韩志超
    Click Element     id=su
```
进入到文件所在目录，在命令行输入robot test_robotframework.robot，运行结果如下：
```bash
D:\Python自动化书\代码\第9章>robot test_robotframework.robot
options {}
==========================================================================
Test Robotframework
==========================================================================
DevTools listening on ws://127.0.0.1:60555/devtools/browser/a6c7e0cb-d7f9-46ad-8d62-92d7cee18ea6
test_baidu_search :: 测试百度搜索                                     | PASS |
------------------------------------------------------------------------------
Test Robotframework                                                   | PASS |
1 critical test, 1 passed, 0 failed
1 test total, 1 passed, 0 failed
==========================================================================
Output:  D:\Python自动化书\代码\第9章\output.xml
Log:     D:\Python自动化书\代码\第9章\log.html
Report:  D:\Python自动化书\代码\第9章\report.html
```
#### Behave
Behave是一款BDD即行为驱动框架，BDD基于交付测试驱动开发（ATDD）理念，使用专有的语法格式来消除需求在传递过程中的失真问题。
Behave的安装方式如下：
```bash
$ pip install behave
```
使用时，首先编写特性（功能）描述文件，如：
文件baidu_search.feature内容
```feature
Feature: 百度搜索
  Scenario: 基本搜索场景
    Given 打开百度
    When 搜索框输入 <简书 韩志超>
    When 点击百度一下按钮
    Then 页面标题应包含 <韩志超>
    Then 关闭浏览器
```
然后编写步骤解释代码，步骤解释代码要放到feature同级的steps目录里，代码如下：
> 代码steps/baidu_search_steps.py内容
```python
from behave import given, when, then, step
from selenium import webdriver
from time import sleep

@given('打开百度')
def step1(context):
    global driver
    driver = webdriver.Chrome()
    driver.get('https://www.baidu.com')
@when('搜索框输入 <{keyword}>')
def step2(context, keyword):
    driver.find_element('id', 'kw').send_keys(keyword)
@when('点击百度一下按钮')
def step3(context):
    driver.find_element('id', 'kw').click()
@then('页面标题应包含 <{text}>')
def step4(context, text):
    sleep(0.5)
    assert text in driver.title
@then('关闭浏览器')
def step5(context):
    driver.quit()
```
每个步骤对应一个实现函数，函数名任意，使用装饰器来匹配featrue文件中的步骤描述。
在feature文件所在路径命令行输入behave命令，运行结果（去掉chromedriver的调试日志）如下：
```
D:\Python自动化书\代码\第9章>behave
Feature: 百度搜索 # baidu_search.feature:1
  Scenario: 基本搜索场景      # baidu_search.feature:3
    Given 打开百度          # steps/baidu_search_steps.py:6
    When 搜索框输入 <简书 韩志超> # steps/baidu_search_steps.py:12
    When 点击百度一下按钮       # steps/baidu_search_steps.py:16
    Then 页面标题应包含 <韩志超>  # steps/baidu_search_steps.py:22
    Then 关闭浏览器          # steps/baidu_search_steps.py:28
1 feature passed, 0 failed, 0 skipped
1 scenario passed, 0 failed, 0 skipped
5 steps passed, 0 failed, 0 skipped, 0 undefined
Took 0m7.993s
```
## 7.2  Pytest简介
Pytest是一个易用、强大、灵活的功能测试框架，并且兼容Unittest和Nose的测试用例。
- 用例编写简单，断言方便；
- 全能的mark标记；
- 强大的测试辅助函数fixtures；
- 灵活的执行控制；
- 丰富的插件。
Pytest安装方法如下：
```bash
$ pip install pytest
```
### 7.2.1  用例编写
Pytest用例支持使用函数或者类来编写用例，也支持运行Unittest格式的用例。
#### 函数式写法
使用函数来编写测试用例非常简单，直接在test开头的测试脚本中编写test开头的测试函数即可，在测试函数中来执行测试步骤，打印测试信息，使用Python原生assert语句进行断言。示例如下：
> 代码test_demo1.py内容
```python
import requests

def test_1():
    assert 1+1 >= 2
def test_2():
    res = requests.get('https://httpbin.org/get')
    assert res.status_code == 200
```
> 注：测试脚本，测试函数名都要以test开头

#### 类式写法
类式写法可以用来组织同一功能的用例，便于在用例之间共享一些属性和方法，或通过继承快速完成一些基础配置。和Unittest的格式的测试类不同，Pytest的测试类不需要继承其他类。
> 代码test_demo2.py内容
```python
import requests
class TestDemo2:
    base_url = 'https://httpbin.org'
    def test_get(self):
        url = self.base_url + '/get'
        res = requests.get(url)
        assert res.status_code == 200
    def test_post(self):
        url = self.base_url + '/post'
        res = requests.post(url)
        assert res.status_code == 200
```

> 注：1. 测试脚本，测试函数名都要以test开头, 类名以Test开头
> 2．测试类不需要继承unittest.TestCase, 测试类中不能有__init__方法

下面的例子演示了一个测试基类的定制与使用。
> 代码test_demo3.py内容
```python
from selenium import webdriver
class Base(object):
    def setup_class(self):
        self.driver = webdriver.Chrome()
    def teardown_class(self):
        self.driver.quit()
class TestBaidu(Base):
    def test_baidu_open(self):
        self.driver.get('https://www.baidu.com')
        assert '百度' in self.driver.title
    def test_baidu_search(self):
        self.driver.get('https://www.baidu.com')
        self.driver.find_element('id', 'kw').send_keys('简书 韩志超')
        self.driver.find_element('id', 'su').click()
        assert '韩志超' in self.driver.title
class Test163(Base):
    def test_163_open(self):
        self.driver.get('https://www.163.com')
        assert '网易' in self.driver.title
```
以上示例中，我们定义了一个测试基类Base，setup_class()是测试类的准备方法，名称固定，该测试类中的所有测试用例执行前执行，teardown_class()是测试类的清理方法，名称固定，该类中的测试用例全部执行完执行。
测试基类设置了一个流程为准备-执行-清理的用例类的模板，并包含一个浏览器驱动driver，TestBaidu()和Test163()两个测试类集成Base便可以在测试到该类时启动浏览器，测试完该类的所有用例后退出浏览器。
### 7.2.2  用例断言
断言又称为检查点，是期望结果与实际结果的对比。断言是用例必不可少的一部分。测试用例可以包含多个断言。
注：当包含多个断言，其中一个断言失败后后面的断言不会执行。如果想要完成所有断言的检查，可以使用pytest-check等插件。
Pytest中断言的写法非常简单，直接使用Python的assert语句即可。通过判断asert后面的表达式来判断该条断言是否通过。Python的表达式非常灵活，可以完成几乎所有的判断。
1． 断言相等
用 assert 实际结果 == 期望结果可以判断相等。
```python
assert res.status_code == 200
```
也可以用用于判断两个字典或列表是否相等，如。
```python
assert res.json() == {“code": "10000", msg: "success"}
```
判断大小关系：
```python
assert 1+1 >= 2
```
判断不相等：
```python
assert 1/3 != 0.3
```
2． 断言包含
用assert 部分 in 整体可以判断包含关系，包含可以是包含某个特定支付串，也可以是列表中包含某一项。
```python
assert '百度' in driver.title
assert 1 in [1,2,3]
```
3． 断言True、False或None
用assert 变量 is True、False或None来判断真假或无值，也可以结合not做反向判断。
```python
assert 1+1 == 2 is True
assert 2>1 is not False
assert  {'success': True}.get('code') is None
```
>注：空列表、元祖、字典、空字符串、0，'0'，None等都被视为假值（相当于`==Flase`但不是is False），其他值都被视为真值。比如我们可以使用assert list1来断言这个列表不为空，用assert dict1.get('code')来断言这个字典中能包含code这个值且code不为0，空格，None等为假的值，如果只判断dict1是否有code这个键，应用assert dict1.get('code') is not None或assert 'code' in dict1.keys()。
### 7.2.3  用例运行
测试用例的运行不需要像Python的函数一样自己手动调用，正确的做法是使用Pytest进行发现和加载用例执行。使用Pytest调用用例有以下两种方式。
1． 通过命令行运行
使用pytest加指定的路径、测试模块或测模块加要测试的用例即可。
运行testcase目录下所有用例（以test开头的py文件中所有test开头的函数或类方法）
```bash
$ pytest testcase/ 
```
运行指定目录指定测试模块（文件），多个文件或路径以空格隔开。
```bash
$ pytest testcase/test_requests.py
```
运行单个测试函数、测试类或测试方法如下：
```bash
$ pytest testcase/test_requests.py::test_get
$ pytest testcase/test_requests02.py::TestRequests
$ pytest testcase/test_requests02.py::TestRequests::test_post
```
更多的运行控制请参考8.7用例运行。
2． 代码里调用
```python
if __name__ == '__main__':
    pytest.main(["test_demo1.py", "-v"])
```
3． 常用的执行参数
通过pytest ...命令,可以实现非常灵活的执行控制，常用的参数如下：
- 详细显示模式：-v，否则执行通过的用例只显示一个点；
- 安静模式：-q：不显示环境信息；
- 命令行显示print输出：-s，否则捕获用例输出并显示到报告中；
- 运行目录及子包下的所有用例：pytest 目录名；
- 运行指定模块所有用例：pytest test_reg.py
- 运行指定模块指定类指定用例：pytest test_reg.py::TestClass::test_method
- 运行名称包含指定表达式的用例：-k 表达式(支持and or not),如pytest -k "test_a and test_b"
- 运行指定标签(mark)的用例： -m 标签(支持and or not), 如pytest -m "apitest and level1"
- 遇到失败后停止：-x/--exitfirst 首次失败后退出(可用于保留出错现场) --maxfails=3 3次失败后退出
- 执行上次失败的用例 --lf/--last-failed
- 先执行上次失败的用例,再执行成功的用例 --ff/--failed-first
- 只收集用例，不执行 --collect-only
- 显示执行最慢的前N条用例：--durations=N
- 显示所有可用的Fixture函数：--fixtures
-  
## 7.3  测试准备及清理
Test Fixtures测试装置，即测试准备及清理方法。一般来说，每条用例都需要一定的预置条件或数据准备。而如果用例执行了修改操作或产生了新的数据，还应该对环境进行还原和清理。在测试框架中，这种准备方法叫做setup，清理方法叫做teardown，这两种方法统称为Test Fixtures（夹具），像一个夹子一样把用例夹在中间。
这种Test Fixtures是有一定范围的。可以每次只夹一条用例（相当于每条用例都先执行setup方法，执行完用例再执行teardown方法），也在一批用例执行前执行一次setup，都执行完再执行teardown。
### 7.3.1  内置的setup和teardown方法
同Unittest一样，Pytest也支持不同范围的测试准备（setup）和测试清理（teardown）方法。Pytest中的测试准备和测试清理方法分为模块级、类级和函数、方法级，对应的函数名称如下：
- setup_module()/teardown_module()：模块及测试准备及清理，整个测试模块（脚本）的所有用例执行前和执行后执行；
- setup_class()/teardown_class()：类级测试准备及清理，测试类的所有用例之前前和执行后执行；
- setup_function()/teardown_function()：函数级测试准备及清理，每个测试函数执行前后执行；
- setup_method()/teardown_method()：方法及测试准备及清理，测试类中每个用例执行前执行；
- 在测试脚本中，编写固定名称的函数或方法，使用示例如下：

> 代码test_ demo4.py内容
```python
def setup_module():
    print('setup_module')
    
def teardown_module():
    print('teardown_module')
    
def setup_function():
    print('---setup function---')
    
def teardown_function():
    print('---teardown function---')
    
def test_1():
    print('test_1')
    
def test_2():
    print('test_2')
    
def test_3():
    print('test_3')
    
class TestA(object):
    def setup_class(self):
        print('setup_class')
    def teardown_class(self):
        print('teardown_class')
    def setup_method(self):
        print('setup_method')
    def teardown_method(self):
        print('teardown_method')
    def test_a(self):
        print('test_a')
    def test_b(self):
        print('test_b')
```
### 7.3.2  Fixtures函数
Fixtures函数是Pytest的精髓之处，使用@pytest.fixture装饰器即可快速将函数转为Fixture函数，Fixture函数中可以即包含setup测试准备及teardown测试清理步骤，使用yield隔开。
很多三方插件也会提供很多可以使用的Fixtures方法，比如pytest-selenium插件提供名selenium、driver的fixture方法使用例可以直接拿到浏览器驱动driver，pytest-base-url插件提供名为base_url的fixture方法来使得可以配置服务基础URL。在命令行使用pytest --fixture命令查看可用的Fixture函数。
另外，我们也可以自定义Fixture方法来完成测试准备及测试清理，比如加载读取数据文件、启动关闭浏览器等等。自定义Fixture方法可以通过scope指定作用范围，Pytest中的Test Fixtures方法以下有5种范围：
- Session会话级：运行一次Pytest算一次会话。运行期间只setup/teardown一次
- Package包级：对每个包Python包setup/teardown一次
- Module模块级：对每个Python脚本setup/teardown一次
- Class级：对每个测试类setup/teardown一次
- Function/Method级：
- 示例如下：
> 代码test_demo5.py内容
```python
import pytest
import requests

@pytest.fixture(scope='module')
def http():
    # 测试准备
    session = requests.session()
    session.headers = {'Token': 'abc123'}
    yield session  # 返回会话
    # 测试清理
    session.close()
    
def test_httpbin_get(http):  # fixture作为参数可以使用
    res = http.get('https://httpbin.org/get')
    assert res.status_code == 200
```
> 注：如果不需要清理方法，yield也可以使用return返回，如果setup出错，用例及teardown则不会执行。

Fixtures方法可以通过用例参数方式注入到用例中使用，Fixture方法之间也可以相互注入。Fixtures方的主要使用方式有3种：
 1. Fixtures函数名直接作为用例参数使用（参数结果及Fixture方法返回值）
 2. 在用例上使用@pytest.mark.userfixtures()；
 3. Fixture方法上使用autouse=True来使用例自动使用。

> 代码test_demo6.py内容
```python
import pytest
import requests

@pytest.fixture(autouse=True)
def setup_teardown():
    print('执行 测试准备')
    yield
    print('执行 测试清理')
    
@pytest.fixture
def http():
    session = requests.session()
    yield session  # 返回会话
    session.close()
    
@pytest.fixture
def login(http):  # fixture中也可以使用其他fixture方法
    print('登录')
    # http.post(...)
    
# 自动使用setup_teardown
def test_1():
    print('用例1')
    
# 显示使用login（包括自动使用setup_teardown）
@pytest.mark.usefixtures('login')
def test_2():
    print('用例2')
    
# 通过参数使用，可以使用多个，同时可以使用fixture函数的返回值
def test_3(http, login):
    print('用例2')
```
除了需要自动使用的场景外，一般推荐使用用例参数的方式来使用fixture，这种方式可以拿到fixture方法的返回结果来使用。
### 7.3.3  Fixture返回功能函数
假如我们有一个读取数据的Fixture方法，我们可能需要在根据用例中的不同参数来读取不同的内容，这时候需要把Fixture函数作为一个工厂函数来使用，即在Fixture函数中定义一个内部功能函数，并返回给用例使用。示例如下：
> 代码test_demo7.py内容
```python
import pytest

DATA = {'user1': '张三','user2': '李四'}

@pytest.fixture
def get_user():
    def func(key):  # 定义一个内部功能函数
        return DATA.get(key)
    return func  # 返回一个函数
    
def test_1(get_user):
    name = get_user('user1')  # 在用例中使用不同的参数获取不同的数据
    print(name)
```
### 7.3.4  Fixtures共享及优先级
Fixtures方法一般作为公用的辅助方法或全局变量来使用，因此需要在不同用例中都能使用。Pytest框架中使用固定名称的conftest.py文件，来集中管理Fixtures方法。
注：在Pytest中conftest.py可以用来编写Fixture方法、编写钩子方法、还可以用例导入其所在目录或包的路径（比如，用例确保项目根目录被导入），如果conftest.py所在目录没有__init__.py文件，则Pytest自动导入conftest.py所在目录，如果有则向上找到最上层的包（最上层一个包含__init__.py的目录），并导入包的所在目录，以确保conftest.py可以使用。
#### 生效范围
confitest.py中的Fixture方法，对所在目录及子目录下的所有用例生效，无需导入便可直接使用。
#### 优先级
当存在同名的Fixture函数时，采用用例就近原则。用例文件中的Fixtures方法优先于当前目录conftest.py中的Fixtures方法，优先于上级目录conftest.py中的Fixtures方法。
因此，在项目不同模块中使用conftest.py可以实现不同级别的测试准备、清理或辅助方法，如：
```
project
   - conftest.py
   - module1/
       -conftest.py
   - module2/
       -conftest.py
```
## 7.4  参数化数据驱动
数据驱动测试（DDT）是指，将不同数据代入标准自动化流程中进行验证的一种方式。数据中往往还包含期望结果，一组数据即一条用例。
数据驱动测试适用于，同一测试流程，需要不同的测试数据进行测试的场景。比如使用不同的关键词进行搜索、不同的用户登录下单、同一个接口使用不同的参数、单元测试函数多种入参及返回值校验等等。
数据驱动测试，使我们可以不需要多次复制同一过程用例，并修改其中的数据及期望结果，只需要编写一个统一的参数化模板用例。
编写数据驱动测试用例时，要求过程可统一。即所有的数据，可以采用统一的测试步骤和断言步骤来执行。
由于异常场景的结果可能各有不同，通常来说，数据驱动更适合正常场景的验证。
在Pytest中实现数据驱动有以下3种方式：
 1. 使用自定义Fixture函数的params参数进行参数化；
 2. 用例上使用`@pytest.mark.paramitrize`装饰器进行参数化；
 3. conftest.py文件中使用pytest_generate_tests钩子方法实现参数化。
### 7.4.1  使用Fixture函数进行数据驱动测试
由于Fixture函数的定位是测试准备和测试清理的用例辅助方法，因此当需要测试同一个测试流程，在不同的预置（测试准备）条件下的场景，我们可以使用自定义Fixture函数的params参数来实现。如，测试不同用户的下单流程，登录作为一个准备步骤，我们可以使用params指定多组用户数据，来使得使用该Fixture功能的测试用例每个数据各执行一次。
示例如下：
> 代码test_demo8.py内容
```python
import pytest
USERS = [
    {'username': '张三', 'password': '123456'},
    {'username': '李四', 'password': '000000'},
    {'username': '王五', 'password': '111111'},
]
@pytest.fixture(params=USERS)
def login(request):  # request是一个固定的参数，代表调用方
    # request.param即USERS中的每一条数据
    username= request.param.get('username')
    password= request.param.get('password')
    print(f'[登录] 用户名：{username} 密码：***')
def test_shopping(login):
    pass
```
由于test_shopping用例引用了一个参数化的Fixture函数，该用例会执行多次。
Fixture函数中的request参数，是Pytest一个内置的Fixture对象，通过request对象可以拿到许多有用的信息，如：
- request.fspath：执行测试起始路径
-  request.session：当前测试会话
- request.config：当前测试配置对象
- request.function：当前测试用例函数/方法
request对象更多的属性及方法可以在Fixture函数中使用print(dir(request))查看，或者参考Pytest官方文档。
### 7.4.2  使用参数化标记
在测试用例中，数据的丰富性是非常重要的。对于在测试步骤中需要使用不同的数据进行验证的，我们可以使用@pytest.mark.paramitrize标记来进行参数化。
例如：
> 代码test_demo9.py内容
```python
import pytest
def add(a, b):   # 被测函数
    return a + b
# 测试数据，也可以从文件中读取出来
DATA = [
    (1, 2, 3),  # 分别代表 a,b和期望结果，即验证1+2=3
    (0, 0, 0),
    (-1,-1,-2)
]
@pytest.mark.parametrize('item', DATA)  # 'item'是每组数据的变量名，相当于for item in DATA: ...
def test_add(item):  # 使用同名参数item来接收每一组数据
    a, b, excepted = item   # 利用自动解包将每一组的3个数分别赋值给a,b,excepted（代表期望结果）
    actual = add(a, b)   # 调用被测函数得到实际结果
assert excepted == actual  # 断言期望结果与实际结果相等
if __name__ == '__main__':
    pytest.main([__file__])
```
利用Python的自动解包特性，我们在遍历嵌套列表时，还可以直接使用3个变量来接收每一组数据，如：
```python
for a,b,excepted in DATA: …
```
同理，在参数化指定变量名时，也可以直接使用多个变量接收，示例如下：
```python
@pytest.mark.parametrize('a,b,excepted', DATA)   # 'a,b,excepted'要放同一个引号中
def test_add(a,b,excepted):  # 使用同名参数来接收每一组数据
    actual = add(a, b)   # 调用被测函数得到实际结果
    assert excepted == actual  # 断言期望结果与实际结果相等
```
在数据驱动测试中，一组数据即一条数据，我们还可以ids参数，对每组数据来指定识别ID，示例如下：
```python
IDS =  ['整数相加', '0相加', '负数相加']

@pytest.mark.parametrize('a,b,excepted', DATA, ids=IDS)
def test_add(a,b,excepted):  # 使用同名参数来接收每一组数据
    actual = add(a, b)   # 调用被测函数得到实际结果
    assert excepted == actual  # 断言期望结果与实际结果相等
```
Pytest的用例id默认安装ASCII编码，当使用中文作为用例ID标记时，可以使用钩子方法来解决unicode转义问题，在用例同级或上级conftest.py中添加如下代码：
```python
def pytest_collection_modifyitems(items):  # 使用钩子方法解决IDS中文转义问题
	    for item in items:
	        item.name = item.name.encode("utf-8").decode("unicode_escape")
	        item._nodeid = item.nodeid.encode("utf-8").decode("unicode_escape")
```
### 7.4.3  使用钩子方法生成多条用例
Pytest自带的钩子方法pytest_generate_tests，可以用来生成多条用例，使用示例如下：
在用例同级或上级conftest.py中添加两个钩子方法，一个用例自定义一个命令行参数，一个用例根据参数格式参数化生成用例。
```python
def pytest_addoption(parser):
    # 添加一个命令行参数--browser，支持传入多次，默认只运行chrome浏览器
    parser.addoption("--browser", action="append",default=['chrome'],
                     help="Browsers For Run Selenium")
def pytest_generate_tests(metafunc):
    if "browser" in metafunc.fixturenames:  # 当用例中使用了browser这个Fixture时
        # 根据browser参数列表，对browser这个Fixture函数进行参数化
        metafunc.parametrize("browser", metafunc.config.getoption("browser"))
```
用例内容如下：
文件test_demo10.py内容
```python
from selenium import webdriver

BROWSER_MAP = {  # 根据参数映射不同浏览器的WebDriver类
    'chrome': webdriver.Chrome,
    'firefox': webdriver.Firefox,
    'ie': webdriver.Ie,
    'Edge': webdriver.Edge,
    }
def test_baidu(browser):
    print(f'使用浏览器 {browser}执行测试')
    # 根据browser参数获取对应的WebDriver类（获取不到时使用Chrome），并实例化
    driver = BROWSER_MAP.get(browser.lower(), webdriver.Chrome)()
    driver.get('https://www.baidu.com')
    assert '百度' in driver.title
    driver.quit()
```
在命令行，用例所在目录下运行
pytest test_demo10.py -qs --browser=chrome --browser=firefox
便可以实现启动不同的浏览器来分别执行该用例。
## 7.5  用例组织及标记
不同于Unittest中使用TestSuite类组织一批测试用例，Pytest中主要通过遍历目录及子目录的方式来发现并收集用例。Pytest主要通过目录、用例名和标记来组织用例。
### 7.5.1  使用目录组织用例
例如，我们可以使用不同的子文件夹来存放不同模块的用例。
```
project
  - module1/
    test_api.py
  - module2/
test_web.py
```
我们可以将每个目录视为一个测试集，在命令行可以通过pytest命令指定一个或多个用例来执行一个或多个测试集，如：
```bash
$ pytest -qs project/module1 project/module2
```
### 7.5.2  使用独特的用例名来区分用例
除了使用目录来区分不同模块的用例之外，我们还可以在用例中使用不同的单词来区分用例，如使用_negative后缀来表示负向用例，用_prod后缀来表示生产环境的用例，用_ddt来表示数据驱动的用例等等。
在用例运行时可以在命令行使用-k参数加特定的单词来执行不同的用例，如
```
$ pytest -k prod
```
即执行用例名称中带有prod字样的用例。
### 7.5.3  Mark用例标记
另外，Pytest还提供了更灵活的用例标记（等同于tag），来标记不同的用例，在非严格模式下，我们可以使用任意的标记来归类用例，例如：
文件test_demo11.py内容
```python
import pytest

@pytest.mark.smoke
def test_1():
    pass
@pytest.mark.todo
def test_2():
    pass
@pytest.mark.todo
@pytest.mark.apitest
def test_3():
    pass
```
上例中的smoke、todo、apitest是我们自定义的标记，用来表示冒烟用例，待完成的用例、接口层用例。一个用例可以添加多个标记。
在命令行使用-m参数来指定标记运行，如：
```bash
$ pytest test_demo11.py -m smoke
```
或
```bash
$ pytest test_demo11.py -m "not todo"
```
> 注：随意使用标记会导致，不知道整个项目中到底有哪些标记，因此为注册的标记运行时会有警告信息，如PytestUnknownMarkWarning: Unknown pytest.mark.smoke，可以忽略，或者在配置文件pytest.ini中使用markers注册，如：

```ini
[pytest]
markers =
    smoke: smoke test case
    todo: test case under developing
    apitest: api test case
```
### 7.5.4  标记跳过和期望失败
对于尚不具备测试条件的，如功能尚未提测，用例尚未写完，不适用当前环境，Bug待解决等用例，我们可以使用skip标记暂时跳过，或使用skipif根据条件进行跳过。在Fixture或用例函数中，也开始使用pytest.skip()方法来实现在指定条件下跳过用例。
对于负向（就应该执行失败）的用例或已知问题，我们可以使用xfail标记用例为期望失败。对于负向用例，xfail是通过状态（即exceptedFailure符合预期的失败），而xpass则是失败状态（unexceptedSuccess不符合预期的成功）。同样也可以再Fixture或用例函数中使用pytest.xfail方法来在某些条件下期望失败。
使用示例如下：
> 代码test_demo12.py内容
```python
import sys
import pytest

DEBUG = True
@pytest.mark.skip(reason="功能还没开发好")
def test_1():
    pass
@pytest.mark.skipif(DEBUG is True, reason="DEBUG模式下不执行")
def test_2():
    pass
def test_3():
    if sys.platform.startswith("win"):
        pytest.skip('不支持Windows平台')
@pytest.mark.xfail(reason="已知失败")
def test_2():
    assert '1'+'2' == '3'
```
在执行时需要加上-rxXs参数，才能显示显示跳过skipped、期望失败xfailed、非期望成功xpass状态用例的额外信息。
## 7.6  日志、配置文件及钩子方法
Pytest支持在项目根目录（rootdir）下使用配置文件pytest.ini来完成一些定制。Hooks钩子方法则映射了Pytest搜集用例、执行用例、生成报告的完整过程，我们可以通过编写同名的钩子方法来修改和定制Pytest的执行流程。
### 7.6.1  使用日志
在项目中必要的输出信息可以帮助我们显示测试步骤的一些中间结果和快速的定位问题，虽然Pytest框架可以自动捕获print信息并输出屏幕或报告中，当时更规范的应使用logging的记录和输出日志。 相比print, logging模块可以分等级记录信息。
#### 日志等级配置
实用方法层、页面对象层、Fixture业务层、用例层都可以直接使用logging来输出日志, 使用方法。
```python
# test_logging.py
import logging
def test_logging():
    logging.debug('调试信息')
    logging.info('步骤信息')
    logging.warning('警告信息，一般可以继续进行')
    logging.error('出错信息')
    try:
       assert 0
    except Exception as ex:
        logging.exception(ex)  # 多行异常追溯信息，Error级别
    logging.critical("严重出错信息")
```
使用pytest运行不会有任何的log信息，因为Pytest默认只在出错的信息中显示WARNING以上等级的日志。 要开启屏幕实时日志，并修改log显示等级。
Log等级：NOTSET < DEBUG < INFO < WARNING(=WARN) < ERROR < CRITICAL
```ini
# pytest.ini
[pytest]
log_cli=True
log_cli_level=INFO
```
运行pytest test_logging.py，查看结果：
```
--------------------------------------------- live log call ----------------------------------------------
INFO     root:test_logging.py:5 步骤信息
WARNING  root:test_logging.py:6 警告信息，一般可以继续进行
ERROR    root:test_logging.py:7 出错信息
ERROR    root:test_logging.py:11 assert 0
Traceback (most recent call last):
  File "/Users/apple/Desktop/demo/test_logging.py", line 9, in test_logging
    assert 0
AssertionError: assert 0
CRITICAL root:test_logging.py:12 严重出错信息
```
由于日志等级设置的为INFO级别，因此debug的日志不会输出。
对于不同层日志级别的使用规范，可以在实用方法层输出debug级别的日志，如组装的文件路径，文件读取的数据，执行的sql，sql查询结果等等。 在PageObject层输出info级别的日志，如执行某个页面的某项操作等。 Fixtures层和用例层可以根据需要输出一些必要的info，warning或error级别的信息。
#### 日志格式配置
默认的日志格式没有显示执行时间，我们也可以自定义日志输出格式。
```ini
# pytest.ini
...
log_cli_format=%(asctime)s %(levelname)s %(message)s
log_cli_date_format=%Y-%m-%d %H:%M:%S
```
%(asctime)s表示时间，默认为Sat Jan 13 21:56:34 2018这种格式，我们可以使用log_cli_date_format来指定时间格式。
%(levelname)s代表本条日志的级别
%(message)s为具体的输出信息
再次运行pytest test_logging.py，显示为以下格式：
```bash
--------------------------------------------- live log call ----------------------------------------------
2019-11-06 21:44:50 INFO 步骤信息
2019-11-06 21:44:50 WARNING 警告信息，一般可以继续进行
2019-11-06 21:44:50 ERROR 出错信息
2019-11-06 21:44:50 ERROR assert 0
Traceback (most recent call last):
  File "/Users/apple/Desktop/demo/test_logging.py", line 9, in test_logging
    assert 0
AssertionError: assert 0
2019-11-06 21:44:50 CRITICAL 严重出错信息
```
更多日志显示选项
- %(levelno)s: 打印日志级别的数值
- %(pathname)s: 打印当前执行程序的路径，其实就是sys.argv[0]
- %(filename)s: 打印当前执行程序名
- %(funcName)s: 打印日志的当前函数
- %(lineno)d: 打印日志的当前行号
- %(thread)d: 打印线程ID
- %(threadName)s: 打印线程名称
- %(process)d: 打印进程ID
#### 日志文件配置
在pytest.ini中添加以下配置
```ini
...
log_file = logs/run.log
log_file_level = DEBUG
log_file_format = %(asctime)s %(levelname)s %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S
```
log_file是输出的文件路径，输入到文件的日志等级、格式、日期格式要单独设置。
> 注意：输出到文件的日志每次运行覆盖一次，不支持追加模式。
### 7.6.2  配置文件
如果使用配置文件，pytest.ini至少要有一个[pytest]段，否则后报错。常用的配置项如下：
- addopts：默认额外的参数；
- cache_dir：缓存目录，用于缓存上次执行失败的用例；
- markers：注册的自定义标记及说明；
- norecursedirs：不遍历的目录；
- testpaths：测试目录；
- python_files：测试脚本匹配规则，默认test_开头的py文件视为用例，如果有的测试脚本不是以test_开头，可以配置为pythonfiles = *；
- python_class：测试类匹配规则，默认Test开头的类视为测试类；
- python_functions：测试函数及测试方法匹配规则，默认test_开头的函数及方法视为测试用例；
- log_print：用例失败时是否显示相关日志；
- log_cli：配置为ture时开启命令行日志；
- log_file：配置日志文件路径，每次覆盖，不支持追加模式；
- log_cli_level/log_file_level：配置输出到命令行及文件的日志等级；
- log_cli_format/log_file_format：配置输出到命令行及文件的日志格式；
- log_cli_date_format/log_file_date_format：配置日志的日期格式。
- console_output_style：命令行输出样式，支持classic、progress、counts三种样式；
- filterwarnings：过滤的警告；
- xfail_strict：启用时，标记为xfail的用例通过时状态为Fail，否则为XPassed。
参考配置如下：

> 文件pytest.ini内容
```ini
[pytest]
addopts = -qrxXs
markers =
    smoke: smoke test case
    todo: test case under developing
    apitest: api test case
norecursedirs = .*
testpaths = testcases
python_files = test_*
python_classes =   Test*
python_functions = test_*
log_cli = true
log_cli_level = info
log_cli_format = %(asctime)s %(levelname)s %(message)s
log_cli_date_format = %Y-%m-%d %H:%M:%S
log_file = last_run.log
log_file_level = info
log_file_format = %(asctime)s %(levelname)s %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S
```
除Pytest系统自带的命令行参数（option）和配置项（ini）外，安装的三方包也会增加一些可用的Fixture、命令行参数及配置项。我们也可以使用Hooks钩子方法来自定义命令行参数及配置项。
### 7.6.3  Hooks钩子方法
Hooks钩子方法是指标准的执行流程中的每个步骤，支持用户编写同名的函数来“捎带执行”一些操作，或替换掉原有的执行步骤。钩子方法Pytest的另一大精髓及灵活之处。
Pytest中的钩子方法按不同阶段分以下6种类型：
- 启动时的钩子方法：如加载conftest.py文件及三方插件，解析命令行参数等；
- 初始化时的钩子方法：如添加命令行参数、添加钩子方法、配置、反配置、启动测试会话、结束会话等；
- 收集用例时的钩子方法：如启动搜集、忽略目录、遍历目录、遍历文件、生成用例、生成参数化id、修改条目、收集结束等等；
- 测试运行时的钩子方法：如启动测试、运行setup、执行用例、运行teardown、生成报告等；
- 生成测试结果时的钩子方法：如生成报告头、测试状态、命令行最终运行总结等；
- 调试/交互钩子方法：如内部出错、键盘终端等过程的钩子方法；
钩子方法需要在conftest.py文件中编写，具体的钩子方法名称可以参考Pytest官方文档API参考中Hooks一节。

> 注：所有用户自定义的钩子方法，如果返回了有效值（非None）则会导致系统的原方法不再执行。

Pytest钩子使用示例如下：

> 代码conftest.py部分内容
```python
from datetime import datetime

def pytest_addoption(parser):  # 初始化是的钩子放假，用来增加命令行参数
    # 添加命令行参数--send_email，执行时带上该参数则视为ture
    parser.addoption("--send_email", action="store_true", help="send email after test")
    # 添加邮件主题、收件人、正文配置项
    parser.addini('email_subject', help='test report email subject')
    parser.addini('email_receivers', help='test report email receivers')
    parser.addini('email_body', help='test report email body')
    
def pytest_configure(config):  # 初始化是的配置方法
    log_file = config.getini('log_file')  # 如果配置文件配置了log_file
    if log_file:
        # 使用当前时间格式化后log_file名称，替换原有的配置
        # 如果配置的是不带时间格式的如log_file=last_run.log，格式化后不变，
        # 如果配置log_file=%Y%m%d%H%M%S.log，格式后成当前时间
        config.option.log_file = datetime.now().strftime(log_file)
        
def pytest_terminal_summary(config):  # 生成报告时的命令行最终总结方法
    send_email = config.getoption("--send-email")
    email_receivers = config.getini('email_receivers').split(',')
    if send_email is True and email_receivers:
        log_file = config.getoption('log_file')
        email_subject = config.getini('email_subject') or 'TestReport'
        email_body = config.getini('email_body') or 'Hi'
        if email_receivers:
            print('发送邮件 ...')
```
上例中分别使用了pytest_addoption、pytest_configure和pytest_testminal_summary来添加自定义参数及选型、修改运行配置及在运行完后执行自定义操作。

> 注：钩子方法的名称和参数是固定的，非特殊情况不建议返回有效值来阻止Pytest原有过程执行。
## 7.7  Allure测试报告的使用
Allure是一款灵活、华丽的测试报告框架，支持按分类、测试套件、特性等多种展示方式，并支持多种语言及测试框架，如图7.1所示

图7.1  Allure测试报告

Allure报告中支持按不同的角度来查看用例的执行情况，报告包含以下几块：
- Overview：概览；
- Categories：失败用例分类；
- Suites：测手套件，对应pytest中的测试类；
- Graphs：图表，报告用例总体的通过状态，标记的不同严重等级和执行时间分布；
- Timeline：执行的时间线；
- Behaviors：BDD行为驱动模式，按史诗、功能、用户场景等来标记和组织用例；
- Packages：按包目录来查看用例。
- 
作为一款通用型的测试报告框架，Allure支持多种测试框架如Pytest、Behave、Nose、JUnit、TestNG、Spock、Cucumber等非常多的框架。
其官方文档地址为：https://docs.qameta.io/allure
### 7.7.1  Allure-Pytest安装方法
Pytest使用Allure报告我们需要安装以下两个工具：
 1. Allure-Pytest：Python三方包，用于运行Pytest用例时生成Allure测试报告数据；
 2.  allure-commandline （Allure命令行工具）：用于将Allure测试报告数据生成HTML报告。
Allure-Pytest可以在命令行通过pip install allure-pytest安装。
Allure命令行工具安装方法如下。
- MacOS系统：
```bash
$ brew install allure
```
- CentOS系统：
```bash
$ yum install allure
```
- Windows系统: 下载allure-commandline工具包, 下载后解压，进入bin目录，使用allure.bat即可，下载地址如下：
https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/。
> 注：解压后建议把解压后的文件夹的bin目录配置到环境变量PATH中去，以方便在命令行任意位置直接使用allure命令。
### 7.7.2  Allure报告的基本使用方法
在Pytest框架中使用Allure
 1. 执行Pytest测试并生成Allure报告数据
pytest . --alluredir=/reports/allure-data
执行该命令将为每条用例生成JSON格式的测试结果数据。
 2. 使用Allure命令行工具生成报告并启动
```
$ allure generate /reports/allure-data -o /reports/allure-report
```
执行该命令将根据生成的测试结果数据生产HTML格式的测试报告。如果测试报告目录/reports/allure-report已存在，则可以加上“--clean”参数来重新生成。
Allure生成的HTML（reports/allure-report/index.html）需要静态服务器才能正常打开（双击用浏览器打开index.html会加载不出报告数据）。可以使用以下命令打开测试报告：
```bash
$ allure open /reports/allure-report
```

> 注：由于PyCharm中集成了网页静态服务器，在Pycharm中是可以正常通过浏览器打开查看报告的。

也可以直接根据测试结果数据启动服务查看测试报告，命令如下：
```
$ allure serve /reports/allure-data
```
允许该命令会自动生成并打开HTML报告。
### 7.7.3  标记测试用例
Allure-pytest可以自动识别pytest用例的失败、通过、skip、xfail等各种状态原因，并提供更多额外的标记，来完善用例信息，常用标记如下：
- @allure.epics()：设置报告中用例所属史诗，即重大需求；
- @allure.feature()：设置报告中用例所属（所测）功能；
- @allure.story()：设置报告中用例所属（所测）用户场景；
- @allure.step()：设置报告中用例用例步骤，一般用于标记测试步骤函数；
- @allure.severity()：设置用例严重等级，TRIVIAL、NORMAL及CRITICAL；
- @allure.title()：设置用例标题；
- @allure.description()：设置用例描述；
- @allure.description_html()：设置用例HTML格式描述；
- @allure.attach.file()：在报告中为用例添加附件；
- @allure.link()：在报告中为用例添加链接；
- @allure.issue()：在报告中为用例添加关联Bug链接；
- @allure.testcase()：在报告中用例添加关联测试用例链接；
一些的常见的使用方式如下。
#### 标记测试步骤
```python
import allure
@allure.step('加法')
def add(x, y):
    return x+y
```
当用例调用该方法时，报告中会视为一个步骤，根据调用关系识别步骤的嵌套。
如果想要在步骤描述中添加函数的参数或在用例中使用，可以使用以下方式：
```python
import allure
def test_add():
    with allure.step('步骤1: 计算1+2'):
        s = 1 + 2
        assert s == 3
```
#### 为用例添加额外信息
为丰富报告内容，可以在Allure报告中为用例添加附件、标题、描述等信息，示例如下。
 1. 添加附件
例如为用例添加截图报告，使用方式如下：
```python
@allure.attach.file('snapshots/login_page.png', attachment_type=allure.attachment_type.PNG)
def test_login():
    pass
```
 2. 添加标题和描述
可以使用@allure.title来自定义报告中的用例标题，使用@allure.description来添加用例描述，示例如下：
```python
@allure.title("测试登录功能")
@allure.description('这只是一个Demo')
def test_login():
    pass
```
也可以使用allure_description_html()添加HTML格式的描述，示例如下：
```python
@allure.description_html('''<table>
<tr><td>用户</td><td>密码</td></tr>
<tr><td>张三</td><td>123456</td></tr>
</table>''')
def test_register():
    pass
```
 3. 添加链接、issue链接、用例链接
为增加用例与项目管理平台、用例管理平台或缺陷管理平台的管理，还可以在报告中为用例添加链接、Bug链接或用例链接，示例如下：
```python
@allure.link('http://...')
@allure.issue('B140', 'Bug描述')
@allure.testcase('http://...', '用例名称')
def test_login():
    pass
3.BDD模式组织用例
Allure-Pytest支持安装BDD（行为驱动开发）的理念按照史诗（Epic)、功能（Feature）及场景的方式来组织和运行用例，例如：
@allure.epic('后台管理系统v1')
@allure.feature('登录功能')
class TestLogin:
    @allure.story('管理员正常登录')
    def test_login_as_admin():
         pass
    @allure.story('普通用户正常登录')
    def test_login_as_user():
         pass
@allure.epic('后台管理系统v1')
@allure.feature('注册功能')
class TestRegister:
    @allure.story('正常新用户注册')
    def test_register():
        pass
```
在运行用例时，可以使“--allure-epics”、“ --allure-features”或“--allure-stories”参数来指定多个史诗、功能或场景运行，例如：
```bash
$ pytest . --allure-features 登录功能,注册功能
```
#### 标记用例严重级别（优先级）
Allure-Pytest还提供里TRIVAL（微小）、NORMAL（普通）、CRITICAL（严重）几个用例优先级，使用方式如下：
```python
@allure.severity(allure.severity_level.TRIVIAL)
def test_a():
    pass
@allure.severity(allure.severity_level.NORMAL)
def test_b():
    pass
@allure.severity(allure.severity_level.CRITICAL)
def test_c():
    pass
```
在命令行可以通过以下方式选择优先级执行：
```bash
$ pytest .--allure-severities normal,critical
```
> 注：由于按严重级别来评价用例有点不太符合本地习惯，笔者更习惯使用插件pytest-level来标记用例优先级。
## 7.8  Pytest其他插件的使用
处理随处可用Fixture方法，Hooks灵活的定制之外，丰富的插件也是Pytest吸引人的特性之一。常用的插件如下：
- pytest-html：生成HTML测试报告；
- allure-pytest：身材漂亮的Allure测试报告数据；
- pytest-check：复合断言，检查多个断言而不会中断；
- pytest-rerunfailures：用例失败时自动重试；
- pytest-timeout：限定用例执行时间；
- pytest-xdist：多进程并行执行用例；
- pytest-level：标记用例等级；
- pytest-owner：标记用例归属人；
- pytest-ordering：标记用例执行顺序；
- pytest-base-url：根据base_url配置来测试不同的环境；
- pytest-ini：复用pytest.ini进行多环境配置；
- pytest-datadir：配置项目数据目录；
- pytest-variables：[数据分离]使用JSON或YAML文件的测试数据；
- pytest-selenium：[框架]跨浏览器执行Selenium用例;
- pytest-bdd：[框架]执行BDD模式的用例（类似Behave）；
- pytest-requests：[框架]执行yaml格式的接口用例（类似HttpRunner）。
### 7.8.1  插件pytest-html的使用
pytest-html是Pytest生成HTML报告的一款插件，可以生成单文件报告，支持显示环境信息，用例筛选，可以通过Hooks方法定制报告标题、概要、内容等，另外结合pytest-selenium用例失败时可以自动生成截图。
- 官方文档：https://pytest-html.readthedocs.io/en/latest/user_guide.html。
- 安装方法：`pip install pytest-html`
- 使用方法：命令行运行`pytest –html=report.html`
- 生成单文件报告：命令行运行`pytest –html=report.html –self-contained-html`
> 注：默认pytest-html报告为了准寻邮件安全策略，会生成报告HTML文件及样式文件两个文件，不方便发送，单独只发送报告HTML文件会导致样式缺失。可以使用—self-contained-html参数生成一个内置样式的单文件报告，通过邮件附件发送。
### 7.8.2  插件pytest-check的使用
使用assert断言时，当某一条断言失败后，该条用例即视为失败，后面的断言不会再进行判断。有时我们需要每一次可以检查所有的检查点，输出所有断言失败项。此时我们可以使用pytest-check插件进行复合断言。插件使用方式如下
- 官方文档：https://pypi.org/project/pytest_check/
- 安装方法：`pip install pytest-check。`
所谓复合断言即，当某条断言失败后仍继续检查下面的断言，最后汇总所有失败项。
pytest-check使用方法
```python
import pytest_check as check

# ...
check.equal(200, es_dict.get("code"))
check.equal("添加卡成功",res_dict.get("msg"))
check.is_true(res_dict.get('success'))
check.is_true(db.check_card(card_number))
```
除此外常用的还有:
- check.is_false()：断言值为False
- check.is_none(): 断言值为None
- check.is_not_none()：断言值不为None
### 7.8.3  插件pytest-rerunfailures的使用
在自动化测试特别是UI自动化测试中，不稳定用例（时而成功，时而失败）是一个让人头疼的问题。对于不稳定用例最简单的处理方式就是，失败重跑（rerun）。使用pytest-rerunfailures插件可以为所有用例或个别用例添加失败后重跑的机制。插件使用方式如下。
- 官方文档：[https://pypi.org/project/pytest-rerunfailures/](https://pypi.org/project/pytest-rerunfailures/)
- 安装方法：`pip install pytest-rerunfailures`
- 全局使用：`pytest --reruns 5 --reruns-delay 1`
- 单独使用：
```python
@pytest.mark.flaky(reruns=5, reruns_delay=2)
def test_example():
    import random
    assert random.choice([True, False])
```
### 7.8.4  插件pytest-timeout的使用
为防止有些用例在执行时卡住，为用例设置超时时间往往是必要的，我们可以使用pytest-timeout插件来为所有用例或者单个用例配置超时时间。插件使用方式如下。
- 官方文档：
- 安装方法：`pip install pytest-timeout`
- 全局使用：`pytest --timeout=300`
- 配置方法：
```ini
[pytest]
timeout = 300
- 单独使用：
@pytest.mark.timeout(60)
def test_foo():
    pass
```
### 7.8.5  插件pytest-xdist的使用
如果想要并行执行用例以提高用例执行效率，则可以使用pytest-xdist插件。使用该插件可以启动多个进程，平均分配用例以并行执行。由于用例分配具有随机性，因此要求用例之间没有顺序依赖关系。插件使用方法如下。
- 官方文档：[https://pypi.org/project/pytest-xdist/](https://pypi.org/project/pytest-xdist/)
- 安装方法：`pip install pytest-xdist`
- 使用方法：`pytest -n 3`
### 7.8.6  插件pytest-level的使用
使用pytest-level插件可以为用例标记等级（优先级），并指定运行相应等级以上的用例（level数字小的等级较高）。
- 官方文档：https://pypi.org/project/pytest-timeout/
- 安装方法： `pip install pytest-level`
- 使用方法：
```python
@pytest.mark.level(1)
def test_basic_math():
    assert 1 + 1 == 2
    
@pytest.mark.level(2)
def test_intermediate_math():
    assert 10 / 2 == 5
    
@pytest.mark.level(3)
def test_complicated_math():
    assert 10 ** 3 == 1000
```

运行方法：`python -m pytest --level 2`

### 7.8.7  插件pytest-ordering的使用
有时候我们需要使用例按一定顺序执行，此时可以使用pytest-ordering这个插件，并标记用例执行顺序，使用方式如下：
- 官方文档：[https://github.com/ftobia/pytest-ordering](https://github.com/ftobia/pytest-ordering)
- 安装方法：`pip install pytest-ordering`
- 使用方法：
```python
import pytest

@pytest.mark.run(order=2)
def test_foo():
    assert True
    
@pytest.mark.run(order=1)
def test_bar():
    assert True
```
### 7.8.8  插件pytest-ini的使用
当我们需要测试多套环境时，可以使用pytest-ini这个插件。该插件复用pytest.ini配置并增加了全局变量配置，各个环节配置，并支持使用任意自定义端配置，使用方式如下：
- 官方文档：https://pypi.org/project/pytest-ini
- 安装方法：`pip install pytest-ini`
- 使用方法：
> pytest.ini文件配置
```ini
[pytest]
env = prod

[email]
smtp_host = smtp.sina.com
smtp_user = test_results@sina.com
smtp_pwd = ${SMTP_PWD}

[global]
user = user001

[test]
user = test001

[prod]
user = ${USER}
is_male = true
age = 18
score = 87.5
hobbies = ["sing", "dance"]
base_url = https://httpbin.org
url = %(base_url)s/get?token=%(token)s
token =
```
使用示例如下：
```python
import os
def test_get_vars_from_pytest_ini(env_vars):
    # 获取当前环境变量'user'值
    user = env_vars.get('user')
    # 直接获取系统环境变量'USER'值
    user2 = env_vars.get('USER')
    assert user == os.getenv('USER')
    assert user2 == os.getenv('USER')
```

### 插件pytest-faker的使用
构造测试数据是测试过程中一个常见的需求，比如随机的人名、Email、地址、合法的时间戳等。基于三方库Faker的Pytest-faker插件来快速生成各种数据。
- 官方文档：https://faker.readthedocs.io
- 安装方法：`pip install pytest-faker`
使用示例如下：

```python
@pytest.fixture(scope='session')
def faker_locale(faker_locale):
    return 'zh_CN'  # 配置语言
    
def test_faker(faker):
    print('随机名称', faker.name())
    print('随机用户名', faker.user_name())
    print('随机手机号', faker.phone_number())
    print('随机密码', faker.password())
    print('随机地址', faker.address())
    print('随机Email地址', faker.email())
    print('随机日期', faker.date())
    print('随机标准日期时间', faker.iso8601())
    print('随机经纬度', faker.latlng())
```
执行用例，运行结果如下：
```
随机名称 郭建华
随机用户名 pzou
随机手机号 13010864656
随机密码 U8Ng4p$d+1
随机地址 湖南省云县新城杨路j座 903547
随机Email地址 juanluo@example.com
随机日期 1973-06-22
随机标准日期时间 1990-04-28T16:29:34
随机经纬度 (Decimal('36.5451985'), Decimal('112.259158'))
更多使用方式可以参考官方文档。
```
## 7.9  小结
本节主要讲述了Python常见的测试框架及Pytest测试框架的基本使用，Pytest提供了非常灵活的用例组织和运行方式，同时可以通过Hooks方法来定制和修改Pytest的执行流程。Fixture函数是Pytest测试框架的精髓，无论作为测试准备、测试清理方法还是其他辅助方法或实用工具函数，Fixture以其方便易用性，大大增强了用例编写的灵活性。

