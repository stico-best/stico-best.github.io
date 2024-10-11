---
title: Python+Selenium脚本
abbrlink: e4f344ea
date: 2022-12-14 17:50:51
tags:
---

**python + selenium UI自动化测试脚本原创**

<!--more-->

# Driver的封装

针对Webhis-baoji的UI自动化脚本，由于其本地URL是始终固定不变的，所需调用的driver也是不变的，故欲打算采用“单例设计模式”。

## 单例设计模式

单例（Singleton）模式的定义：指一个类只有一个实例，且该类能自行创建这个实例的一种模式。例如，Windows 中只能打开一个任务管理器，这样可以避免因打开多个任务管理器窗口而造成内存资源的浪费，或出现各个窗口显示内容的不一致等错误。

在计算机系统中，还有 Windows 的回收站、操作系统中的文件系统、多线程中的线程池、显卡的驱动程序对象、打印机的后台处理服务、应用程序的日志对象、数据库的连接池、网站的计数器、Web 应用的配置对象、应用程序中的对话框、系统中的缓存等常常被设计成单例。

单例模式是设计模式中最简单的模式之一。通常，普通类的构造函数是公有的，外部类可以通过“new 构造函数()”来生成多个实例。但是，如果将类的构造函数设为私有的，外部类就无法调用该构造函数，也就无法生成多个实例。这时该类自身必须定义一个静态私有实例，并向外提供一个静态的公有函数用于创建或获取该静态私有实例。

单例模式有 3 个特点：

- 单例类只有一个实例对象
- 该单例对象必须由单例类自行创建
- 单例类对外提供一个访问该单例的全局访问点

------

## UI-WEBHIS目录结构

本章主在展示driver类的封装，仅以挂号功能作为展示及验证。

```markdown
webhis-baoji(主目录)/
│
├── tool(工具子目录)/
│   └── driver.py(driver的封装)
│   └── function.py(函数的调用)
│
└── script(脚本子目录-功能)/	# 以下为当前已实现功能脚本代码
    └── login.py(登录-功能)
    └── registered.py(门诊急挂号-功能)
    └── orders_entry.py(门诊医嘱录入-功能)
    └── >>>...<<<
```

------

## driver.py

所需导包：

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
import pathlib
```

封装：

```python
"""Webhis-Baoji Chrome浏览器驱动 配置及调用"""

"""-----多个py文件共同调用同一个webhis-driver时, 使用以下方法-----"""
class DriverSingleton:
    _instance = None

    @staticmethod
    def getInstance():
        if DriverSingleton._instance == None:
            options = webdriver.ChromeOptions()
            options.add_experimental_option('excludeSwitches', ['enable-logging'])
            options.add_experimental_option("excludeSwitches", ["enable-automation"])
            options.add_experimental_option("useAutomationExtension", 'False')
            options.add_argument("--user-data-dir=" + str(pathlib.Path.home()) + r"\AppData\Local\Google\Chrome\seleniumTest")
            DriverSingleton._instance = webdriver.Chrome(service=Service(r'C:\Program Files\Google\Chrome\Application\chromedriver.exe'), options=options)
        return DriverSingleton._instance

"""-----单个py文件单独调用webhis-driver时, 使用以下方法-----"""
"""
options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option("useAutomationExtension", 'False')
options.add_argument("--user-data-dir=" + str(pathlib.Path.home()) + r"\AppData\Local\Google\Chrome\seleniumTest")

# 创建 WebDriver 对象, 指明使用chrome浏览器驱动
driver = webdriver.Chrome(service=Service(r'C:\Program Files\Google\Chrome\Application\chromedriver.exe'), options=options)
"""
```



在这个类中，_instance是一个类变量，用于存储DriverSingleton的实例。getInstance是一个静态方法，用于获取DriverSingleton的实例。

- 如果_instance为None，则创建一个新的webdriver.Chrome实例并将其存储在_instance中。

(webdriver.Chrome实例使用了一些特定的选项，例如禁用日志记录，禁用自动化，禁用自动化扩展，并设置用户数据目录)

- 如果_instance不为None，则直接返回_instance。这就保证了无论何时调用getInstance方法，都会返回同一个webdriver.Chrome实例。

这种设计模式在需要确保类只有一个实例，并且需要全局访问点的情况下非常有用。在这个情况下，webdriver.Chrome实例在整个应用程序中都使用同一个浏览器实例。

------

## function.py

所需导包：

```python
import sys
sys.path.append('C:\webhispython\Webhis-test\webhis-baoji')

import inspect
import time
from selenium import webdriver
from selenium.webdriver import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException
```

------

### 登录-函数模块

```python
"""---登录 模块---"""
# login(driver， 用户名, 密码, 登录地点, 登录科室)
def login(driver, username, password, xpath_role, xpath_location):  # login(用户名, 密码, 角色, 登录地点)
    time.sleep(5)  # 停止5秒，等待浏览器加载页面

    """系统登录首页"""
    driver.find_element(by=By.ID, value='login_username').send_keys(username)  # 用户名
    driver.find_element(by=By.ID, value='login_password').send_keys(password)  # 密码
    driver.find_element(by=By.ID, value='nextBtn').click()
    driver.implicitly_wait(5)

    """登录详细选项页"""
    # 角色下拉框选值
    item = driver.find_element(by=By.ID, value='entryForm_roleMstrId')
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    driver.find_element(by=By.XPATH, value=xpath_role).click()  # 选择角色
    driver.implicitly_wait(5)

    # 科室下拉框选值
    item = driver.find_element(by=By.ID, value='entryForm_locationMstrId')
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    driver.find_element(by=By.XPATH, value=xpath_location).click()  # 选择科室
    driver.implicitly_wait(5)

    # 点击登录跳转
    driver.find_element(by=By.ID, value='loginBtn').send_keys(Keys.ENTER)
    driver.implicitly_wait(5)

    time.sleep(3)
    if True:
        function_name = inspect.currentframe().f_code.co_name
        print(f"函数 {function_name} 执行完成")
