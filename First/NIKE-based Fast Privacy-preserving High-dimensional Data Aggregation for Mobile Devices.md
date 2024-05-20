# NIKE-based Fast Privacy-preserving High-dimensional DataAggregation for Mobile Devices

## 01 Abstract

在本文中，提出了一个安全的总和计算系统，其中服务器希望在不损害单个输入的情况下定期计算一组移动用户的私有输入的总和。首先，我们提出了一种用于移动用户的非交互式成对密钥生成方案，其中用户之间的非交互性通过将密钥材料生成任务外包给两个非串通的密码秘密提供者来实现。其次，我们设计了一种高效的聚合方案，具有较低的通信和计算开销以及故障鲁棒性。

## 02 Introduction

在这项工作中，我们考虑了用于机器学习的**移动设备数据的安全聚合**，场景中只有一组移动用户和一个云服务器，该服务器希望获得网络中存或的用户输入的总和。用户的移动性给当前用户输入的聚合计算带来了新的挑战，称为鲁棒性或故障鲁棒性(robust)，由于移动用户加入和退出系统，它们之间的通信是一个限制因素。

安全聚合的理想方案是拥有一个可信的聚合器，该聚合器收集来自用户的输入，计算总和发送给服务器。基于加密的解决方案为输入提供了更强的隐私保证，但是将加密的输入直接发送到服务器并不安全，因为服务器需要对其进行解密才能执行聚合求和。用户使用阈值加性同态加密方案加密输入并将密文发送到服务器的解决方案对于高维数据具有挑战性。特别是，服务器将密文相乘并获得用户输入总和的加密。然后服务器发送密文进行分布式解密。这种方法有两个主要缺点:    (1)服务器可能在发送解密之前通过在一个用户输入的加密中添加一些随机数来作弊;   (2)用户和/或服务器之间的分布式解密涉及至少一轮通信。考虑到用户退出的情况，这不是一个健壮的解决方案。在设计加密协议时，通常需要在计算工作量和通信开销之间进行权衡。

<u>Diffie-Hellman密钥交换协议建立成对密钥，涉及到用户数量的二次通信复杂度，开销很大</u>。在[7]中，用户输入被用户选择的密钥和网络中存在的所有其他用户的成对密钥之和双重屏蔽，其中用户选择的密钥和成对密钥的DH密钥在所有其他用户之间秘密共享，使用阈值秘密共享方案来处理故障恢复情况。对于大规模网络，每次<u>执行阈值秘密共享操作都是成本有效</u>的，而计算高维数据的成对密钥的附加噪声是一项昂贵的操作。

本文的目标是设计一种通信和计算效率高、适合于频繁的聚合和计算应用的私有数据聚合协议。解决这个问题的有效通信方案是部署**非交互式密钥交换**(NIKE)协议。我们对密钥交换阶段的解决方案是使用外包给一对不串通的加密秘密服务提供商的NIKE方案，而不是以分布式的方式自行运行协议，这意味着用户不需要彼此交互，只需在离线加入系统时与秘密服务提供商进行一次通信，并获得两对密钥计算材料或密钥材料(或者当密钥材料需要刷新时)。这使得用户之间的通信成本为零。

##  03 PRELIMINARIES

- PRG：伪随机生成器
- KDF：密钥派生函数(KDF)接受初始密钥材料并输出一个或多个加密强密钥。许多应用程序和协议需要kdf来派生密钥材料。密钥导出函数通常是伪随机函数，用于将密钥扩展为更长的密钥，也可到处有一个或多个随机key。

目前需要解决两个问题：1）密钥协商开销巨大；2）秘密分享和恢复很消耗时间

针对第一个问题，引入了两个不合谋的SSPs，为了解决第二个问题，引入了正则图和邻居用户的概念，每个用户只和邻居协商mask原始数据的噪声，并且share只使用了secret share(2,3)，在成对密钥的两个持有者和服务器 S之间共享，大大减少了秘密分享的开销。

<div align="center" >
<img width=400px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-56.png">
</img>
</div>

## 04 NON-INTERACTIVE PAIRWISE KEY ESTABLISHMENT SCHEME

在离线阶段生成两方需要的主密钥。

NPKC协议：在任意两个用户$U_i、U_j$之间，生成一个公共的主密钥$MK_{i,j}$，最后的成对加密密钥将由主密钥通过KDF生成。引入了两个不合谋的SSP--SSP1和SSP2，$SSP_i$分别持有自己的$k-1$次多项式$f_i(x)$，并且有$f(x)=f(x_1)f(x_2)$，最终，$U_i$将得到多项式$g_i(x)$用来生成最终的主密钥。

第一阶段协商参数和第二阶段的联合计算d，现有很成熟的方案，可继续参考论文。对于每个用户$U_j$，接受到两个SSP的$s_{1,j}$和$s_{2,j}$，有$s_{1,j}=f_1(j)^df_1(x)$、$s_{2,j}=f_2(j)^df_2(x)$。之后用户$U_i$本地计算$g_i(x)=s_{1,i}(x)s_{2,i}(x)=f(j)^df(x), 其中 f(x)=f(x_1)f(x_2)$。

