
## [原文](https://coolshell.cn/articles/11609.html)

# TCP的RTT算法

从前面的TCP重传机制我们知道Timeout的设置对于重传非常重要。

- 设长了，重发就慢，丢了老半天才重发，没有效率，性能差；

- 设短了，会导致可能并没有丢就重发。于是重发的就快，会增加网络拥塞，导致更多的超时，更多的超时导致更多的重发。

而且，这个超时时间在不同的网络的情况下，根本没有办法设置一个死的值。
只能动态地设置。 
为了动态地设置，TCP引入了RTT——Round Trip Time，也就是一个数据包从发出去到回来的时间。
这样发送端就大约知道需要多少的时间，从而可以方便地设置Timeout——RTO（Retransmission TimeOut），
以让我们的重传机制更高效。 听起来似乎很简单，好像就是在发送端发包时记下t0，
然后接收端再把这个ack回来时再记一个t1，于是RTT = t1 – t0。
没那么简单，这只是一个采样，不能代表普遍情况。


### 经典算法

[RFC793](http://tools.ietf.org/html/rfc793) 中定义的经典算法是这样的：

1）首先，先采样RTT，记下最近好几次的RTT值。

2）然后做平滑计算SRTT（ Smoothed RTT）。
公式为：（
其中的 α 取值在0.8 到 0.9之间，这个算法英文叫Exponential weighted moving average，中文叫：加权移动平均）

> SRTT = ( α * SRTT ) + ((1- α) * RTT)

3）开始计算RTO。公式如下：

> RTO = min [ UBOUND,  max [ LBOUND,   (β * SRTT) ]  ]

其中：

- UBOUND是最大的timeout时间，上限值
- LBOUND是最小的timeout时间，下限值
- β 值一般在1.3到2.0之间。

#### Karn / Partridge 算法
但是上面的这个算法在重传的时候会出有一个终极问题——你是用第一次发数据的时间和ack回来的时间做RTT样本值，
还是用重传的时间和ACK回来的时间做RTT样本值？

这个问题无论你选那头都是按下葫芦起了瓢。 如下图所示：

![](../../images/tcp/tcp_Karn-Partridge-Algorithm.JPEG)

- 情况（a）是ack没回来，所以重传。
如果你计算第一次发送和ACK的时间，那么，明显算大了。

- 情况（b）是ack回来慢了，但是导致了重传，但刚重传不一会儿，
之前ACK就回来了。
如果你是算重传的时间和ACK回来的时间的差，就会算短了。

所以1987年的时候，
搞了一个叫[Karn / Partridge Algorithm](http://en.wikipedia.org/wiki/Karn's_Algorithm)，
这个算法的最大特点是 **忽略重传，不把重传的RTT做采样**（你看，你不需要去解决不存在的问题）。

但是，这样一来，又会引发一个大BUG  
**如果在某一时间，
网络闪动，突然变慢了，产生了比较大的延时，这个延时导致要重转所有的包（因为之前的RTO很小），
于是，因为重转的不算，所以，RTO就不会被更新，这是一个灾难。** 
于是Karn算法用了一个取巧的方式——只要一发生重传，
就对现有的RTO值翻倍（这就是所谓的 Exponential backoff），
很明显，这种死规矩对于一个需要估计比较准确的RTT也不靠谱。

Jacobson / Karels 算法
前面两种算法用的都是“加权移动平均”，这种方法最大的毛病就是如果RTT有一个大的波动的话，
很难被发现，因为被平滑掉了。所以，1988年，又有人推出来了一个新的算法，
这个算法叫Jacobson / Karels Algorithm（[参看RFC6289](http://tools.ietf.org/html/rfc6298)）。
这个算法引入了最新的RTT的采样和平滑过的SRTT的差距做因子来计算。 
公式如下：（其中的DevRTT是Deviation RTT的意思）

> SRTT = SRTT + α (RTT – SRTT)  —— 计算平滑RTT

> DevRTT = (1-β)*DevRTT + β*(|RTT-SRTT|) ——计算平滑RTT和真实的差距（加权移动平均）

> RTO= µ * SRTT + ∂ *DevRTT —— 神一样的公式

（其中：在Linux下，α = 0.125，β = 0.25， μ = 1，∂ = 4 ——这就是算法中的“调得一手好参数”，
nobody knows why, it just works…） 
最后的这个算法在被用在今天的TCP协议中
（Linux的源代码在：tcp_rtt_estimator）。




