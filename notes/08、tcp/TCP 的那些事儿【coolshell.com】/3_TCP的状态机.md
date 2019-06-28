
## [原文](https://coolshell.cn/articles/11564.html)

 
# TCP的状态机

其实，网络上的传输是没有连接的，包括TCP也是一样的。

而TCP所谓的“连接”，其实只不过是在通讯的双方维护一个“连接状态”，让它看上去好像有连接一样。
所以，TCP的状态变换是非常重要的。

下面是：“TCP协议的状态机”（图片来源） 和 “TCP建链接”、“TCP断链接”、“传数据” 的对照图，
我把两个图并排放在一起，这样方便在你对照着看。
另外，下面这两个图非常非常的重要，你一定要记牢。

（吐个槽：看到这样复杂的状态机，就知道这个协议有多复杂，
复杂的东西总是有很多坑爹的事情，所以TCP协议其实也挺坑爹的）

![](../../images/tcp/tcp_fsm.png)

TCP协议的状态机

![](../../images/tcp/tcp-hand-shake_transission_close.jpeg)

TCP建链接”、“TCP断链接”、“传数据


很多人会问，为什么建链接要3次握手，断链接需要4次挥手？

对于建链接的3次握手，主要是要初始化Sequence Number 的初始值。
通信的双方要互相通知对方自己的初始化的Sequence Number（缩写为ISN：Inital Sequence Number）——所以叫SYN，
全称Synchronize Sequence Numbers。
也就上图中的 x 和 y。这个号要作为以后的数据通信的序号，
以保证应用层接收到的数据不会因为网络上的传输的问题而乱序（TCP会用这个序号来拼接数据）。
对于4次挥手，其实你仔细看是2次，因为TCP是全双工的，
所以，发送方和接收方都需要Fin和Ack。
只不过，有一方是被动的，所以看上去就成了所谓的4次挥手。
如果两边同时断连接，那就会就进入到CLOSING状态，然后到达TIME_WAIT状态。
下图是双方同时断连接的示意图（你同样可以对照着TCP状态机看）：

![](../../images/tcp/tcp_closes_imul.png)



### 另外，有几个事情需要注意一下：

#### 关于建连接时SYN超时。
试想一下，如果server端接到了client发的SYN后回了SYN-ACK后client掉线了，
server端没有收到client回来的ACK，
那么，这个连接处于一个中间状态，即没成功，也没失败。
于是，server端如果在一定时间内没有收到的TCP会重发SYN-ACK。
在Linux下，默认重试次数为5次，重试的间隔时间从1s开始每次都翻售，
5次的重试时间间隔为1s, 2s, 4s, 8s, 16s，总共31s，第5次发出后还要等32s都知道第5次也超时了，
所以，总共需要 1s + 2s + 4s+ 8s+ 16s + 32s = 2^6 -1 = 63s，TCP才会把断开这个连接。

#### 关于SYN Flood攻击。
一些恶意的人就为此制造了SYN Flood攻击——给服务器发了一个SYN后，就下线了，
于是服务器需要默认等63s才会断开连接，
这样，攻击者就可以把服务器的syn连接的队列耗尽，让正常的连接请求不能处理。
于是，Linux下给了一个叫tcp_syncookies的参数来应对这个事——当SYN队列满了后，
TCP会通过源地址端口、目标地址端口和时间戳打造出一个特别的Sequence Number发回去（又叫cookie），
如果是攻击者则不会有响应，如果是正常连接，则会把这个 SYN Cookie发回来，
然后服务端可以通过cookie建连接（即使你不在SYN队列中）。

请注意，请先千万别用tcp_syncookies来处理正常的大负载的连接的情况。
因为，synccookies是妥协版的TCP协议，并不严谨。
对于正常的请求，你应该调整三个TCP参数可供你选择，

第一个是：tcp_synack_retries 可以用他来减少重试次数； \
第二个是：tcp_max_syn_backlog，可以增大SYN连接数；  \
第三个是：tcp_abort_on_overflow 处理不过来干脆就直接拒绝连接了。

#### 关于ISN的初始化。
ISN是不能hard code的，不然会出问题的——比如：
如果连接建好后始终用1来做ISN，
如果client发了30个segment过去，但是网络断了，于是 client重连，又用了1做ISN，
但是之前连接的那些包到了，于是就被当成了新连接的包，
此时，client的Sequence Number 可能是3，
而Server端认为client端的这个号是30了。
全乱了。