```



------

### 门诊急挂号-函数模块

```python
"""---门急诊挂号 模块---"""
# registration(driver, 患者门诊病案号, 挂号资源代码)
def registration(driver, record_number, registration_resources_code):  # 门急诊挂号
    time.sleep(3)  # 停止5秒，等待浏览器加载页面

    """进入门急诊挂号页面"""
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-trigger']")  # 展开大类菜单
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-submenu-title']//*[text()='门诊医生站']")  # 大类菜单
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-item ant-menu-item-selected']")  # 子类菜单(门诊医生站)
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='ant-tabs-nav-list']//*[text()='门诊急挂号']")  # 进入"门急诊挂号"页面
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-trigger']")  # 缩回大类菜单
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    # 显式等待, 至"门急诊挂号"ID加载后再执行
    wait = WebDriverWait(driver, 10)
    wait.until(EC.presence_of_element_located((By.ID, 'rc-tabs-0-tab-5603808')))

    """WPB选择患者"""
    time.sleep(2)
    item = driver.find_element(by=By.XPATH, value="//input[contains(@placeholder,'请输入姓名/手机号/病案号')]")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    # 输入所需患者门诊病案号, 唯一身份对应信息
    driver.find_element(by=By.XPATH, value="//input[contains(@placeholder,'请输入姓名/手机号/病案号')]").send_keys(record_number)
    driver.implicitly_wait(10)  # 待WPB搜索框加载并检索出send的患者数据
    time.sleep(2)

    # 定位第一条患者并点击选中
    item = driver.find_element(by=By.XPATH, value="//*[@class='ant-table ant-table-layout-fixed ant-table-fixed-header']//*[@class='ant-table-tbody']/tr[2]")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(2)

    """选择挂号资源"""
    time.sleep(2)
    item = driver.find_element(by=By.XPATH, value="//*[@id='rc-tabs-0-panel-5603808']/div/div/div/div/div/div/div[1]/form/div/div[95]/div/div/div/span/div/div/div[2]/input")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    # 输入所需挂号资源代码, 唯一代码对应信息
    driver.find_element(by=By.XPATH, value="//*[@id='rc-tabs-0-panel-5603808']/div/div/div/div/div/div/div[1]/form/div/div[95]/div/div/div/span/div/div/div[2]/input").send_keys(registration_resources_code)
    driver.implicitly_wait(5)  # 待资源列表加载并检索出send的资源数据
    time.sleep(3)
    # 定位第一条资源并点击选中
    driver.find_element(by=By.XPATH, value="//tr[@data-row-key='0' and contains(@class, 'ant-table-row-selected')]").click()
    driver.implicitly_wait(5)

    """账户支付"""
    time.sleep(2)
    item = driver.find_element(by=By.XPATH, value="//*[@class='regnRegistration_btnGroup_aOve9']//*[contains(text(),'账户支付')]")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    driver.implicitly_wait(10)

    try:
        driver.find_element(By.XPATH, "//*[text()='挂号成功！']")
        driver.implicitly_wait(20)
        print("--- ATTENTION: 患者挂号成功! ---")
    except NoSuchElementException:
        print("--- ATTENTION: 函数正常运行 但未完成挂号, 注意网络延迟并重试! ---")

    time.sleep(3)
    if True:
        function_name = inspect.currentframe().f_code.co_name
        print(f"函数 {function_name} 执行完成")
