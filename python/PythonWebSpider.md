# Python 网络爬虫与信息提取

The Website is the API

作为网站获取数据, 我们只需要把网站当做API即可


## Instruction
### 库

通过一下第三方库则可以完成普通怕成

- Requests
  发起网络请求

  - robots.txt
    爬虫排除标准
    盗亦有道
  - 实战
  
- Beautiful Souq

  解析HTML
  - re
    正则表达式

- Scrapy
  爬虫框架, 中规模速度快

### IDE - 集成开发环境

#### 文本工具类

- IDLE
  - 自带, 默认, 常用
  - 交互式/ 文件式
- Sublime Text
  - 编程工具, 专业编程体验
  - 自定义

#### 集成工具类

- Wing
  - 买断制, 公司维护
  - 调试功能丰富, 版本控制/ 同步
  - 适用于多人开发
- Visual Studio
  - MicroSoft 维护
  - 适用于Windows
- Eclipse
  - 需要PyDev支持
  - 开源

- PyCharm
  - 适用于大型项目
- Anacond
  - 开源, 
  - 适用于 科学计算/数据分析



## 信息提取

### Requests

- Install
  - `pip install requests`
  - 启动`IDLE`, `import requests`

#### Get()

`r = requests.get(url)` 构造一个Requests, 服务器返回Response对象

完整使用方法为

```Python
requests.get(url, params=None, **kwargs)

# url: 链接
# params: url中额外参数, 字典或字节流格式
# **kwargs: 12个控制访问参数
```

Response常用属性

- `.status_code` - HTTP请求返回状态
  - 200 - 正常响应
- `.text` - HTTP相应内容的字符串形式
- `.encoding` - 从HTTP header猜测编码
  - 指定编码 - `.encoding = 'utf8'`
  - 如header不含编码则默认ISO-8859-1
- `.apparent_encoding` - 从内容分析编码
- `.content` - 响应内容的二进制形式, 例如图片

#### 异常处理

- `requests.ConnectionError`
- `requests.HTTPError`
- `requests.URLRequired`
- `requests.TooManyRedirects`
  重定向过多
- `requests.ConnectTimeout`
  与服务器连接超时
- `requests.Timeout`
  发出URL请求到收到的超时

Response提供的方法, 可判断`status_code`是否为200

```Python
def getHTMLText(url):
	try:
    r=requests.get(url, timeout=30)
    r.raise_for_status() #如果状态不是200则引发HTTPError异常
    return r.text
  except:
    return "Error"
```

### Requests 主要方法

为了更好地理解方法, 我们应当理解**HTTP协议**

| .request() | 构造请求, 以支撑以下方法 |
| ---------- | ---- |
| .get()     |获取URL位置资源 |
| .head()    |获取头部信息|
| .post()    |请求后附加用户新数据|
|.put()|向URL存储资源覆盖原位置|
|.patch()|提交局部修改请求|
|.delete()|提交删除请求|
|options|不常用|

PATCH提交部分, PUT提交全部, 有节省带宽的优势

#### HTTP

超文本传输协议, 基于"请求与响应"模式, 无状态的应用层协议 (基于TCP)

采用URL作为定位网络资源的标识



#### .head()

只获取header, 节省带宽

#### .post()

post字典时, 将自动存储为form, 字符串则自动归为data字段

```python
p = {'名字': '张大哥', '年龄': '666'}
r = requests.post(url, data = p)
print(r.text)
'''
{
	'form': {
		'名字': '张大哥',
		'年龄': '666'
	},
}
'''
```

#### .put()

同.post()

#### Requests

`requests.request(method, url, **kwargs)`

**method**

- GET/HEAD/POST/PUT/PATCH/delete/OPTIONS
- HTTP协议对应的请求功能

**kwargs(可选)**

- params

  字典或字节序列, 作为参数增加到url
  `http://a.com/ws?key1=value1&key2=value2`

- data
  字典, 字节序列或文件对象, 作为Requests内容

  ```python
  data = {'k1':'v1', 'k2':'v2'}
  r = requests.request('POST', url, data=data)
  ```

- json
  作为Requests内容

  ```python
  json = {'k1':'v1'}
  r = requests.request('POST', url, json=json)
  ```

- headers
  字典, HTTP头

  ```python
  hd = {'user-agent':'Chrome/10'}
  r = requests.request('POST', url, headers=hd)
  ```

- cookies / auth / files

- timeout
  超时时间, 单位(s), 异常抛出timeout

  ```python
  r = requests.request('GET', url, timeout=10)
  ```

- proxies

  字典类型, 代理访问服务器, 增加登录类型

- 开关字段 (True/False)
  - allow_redirects -> 重定向
  - stream -> 获取内容立即下载
  - verify -> SSL开关
  - cert -> 本地SSL证书路径

#### POST

