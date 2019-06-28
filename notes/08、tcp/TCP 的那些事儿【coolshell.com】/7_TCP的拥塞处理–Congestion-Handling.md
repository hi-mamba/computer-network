
## [原文](https://coolshell.cn/articles/11609.html)

# TCP的拥塞处理 – Congestion Handling

> 拥塞窗口（Congestion Window简称cwnd）：指某一源端数据流在一个RTT内可以最多发送的数据包数。

> TCP的核心是拥塞控制，目的是探测网络速度，保证传输顺畅

> RTO：Retransmission TimeOut 超时重传

> RTT: Round Trip Time 一个数据包从发出去到回来的时间

> cwnd：Congestion Window 拥塞窗口

> ssthresh：slow start threshold 缓慢启动阈值

上面我们知道了，TCP通过Sliding Window来做流控（Flow Control），
但是TCP觉得这还不够，因为Sliding Window需要依赖于连接的发送端和接收端，
其并不知道网络中间发生了什么。
TCP的设计者觉得，一个伟大而牛逼的协议仅仅做到流控并不够，
因为流控只是网络模型4层以上的事，TCP的还应该更聪明地知道整个网络上的事。

具体一点，我们知道TCP通过一个timer采样了RTT并计算RTO，
但是，**如果网络上的延时突然增加，那么，TCP对这个事做出的应对只有重传数据，
但是，重传会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，
于是，这个情况就会进入恶性循环被不断地放大。
试想一下，如果一个网络内有成千上万的TCP连接都这么行事，那么马上就会形成“网络风暴”，
TCP这个协议就会拖垮整个网络**。这是一个灾难。

所以，TCP不能忽略网络上发生的事情，而无脑地一个劲地重发数据，对网络造成更大的伤害。
对此TCP的设计理念是：**TCP不是一个自私的协议，当拥塞发生的时候，要做自我牺牲。
就像交通阻塞一样，每个车都应该把路让出来，而不要再去抢路了**。