```



------

### 门诊医嘱录入-函数模块

```python
"""---门诊医嘱录入 模块---"""
# outpatient_orders(drivr， 已有挂号患者， pickshell药品， 数量， 剂量， 用法， 频次, 周期)
def outpatient_orders(driver, patient_msg, order_insert, quantity, dosage, usage, frequency, cycle):
    time.sleep(5)  # 停止5秒，等待浏览器加载页面

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-trigger']")  # 展开大类菜单
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-submenu-title']//*[text()='门诊医生站']")  # 大类菜单
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-item ant-menu-item-selected']")  # 子类菜单(门诊医生站)
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='ant-tabs-nav-list']//*[text()='医生看诊']")  # 进入"医生看诊"页面
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-trigger']")  # 缩回大类菜单
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//button[@class='ant-btn ant-btn-link ant-btn-icon-only']")  # 患者列表刷新
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    driver.implicitly_wait(5)
    driver.find_element(by=By.XPATH, value=patient_msg).click()  # 选中患者列表对应挂号患者
    time.sleep(5)

    # 显式等待, 医嘱pickshell加载
    wait = WebDriverWait(driver, 10)
    wait.until(EC.presence_of_element_located((By.XPATH,  "//*[@class='kt-datapicker kt-datapicker-singleRow']//*[@placeholder='请输入']")))

    # 定位并点击医嘱pickshell -> 传参, 输入医嘱名称
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-datapicker kt-datapicker-singleRow']//*[@placeholder='请输入']")
    webdriver.ActionChains(driver).move_to_element(item).click().send_keys(order_insert).perform()
    time.sleep(2)

    # 选中列表第一条并点击
    driver.find_element(By.XPATH, '//tr[@data-row-key="0" and contains(@class, "ant-table-row ant-table-row-level-0 kt-table-row-oddeven kt-row-selected-order-picker")]').click()
    time.sleep(2)

    """患者当前医嘱列表重复开立弹窗判断"""
    try:
        wait = WebDriverWait(driver, 3)
        wait.until(EC.presence_of_element_located((By.XPATH, "//*[text()='已经开立，是否继续？']")))

        confirm_button = driver.find_element(by=By.XPATH, value="//*[text()='确定']")
        confirm_button.click()
    except:
        print("---该医嘱可为此患者开立---")
        pass

    """----------数量----------"""
    item = driver.find_element(by=By.ID, value="orderQtyId1")
    webdriver.ActionChains(driver).move_to_element(item).send_keys(quantity).perform()
    driver.implicitly_wait(5)
    time.sleep(1)

    """----------剂量----------"""
    # '剂量'框存在未知异常，无法定位元素
    # 又因前端约束 医嘱落入列表后光标自动定位到'数量'框，故使用ActionChains方法模拟TAB操作跳到'用法'框
    actions = ActionChains(driver)
    actions.send_keys(Keys.TAB, Keys.TAB).perform()
    actions.send_keys(dosage).perform()
    driver.implicitly_wait(5)
    time.sleep(3)

    """----------用法----------"""
    # //*[@class='kt-editable-cell tabIndex4']//input
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    time.sleep(3)

    # 采用上述处理'剂量'框异常的方法后，又有未知原因 click定位到'用法'框后，光标会重新回到'剂量'框
    # 故使用两次click定位'用法'框方法，来避免光标异常定位问题，经测试此方法有效
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
    webdriver.ActionChains(driver).move_to_element(item).send_keys(usage).perform()
    time.sleep(1)
    webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.ENTER).perform()

    # 未知原因 用法框send_key后仍会再弹出下拉框，故设计为传参后立刻点击一处不可编辑区别，强制退出该输入框
    time.sleep(1)
    driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-logo-icon']").click()
    driver.implicitly_wait(5)
    time.sleep(3)

    """----------频次----------"""
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex5']")
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    driver.implicitly_wait(5)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex5']")
    webdriver.ActionChains(driver).move_to_element(item).send_keys(frequency).perform()
    time.sleep(1)
    webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.ENTER).perform()

    # 未知原因 频次框send_key后仍会再弹出下拉框，故设计为传参后立刻点击一处不可编辑区别，强制退出该输入框
    time.sleep(1)
    driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-logo-icon']").click()
    driver.implicitly_wait(5)
    time.sleep(3)

    """----------周期----------"""
    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
    for _ in range(4):
        webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.BACKSPACE).perform()
    time.sleep(1)

    item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
    webdriver.ActionChains(driver).move_to_element(item).send_keys(cycle).perform()
    time.sleep(3)

    """医嘱保存"""
    driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLineItem_RITqA diagnosisAnYang_iconCursor_Jv_RO']//*[text()='保存']").click()

    """患者医嘱重复预警弹窗判断"""
    try:
        wait = WebDriverWait(driver, 3)
        wait.until(EC.presence_of_element_located((By.XPATH, "//*[@class='ant-modal']//*[text()='医嘱重复报警']")))

        confirm_button = driver.find_element(by=By.XPATH, value="//*[text()='强制保存']")
        webdriver.ActionChains(driver).move_to_element(confirm_button).click().perform()
    except:
        print("---该医嘱可为此患者开立---")
        pass
    
    time.sleep(1)

    """医嘱保存成功弹窗校验"""
    try:
        item = driver.find_element(By.XPATH, "//*[text()='医嘱保存成功']")
        webdriver.ActionChains(driver).move_to_element(item).perform()
        driver.implicitly_wait(5)
        print("--- ATTENTION: 医嘱保存成功! ---")
    except NoSuchElementException:
        print("--- ATTENTION: 函数正常运行 但未成功保存医嘱，注意网络延迟并重试! ---")

    time.sleep(3)
    if True:
        function_name = inspect.currentframe().f_code.co_name
        print(f"函数 {function_name} 执行完成")
```



------

### WPB选择患者-函数模块

```python
"""---WPB选择患者 模块---"""
# wpb_select(driver, 患者门诊病案号)
def wpb_select(driver, record_number):
    time.sleep(2)

    item = driver.find_element(by=By.XPATH, value="//input[contains(@placeholder,'请输入姓名/手机号/病案号')]")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    # 输入所需患者门诊病案号, 唯一身份对应信息
    driver.find_element(by=By.XPATH, value="//input[contains(@placeholder,'请输入姓名/手机号/病案号')]").send_keys(record_number)
    driver.implicitly_wait(10)  # 待WPB搜索框加载并检索出send的患者数据
    time.sleep(2)

    # 定位第一条患者并点击选中
    item = driver.find_element(by=By.XPATH, value="//*[@class='ant-table ant-table-layout-fixed ant-table-fixed-header']//*[@class='ant-table-tbody']/tr[2]")
    webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
    driver.implicitly_wait(5)
    time.sleep(2)

    time.sleep(3)
    if True:
        function_name = inspect.currentframe().f_code.co_name
        print(f"函数 {function_name} 执行完成")
```



------

# Webhis-门诊急挂号-流程测试脚本

{% asset_img Outpatient_registration.png  %}

## registration.py

### 所需导包

```python
import time
import sys
sys.path.append('C:\webhispython\Webhis-test\webhis-baoji')