.post(url, data-None, json=None, **kwargs)



#### GET

基本框架. 网络连接有风险,异常处理很重要.

```python
try:
  r = requests.get(url, timeout=30)
  r.raise_for_status() #如果不是200则抛出异常
  r.encoding = r.apparent_encoding
  return r.text
except:
  return '异常'
```



## 盗亦有道

小规模, 数据量小, 速度要求不高, 适用requests库. 针对特定网页能发挥巨大作用.

中规模, 网站爬虫, 适用scrapy. 大规模, 搜索引擎, 定制开发.

**不要对服务器进行"骚扰", 不造成法律问题, 不带来隐私泄露**

### 反爬虫

**来源审查**, 判断User-Agent

**发布公告**, Robots协议, 道德限制

### Robots协议

**Robotx Exclusion Standard**, 告知爬虫可爬取目录. 存放于根目录robots.txt

如果网站不提供robots.txt 说明网站允许所有爬虫

#### Robots协议的使用

类人行为可不参考Robots协议, 不对服务器造成负担. 但**不能用于商业用途**



## Requests 实战

### 京东商品爬取

我们需要做的是, 通过URL获得商品的信息

### Amazon商品爬取

Amazon对网络爬虫有限制, 需要HTTP头模拟浏览器用户

```python
import requests
--snip--

headers = {
    "User-Agent": "Mozilla/5.0"
}
r = requests.get(url, headers = headers)
```

### 搜索引擎API

本次我们使用百度的API来学习

```python
--snip--
url = 'http://baidu.com/s'
kv = {
  'wd': 'python'
}
r = requests.get(url, params = kv)

# http://baidu.com/s?wd=python
```

### 图片的爬取

网络图片的格式以`*.jpg`类为后缀, 是一个文件

```python
import requests
import os

root = '~/pictures/'
url = 'http://www.baidu.com/123.jpg'
path = url.split('/')[-1]
try:
    if not os.path.exists(root):
        os.mkdir(root)
    if not os.path.exists(path):
        r = requests.get(url)
        with open(path, 'wb') as f:
        f.write(r.content) #二进制形式写入
        f.close()
    else:
      print('文件已存在')
except:
    pass
```

在功能上, 代码并无难度. 但在工程项目中, 可靠性和稳定性极为重要. 我们的代码怎么执行都不会报错.

### IP归属查询API

