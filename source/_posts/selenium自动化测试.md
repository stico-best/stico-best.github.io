---
title: selenium自动化测试
abbrlink: 3cb24475
date: 2022-09-14 13:21:11
tags: 1
---

**Python selenium UI自动化测试技术分享**

<!--more-->

# 初入URL页面即警告框弹窗的解决办法

```python
options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option("useAutomationExtension", 'False')
options.add_argument("--user-data-dir=" + str(pathlib.Path.home()) + r"\AppData\Local\Google\Chrome\seleniumTest")

# 创建 WebDriver 对象，指明使用chrome浏览器驱动
driver = webdriver.Chrome(service=Service(r'C:\Program Files\Google\Chrome\Application\chromedriver.exe'), 
                          options=options)  
```

以Chrome浏览器调用为例，service处路径规范为本地chrome文件夹内部的chromedriver绝对路径，options是对chromedriver所调用的chrome浏览器基本设置的修改。

浏览器URL搜索栏搜索 'chrome://flags'，如下图Insecure origins trested as secure处添加需要使用的测试网址url，英文逗号分隔，置为Enabled重启浏览器即可。

{% asset_img flags设置.png  %}

------

# Selenium的三种等待方法

## sleep()：强制等待

```python
import time
"""程序代码块"""
time.sleep(N)  # N为整数, 意为编译器运行至该行，强制暂停等待加载N秒
```

执行 sleep()后线程休眠。

固定的等待，常用于演示，调试，初学者观察效果。实际项目中不建议使用这种等待，由于网络的好坏导致等待时间不确定，如果设置的等待时间不足够长，元素找不到会报错，如果设置的等待时间过长，保证元素可以找到，但其实可能用不了这么久。这种等待方法十分笨拙，无论页面加载得如何，都必须等待n秒，可以说是十分不智能了。

但是强制等待也不是一无是处，比如界面操作完要关闭，但是最后你提交了一些数据，如果非常快的退出浏览器会丢失请求。那就需要强制等待一定时间，这个跟隐式等待还是显示等待都无关。再比如当我们没有找到元素而报错，定位问题时，可以先用sleep()来尝试一下，确定是否是由于等待时间的问题没有找到元素，如果是的话就可以用显示等待来代替。

## implicitly_wait()：隐式等待

```python
from selenium import webdriver
"""程序代码块"""
driver = webdriver.Chrome(...)
driver.implicitly_wait(N)  # N为整数, 意为编译器运行至该行，隐式等待N秒
```

原理：隐式等待，就是在创建driver时，为浏览器对象创建一个等待时间，这个方法是得不到某个元素就等待一段时间，直到拿到某个元素位置。

注意：在使用隐式等待的时候，实际上浏览器会在你自己设定的时间内不断的刷新页面去寻找我们需要的元素 他是全局等待。是对页面中的所有元素设置加载时间。

隐式等待可以理解成在规定的时间范围内，浏览器在不停的刷新页面，直到找到相关元素提前结束等待。如果超出了设置时间还未找到定位元素则抛出异常。但如果页面本身包含一个超大视频之类的文件，就算我们需要定位的元素在最开始已经加载出来，却依旧要等待所有文件加载结束之后，脚本才能继续执行，依旧算是有些弊端。

implicitly_wait(最大等待时长)默认参数的单位为秒。如果在代码中你设置了隐式等待时长为10秒，首先这10秒并非一个固定的等待时间，它并不影响脚本的执行速度。其次，它并不针对页面上的某一元素进行等待。当脚本执行到某个元素定位时，如果元素可以定位，则继续执行，如果元素定位不到，则它将以轮询的方式不断地判断元素是否被定位到。假设在第六秒定位到了元素则继续执行，若直到超出设置的时长10秒还没有定位到元素，则抛出异常。

缺点：隐式等待对于有些条件无法生效，比如url的改变，窗口的延迟新增，动态属性延迟加载……

## WebDriverWait()：显示等待

```python
from selenium.webdriver.support.ui import WebDriverWait
"""程序代码块"""
wait = WebDriverWait(driver, 10)
wait.until(EC.presence_of_element_located((By.ID, 'XXX')))
```

显示等待是Selenium客户可以使用的命令式过程语言。

