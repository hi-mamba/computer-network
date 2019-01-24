
## [原文](https://www.jianshu.com/p/8f61c95c078d)

# HTTP响应报文
```http request

$ curl -I www.baidu.com
HTTP/1.1 200 OK
Server: bfe/1.0.8.18
Date: Thu, 03 Nov 2016 08:30:43 GMT
Content-Type: text/html
Content-Length: 277
Last-Modified: Mon, 13 Jun 2016 02:50:03 GMT
Connection: Keep-Alive
ETag: "575e1f5b-115"
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Pragma: no-cache
Accept-Ranges: bytes

```
解释：

- HTTP/1.1 200 OK，响应状态行

- HTTP/1.1，访问采用的协议为HTTP，版本为1.1

- 200，HTTP状态码，表示文档正确返回

- OK，一个描述性的原因短语

- Server，服务器应用程序软件的名称和版本

- Date，服务器产生响应的日期

- Content-Type，实体中所承载对象的类型，例子中响应实体的主体是一个html文本。

- Content-Length，所传送实体主体的长度或大小，例子中响应实体的主体包含了277字节的数据

- Last-Modified，资源的最后修改日期时间

- Connection，连接方式，close或keep-alive

- ETag，资源的匹配信息

- Cache-Control，控制缓存的行为

- Pragma，报文指令

- Accept-Ranges，是否接受字节范围请求

 