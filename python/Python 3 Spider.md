# Python 3 Spider



## Introduction

本笔记摘于 图灵教育《Python3 网络爬虫开发实战》

![macos](/Users/cyo57/Desktop/macos.png)

## 开发环境

### Python3

### 请求库

### 解析库

### 数据库

### Web库

### App库

### 爬虫框架

### 部署库相关



## 理论基础

### HTTP原理

### Web原理

### 爬虫原理

### 会话和Cookies

### 代理原理



## 基础库

作为一名菜狗, 学习之初便是模拟浏览器向服务器发送请求. 但是对于现阶段我们来说 复杂的请求需要自己构造吗? 需要关心数据结构吗? 需要学习HTTP/ TCP/ IP的通信协议吗? 需要知道服务器的响应和应答吗?

废话我当然不会, 但是我们可以使用Python库来解决, 比如`urllib`, `httplib2`, `requests`, `treq`等

有了这些, 便不再需要关心请求是什么, 参数是什么. 而专注于爬虫逻辑本身, 大大提升了开发效率.

### urllib

在Python 3 中, 内置了`urllib`库, 其中包含 4 个模块

- request - 最基本的HTTP请求模块, 可模拟发送请求
- error - 异常处理模块, 捕获异常保证程序不会意外终止
- parse - 工具模块, 提供URL处理方法, 比如拆分 解析 合并
- robotparser - 识别robots.txt

#### 发送请求

##### urlopen

`urllib.requests`提供基本的HTTP请求方法, 他还支持授权验证, 重定向, Cookies

```python
import urllib.request

res = urllib.request.urlopen('URL')
print(res.read().decode('utf-8'))
```

接下来查看他返回的类型是什么, 利用`type`方法输出

```python
print(type(res))
```

输出结果为

```python
<class 'http.client.HTTPResponse'>
```

可以发现他是`HTTPResposne`类型的对象, 包含方法read, readinto, getheader, getheaders, fileno,

包含属性msg, version, status, reason, debuglevel, closed

例如, 调用`status`可以得到返回结果状态码, 如 200 表示成功, 404 表示网页未找到等等

查看`urlopen`方法的参数

```python
urllib.request.urlopen(url, data=None, [timeout,]*, cafile=None, capath=None, cadefault=False, context=None)
```

除了URL, 还包括data, timeout等

**data**

为可选参数, 如果要添加, 需要使用bytes方法将参数转化为字节流编码格式的内容, bytes 类型. 如果传递这个参数, 则他的请求方式就从GET换为POST

```python
import urllib.parse  
import urllib.request  

data = bytes(urllib.parse.urlencode({'word': 'hello'}), encoding='utf8')  
response = urllib.request.urlopen('http://httpbin.org/post', data=data)  
print(response.read())
```

这里传递了参数word, 值为hello. 

方法第一个参数需要str类型, 需要用urllib.parse模块的`urlencode`方法将参数字典转化为字符串

第二个参数指定编码格式																																								



### requests

### re - 正则表达式



## 解析库



## 存储



## Ajax数据



## Selenium



## 验证码



## Proxy



## Login



## App爬取



## PySpider



## Scrapy



## Selector



## 分布式



