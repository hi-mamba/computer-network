
## [原文](https://coolshell.cn/articles/11564.html)

# TCP头格式  

接下来，我们来看一下TCP头的格式


![](../../images/tcp/TCP-Header.png)

### 你需要注意这么几点：

- TCP的包是没有IP地址的，那是IP层上的事。但是有源端口和目标端口。

- 一个TCP连接需要四个元组来表示是同一个连接（src_ip, src_port, dst_ip, dst_port）准确说是五元组，
还有一个是协议。但因为这里只是说TCP协议，所以，这里我只说四元组。


- 注意上图中的四个非常重要的东西：

  - Sequence Number是包的序号，用来解决网络包乱序（reordering）问题。
  
  - Acknowledgement Number就是ACK——用于确认收到，用来解决不丢包的问题。
  
  - Window又叫Advertised-Window，也就是著名的滑动窗口（Sliding Window），用于解决流控的。
  
  - TCP Flag ，也就是包的类型，主要是用于操控TCP的状态机的。

关于其它的东西，可以参看下面的图示

![](../../images/tcp/TCP-Header-02.jpeg)








