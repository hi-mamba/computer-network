
 原文：<https://twitter.com/plantegg/status/1700011179324920117/photo/1>

# localhost和127.0.0.1的区别

所有本地地址都不会路由给物理网卡，都走了lo这个`虚拟网卡`
tcpdump 也能抓到所有127/localhost/本地ip的网络包
严格来讲都是走了网卡，只是这个网卡包含虚拟网卡，走lo也经历完整的内核网络协议栈
lo算是一个特殊的网卡


## 如何验证？

把 /etc/hosts 清空、dns cache flush
> 也是127.0.0.1 为啥呢？


查到是'RFC6761规定写死，这个是操作系统找不到hostfile会用默认值吗还是浏览器resolve的？

找不到的话就当成一个`普通域名来解析`，会丢给dns server，一般dns server也会返回127



##  127.0.0.1 比 loclhost 快 20%?

127更快是有可能的，少了一次域名解析，虽然dns的解析在本地完成。
去验证了一把localhost解析花了1毫秒，从发起到开始ping 127大概4毫秒，所以快20%大致可信，
如图2，不过这事没意思
![image](https://github.com/hi-mamba/computer-network/assets/7867225/642735b7-8fa9-4b4d-9cd7-e2ed4c47ea5d)

![image](https://github.com/hi-mamba/computer-network/assets/7867225/033f99ab-54ca-40a5-9760-e8dfe2605bc1)



