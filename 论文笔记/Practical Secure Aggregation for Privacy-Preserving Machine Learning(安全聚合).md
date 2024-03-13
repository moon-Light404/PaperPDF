# Practical Secure Aggregation for Privacy-Preserving Machine Learning(安全聚合)

## Abstract

我们为高维数据的安全聚合设计了一种新颖、通信高效、故障健壮的协议。我们的协议允许服务器以安全的方式(即无需学习每个用户的个人贡献)计算来自移动设备的大型用户持有数据向量的总和，并且可以用于，例如，在联邦学习设置中，为深度神经网络聚合用户提供的模型更新。我们证明了我们的协议在诚实但好奇和活跃的对手设置下的安全性，并表明即使任意选择的用户子集在任何时候退出，安全性也是保持的。我们评估了协议的效率，并通过复杂性分析和具体实现表明，即使在大型数据集和客户端池上，其运行时和通信开销仍然很低。

<u>安全计算向量和协议</u>

## SECURE AGGREGATION FOR FEDERATED LEARNING

因此，联邦学习激发了对安全聚合协议的需求:

(1)在高维向量上操作

(2)通信效率高，即使每个实例上都有一组新的用户

(3)对用户退出具有鲁棒性

(4)在服务器中介的、未经身份验证的网络模型的约束下提供最强的安全性

##  CRYPTOGRAPHIC PRIMITIVES

- 秘密分享(Secret Sharing)

We rely on Shamir’s t-out-of-n Secret Sharing [48], which allows a user to split a secret s into n shares, such that any t shares can be used to reconstruct s, but any set of at most t − 1 shares gives no information about s.

方案要求客户端保密两两之间的共享密钥，共享算法$SS.share(s,t,U) \rightarrow {(u,s_u)_{u \in U}}$，输入一个秘密s，用户集合U，阈值t，最终生成s秘密在各用户的子秘密$s_{u_i}$。

重构算法$SS.recon({u,s_u})_{u \in V,t} \rightarrow s$ ，根据某一子集$|V| \ge t$所对应的秘密份额作为输入，恢复出秘密s。



- 伪随机数生成器(Pseudorandom Generator)

给定一个随机数种子，生成无关的随机数，使用PRG(seed)来表示输出的随机数。



- 密钥协商(Key Agreement)

DH密钥交换是让A和B双方拥有一个的密钥，这个密钥只有A和B知道。

