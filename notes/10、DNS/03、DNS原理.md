

## [原文](https://www.jianshu.com/p/cfa515fb5cb8)

# DNS原理

## DNS简介
DNS全称Domain Name System，即域名系统。

域名(domain name)是IP地址的代号，域名解析系统(DNS, domain name system)就负责将域名翻译为对应的IP地址。
DNS协议主要基于UDP，是应用层协议。

- 1、在浏览器的地址栏输入域名，而不是IP地址。减轻了互联网用户的记忆负担。

- 2、网站可能会变更IP地址。可以更改DNS中的对应关系，从而保持域名不变，而IP地址更新。

域名和IP地址的对应关系存储在DNS服务器(DNS server)中

## 域名的层级结构
主机名.次级域名.顶级域名.根域名

> host.sld.tld.root

## DNS解析过程
以下步骤中，DNS缓存中存在域名对应IP则DNS解析成功，若DNS服务器缓存中不存在域名对应IP，
则自动进入下一步

1. 浏览器缓存，当用户通过浏览器访问某域名时，浏览器首先会在自己的缓存中查找是否有该域名对应的IP地址

2. 系统缓存，检查用户计算机系统Hosts文件是否有该域名对应IP；

3. 路由器缓存，当浏览器及系统缓存中均无域名对应IP则进入路由器缓存中检查，
以上三步均为客服端的DNS缓存；

4. ISP（互联网服务提供商）DNS缓存，当在用户客服端查找不到域名对应IP地址，
则将进入ISP DNS缓存中进行查询。比如你用的是电信的网络，则会进入电信的DNS缓存服务器中进行查找；

5. 根域名服务器，全球仅有13台根域名服务器，1个主根域名服务器，其余12为辅根域名服务器。
根域名收到请求后会查看区域文件记录，若无则将其管辖范围内顶级域名（如.com）服务器IP告诉本地DNS服务器；

6. 顶级域名服务器，顶级域名服务器收到请求后查看区域文件记录，
若无则将其管辖范围内主域名（如ecdoer.com）服务器的IP地址告诉本地DNS服务器；

7. 主域名服务器，主域名服务器接受到请求后查询自己的缓存，
如果没有则进入下一级域名服务器（www.ecdoer.com）进行查找，并重复该步骤直至找到正确纪录；

8. 保存结果至缓存，本地域名服务器把返回的结果保存到缓存，以备下一次使用，
同时将该结果反馈给客户端，客户端通过这个IP地址与web服务器建立链接

### hosts 映射
可以配置本地的域名解析
```
cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1   localhost
255.255.255.255 broadcasthost
::1             localhost 
```

## DNS查询

查询是否正常解析
```
nslookup kailian.github.io
Server:     202.96.134.33
Address:    202.96.134.33#53

Non-authoritative answer:
kailian.github.io   canonical name = github.map.fastly.net.
github.map.fastly.net   canonical name = prod.github.map.fastlylb.net.
Name:   prod.github.map.fastlylb.net
Address: 151.101.36.133
```

查询注册情况
```
whois github.com

Whois Server Version 2.0

Domain names in the .com and .net domains can now be registered
with many different competing registrars. Go to http://www.internic.net
for detailed information.

GITHUB.COM.KHALEDELANSARI.COM
GITHUB.COM

...
```
dig显示整个查询过程
```
dig kailian.github.io

# 查询参数和统计
<<>> DiG 9.8.3-P1 <<>> kailian.github.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60058
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 0

# 查询内容
;; QUESTION SECTION:
;kailian.github.io.     IN  A

# DNS答复
;; ANSWER SECTION:
kailian.github.io.  2566    IN  CNAME   github.map.fastly.net.
github.map.fastly.net.  600 IN  CNAME   prod.github.map.fastlylb.net.
prod.github.map.fastlylb.net. 600 IN    A   151.101.16.133

#DNS传输信息
;; Query time: 39 msec
;; SERVER: 202.96.134.33#53(202.96.134.33)
;; WHEN: Sat Jul 16 21:46:39 2016
;; MSG SIZE  rcvd: 125 
```

分级查询

- 从"根域名服务器"查到"顶级域名服务器"的NS记录和A记录（IP地址）

- 从"顶级域名服务器"查到"次级域名服务器"的NS记录和A记录（IP地址）