from tool.driver import DriverSingleton  # 从tool目录driver.py下，调用公用driver驱动
from tool.function import login, registration  # 从tool目录function.py下，调用login()和registration()函数
```

### 主函数

```python
driver = DriverSingleton.getInstance()  # 从driver.py下调用DriverSingleton.getInstance()方法以打开同一个driver实例
driver.get(url="http://10.227.16.12/login")
driver.maximize_window()
# time.sleep(5)  # 停止5秒，等待浏览器加载页面


if __name__ == "__main__":
    login(driver, 'RZX', '1', "//*[text()='门急诊医生']", "//*[text()='神经内科二科门诊']")  # 用户名, 密码, 登录地点, 登录科室
    registration(driver, '0000000034', 'ZZHRZX')  # '测试01'门诊病案号'0000000034', '专家号-RZX'挂号资源代码'ZZHRZX'
    time.sleep(3)
    print("Success!")
    driver.quit()

```

1. 调用 driver.py 中 DriverSingleton 方法下的浏览器驱动 ，作为 driver 被脚本调用
2. 主函数走 function.py 中的 login() 函数进行登录
3. 再走 function.py 中的 registration() 函数进行门诊挂号

登录函数 login() 传参： 用户名username， 密码password，登录角色xpath_role，登录地点xpath_location

挂号函数 registration() 传参： 门诊患者病案号record_number， 挂号资源代码registration_resources_code

------

# Webhis-门诊单个医嘱录入-流程测试脚本

{% asset_img Outpatient_doctor_station.png  %}

## orders_entry.py

### 所需导包

```python
import sys
sys.path.append('C:\webhispython\Webhis-test\webhis-baoji')
import time

from tool.driver import DriverSingleton  # 从tool目录driver.py下，调用公用driver驱动
from tool.function import login, outpatient_orders  # 从tool目录function.py下，调用login()和outpatient_orders()函数
```

### 主函数

```python
driver = DriverSingleton.getInstance()  # 从driver.py下调用DriverSingleton.getInstance()方法以打开同一个driver实例
driver.get(url="http://10.227.16.12/login")
driver.maximize_window()
time.sleep(5)  # 停止5秒，等待浏览器加载页面


if __name__ == "__main__":
    # driver， 用户名, 密码, 登录地点, 登录科室
    login(driver, 'RZX', '1', "//*[text()='门急诊医生']", "//*[text()='神经内科二科门诊']")

    # drivr， 已有挂号患者， pickshell药品， 数量， 剂量， 用法， 频次， 周期
    outpatient_orders(driver, "//*[text()='测试01 13:54 知名专家号']", "布洛芬缓释胶囊", "2", "10", "口服", "qd8", "3")

    time.sleep(3)
    print("Success!")
    driver.quit()
```

1. 调用 driver.py 中 DriverSingleton 方法下的浏览器驱动 ，作为 driver 被脚本调用
2. 主函数走 function.py 中的 login() 函数进行登录
3. 再走 function.py 中的 outpatient_orders() 函数进行医嘱录入

登录函数 login() 传参：driver，用户名username，密码password，登录角色xpath_role，登录地点xpath_location

医嘱录入函数 outpatient_orders() 传参：driver，已有挂号患者patient_msg，pickshell药品order_insert，数量quantity，剂量dosage，用法，频次frequency，周期cycle

------

# Webhis-门诊自定义多医嘱录入-流程测试脚本详解

## 前情摘要

​		该脚本利用登录、挂号、医嘱等函数，selenium、ActionChains、webdriver等将前面所展示的内容做了一个整合，以实现从编译器终端录入多种医嘱的效果，代码量将近四百行，多以调用函数的方式运行，总耗时大概三周的闲暇时间搞定。

​		由于 宝鸡项目Web端 门诊医生站开立医嘱 会存在很多特殊情况，不同的医嘱类型、不同的维护、不同的页面参数，系统参数都会导致Web前端页面的效果不同，会出现不同的弹窗等，针对多种情况去做脚本的判断部分没有意义，故此脚本为UI自动化测试脚本的终章。

​		以下详解部分，也将更详细的切分脚本的代码，附上更多的效果图，以便更好的理解其实现的流程及原理。

------

## 所需导包

```python
import sys
sys.path.append('C:\webhispython\Webhis-test\webhis-baoji')

