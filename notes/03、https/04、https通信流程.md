
## [原文](https://www.jianshu.com/p/e195c6dd3a86)

# https 通信流程


为了搞清楚，https是怎样运转的，接下来是借鉴一篇博文，并稍加改动，形象易懂的解释了整个流程。

- step1： “客户”向服务端发送一个通信请求

>“客户”->“服务器”：你好

step2： “服务器”接受到客户端发来的请求然后向客户返回自己的数字证书。证书中有一个公钥用来加密信息，私钥由“服务器”持有

>“服务器”->“客户”：你好，我是服务器，这里是我返回的数字证书

step3： “客户”收到“服务器”的证书后，它会去验证这个数字证书到底是不是“服务器”的，数字证书有没有什么问题，
数字证书如果检查没有问题，就说明数字证书中的公钥确实是“服务器”的。
检查数字证书后，“客户”会发送一个随机的字符串给“服务器”用私钥去加密（检查数字证书，后文介绍）

>“客户”->“服务器”：向我证明你就是服务器，这是一个随机字符串

step4：服务器把加密的结果返回给“客户”。

>“服务器”->“客户”：{一个随机字符串}（用私钥进行RSA加密）

step5：“客户”用公钥解密这个返回结果，如果解密结果与之前生成的随机字符串一致，
那说明对方确实是私钥的持有者，或者说对方确实是“服务器”。 

验证“服务器”的身份后，“客户”生成一个对称加密算法和密钥，用于后面的通信的加密和解密。
这个对称加密算法和密钥，“客户”会用公钥加密后发送给“服务器”，别人截获了也没用，因为只有“服务器”手中有可以解密的私钥。
这样，后面“服务器”和“客户”就都可以用对称加密算法来加密和解密通信内容了。

>“服务器”->“客户”：{OK，已经收到你发来的对称加密算法和密钥！有什么可以帮到你的？}（用密钥进行对称加密）
“客户”->“服务器”：{我的帐号是aaa，密码是123，把我的余额的信息发给我看看}(用密钥进行对称加密)
“服务器”->“客户”：{你好，你的余额是100元}(用密钥进行对称加密)

 