1. 首先，A和B商量好参数，一个大素数q，和$Z_q$上的一个生成元$g(1 \lt g \lt q)$。表示为$KA.param(k) \rightarrow(G',g,q,H)$,$H$为哈希函数
2. 然后各方通过公共参数生成各自的公私钥，$KA.gen(G',g,q,H) \rightarrow (x,g^x)$,选择随机的$x \rightarrow Z_q$，$x作为私钥s_u^{SK},g_x作为公钥s_u^{PK}$
3. A和B分别将公钥发送给对方
4. 双方生成通信密钥，$s_{u,v}=H((g^{x_v})^{x_u})$,表示为$KA.agree(x_u,g^{x_v}) \rightarrow s_{u,v}$。$KA.agree(s_u^{SK},s_v^{PK})$允许**任何用户$u$将它的私钥$s_u^{SK}$与$v$的公钥$s_v^{PK}$组合在一起获得$u$和$v$之间的共享私钥$s_{u,v}$。**





- 数字签名(Signature Scheme)

对一段明文进行数字签名，以保证其内容没有被篡改。

1. 密钥生成算法$SIG.gen(k) \rightarrow (d^{PK}, d^{SK})$以安全参数为输入，输入一个密钥$d_{SK}$和公钥$d_{PK}$
2. 签名算法对一段消息m用私钥进行签名，$SIG.sign(d^{SK},m) \rightarrow \sigma$
3. 用公钥进行验证，$SIG.ver(d^{PK},m,\sigma) \rightarrow \{0,1\}$，返回一个表示该签名是否有效的比特

## Technical Intuition

单个服务器S聚合来自n个客户端u的输入，每个用户$u \in U$持有私有向量$x_u$，协议的目的是以一种安全的方式计算，保证服务器只学习客户最终聚合的结果，并且用户什么也学习不到。

### Masking with One-Time Pads

假设每一对用户$(u,v)$，$u<v$在某个随机向量$s_{u,v}$上一致，如果u加上它，v减去它，最终可以相互抵消，却掩盖了真实的输入值。每个u计算：
$$
y_u = x_u + \sum_{v \in U:u \lt v}s_{u,v} - \sum_{v \in U:u \gt v }s_{v,u}\pmod R
$$
然后每个$u_i$发送$y_u$给服务器S，S计算：
$$
\begin{aligned}
z&=\sum_{u \in U} y_u \\
&=\sum_{u \in U}(x_u + \sum_{v \in U:u \lt v}s_{u,v}-\sum_{v \in U:u \gt v}s_{v,u}) \\
&=\sum_{u \in U}x_u \pmod R
\end{aligned}
$$

这种方法有两个缺点。首先，用户必须交换随机向量$s_{u,v}$，如果简单地这样做，将需要二次通信开销(|U | * |x|)。

第二是不能容忍一方无法完成协议:如果用户u在与其他用户交换向量后退出，但在将u提交给服务器之前，与u相关的向量掩码不会在总和z中被取消。



###  Efficient Communication and Handling Dropped Users

我们可以通过让各方同意伪随机数生成器(PRG)的共同种子而不是整个掩码$s_{u,v}$来减少通信，这些共享的种子将通过广播Diffie-Hellman公钥并参与密钥协议计算。

处理退出用户的一种方法是将退出通知幸存的用户，并让每个用户使用他们与被删除的用户计算的共同种子进行回复。这种方法仍然存在一个问题:在回复种子之前，其他用户可能会在恢复阶段退出，这将需要一个额外的恢复阶段来报告新丢弃的用户的种子，依此类推，导致轮询的数量最多达到用户数量。

<u>使用阈值秘密共享方案并让每个用户将其Diffie-Hellman秘密的共享发送给所有其他用户</u>。这允许即使在恢复过程中有其他参与方退出，也可以恢复成对种子，只要有最小数量的参与方(等于阈值)仍然存活，并使用被删除用户的密钥共享进行响应。

但是存在一个问题：用户u向服务器发送请求太慢，服务器认为用户已掉线，并要求其他用户提供它们与$u$有关的密钥秘密$s_{u,v}$，从而在最终计算中抵消与$u$相关的掩码值。服务器可能推迟接收到来自客户端$u$的信息$y_u$，此时服务器可以根据$y_u$和$s_{u,v}$恢复出$x_u$，从而导致客户端信息泄露。(注意假设有三个客户端，2号掉线，此时服务器计算的目标实际上是$y_1+y_3$)

![aa](<assets/Practical Secure Aggregation for Privacy-Preserving Machine Learning(安全聚合)/image-20230825172539235.png>)





### **Double-Masking(Pair-wise masking)**

作者引入一个双掩码结构，即使服务器重构了$u$的掩码，也可以保护$x_u$。每个用户额外生成一个随机种子$b_u$，通过秘密共享将$b_u$的份额分发给其他用户，则:
$$
y_u=x_u+PRG(b_u)+\sum_{v \in U:u \lt v}PRG(s_{u,v})-\sum_{v \in U:u \gt v}PRG(s_{v,u})\pmod R
$$

- 在分发阶段，随机数$b$和$s$均采用秘密分享的方案分发出去，保证$t$个用户就可以恢复。
- 在恢复阶段，对于一个诚实的用户$v$，它不会把$b_u$和$s_{u,v}$（份额）同时说出去。如果用户$u$掉线，那么$v$会说出$s_{u,v}$,如果$u$在线，那么$v$会说出$b_u$。

在所有用户都不掉线的情况下，反正$b$是可以恢复出来的，最终求和的时候将$b_u$减去即可。存在用户$u$掉线的情况下，$x_u$不参与求和，此时$b_u$没有用处，只需恢复$s_{u,v}$。即使服务器获得了用户$u$的掩码$s_{u,v}$，但是不知道$b_u$的值，所以也就不能恢复$x_u$，从而保护了用户隐私。

![image-20230825175659266](<assets\Practical Secure Aggregation for Privacy-Preserving Machine Learning(安全聚合)\image-20230825175659266.png>)

在恢复阶段，第4轮中，对于客户端$u$:

在$U_2$中处理$u$的其他客户端中，对于每个客户端$v$，对认证加密后的$e_{u,v}$进行解密得到$s_{v,u}^{SK}||b_{v,u}$，根据用户$u$在线情况选择性的发送给服务器。如果$u$掉线，发送$s_{v,u}^{SK}$，如果在线，就发送$b_{v,u}$。

对于服务器端：

收集从客户端中收集的最少$t$个信息，这群客户端记为$U_5$，<u>对于掉线的客户端，恢复</u>$s_u^{SK}$，$s_u^{SK} \leftarrow SS.recon(\{s_{u,v}^{SK}\}_{v \in U_5},t)$，将它与其他客户端的DH公钥$s_v^{PK}$就可以得到所有的$s_{u,v}$，再送入PRG中计算。

同理，对于正常的客户端，恢复出$b_u$，$b_u \leftarrow SS.recon(\{b_{u,v}\}_{v \in U_5},t)$ ,再送入PRG中计算。最终恢复结果时减去$b_u$，还有加减对应的$s_{u,v}$(文中定义了一个$p_{u,v}$来代表正负)。

服务器聚合公式：
$$
\sum_{i \in U}x_i=\sum_{i \in U}(y_i-PRG(b_i))+\sum_{i \in D}\Big(\sum_{j:i<j}PRG (s_{i,j})-\sum_{j:i>j}PRG(s_{j,i})\Big)
$$
其中U表示在线用户，D表示掉线的用户。



#### 参考

https://zhuanlan.zhihu.com/p/445656765

https://dl.acm.org/doi/abs/10.1145/3133956.3133982

https://blog.csdn.net/wenzhu2333/article/details/107704778