[RFC793](https://tools.ietf.org/html/rfc793)中说，
ISN会和一个假的时钟绑在一起，
这个时钟会在每4微秒对ISN做加一操作，直到超过2^32，又从0开始。
这样，一个ISN的周期大约是4.55个小时。
因为，我们假设我们的TCP Segment在网络上的存活时间不会超过Maximum Segment Lifetime
（缩写为MSL – [Wikipedia语条](https://en.wikipedia.org/wiki/Maximum_segment_lifetime)），
所以，只要MSL的值小于4.55小时，那么，我们就不会重用到ISN。

#### 关于 MSL 和 TIME_WAIT。
通过上面的ISN的描述，相信你也知道MSL是怎么来的了。
我们注意到，在TCP的状态图中，
从TIME_WAIT状态到CLOSED状态，有一个超时设置，
这个超时设置是 2*MSL（RFC793定义了MSL为2分钟，Linux设置成了30s）为什么要这有TIME_WAIT？
为什么不直接给转成CLOSED状态呢？

- 主要有两个原因：

1）TIME_WAIT确保有足够的时间让对端收到了ACK，
如果被动关闭的那方没有收到Ack，就会触发被动端重发Fin，一来一去正好2个MSL， \
2）有足够的时间让这个连接不会跟后面的连接混在一起（你要知道，有些自做主张的路由器会缓存IP数据包，
如果连接被重用了，那么这些延迟收到的包就有可能会跟新连接混在一起）。
你可以看看这篇文章[《TIME_WAIT and its design implications for protocols and scalable client server systems》](http://www.serverframework.com/asynchronousevents/2011/01/time-wait-and-its-design-implications-for-protocols-and-scalable-servers.html)

#### 关于TIME_WAIT数量太多。
从上面的描述我们可以知道，TIME_WAIT是个很重要的状态，
但是如果在大并发的短链接下，TIME_WAIT 就会太多，这也会消耗很多系统资源。
只要搜一下，你就会发现，十有八九的处理方式都是教你设置两个参数，
一个叫tcp_tw_reuse，另一个叫tcp_tw_recycle的参数，
这两个参数默认值都是被关闭的，后者recyle比前者resue更为激进，resue要温柔一些。
另外，如果使用tcp_tw_reuse，必需设置tcp_timestamps=1，否则无效。
这里，你一定要注意，
打开这两个参数会有比较大的坑——可能会让TCP连接出一些诡异的问题
（因为如上述一样，如果不等待超时重用连接的话，新的连接可能会建不上。
正如官方文档上说的一样“It should not be changed without advice/request of technical experts”）。

  - ##### 关于tcp_tw_reuse。
官方文档上说tcp_tw_reuse 加上tcp_timestamps（又叫PAWS, for Protection Against Wrapped Sequence Numbers）
可以保证协议的角度上的安全，
但是你需要tcp_timestamps在两边都被打开（你可以读一下tcp_twsk_unique的源码 ）。
我个人估计还是有一些场景会有问题。

  - ##### 关于tcp_tw_recycle。
如果是tcp_tw_recycle被打开了话，会假设对端开启了tcp_timestamps，
然后会去比较时间戳，如果时间戳变大了，就可以重用。
但是，如果对端是一个NAT网络的话（如：一个公司只用一个IP出公网）或是对端的IP被另一台重用了，
这个事就复杂了。
建链接的SYN可能就被直接丢掉了（你可能会看到connection time out的错误）
（如果你想观摩一下Linux的内核代码，
请参看源码 [tcp_timewait_state_process](https://elixir.bootlin.com/linux/latest/ident/tcp_timewait_state_process)）。

  - ##### 关于tcp_max_tw_buckets。
这个是控制并发的TIME_WAIT的数量，默认值是180000，如果超限，
那么，系统会把多的给destory掉，
然后在日志里打一个警告（如：time wait bucket table overflow），
官网文档说这个参数是用来对抗DDoS攻击的。也说的默认值180000并不小。
这个还是需要根据实际情况考虑。
Again，使用tcp_tw_reuse和tcp_tw_recycle来解决TIME_WAIT的问题是非常非常危险的，
因为这两个参数违反了TCP协议（[RFC 1122](https://tools.ietf.org/html/rfc1122)） 


#### 其实，TIME_WAIT表示的是你主动断连接，
所以，这就是所谓的“不作死不会死”。
试想，如果让对端断连接，那么这个破问题就是对方的了，呵呵。
另外，如果你的服务器是于HTTP服务器，
那么设置一个[HTTP的KeepAlive](https://en.wikipedia.org/wiki/HTTP_persistent_connection)
有多重要（浏览器会重用一个TCP连接来处理多个HTTP请求），
然后让客户端去断链接（你要小心，浏览器可能会非常贪婪，他们不到万不得已不会主动断连接）。



# 数据传输中的Sequence Number  【TCP的那些事儿(上)】


下图是我从Wireshark中截了个我在访问coolshell.cn时的有数据传输的图给你看一下，
SeqNum是怎么变的。（使用Wireshark菜单中的Statistics ->Flow Graph… ）

![](../../images/tpc/tcp_data_seq_num_Wireshark.jpeg)


你可以看到，SeqNum的增加是和传输的字节数相关的。
上图中，三次握手后，来了两个Len:1440的包，
而第二个包的SeqNum就成了1441。
然后第一个ACK回的是1441，表示第一个1440收到了。

注意：如果你用Wireshark抓包程序看3次握手，你会发现SeqNum总是为0，
不是这样的，Wireshark为了显示更友好，使用了Relative SeqNum——相对序号，
你只要在右键菜单中的protocol preference 中取消掉就可以看到“Absolute SeqNum”了


## [TCP重传机制](4_TCP重传机制.md)