- 从"次级域名服务器"查出"主机名"的IP地址
```
dig +trace kailian.github.io

; <<>> DiG 9.8.3-P1 <<>> +trace kailian.github.io
;; global options: +cmd
.           326984  IN  NS  m.root-servers.net.
.           326984  IN  NS  a.root-servers.net.
.           326984  IN  NS  b.root-servers.net.
.           326984  IN  NS  c.root-servers.net.
.           326984  IN  NS  d.root-servers.net.
.           326984  IN  NS  e.root-servers.net.
.           326984  IN  NS  f.root-servers.net.
.           326984  IN  NS  g.root-servers.net.
.           326984  IN  NS  h.root-servers.net.
.           326984  IN  NS  i.root-servers.net.
.           326984  IN  NS  j.root-servers.net.
.           326984  IN  NS  k.root-servers.net.
.           326984  IN  NS  l.root-servers.net.
;; Received 432 bytes from 202.96.134.33#53(202.96.134.33) in 201 ms

io.         172800  IN  NS  ns-a1.io.
io.         172800  IN  NS  ns-a2.io.
io.         172800  IN  NS  ns-a3.io.
io.         172800  IN  NS  ns-a4.io.
io.         172800  IN  NS  ns-d1.io.
io.         172800  IN  NS  ns-l1.io.
io.         172800  IN  NS  ns-y1.io.
;; Received 427 bytes from 193.0.14.129#53(193.0.14.129) in 9275 ms

github.io.      86400   IN  NS  ns2.p16.dynect.net.
github.io.      86400   IN  NS  ns3.p16.dynect.net.
github.io.      86400   IN  NS  ns4.p16.dynect.net.
github.io.      86400   IN  NS  ns1.p16.dynect.net.
;; Received 121 bytes from 64.251.31.179#53(64.251.31.179) in 512 ms

kailian.github.io.  3600    IN  CNAME   github.map.fastly.net.
;; Received 70 bytes from 204.13.251.16#53(204.13.251.16) in 384 ms
```
列出根域名.的所有NS记录，即所有根域名服务器

根据内置的根域名服务器IP地址，DNS服务器向所有这些IP地址发出查询请求，
询问域名的顶级域名服务器io.的NS记录。

## DNS的记录
记录格式

> Domain_name Time_to_live Class Type Value

Time_to_live 生存时间，记录最多可以缓存多久

value 如果是A记录，则value是一个IPv4地址；CNAME记录，则是别名

常见的记录类型type如下：

记录类型	| 含义
|---|---
A	| 主机的IPv4地址
AAAA|	主机的IPv6地址
NS	|该域名所在域的权威域名服务器
MX	|接受特定域名电子邮件的服务器域名
CNAME|	当前域名的一个别名

## DNS相关优化

1） 增加DNS服务器数量

减小服务器压力，当单位服务器需要处理的DNS请求数量较少时速度会更快；

2） 全国多地分布DNS服务器

用户网络访问速度跟用户计算机到服务器的距离有关，
若可以让用户就近访问所在省份的服务器便可以加速DNS解析速度；

3） 双线线路域名解析

当用户网络线路跟服务器网络线路一致时速度较快，而不一致时则较慢，
如：用户使用电信的线路但服务器是联通的线路，访问速度下降

同时解析联通和电信，默认的或者通用的走电信线路，联通的就走联通线路

4） 配置比较快的公共DNS，优先配置国内的

5） DNS预解析

DNS预解析，对页面出现的的域名提前解析
```
<link rel="dns-prefetch" href="//domain.com">
```
6）减少域名数量

减少不同域名的数量减少DNS的查询，包括页面中的链接，图片，脚本文件，样式表，Flash对象等，
将组件分布在两到四个域名之间

## 公共DNS
公共DNS服务器，即系统默认的DNS解析服务器。配置国外的DNS，可能导致域名有时候解析失败。

查看配置
```
cat /etc/resolv.conf

search DHCP HOST
nameserver 202.96.134.33
nameserver 202.96.128.86
```
常用的公共DNS

[114 DNS](http://www.114dns.com/)
```
114.114.114.114
114.114.115.115
```
[阿里 AliDNS](http://www.alidns.com/)
```
223.5.5.5
223.6.6.6
```
[baidu DNS](https://dudns.baidu.com/)
```
180.76.76.76
```
[Google DNS](https://developers.google.com/speed/public-dns/)
```
8.8.8.8
8.8.4.4
```
广东电信 DNS]()
```
202.96.128.86
202.96.128.166
202.96.134.33
202.96.128.68
```

## 参考

[DNS解析过程原理](http://www.ecdoer.com/post/dns.html)

[DNS 原理入门](http://www.ruanyifeng.com/blog/2016/06/dns.html)

 