本次作业使用 [ip138.com](http://ip138.com), API为 `https://site.ip138.com/{{ip}}`



以爬虫的视角看待, 万物皆可爬







## BeautifulSoup

`pip install beautifulsoup4` 安装, 库名导入时实际为`bs4`

美味汤, 能对HTML XML格式解析, 并提取其中内容, **任何内容都将为'utf-8'编码**

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup('<p>data<p>', 'html.parser') #HTML源码和解析器
```

### 基本元素 (BeautifulSoup类)

bs4可以解析所有标签文本. 共有4种解析器

- html.paeser
  bs4 自带
- lxml
  依赖 `lxml`
- xml
  依赖 `lxml`
- html5lib
  依赖 `html5lib`

**Tag**, 标签, 基本信息组织单元, <>和</>标记开头结尾, `.<tag>`返回第一个

**Name**, 标签名字, `<tag>.name`

**Attributes**, 标签属性, 字典形式, `<tag>.attrs`

**NavigableString**, 标签非属性字符串, `<tag>.string`

**Comment**, 标签内字符串的注释

`.<tag>.parent`, 父级; 



### 标签树遍历

#### 下行遍历

.**contents**, 子节点的列表, <tag>的子节点存入列表

.**children**, 子节点的**迭代**, 循环遍历子节点

.**descendants**, 子孙结**迭代**, 包含所有子孙结点

#### 上行遍历

.**parent**, 节点父标签, 一级

.**parents**, 节点父标签, 所有父级

#### 平行遍历

必须为同一父节点, 且有可能获取NavigableString类型

**.next_sibling**, 返回HTML下一个平行节点标签

**.previous_sibling**, 返回上一个平行节点标签

**.next_siblings**, **迭代**, 返回所有后续平行节点标签

**.previous_siblings**, **迭代,** 返回所有前续平行节点标签



### 基于bs4的HTML输出

#### prettify()

为HTML文本的标签和内容增加换行符, 也可以为标签做处理`soup.a.prettify()`



### 信息标记

比如, 我们给 '北京大学' 标记'name', 给 '北京市' 标记 'address'. 比如HTML的<head>, <body>

- 标记后的信息, 增加信息维度
- 标记后的信息可用于通信, 存储, 展示
- 标记后的结构与信息一样, 具有重要价值
- 标记后的信息利于程序理解和运用

国际公认信息标记种类

- XML, eXtensible Markup Language
  `<img src='1.jpg' size=10>哈哈哈</img>`
  - 有效信息不高, 大多数被标签占用

- JSON, JavaScript Object Notation
  键值对 且有数据类型, key: value

  ```json
  "name": {
      "newName": "1",
      "oldName": "2"
  }
  
  "name": ["1", "2"]
  
  "name":"1"
  ```

  - 需要双引号标识类型

- YAML, YAML Ain't Markup Language
  键值对 无数据类型, key: value

  ```yaml
  name:
      newName: 哈
      oldName: 哈哈
  
  name: #平行
  -哈哈
  -哈哈
  
  text: | #多行
  你大师的撒汇顶科技爱上大数据库的哈斯
  大萨达哈时间跨度杀寇决撒囧囧阿迪
  ```

  - 非常简洁

### 信息提取

- 完整解析, 再提取
  准确, 但速度慢
- 无视标记, 直接搜索
  简单, 准确性查
- 融合

eg:

提取HTML中所有URL

- 搜索<a>
- 解析<a>, 提取href

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(html, 'html.parser')

for link in soup.find_all('a'):
    print(link.get('href'))
```

### 信息查找

BeautifulSoup类提供一个方法`<>.find_all(name, attrs, recursive, string, **kwargs)`. 可配合正则表达式`re`使用

- name
  标签名称的检索
- attrs
  对标签属性值的检索`soup.find_all(id='idName')`
  加入正则表达式`soup.find_all(id=re.compile('link'))`
- recursive
  是否对子孙全部检索, 默认True
- string
  精准搜索标签间的字符串 (完全匹配)
- **kwargs

在bs4中,`<tag>()`可替代`<tag>.find_all()`. 而`soup()`可替代`soup.find_all()`

此外, 还包含 7 个扩展方法

- <>.find()
  搜索只返回一个结果
- <>.find_parents()
- <>.find_parent()
- <>.find_next_siblings()
- <>.find_next_sibling()
- <>.find_previous_siblings()
- <>.find_previous_sibling()

### 信息标记

国际通用仍然是老三样,

- XML

- JSON
  适合程序

- YAML
  人类可读性, 适合程序

## BeautifulSoup 实例

### 大学排名

输入大学排名的URL链接, 将大学排名信息的屏幕输出( 排名, 大学名, 总分 );

使用库: requests; bs4

定向爬虫: 仅对URL爬虫, 不扩展

#### 代码结构设计

- 获取html
- 提取信息, 并保存为数据结构
  适用于二维列表保存
- 利用数据结构并展示结果

`isinstance(a, b)`判断a是否为b类型

```python
'''
定向爬虫
'''

import requests
from bs4 import BeautifulSoup
import bs4

def get_html_text(url):
    '''爬取并返回html'''
    try:
        r = requests.get(url)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        print('爬取错误')

def create_list(list, html):
    '''提取html的关键信息并添加到list'''
    soup = BeautifulSoup(html, 'html.parser')
    for i in soup.find('tbody').children:
        #print(str(i) + '\n===================')
        if isinstance(i, bs4.element.Tag):
            t1 = i.find_all('td')
            result_rank = t1[0].string
            result_score = t1[4].string
            t2 = i('a')
            result_name = t2[0].stringAC
            list.append([result_rank.strip(), result_name.strip(), result_score.strip()])

def print_list(list, num):
    '''
    list: 列表
    num: 打印多少函数
    '''
    print_format = '{0:^10}\t{1:{3}^10}\t{2:^10}'
    print(print_format.format('排名', '学校', '总分', chr(12288)))
    for _ in range(num):
        #print('%-20s%-20s\t%s' % (list[_][0], list[_][1], list[_][2]))
        print(print_format.format(list[_][0], list[_][1], list[_][2], chr(12288)))

if __name__ == '__main__':
    url = 'https://shanghairanking.cn/rankings/bcur/2021'
    list = []

    html = get_html_text(url)
    create_list(list, html)
    print_list(list, 10)
```

采用了 requests-bs4 库, 且对中英文混排版做出优化



## Re 正则表达式

通过两周的学习, 我们学习了`Requests`, 了解了`robots.txt`, 并可以使用`Beautiful Soup`解析HTML. 完成了大学排名爬虫.

通过本小节, 我们将学习Re的用法, 提取关键信息. 并通过两个实例学习`Requests`, `bs4`, `re` 的联合使用. 完成 淘宝 股票 爬虫

### Regular Expression

简洁表达字符串的表达式. **一行胜千言**

- 通用字符串表达框架
- 简洁表达
- 针对字符串, 简洁 / 特征
- 判断特征归属

那么我们如何使用正则表达式?

- 编译 -> 编译前只符合语法, 编译后才真正为表达式

### 语法

由字符和操作符构成

操作符包括

|1|2|3|
| ---- | ---- | ---- |
| .    | 任何字符 | |
| []   | 字符集(单个)   |[abc], [a-z]的单个字符|
| [^]  | 非字符集(单个)   |[abc]|
|*|前一字符的0或多次扩展|abc* 表 abc, abcc|
|+|前一字符的1或多次扩展||
|?|前一字符的0或1次扩展|abc? 表 ab, abc|
|\||或, 左右任意一个|ab\|bc 表 ab, bc|
|{m}|拓展前一字符m次|ab{3} 表 abbb|
|{m,n}|拓展前一字符m到n次||
|^|匹配开头||
|$|匹配结尾||
|()|分组标记, 内部只能用\||(ab|cd)表ab或cd|
|\d|数字, 即[0-9]||
|\w|单字符, 即[A-Za-z0-9\_]||

标识IP地址 - `(([1-9]?\d|1\d{2}|2[0-4]\d|25[0-5]).){3}([1-9]?\d|1\d{2}|2[0-4]\d|25[0-5])`

#### 表示类型

Raw String, 表示为`r'text'`. 不会将`\`转义, 因此无需过多担心.

Python原生字符串类型string, 将会转义.

### Re主要方法

| 函数          | 说明                                               |
| ------------- | -------------------------------------------------- |
| re.search()   | 返回match对象(第一个)                              |
| re.match()    | 返回match对象(开始位置)                            |
| re.findall()  | 返回子串 (全部)                                    |
| re.split()    | 去除, 分割后返回列表                               |
| re.finditer() | 返回一个匹配结果的迭代类型, 每个为match对象        |
| re.sub()      | 字符串中替换所有匹配表达式的子串, 返回替代后的子串 |

#### re.search(pattern, string, flags=0)

- pattern
  字符串
- string
  待匹配字符串
- flags
  控制标记
  - re.I, re.IGNORECASE
    忽略大小写
  - re.M, re.MULTLINE
    使 ^ 操作符能将适用于每行
  - re.S, re.DOTALL
    使 . 操作符匹配所有字符, re中默认不匹配换行

```python
import re
# 匹配第一个邮政编码
match = re.search(r'[1-9]\d{5}', 'BTL 100081 10082')
if match:
    print(match.group())
```



#### re.match(pattern, string, flags=0)

#### re.split(pattern, string, maxsplit=0, flags=0)

- maxsplit
  最大份个数, 剩余部分作为最后一个元素输出

#### re.finditer(pattern, string, flags=0)

#### re.sub(pattern, repl, string, count=0, flags=0)

- repl
  替换匹配字符串的字符串
- count
  最大次数

### Match对象

| 属性    | 说明               |
| ------- | ------------------ |
| .string | 待匹配文本         |
| .re     | 匹配时的表达式     |
| .pos    | 搜索文本的开始位置 |
| .endpos | 结束位置           |

| 方法      | 说明                           |
| --------- | ------------------------------ |
| .group(0) | 获取匹配后的字符串             |
| .start()  | 匹配字符串在原字符串的开始位置 |
| .end()    | -                              |
| .span()   | 返回(.start(), .end())         |

### 贪婪/最小匹配

| 操作符 | 说明                                    |
| ------ | --------------------------------------- |
| *?     | 前一字符0次或无限扩展, 最小匹配         |
| +?     | 最小匹配                                |
| ??     | 前一个字符0次或1次扩展, 最小匹配        |
| {m,n}? | 拓展前一个字符m到n次( 包含n ), 最小匹配 |

### 小结

regex = re.compile()

`re.search` 与 `regex.search()` 相等



## [实例]淘宝爬虫

> 获取淘宝搜索页面的信息, 提取商品的名称和价格
>
> 使用requests-re

## Scrapy

功能强大且快速的爬虫框架

实现爬虫功能的一个软件结构和功能组件集合
半成品, 能帮助用户快速实现功能

### 5+2 结构

ENGINE

SCHEDULER - ENGINE

ITEM PIPELINES

SPIDERS

DOWNLOADER - ENGINE

> 请求通过SPIDERS进入(REQUESTS) ENGINE, 将发送到SCHEDULER
>
> SCHEDULER通过ENGINE进入(REQUESTS) DOWNLOADER, 通过ENGINE返回到SPIDERS
>
> SPIDER经过(ITEMS/REQUESTS) ENGINE到达ITEM PIPELINES(ITEMS) 和SCHEDULER(REQUESTS)

### 结构

#### Downloader Middleware

实施Engine, Scheduler和Downloader之间进行用户可配置的控制

修改, 丢弃, 新增 请求或响应

#### Spider

解析Downloader返回的响应

产生爬取项

产生请求

### Scrapy命令行

- startproject
  创建新工程
- genspider
  创建爬虫
- settings
  获得配置信息
- crawl
  运行爬虫
- list
- shell
