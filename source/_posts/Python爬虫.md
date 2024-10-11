---
title: Python爬虫
tags: 3
abbrlink: c386cf1b
date: 2022-06-15 16:07:52
---

**Python爬虫学习**

<!--more-->

# requests模块案例一(搜狗首页数据提取)

requests模块：python中原生的一款基于网络请求的模块，功能非常强大，简单便携，效率极高

作用：模拟浏览器发请求

注意从URL下右键"检查"，查看 请求URL、请求方式、Content-Type的格式等，以及"响应"中的字典或列表格式

- **爬取搜狗首页的页面数据**

```python
# 爬取搜狗首页页面数据
import requests

if __name__ == "__main__":
    # 指定url
    url = 'https://www.sogou.com/'

    # 发起请求
    # response接收get方法返回的响应对象
    response = requests.get(url=url)

    # 获取响应数据 .text返回的是字符串形式的响应数据
    page_text = response.text
    print(page_text)

    # 持久化存储
    with open('./sogou.html', 'w', encoding='utf-8') as fp:
        fp.write(page_text)
    print('爬取数据结束！')
```

爬取结果：(源码臭长)

{% asset_img sogou.png 搜狗首页html数据 %}

------

# requests模块案例二(网页采集器)

- **简单的网页采集器**

```python
import requests

if __name__ == "__main__":

    # UA伪装：将对应的User-Agent封装到一个字段中
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/100.0.1185.36'
    }

    url = 'https://www.sogou.com/web?'
    # 处理url携带的参数query:封装到字典中
    kw = input("输入一个query:")
    param = {
        'query': kw
    }
    # 对指定的url发起的请求对应的url是携带参数的，并且请求过程中处理了参数
    response = requests.get(url=url, params=param, headers=headers)

    page_text = response.text
    fileName = kw + '.html'
    with open(fileName, 'w', encoding='utf-8') as fp:
        fp.write(page_text)
    print(fileName, '保存成功！')
```

**重点：**

**UA**：User-Agent（请求载体的身份标识）

**UA检测**(反爬)：门户网站的服务器会检测对应请求的载体身份标识，如果检测到请求的载体身份标识为某一款浏览器，则说明该请求是一个正常的请求。但如果检测到请求的载体身份标识不是基于某一款浏览器的，则表示该请求为不正常的请求(爬虫)则服务器端就可能拒绝该次请求。

**UA伪装**(反反爬)：让爬虫对应的请求载体身份标识伪装成某一款浏览器



**实现：**

控制台输入一个query关键字，类似搜索引擎中的搜索输入框，输入关键字后对应生成它的html文件，文件下保存该关键字搜索的结果的html形式源码。

例：

query：武汉    ==>>  武汉.html生成，浏览器打开武汉.html有：

{% asset_img wuhan.png 网页提取结果 %}

------

# requests模块案例三(破解百度翻译)

- **编译器版本的"百度翻译"**

```python
import json
import requests

post_url = 'https://fanyi.baidu.com/sug'

# UA伪装
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/100.0.1185.36'
}

word = input("输入您要查询的内容：")
# post请求参数处理（同get请求一致）
data = {
    'kw': word
}

# 请求发送
response = requests.post(url=post_url, data=data, headers=headers)

# 获取响应数据:json方法返回的是obj（如果确认响应数据是json类型才可以使用.json方法）
# Content-Type: application/json
dic_obj = response.json()
# print

fileName = word + '.json'
fp = open(fileName, 'w', encoding='utf-8')
json.dump(dic_obj, fp=fp, ensure_ascii=False)
print("OVER")
```

**实现：**

控制台提示输入查询的内容后，自动生成对应内容的json文件，json文件下内容对应如下

{% asset_img 翻译.png 编译器同屏翻译%}

**功能**：可以控制台输入某单词生成json文件实现对该单词的中文翻译

**问题**：测试过程中输入长句英文是无法正确输出结果

------

# **requests模块案例四(豆瓣电影信息提取)**

- 豆瓣喜剧电影排行榜中电影基本信息的捕获

```python
import requests
import json

url = 'https://movie.douban.com/j/chart/top_list?'
param = {
    'type': '24',
    'interval_id': '100:90',
    'action': '',
    'start': '0',  # 从库中的第几部电影去取
    'limit': '20'  # 一次取出的个数
}

# UA伪装
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/100.0.1185.36'
}

response = requests.get(url=url, params=param, headers=headers)

list_data = response.json()

fp = open('./douban.json', 'w', encoding='utf-8')
json.dump(list_data, fp=fp, ensure_ascii=False)
print("OVER")
```

实现：

字典中start和limit可更改(见注释)，运行生成douban.json文件，显示取出limit数量个电影的基本信息

------

# requests模块案例五(查询KFC餐厅地址)

- 检索国内某城市所有KFC餐厅的地址等信息

```python
import requests

url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'

param = {
    'cname': '',
    'pid': '',
    'keyword': '襄阳',  # keyword处可改动，变化所要查询的国内城市名称
    'pageIndex': '1',
    'pageSize': '10'
}

# UA伪装
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/100.0.1185.36'
}

response = requests.post(url=url, params=param, headers=headers)

page_text = response.text
with open('./地址查询.html', 'w', encoding='utf-8') as fp:
    fp.write(page_text)
print("OVER")
```

**实现：**

功能类似于官网[肯德基餐厅信息查询 (kfc.com.cn)](http://www.kfc.com.cn/kfccda/storelist/index.aspx)下"餐厅关键字"查询，控制台输入关键字，即可生成html文件呈现该关键字城市下所有KFC餐厅的地址信息

例：

城市：襄阳

{% asset_img 地址查询.png 查询对应城市KFC餐厅地址信息 %}