import pathlib
import inspect
import time
from selenium import webdriver
from selenium.webdriver import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
```

------

## driver的调用及设置

- 对新调用的 Google 浏览器的启动参数进行设置：

```python
options = webdriver.ChromeOptions()
```

------

使用Cursor的编译器执行脚本，脚本运行过程中，终端会时不时弹出一些ERROR信息，很影响脚本中的print打印内容显示。

例如：[ERROR:ssl_client_socket_impl.cc(968)] handshake failed; returned -1, SSL error code 1, net_error -101

这个错误信息是由于 SSL 握手失败导致的，宝鸡Webhis系统由本地启动，通过VPN访问远程服务器，SSL证书问题等并不需要着重考虑。

故使用以下两项设置，以强制关闭诸如此类的警告打印：

- 设置 Chrome 的日志级别，3表示只记录ERROR级别的日志：

```python
options.add_argument('--log-level=3')
```

- 禁用了 Chrome 的日志记录功能。这可以使得 Chrome 在运行时更加安静，不会输出不必要的日志信息：

```python
options.add_experimental_option('excludeSwitches', ['enable-logging'])
```

------

- 该设置禁用了 Chrome 的自动化控制提示。在使用 Selenium 控制 Chrome 时，浏览器顶部的 "Chrome正在受到自动软件的控制" 这个提示将不会出现：

```python
options.add_experimental_option("excludeSwitches", ["enable-automation"])
```

------

- 该设置禁用了 Chrome 的自动化扩展。这可以防止 Chrome 在启动时加载不必要的扩展，从而提高启动速度：

```python
options.add_experimental_option("useAutomationExtension", 'False')
```

------

- 这个设置约束了 Chrome 的本地数据目录。这意味着 Chrome 将会在这个目录下存储用户的个人信息，如浏览历史、书签等。这个选项可以在多次运行 Selenium 时保持相同的浏览环境：

```python
options.add_argument("--user-data-dir=" + str(pathlib.Path.home()) + r"\AppData\Local\Google\Chrome\seleniumTest")
```

------

- 创建 WebDriver 对象, 指明使用chrome浏览器驱动，并进行以上options的设置：

```python
driver = webdriver.Chrome(service=Service(r'C:\Program Files\Google\Chrome\Application\chromedriver.exe'), options=options)
```

------

- 调项目环境URL，启动浏览器自动化脚本控制：

```python
driver.get(url="http://10.227.16.12/login")
driver.maximize_window()
time.sleep(3)  # 强制停止5秒，等待浏览器加载页面
```

------

## 登录-函数模块

项目系统登录页面有主页面和子页面之分，主页面输入登录账户密码，子页面选择登录人的角色及登录地点。

登录函数：

```python
def login(driver, username, password, xpath_role, xpath_location):  # login(用户名, 密码, 角色, 登录地点)
```

登录主页面：

```python
print("脚本已进入 [登录菜单] ")
"""系统登录首页"""
driver.find_element(by=By.ID, value='login_username').send_keys(username)  # 用户名
driver.find_element(by=By.ID, value='login_password').send_keys(password)  # 密码
driver.find_element(by=By.ID, value='nextBtn').click()
driver.implicitly_wait(5)
```

{% asset_img 登录主页面.png  %}

登录子页面：

```python
"""登录详细选项页"""
print("脚本已进入 [登录子菜单] ")
# 角色下拉框选值
item = driver.find_element(by=By.ID, value='entryForm_roleMstrId')
webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
driver.find_element(by=By.XPATH, value=xpath_role).click()  # 选择角色
driver.implicitly_wait(5)

# 科室下拉框选值
item = driver.find_element(by=By.ID, value='entryForm_locationMstrId')
webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
driver.find_element(by=By.XPATH, value=xpath_location).click()  # 选择科室
driver.implicitly_wait(5)

# 点击登录跳转
driver.find_element(by=By.ID, value='loginBtn').send_keys(Keys.ENTER)
driver.implicitly_wait(5)

```

{% asset_img 登录子页面.png  %}

------

## 门诊多医嘱录入-函数模块 [重]

### 整体结构概述

医嘱录入的过程本身就是顺序执行到底的，所以这部分脚本代码也保持顺序执行的逻辑。本意就是想要设计一个能够自主任意的录入多次多种医嘱的脚本，所以设计了 函数嵌套的方式，以实现循环执行录入医嘱的函数，通过终端输入的方式，自主决定录入不同数量的医嘱、不同种类的医嘱。

当然，编译器终端输入以实现可控制性的方法固然是最原始最笨的办法，因为在脚本执行的过程中，driver调用的浏览器窗口设定为最大化maxmize_window；而每次自定义选择医嘱时，又需要切出浏览器，在终端存值，会很影响感官。

- 我能设想到的最理想的是，用前端方法设计一个大小合适且可拖动调整的小窗，同步编译器终端的功能，可通过小窗输入输出终端内容，并将该窗口置于可视桌面最前端，这样既能看到浏览器自动化运行的过程，又能快捷终端输入输出，也不会很影响视觉。这想法只能说以我当前能力是无法实现的了，以后如果有机会，可以拓展到其他工作项目的自动化脚本里去。

多医嘱录入函数的框架大致如下：

```python
def outpatient_orders(driver, patient_msg):
    ...  # 脚本进入医生看诊页面的代码执行
    def orders_entry_main():
        def loop_entry(entry):
            match entry:
                case '1': pickshell医嘱
                case '2': 检验类医嘱
                case '3': 检查类医嘱
                case _: 提示输入错误
        while True:
            entry = input()
            loop_entry(entry)
            if entry == '0':
                break
    orders_entry_main()
outpatient_orders(driver, patient_msg)
```

函数嵌套方法：

- loop_entry()函数传参entry，通过match...case方法，读取entry的值，进而进行不同的case操作，以实现参数控制代码执行范围，自定义选择开立的医嘱类型
- orders_entry_main()函数通过while True无限循环调用loop_entry()函数，entry通过终端键盘input输入的方法传参给loop_entry()，输入字符'0'时跳出循环
- outpatient_orders()函数传参patient_msg，脚本进入到医生看诊页面后，选中对应挂号患者patient_msg，以为指定患者开立医嘱执行嵌套内的函数

------

### 脚本进入医生看诊页面

{% asset_img 医生看诊页面.png  %}

① 展开大类主菜单，并进入门诊医生站对应的大类菜单：

```python
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-trigger']")  # 展开大类菜单
webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
time.sleep(1)

driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-submenu-title']//*[text()='门诊医生站']")  # 大类菜单
time.sleep(1)
```

② 进入子类菜单，并进入医生看诊页面：

```python
item = driver.find_element(by=By.XPATH, value="//*[@id='root']//*[@class='ant-menu-item ant-menu-item-selected']")  # 子类菜单(门诊医生站)
webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
time.sleep(1)