主要思想是最长等待n秒，n秒内每隔一段时间去检查需要定位的元素是否存在（默认检测频率为0.5s），若存在则提前结束等待。若超时未找到则报错。默认抛出异常为：NoSuchElementException。

这种等待比隐性等待更智能了一些，无视整个页面的加载，只要需要的元素存在了，就结束等待。显示等待不仅针对元素定位有效，而且可以针对某种行为（url的改变，属性的延迟加载）看它是否具备了一定的特征，就开始有所动作了。

函数详细介绍：

`WebDriverWait(driver, timeout, poll_frequency=0.5, ignored_exceptions=None)`

- driver：浏览器驱动
- timeout：最长等待时间，默认以秒为单位
- poll_frequency：监测的时间间隔，默认为0.5秒
- ignored_exceptions：超时后的异常信息，默认情况下抛NoSuchElementException异常

WebDriverWait一般有 until 和 until_not 方法配合使用

- until(method,message)
- until_not(method ,message)

------

# 终端打印 判断函数是否完整执行

对于强迫症的我，会抓住很多细枝末节的地方挑刺，webhis测试脚本运行主要是通过调用function.py下记录的各个模块函数来进行的，而又由于python逐行逐句执行的特性，在function.py下每个函数末尾增加如下代码：

```python
import inspect

...
if True:
        function_name = inspect.currentframe().f_code.co_name
        print(f"函数 {function_name} 执行完成")
```

脚本执行过程中，某函数执行完成后，编译器终端打印：“函数XXX执行完成”，以助于排查脚本报错时具体是哪个函数模块的问题。

```markdown
Python 的 inspect 模块提供了一些函数，用于获取活动对象（如函数、类、方法）的信息。这些函数可以帮助查看对象的内部结构，了解它们的工作原理。

以下是 inspect 模块的一些常用函数：

- inspect.getmembers(object, predicate=None): 返回一个包含对象的所有成员的列表，每个成员都是一个 (name, value) 对的形式。如果提供了 predicate 参数，那么只有 predicate(value) 为 True 的成员才会被返回。

- inspect.getdoc(object): 返回对象的文档字符串。

- inspect.getsource(object): 返回对象的源代码（如果可用）。

- inspect.getfile(object): 返回定义对象的源文件的路径（如果可用）。

- inspect.currentframe(): 返回当前的栈帧。

我的代码中，inspect.currentframe().f_code.co_name 被用来获取当前函数的名字。inspect.currentframe() 返回当前的栈帧，.f_code 返回这个栈帧的代码对象，.co_name 返回这个代码对象的名字，也就是当前函数的名字。
```

------

# try-except校验 判断不定出现弹窗

webhis医嘱开立过程中，有重复医嘱是否开立判断校验。

- 为患者开立某pickshell医嘱时，当其医嘱列表存在该医嘱，会有“确认是否开立重复医嘱”的弹窗校验
- 医嘱保存时，有“是否保存重复医嘱”的弹窗校验
- WPB选中患者，进行某些操作时（如挂号），会对患者基本信息校验，（例如身份证错误）

故可在对应脚本代码位置增加try...expect...方法的校验

```python
"""-----患者当前医嘱列表重复开立弹窗判断-----"""
try:
    wait = WebDriverWait(driver, 3)
    wait.until(EC.presence_of_element_located((By.XPATH, "//*[text()='已经开立，是否继续？']")))

    confirm_button = driver.find_element(by=By.XPATH, value="//*[text()='确定']")
    confirm_button.click()
except:
    print("---该医嘱可为此患者开立---")
    pass


"""-----患者医嘱重复预警弹窗判断-----"""
try:
    wait = WebDriverWait(driver, 3)
    wait.until(EC.presence_of_element_located((By.XPATH, '//*[@id="root"]/div[2]/div/div/div/div[2]/div/div[2]/div/div/div[2]/div/div[1]/div/div/div[2]')))

    confirm_button = driver.find_element(by=By.XPATH, value="//*[text()='强制保存']")
    confirm_button.click()
except:
    print("---该医嘱可为此患者开立---")
    pass
```

当脚本执行过程中 有如上 校验弹窗出现，可以通过显式等待元素定位的方法，对弹窗进行模拟操作以关闭弹窗，进行后续脚本代码的执行。

如果有需要的话，可以增加expect抛出异常方法。

------

