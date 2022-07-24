# HTTP

webservice = http + XML; Rest = http + json;
Ajax

## Instruction

Q: 什么是协议?
A: 双/多方都遵从共同的规范. 例如 ftp, http, stmp, pop, tcp/ip

Q: 什么叫连接
A: 网络上的虚拟电路

Q: HTTP一定要浏览器发送吗

A: NO, 只要满足协议, 什么工具都可以



HTTP请求信息和响应信息的格式

请求 Request

- 请求行
  - 请求方法
    GET/ POST/ HEAD/ PUT/ DELETE/ TRACE/ OPTIONS
  - 请求路径
  - 协议
    `HTTP/1.1 200 OK`
  
- 请求头信息
  `key: value`
  头信息结束后, 有一个**空行**, 即使没有主体信息也**不可少**

- 请求主体信息 (可选)

- ```html
  POST /0606/01.php HTTP/1.1
  Host: localhost
  Content-type:
  application/x-www-forn-urlencode
  Content-length:5
  
  Age=3
  ```

- 

响应 Response

- 响应行
  - 协议
  - 状态码
  - 状态文字
- 响应头信息
  `key: value`
  空行
- 主体信息 (可选)

- ```html
  HTTP/1.1 200 OK
  Content-type: text/html
  Content-length: 5
  
  hello
  ```

## 方法与状态码

GET/ POST/ HEAD/ PUT, TRACE/ DELETE/ OPTIONS

HEAD -> 和GET基本一致, 但不返回内容
POST -> 提交表单
TRACE -> 测试代理有没有修改HTTP请求
OPTIONS -> 返回服务器