item = driver.find_element(by=By.XPATH, value="//*[@class='ant-tabs-nav-list']//*[text()='医生看诊']")  # 进入"医生看诊"页面
webdriver.ActionChains(driver).move_to_element(item).click(item).perform()
print("脚本已切换到 [门诊医生站] 页面")
time.sleep(1)
```

③ 选中患者，进入门诊医生站待录入医嘱：

```python
item = driver.find_element(by=By.XPATH, value="//button[@class='ant-btn ant-btn-link ant-btn-icon-only']")  # 患者列表刷新
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(5)
driver.find_element(by=By.XPATH, value=patient_msg).click()  # 选中患者列表对应挂号患者
print("已选中 门诊挂号患者[ %s ]" % patient_msg)
time.sleep(5)  # 强制等待页面加载, 待患者信息、门诊医生站功能加载
```

------

### pickshell医嘱录入功能的实现

{% asset_img pickshell输入医嘱.png  %}

如上图，以 medicine传参医嘱完整全称 定位到pickshell输入框。部分医嘱例如葡萄糖注射液，列表会显示名称带有“葡萄糖注射液”的所有医嘱，会出现不同浓度、不同规格、不同厂商等多种葡萄糖注射液医嘱，此处并不做特殊情况的考虑，均定位到列表内第一条医嘱并点击。

```python
medicine = input("请输入所要开立的pickshell药品完整名称: ")

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-datapicker kt-datapicker-singleRow']//*[@placeholder='请输入']")
webdriver.ActionChains(driver).move_to_element(item).click().send_keys(medicine).perform()
time.sleep(3)  # 强制等待给检索医嘱列表的加载时间

# 定位并选择目录列表第一条医嘱
driver.find_element(By.XPATH, '//tr[@data-row-key="0" and contains(@class, "ant-table-row ant-table-row-level-0 kt-table-row-oddeven kt-row-selected-order-picker")]').click()
time.sleep(2)
```

------

{% asset_img 医嘱重复弹窗.png  %}

如上图，医嘱落入医生站时有多个校验，医嘱重复校验弹窗就是其一。当该患者的医嘱列表存在相同医嘱，会出现并需要判断点击。故在脚本代码中使用try...except...方法同步进行校验。

```python
"""患者当前医嘱列表重复开立弹窗判断"""
try:
	wait = WebDriverWait(driver, 10)
	wait.until(EC.presence_of_element_located((By.XPATH, "//*[text()='已经开立，是否继续？']")))

	confirm_button = driver.find_element(by=By.XPATH, value="//*[text()='确定']")
	confirm_button.click()
except:
	pass
```

------

{% asset_img 医嘱详细信息录入.png  %}

医嘱落入医生站后，则需要对其医嘱的信息进行手动的填写了，过程都是类似的，但是这部分内容一直存在历史bug，前端开发在此处放置的下拉框会出现异常跳转、光标回弹的问题。**撰写此处脚本的时候，着实是见识到了水货开发的实力**。。。当然脚本代码也用了最原始最简单的方法避免这些bug：time.sleep()设定强制等待、点击其他空白可定位位置以防止原本sendkeys的值消失、在前端光标自动回弹后再重复定位下一个元素重新存值。

上述所说的前端bug问题具体为：光标定位到“剂量”框并存值后，切到下一个“用法”框时，光标会自动跳转回“剂量”框去；有时脚本在执行过程中，已向“用法”框存值后，切到其他输入框时，原存值会莫名消失等（此问题非必现，大多运行时正常，故脚本内并未做相关问题的解决方案；如果执行过程中复现此问题，只能重新录入或快速手动输入了）。。。

- “数量”输入框

```python
"""----------数量----------"""
item = driver.find_element(by=By.XPATH, value="//*[@id='orderQtyId1']")
webdriver.ActionChains(driver).move_to_element(item).send_keys("3").perform()  # '数量'固定存值'3'
driver.implicitly_wait(3)
time.sleep(1)
```

- “剂量”输入框

```python
"""----------剂量----------"""
# '剂量'框存在未知异常，无法定位元素
# 又因前端约束 医嘱落入列表后光标自动定位到'数量'框，故使用ActionChains方法模拟TAB操作跳到'用法'框
actions = ActionChains(driver)
actions.send_keys(Keys.TAB, Keys.TAB).perform()
actions.send_keys("1").perform()
driver.implicitly_wait(5)
time.sleep(3)
```

- “用法”输入框

```python
"""----------用法----------"""
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
time.sleep(3)

# 采用上述处理'剂量'框异常的方法后，又有未知原因 click定位到'用法'框后，光标会重新回到'剂量'框(前端代码问题)
# 故使用两次click定位'用法'框方法，来避免光标异常定位问题，经测试此方法有效
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
time.sleep(1)

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex4']")
webdriver.ActionChains(driver).move_to_element(item).send_keys("口服").perform()
time.sleep(1)
webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.ENTER).perform()

# 未知原因 用法框send_key后仍会再弹出下拉框，故设计为传参后立刻点击一处不可编辑区别，强制退出该输入框
time.sleep(1)
driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-logo-icon']").click()
driver.implicitly_wait(5)
time.sleep(3)
```

- “频次”输入框

```python
"""----------频次----------"""
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex5']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(5)

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex5']")
webdriver.ActionChains(driver).move_to_element(item).send_keys("qd8").perform()
time.sleep(1)
webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.ENTER).perform()