当$U_i$计算与$U_j$之间的成对掩码时，$MK_{ij}=KDF(g_i(j)g(0)^{e-1})$，从论文中可知$MK_{ij}=MK_{ji}$

<div align="center" >
<img width=400px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-57.png">
</img>
</div>

介绍一下两方如何计算$s_{i,j}$,第一步使用协议$\pi_{MIC}$ SSP1和SSP2之间联合计算e的逆元d，并且分别拥有子秘密$d1$和$d2$，第二步通过协议$\pi_{EXP}$,SSP1和SSP2共同计算$f_1(j)^d$,之后SSP1又计算$s_{1,j}(x)=f_1(j)^df_1(x)$ 

<div align="center" >
<img width=500px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-57.png">
</img>
</div>

其中，子协议$\pi_{EXP}$如下所示：

<div align="center" >
<img width=400px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-58.png">
</img>
</div>

## 05 SECURE AGGREGATE SUM SCHEME

聚合阶段分为两个步骤，离线阶段每个用户从两个ssp接收两对秘密共享多项式，用于生成在聚合协议中需要使用的密钥材料，也从服务器接收到它的邻居信息。在线阶段服务器S和用户U运行聚合协议，服务器只接收到聚合和sum作为输出。

每个User都有两个秘密共享多项式$(s_{1,i}(x),s_{2,i}(x))$和$(c_{1,i}(x),c_{2,i}(x))$，它们用于NPKC派生成对屏蔽密钥和身份验证加密(AE)密钥。

- $MK_{i,j}^l=F(MK_{ij}||TS_l)$ and  $MK_{i,j}^{l,\tau} =F^{v-\tau}(MK_{i,j}^l||v||\tau)$
- $EK_{i,j}^l=F(EK_{ij}||TS_l)$ and  $EK_{i,j}^{l,\tau} = F^{v-\tau}(EK_{i,j}^l||v||\tau)$

在时间间隔$[T_l,T_{l+1}]$中的第$\tau$个会话，$0 \leq \tau \leq v-1$,$TS_l$是由服务器发送的普通时间戳。

在线阶段，将进行数据mask和加密(Mask-then-Encrypt),和Google不同的是，现在用户$U_i$并不是和全部的其他用户生成pairewise key来mask原始数据，而是只和自己的邻居协商一个mask噪声(成对密钥掩码)。

> Mask and Create One-time Pairwise Key Share

$$
y_i=x_i+\sum_{U_j\in N_U(U_i)}(-1)^{\delta_{i,j}}q_{i,j}
$$



$q_{i,j}$是由邻居用户在时间间隔$[T_l,T_{l+1}]$的第$\tau$轮生成的的一次性成对键$MK_{i,j}^{l,\tau}$，当$i\leq j时\delta_{i,j}=0$，反之等于1，也就是说当$i\leq j时等于1，i $ 。$q_{i,j}=PRG(MK_{i,j}^{l,\tau}, TS_{\tau})$, $U_j \in N_U(U_i)$。

用户$U_i$对一次性成对键$MK_{i,j}^{l,\tau}$在$U_i、U_j和S$之间进行一个$Share(2,3)$的分享，如果$U_i$或 $U_j$掉线，另.一个用户需要将它的分享发送给服务器S来恢复一次性成对键。

> Encrypt and Create One-time Key Shares

用户$U_i$使用一个新生成的密钥$k_i$,加入伪随机数到mask过后的数据上，
$$
\alpha_i = Enc_{k_i}(y_i)=y_i + k_i=x_i+k_i+\sum_{U_j\in N_U(U_i)}(-1)^{\delta_{i,j}}q_{i,j}
$$
其中$q_{i,j}=PRG(MK_{i,j}^{l,\tau},TS_{\tau}),k_i=PRG(k_i,TS_{\tau})$。$k_i$此时是使用$share(t,m)$在用户之间分享的。我们规定用户$U_i$的邻居掉线的最多为$\eta_{max}$,因为掉线太多无法恢复$k_i$

> Aggregation with Dropout

每个 用户收到来自服务器的请求后，检查系统中存活的用户数量是否至少t个，还有它的邻居掉线的个数少于$\eta_{max}$，然后执行以下操作：

1、对于所有在线用户$U_2$，包括自己，上传关于$k_i$的share；

2、对于所有掉线的邻居用户，上传对应的 one time pairwise key的shares；

服务器执行$Rec_m^t$获得所有$k_i$求得$\sum_{Ui\in U_2}y_i$，对于掉线用户 ，可以使用$Rec_3^2$恢复one-time pairwise key，然后消除对应的噪声，求得$\sum_{U_i\in U_2}x_i$。

---

和谷歌的安全聚合相比，本文在效率上有所提升。

1、引入了NIKE，在离线情况下计算主密钥，这种方法比DH更加高效；

2、引入了用户邻居和l-正则图的概念，每个用户的邻居数量是l个，每个用户只和邻居协商密钥，减少了需要协商的密钥个数和所花费的开销；

3、对于one-time pairwise key的秘密分享采用$share_3^2$来取代$share_m^t$，这样每个用户每次聚合最多执行$l/2$次$share_3^2$，比执行一次$share_m^t$高效的多；

4、服务器聚合阶段，用户根据不同的情况选择上传不同的数据，减少了通信开销。