关于拥塞控制的论文请参看《[Congestion Avoidance and Control](http://ee.lbl.gov/papers/congavoid.pdf)》(PDF)

## 拥塞控制主要是四个算法：

- 1）慢启动
- 2）拥塞避免
- 3）拥塞发生
- 4）快速恢复

这四个算法不是一天都搞出来的，这个四算法的发展经历了很多时间，到今天都还在优化中。 
备注:

- 1988年，TCP-Tahoe 提出了1）慢启动，2）拥塞避免，3）拥塞发生时的快速重传
- 1990年，TCP Reno 在Tahoe的基础上增加了4）快速恢复

## 慢热启动算法 – Slow Start

首先，我们来看一下TCP的慢热启动。慢启动的意思是，刚刚加入网络的连接，一点一点地提速，
不要一上来就像那些特权车一样霸道地把路占满。新同学上高速还是要慢一点，不要把已经在高速上的秩序给搞乱了。

慢启动的算法如下(cwnd全称Congestion Window:拥塞窗口)：

1）连接建好的开始先初始化cwnd = 1，表明可以传一个MSS大小的数据。

2）每当收到一个ACK，cwnd++; 呈线性上升

3）每当过了一个RTT，cwnd = cwnd*2; 呈指数让升

4）还有一个ssthresh（slow start threshold），是一个上限，当cwnd >= ssthresh时，
就会进入“拥塞避免算法”（后面会说这个算法）

所以，我们可以看到，如果网速很快的话，ACK也会返回得快，RTT也会短，
那么，这个慢启动就一点也不慢。下图说明了这个过程。

![](../../images/tcp/tcp_slow_start.JPEG)

这里，
我需要提一下的是一篇Google的论文《[An Argument for Increasing TCP’s Initial Congestion Window](http://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/36640.pdf)》
Linux 3.0后采用了这篇论文的建议——把cwnd 初始化成了 10个MSS。 
而Linux 3.0以前，比如2.6，Linux采用了RFC3390，cwnd是跟MSS的值来变的，
如果MSS< 1095，则cwnd = 4；如果MSS>2190，则cwnd=2；其它情况下，则是3。

## 拥塞避免算法 – Congestion Avoidance

前面说过，还有一个ssthresh（slow start threshold），是一个上限，
当cwnd >= ssthresh时，就会进入“拥塞避免算法”。
一般来说ssthresh的值是65535，单位是字节，当cwnd达到这个值时后，算法如下：

1）收到一个ACK时，cwnd = cwnd + 1/cwnd

2）当每过一个RTT时，cwnd = cwnd + 1

这样就可以避免增长过快导致网络拥塞，慢慢的增加调整到网络的最佳值。很明显，是一个线性上升的算法。

## 拥塞状态时的算法

前面我们说过，当丢包的时候，会有两种情况：

1）等到RTO超时，重传数据包。TCP认为这种情况太糟糕，反应也很强烈。

  - sshthresh =  cwnd /2
  - cwnd 重置为 1
  - 进入慢启动过程
  
2）Fast Retransmit算法，也就是在收到3个duplicate ACK时就开启重传，而不用等到RTO超时。

- TCP Tahoe的实现和RTO超时一样。
- TCP Reno的实现是：
  - cwnd = cwnd /2
  - sshthresh = cwnd
  - 进入快速恢复算法——Fast Recovery
  
上面我们可以看到RTO超时后，sshthresh会变成cwnd的一半，
这意味着，如果cwnd<=sshthresh时出现的丢包，那么TCP的sshthresh就会减了一半，
然后等cwnd又很快地以指数级增涨爬到这个地方时，就会成慢慢的线性增涨。
我们可以看到，TCP是怎么通过这种强烈地震荡快速而小心得找到网站流量的平衡点的。

## 快速恢复算法 – Fast Recovery

##### TCP Reno

这个算法定义在RFC5681。
快速重传和快速恢复算法一般同时使用。
快速恢复算法是认为，你还有3个Duplicated Acks说明网络也不那么糟糕，所以没有必要像RTO超时那么强烈。 
注意，正如前面所说，进入Fast Recovery之前，cwnd 和 sshthresh已被更新：

- cwnd = cwnd /2
- sshthresh = cwnd

然后，真正的Fast Recovery算法如下：

- cwnd = sshthresh  + 3 * MSS （3的意思是确认有3个数据包被收到了）
- 重传Duplicated ACKs指定的数据包
- 如果再收到 duplicated Acks，那么cwnd = cwnd +1
- 如果收到了新的Ack，那么，cwnd = sshthresh ，然后就进入了拥塞避免的算法了。

如果你仔细思考一下上面的这个算法，你就会知道，上面这个算法也有问题，那就是——它依赖于3个重复的Acks。
注意，3个重复的Acks并不代表只丢了一个数据包，很有可能是丢了好多包。
但这个算法只会重传一个，而剩下的那些包只能等到RTO超时，
于是，进入了恶梦模式——超时一个窗口就减半一下，多个超时会超成TCP的传输速度呈级数下降，而且也不会触发Fast Recovery算法了。

通常来说，正如我们前面所说的，SACK或D-SACK的方法可以让Fast Recovery或Sender在做决定时更聪明一些，
但是并不是所有的TCP的实现都支持SACK（SACK需要两端都支持），
所以，需要一个没有SACK的解决方案。而通过SACK进行拥塞控制的算法是FACK（后面会讲）

##### TCP New Reno

于是，1995年，TCP New Reno（参见 RFC 6582 ）算法提出来，
主要就是在没有SACK的支持下改进Fast Recovery算法的——

当sender这边收到了3个Duplicated Acks，进入Fast Retransimit模式，开发重传重复Acks指示的那个包。
如果只有这一个包丢了，那么，重传这个包后回来的Ack会把整个已经被sender传输出去的数据ack回来。
如果没有的话，说明有多个包丢了。
我们叫这个ACK为Partial ACK。

一旦Sender这边发现了Partial ACK出现，那么，sender就可以推理出来有多个包被丢了，
于是乎继续重传sliding window里未被ack的第一个包。
直到再也收不到了Partial Ack，才真正结束Fast Recovery这个过程
我们可以看到，这个“Fast Recovery的变更”是一个非常激进的玩法，
他同时延长了Fast Retransmit和Fast Recovery的过程。

算法示意图
下面我们来看一个简单的图示以同时看一下上面的各种算法的样子：

![](../../images/tcp/tcp.fr_.jpg)




#### FACK算法

FACK全称Forward Acknowledgment 算法，
论文地址在这里（PDF）
[Forward Acknowledgement: Refining TCP Congestion Control](http://conferences.sigcomm.org/sigcomm/1996/papers/mathis.pdf) 
这个算法是其于SACK的，
前面我们说过SACK是使用了TCP扩展字段Ack了有哪些数据收到，哪些数据没有收到，
他比Fast Retransmit的3 个duplicated acks好处在于，
前者只知道有包丢了，不知道是一个还是多个，而SACK可以准确的知道有哪些包丢了。 
所以，SACK可以让发送端这边在重传过程中，把那些丢掉的包重传，而不是一个一个的传，
但这样的一来，如果重传的包数据比较多的话，又会导致本来就很忙的网络就更忙了。
所以，FACK用来做重传过程中的拥塞流控。

- 这个算法会把SACK中最大的Sequence Number 保存在snd.fack这个变量中，snd.fack的更新由ack带秋，
如果网络一切安好则和snd.una一样（snd.una就是还没有收到ack的地方，
也就是前面sliding window里的category #2的第一个地方）

- 然后定义一个awnd = snd.nxt – snd.fack
（snd.nxt指向发送端sliding window中正在要被发送的地方——前面sliding windows图示的category#3第一个位置），
这样awnd的意思就是在网络上的数据。
（所谓awnd意为：actual quantity of data outstanding in the network）

- 如果需要重传数据，那么，awnd = snd.nxt – snd.fack + retran_data，也就是说，awnd是传出去的数据 + 重传的数据。

- 然后触发Fast Recovery 的条件是： ( ( snd.fack – snd.una ) > (3*MSS) ) || (dupacks == 3) ) 。
这样一来，就不需要等到3个duplicated acks才重传，而是只要sack中的最大的一个数据和ack的数据比较长了（3个MSS），那就触发重传。
在整个重传过程中cwnd不变。
直到当第一次丢包的snd.nxt<=snd.una（也就是重传的数据都被确认了），
然后进来拥塞避免机制——cwnd线性上涨。

我们可以看到如果没有FACK在，那么在丢包比较多的情况下，原来保守的算法会低估了需要使用的window的大小，
而需要几个RTT的时间才会完成恢复，而FACK会比较激进地来干这事。 
但是，FACK如果在一个网络包会被 reordering的网络里会有很大的问题。

# 其它拥塞控制算法简介

### TCP Vegas 拥塞控制算法

这个算法1994年被提出，它主要对TCP Reno 做了些修改。这个算法通过对RTT的非常重的监控来计算一个基准RTT。
然后通过这个基准RTT来估计当前的网络实际带宽，
如果实际带宽比我们的期望的带宽要小或是要多的活，那么就开始线性地减少或增加cwnd的大小。
如果这个计算出来的RTT大于了Timeout后，那么，不等ack超时就直接重传。
（Vegas 的核心思想是用RTT的值来影响拥塞窗口，而不是通过丢包）
 这个算法的论文是《TCP Vegas: End to End Congestion Avoidance on a Global Internet》
 这篇论文给了Vegas和 New Reno的对比：

![](../../images/tcp/tcp_vegas_newreno.JPEG)

关于这个算法实现，你可以参看Linux源码：
[/net/ipv4/tcp_vegas.h](https://elixir.bootlin.com/linux/latest/source/net/ipv4/tcp_vegas.h)，
[/net/ipv4/tcp_vegas.c](http://lxr.free-electrons.com/source/net/ipv4/tcp_vegas.c)


#### HSTCP(High Speed TCP) 算法

这个算法来自[RFC 3649](https://tools.ietf.org/html/rfc3649)
（[Wikipedia词条](https://en.wikipedia.org/wiki/HSTCP)）。
其对最基础的算法进行了更改，他使得Congestion Window涨得快，减得慢。其中：

- 拥塞避免时的窗口增长方式： cwnd = cwnd + α(cwnd) / cwnd

- 丢包后窗口下降方式：cwnd = (1- β(cwnd))*cwnd

注：α(cwnd)和β(cwnd)都是函数，如果你要让他们和标准的TCP一样，
那么让α(cwnd)=1，β(cwnd)=0.5就可以了。
对于α(cwnd)和β(cwnd)的值是个动态的变换的东西。 
关于这个算法的实现，
你可以参看Linux源码：
[/net/ipv4/tcp_highspeed.c](http://lxr.free-electrons.com/source/net/ipv4/tcp_highspeed.c)

#### TCP BIC 算法
2004年，产内出BIC算法。
现在你还可以查得到相关的新闻《Google：美科学家研发BIC-TCP协议 速度是DSL六千倍》 
BIC全称Binary Increase Congestion control，在Linux 2.6.8中是默认拥塞控制算法。
BIC的发明者发这么多的拥塞控制算法都在努力找一个合适的cwnd – Congestion Window，
而且BIC-TCP的提出者们看穿了事情的本质，其实这就是一个搜索的过程，
所以BIC这个算法主要用的是Binary Search——二分查找来干这个事。 
关于这个算法实现，
你可以参看Linux源码：
[/net/ipv4/tcp_bic.c](http://lxr.free-electrons.com/source/net/ipv4/tcp_bic.c)

#### TCP WestWood算法
westwood采用和Reno相同的慢启动算法、拥塞避免算法。
westwood的主要改进方面：在发送端做带宽估计，当探测到丢包时，根据带宽值来设置拥塞窗口、慢启动阈值。
那么，这个算法是怎么测量带宽的？每个RTT时间，会测量一次带宽，测量带宽的公式很简单，
就是这段RTT内成功被ack了多少字节。因为，这个带宽和用RTT计算RTO一样，
也是需要从每个样本来平滑到一个值的——也是用一个加权移平均的公式。
另外，我们知道，如果一个网络的带宽是每秒可以发送X个字节，
而RTT是一个数据发出去后确认需要的时候，所以，X * RTT应该是我们缓冲区大小。
所以，在这个算法中，ssthresh的值就是est_BD * min-RTT(最小的RTT值)，
如果丢包是Duplicated ACKs引起的，那么如果cwnd > ssthresh，
则 cwin = ssthresh。如果是RTO引起的，cwnd = 1，进入慢启动。 
关于这个算法实现，你可以参看Linux源码： 
[/net/ipv4/tcp_westwood.c](http://lxr.free-electrons.com/source/net/ipv4/tcp_westwood.c)

## 其它
更多的算法，
你可以从[Wikipedia的 TCP Congestion Avoidance Algorithm](https://en.wikipedia.org/wiki/TCP_congestion_control)
词条中找到相关的线索

## 后记
好了，到这里我想可以结束了，TCP发展到今天，里面的东西可以写上好几本书。
本文主要目的，还是把你带入这些古典的基础技术和知识中，
希望本文能让你了解TCP，更希望本文能让你开始有学习这些基础或底层知识的兴趣和信心。

当然，TCP东西太多了，不同的人可能有不同的理解，而且本文可能也会有一些荒谬之言甚至错误，还希望得到您的反馈和批评。