# 未知原因 频次框send_key后仍会再弹出下拉框，故设计为传参后立刻点击一处不可编辑区别，强制退出该输入框
time.sleep(1)
driver.find_element(by=By.XPATH, value="//*[@class='kt-global-header-common-logo-icon']").click()
driver.implicitly_wait(5)
time.sleep(3)
```

- “周期”输入框

在录入数量、剂量、频次后，系统会自动换算出“周期”所需的值，脚本在此处为测试能否正常传值，所以也采取传入固定值的方式。使用send_keys全选CTRL + A以清除“周期”内原有值的方法未生效，换成for循环连续4次进行BACKSPACE操作代替。

```python
"""----------周期----------"""
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
time.sleep(1)

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
for _ in range(4):
webdriver.ActionChains(driver).move_to_element(item).send_keys(Keys.BACKSPACE).perform()
time.sleep(1)

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-editable-cell tabIndex6']")
webdriver.ActionChains(driver).move_to_element(item).send_keys("3").perform()
time.sleep(3)
```

- 医嘱保存

```python
"""医嘱保存"""
driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLineItem_RITqA diagnosisAnYang_iconCursor_Jv_RO']//*[text()='保存']").click()
```

------

{% asset_img 医嘱保存重复校验.png  %}

不光在开立医嘱时有医嘱重复校验，在最后保存医嘱列表时也会弹窗类似重复的校验。针对此类弹窗，采取相同方法，try...except...当定位到“强制保存”的文本，则点击此文本对应按钮，以确认关闭该校验弹窗。

```python
"""患者医嘱重复预警弹窗判断"""
try:
    confirm_button = driver.find_element(by=By.XPATH, value="//*[@class='ant-modal-body']//button[text()='强制保存']")
    webdriver.ActionChains(driver).move_to_element(confirm_button).click().perform()
    driver.implicitly_wait(3)
    time.sleep(1)
except:
	pass
```

------

{% asset_img 医嘱保存成功校验.png  %}

上述所有录入医嘱的操作执行完毕后，要对所有医嘱进行保存才可退出，点击保存后需要物理等待程序加载、数据库存值，保存成功会有“医嘱保存成功”弹窗。

针对该弹窗的定位抓取，终端print打印脚本执行结果是我认为有必要的，所以增加了此脚本代码形式的校验。

```python
"""医嘱保存成功弹窗校验"""
try:
    item = driver.find_element(By.XPATH, "//*[text()='医嘱保存成功']")
    webdriver.ActionChains(driver).move_to_element(item).perform()
    driver.implicitly_wait(5)
    print("--- ATTENTION: 医嘱保存成功! ---")
except NoSuchElementException:
    print("--- ATTENTION: 函数正常运行 但未成功保存医嘱，注意网络延迟并重试! ---")
```

------

**pickshell医嘱开立 部分完整脚本运行截图：**

{% asset_img pickshell运行系统截图.png  %}

{% asset_img pickshell运行终端截图.png  %}

------

### 检验类医嘱录入功能的实现

{% asset_img 检验类医嘱开立方法.png  %}

检验医嘱开立在功能上可以通过详细检验组分类，选择性显示不同类别的检验医嘱，从而勾选所需要的。但是这本就对于需变动脚本不便，自然也不必要使用此方式。索性直接用inspection_id传参项目代码，既具有唯一性，又可以跳过勾选多个复选框之一时麻烦的定位等问题。所以本脚本在执行检验类医嘱的过程只分为：打开页面 -> 输入inspection_id -> 勾选列表第一个复选框 -> 点击确定。就能简洁且顺利的搞定此部分。

- 进入“检验”类医嘱开立弹窗

```python
"""按钮开立检验类医嘱"""
print("正在进入检验类医嘱开立弹窗...")
item = driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLine_BsP7f']//*[text()='检验']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
time.sleep(3)
```

- 终端传参inspection_id项目代码

```python
# 搜索框搜索 项目代码 以唯一性元素 定位所需项目
inspection_id = input("请输入所要开立的检验类医嘱-项目代码: ")
item = driver.find_element(by=By.XPATH, value="//*[@class='labReqApplicationDialog_container_J2E8u']//input[contains(@placeholder,'请输入项目描述/项目代码')]")
webdriver.ActionChains(driver).move_to_element(item).click().send_keys(inspection_id).send_keys(Keys.ENTER).perform()
driver.implicitly_wait(5)
time.sleep(2)

```

- 勾选列表下第一个复选框，点击确定按钮并保存医嘱

```python
item = driver.find_element(by=By.XPATH, value="//*[@class='ant-table-body']//*[@class='ant-table-row ant-table-row-level-0']//input[1]")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(5)
time.sleep(2)

