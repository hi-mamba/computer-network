## [原文](https://www.zhihu.com/question/31079440/answer/106497081)

# TCP如何保持长连接,并识别不同请求的?

> TCB: Transmission Control Block 来唯一识别一个TCP连接，用五元组来识别一个TCP连接太不方便。


TCP进程从IP进程取走数据，这个数据包含IP Header + IP Payload，TCP进程先计算CRC，如何计算CRC？CRC覆盖了以下字段，其中包含IP头12个字节：

[IP Source Address + IP Destination + Total Length + Reserved + Protocol ] + TCP Header + TCP Payload

要先把TCP Header里原始的CRC值0 处理，比较新计算的CRC和原始的CRC是否一致，如果不一致，丢弃；如果一致，
则需要把TCP Payload提交给特定的session ID，这个session ID在不同的操作系统上有不同的名称，在cisco IOS 我们称之为TCB，这个TCB只在本地有意义。

最初TCP进程在用户进程调用socket的时候，建立了一个映射关系，五元组 <---->session ID (TCB):


[IP Source Address + IP Destination + Source Port +Destination Port+Protocol ] <--->session ID (TCB)

很显然，五元组的作用是从IP进程取来数据使用的，因为IP Header + TCP Header 没有这样的session ID，
只有五元组做KEY值；有了这个KEY值就可以唯一地映射到session ID，于是把TCP Payload 提交给session ID 所对应的session。

所以无论是session ID， 还是TCB，只是名称不同，其实是一个意思。



## [ http长链接介绍](/notes/02、http/15、http长链接介绍.md)