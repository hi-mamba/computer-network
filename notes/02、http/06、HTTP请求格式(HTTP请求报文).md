

# HTTP请求格式(HTTP请求报文)


## 一个HTTP请求报文由4部分组成：


- 请求行（request line）

- 请求头部（header）

- 空行

- 请求数据


下图给出了请求报文的一般格式。

![](../images/http/http_request_args.png)

HTTP请求报文图

## 1、请求行
请求行包括：请求方法字段、URL字段、HTTP协议版本字段。它们用空格分隔。

> 例如，GET /index.html HTTP/1.1。

HTTP协议的请求方法包括：GET、POST、HEAD、PUT、DELETE、OPTIONS、TRACE、CONNECT。

而常见的有如下几种：

- 1). GET

最常见的一种请求方式，当客户端要从服务器中读取文档时，
当点击网页上的链接或者通过在浏览器的地址栏输入网址来浏览网页的，使用的都是GET方式。

GET方法要求服务器将URL定位的资源放在响应报文的数据部分，回送给客户端。
使用GET方法时，请求参数和对应的值附加在URL后面，利用一个问号（"?"）代表URL的结尾与请求参数的开始，
传递参数长度受限制。例如，/index.jsp?id=100&op=bind，这样通过GET方式传递的数据直接表示在地址中。
显然，这种方式不适合传送私密数据。

另外，由于不同的浏览器对地址的字符限制也有所不同，一般最多只能识别1024个字符，
所以如果需要传送大量数据的时候，也不适合使用GET方式。

以用google搜索domety为例，Request格式如下：
```http request
GET /search?hl=zh-CN&source=hp&q=domety&aq=f&oq= HTTP/1.1  
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint, 
application/msword, application/x-silverlight, application/x-shockwave-flash, */*  
Referer: http://www.google.cn/ 
Accept-Language: zh-cn  
Accept-Encoding: gzip, deflate  
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
Host: www.google.cn  
Connection: Keep-Alive  
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g; 
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y-
FxlRugatx63JLv7CWMD6UB_O_r

```

- 2). POST

对于上面提到的不适合使用GET方式的情况，可以考虑使用POST方式，
因为使用POST方法可以允许客户端给服务器提供信息较多。

POST方法将请求参数封装在HTTP请求数据中，以名称/值的形式出现，可以传输大量数据，
这样POST方式对传送的数据大小没有限制，而且也不会显示在URL中。

POST方式大多用于页面的表单中。
还以上面的搜索domety为例，如果使用POST方式的话，格式如下：

```http request
POST /search HTTP/1.1  
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint, 
application/msword, application/x-silverlight, application/x-shockwave-flash, */*  
Referer: http://www.google.cn/
Accept-Language: zh-cn  
Accept-Encoding: gzip, deflate  
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
Host: www.google.cn 
Connection: Keep-Alive  
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g; 
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y-
FxlRugatx63JLv7CWMD6UB_O_r  

hl=zh-CN&source=hp&q=domety

```

- 3). HEAD

HEAD就像GET，只不过服务端接受到HEAD请求后只返回响应头，而不会发送响应内容。
当我们只需要查看某个页面的状态的时候，使用HEAD是非常高效的，因为在传输的过程中省去了页面内容。

## 2、请求头部
请求头部由关键字/值对组成，每行一对，关键字和值用英文冒号“:”分隔。
请求头部通知服务器有关于客户端请求的信息，典型的请求头有：

- User-Agent：产生请求的浏览器类型。

- Accept：客户端可识别的内容类型列表。

- Host：请求的主机名，允许多个域名同处一个IP地址，即虚拟主机。

## 3、空行
最后一个请求头之后是一个空行，发送回车符和换行符，通知服务器以下不再有请求头。

## 4、请求数据
请求数据不在GET方法中使用，而是在POST方法中使用。POST方法适用于需要客户填写表单的场合。
与请求数据相关的最常使用的请求头是Content-Type和Content-Length。

 