item = driver.find_element(by=By.XPATH, value="//*[@class='kt-dialog-footer kt-portal-footer-container']//button[text()='确定']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(5)
time.sleep(2)

"""医嘱保存"""
driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLineItem_RITqA diagnosisAnYang_iconCursor_Jv_RO']//*[text()='保存']").click()
time.sleep(2)
```

- 医嘱保存成功校验

效果同pickshell的医嘱保存成功校验

```python
"""医嘱保存成功弹窗校验"""
try:
wait = WebDriverWait(driver, 10)
wait.until(EC.presence_of_element_located((By.XPATH, "//*[text()='医嘱保存成功']")))

item = driver.find_element(By.XPATH, "//*[text()='医嘱保存成功']")
webdriver.ActionChains(driver).move_to_element(item).perform()
driver.implicitly_wait(5)
time.sleep(2)
print("--- ATTENTION: 医嘱保存成功! ---")
except NoSuchElementException:
print("--- ATTENTION: 函数正常运行 但未成功保存医嘱，注意网络延迟并重试! ---")
```

------

**检验类医嘱开立 部分完整脚本运行截图：**

{% asset_img 检验类医嘱运行系统截图.png  %}

{% asset_img 检验类医嘱运行终端截图.png  %}

------

### 检查类医嘱录入功能的实现

{% asset_img 检查类医嘱开立方法.png  %}

检查医嘱的脚本代码开立方法大体和检验医嘱一致，通过examination_id传参项目代码，勾选对应项目以实现开立。只不过针对检查类医嘱，会有选择检查部位的情况需要考虑，故再增加一个判断校验，当发现所开立的医嘱需要再录入部位，则默认勾选并带入部位列表下第一个值。所以本脚本在执行检查类医嘱的过程分为：打开页面 -> 输入examination_id -> 勾选列表第一个复选框 -> 判断该项目是否需要录入部位（需要则默认勾选第一个部位；不需要则跳过） -> 点击确定。

- 进入“检查”类医嘱开立弹窗

```python
"""按钮开立检查类医嘱"""
print("正在进入检查类医嘱开立弹窗...")
item = driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLine_BsP7f']//*[text()='检查']")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
time.sleep(3)
driver.implicitly_wait(10)
```

- 录入“检查目的”

```python
item = driver.find_element(by=By.XPATH, value="//*[@class='ant-modal-body']//*[@class='ant-select-selector']")
webdriver.ActionChains(driver).move_to_element(item).click().send_keys("协助诊断").send_keys(Keys.ENTER).perform()
driver.implicitly_wait(5)
time.sleep(1)
```

- 终端传参examination_id项目代码 并勾选

```python
# 搜索框搜索 项目代码 以唯一性元素 定位所需项目
examination_id = input("请输入所要开立的检查类医嘱-项目代码: ")
item = driver.find_element(by=By.XPATH, value="//*[@class='ant-input-affix-wrapper']")
webdriver.ActionChains(driver).move_to_element(item).click().send_keys(examination_id).perform()
time.sleep(1)

item = driver.find_element(by=By.XPATH, value="//*[@class='ant-table-body']//*[@class='ant-table-row ant-table-row-level-0']//input[1]")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(4)
time.sleep(2)
```

- 所开立检查项目是否需要录入部位的判断 及部位的录入

```python
"""检查医嘱是否存在部位待选择情况判断"""
try:
    driver.find_element(by=By.XPATH, value="//*[@class='ant-input-affix-wrapper']//*[contains(@placeholder,'请输入部位名称')]")

    print("该检查类医嘱有部位维护, 需要选择部位才能开立! ")
    # 脚本仅勾选第一个部位, 不打算做多种多个部位勾选的细分代码
    item = driver.find_element(by=By.XPATH, value="//*[@class='ant-checkbox-group examReqmultiApplicationDialog_group_SOSLw']//input[1]")
    webdriver.ActionChains(driver).move_to_element(item).click().perform()
    driver.implicitly_wait(3)
    time.sleep(1)
except:
    pass
```

- 点击确定 落入医嘱

```python
item = driver.find_element(by=By.XPATH, value="//*[@class='kt-dialog-footer kt-portal-footer-container']//button[contains(text(),'确定')]")
webdriver.ActionChains(driver).move_to_element(item).click().perform()
driver.implicitly_wait(4)
time.sleep(2)
```

- 医嘱重复开立弹窗校验

```python
"""当前患者该医嘱已开立弹窗校验"""
try:
    wait = WebDriverWait(driver, 10)
    wait.until(EC.presence_of_element_located((By.XPATH, "//*[@class='ant-modal-content']//*[contains(text(),'已经开立，是否继续？')]")))

    confirm_button = driver.find_element(by=By.XPATH, value="//*[@class='kt-dialog-confirm-footer']//button[contains(text(),'确定')]")
    webdriver.ActionChains(driver).move_to_element(confirm_button).click().perform()
    print("--- ATTENTION: 检查项目成功落入医嘱列表 ---")
except:
    pass
```

- 医嘱保存及保存成功校验

```python
"""医嘱保存"""
driver.find_element(by=By.XPATH, value="//*[@class='diagnosisAnYang_secondLineItem_RITqA diagnosisAnYang_iconCursor_Jv_RO']//*[text()='保存']").click()
time.sleep(2)

"""医嘱保存成功弹窗校验"""
try:
    wait = WebDriverWait(driver, 10)
    wait.until(EC.presence_of_element_located((By.XPATH, "//*[text()='医嘱保存成功']")))

    item = driver.find_element(By.XPATH, "//*[text()='医嘱保存成功']")
    webdriver.ActionChains(driver).move_to_element(item).perform()
    driver.implicitly_wait(5)
    time.sleep(2)
    print("--- ATTENTION: 医嘱保存成功! ---")
except NoSuchElementException:
    print("--- ATTENTION: 函数正常运行 但未成功保存医嘱，注意网络延迟并重试! ---")
```

------

**检查类医嘱开立 部分完整脚本运行截图：**

{% asset_img 检查类医嘱运行系统截图.png  %}

{% asset_img 检查类医嘱运行终端截图.png  %}

------

**以上就是基于python 3.10.10 和 selenium 4 版本，结合康博嘉宝鸡项目Webhis门诊医生站系统制作的 UI自动化测试脚本。此项目属于是我的第一份工作的第一个项目，功能性还是比较简单和单一的，四百行的代码量也并不多，代码内容结构也都是比较单一固定的，只能说这个项目于我巩固selenium的使用，恢复对python的学习起到了很大的帮助吧，还望以后的职业生涯再接再厉咯，这个md应该就写至此为止了！**
