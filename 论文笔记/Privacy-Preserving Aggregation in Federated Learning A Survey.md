# Privacy-Preserving Aggregation in FederatedLearning: A Survey

## INTRODUCTION

与PPML相比，PPFL进一步考虑了异构参与者，例如不同的计算能力和带宽，以及更复杂的隐私和安全威胁模型。

主要分类包括(1)基于掩码的聚合，(2)基于HE的聚合，(3)基于mpc的聚合，(4)基于DP的聚合，(5)基于区块链的聚合，(6)基于TEE的聚合。

---

## OVERVIEW

**Cross-Silo setting and Cross-Device setting**

 ![image-20230916121142811](<assets\Privacy-Preserving Aggregation in Federated Learning A Survey\image-20230916121142811.png>)

**Privacy Threats to Federated Learning**

- 对用户模型的隐私威胁：单个FL参与者的局部数据可以通过其局部训练模型的一小部分被泄露
- 对全局模型的隐私威胁：标准FL方案假设全局模型是明文，需要考虑全局模型的私密性
- 单个或多个攻击者合串通
- 诚实:诚实地遵守协议。
- 被动恶意(诚实但好奇或半诚实):试图在不偏离协议的情况下了解诚实参与者的私人信息。
- 主动恶意(当上下文明确时为恶意):可以随时以任何方式偏离协议，例如操纵身份或向他人发送欺诈性消息。

---

##  TECHNIQUES FOR PRIVACY-PRESERVING AGGREGATION

### 1.One-time Pad

在密码学中，一次性密钥(OTP)是一种发送方随机生成只用于加密消息一次的私钥的技术。对于解密，接收方需要使用匹配的OTP作为密钥。这样，OTP的随机性保证了每个消息的加密都是唯一的，与其他任何加密都没有关系，因此无法破解OTP加密的消息。因此，OTP加密系统提供了可证明的无条件安全性[38]。

### 2.Homomorphic Encryption

同态加密(HE)是一种允许对加密数据进行函数求值，同时保留功能特征和数据格式的加密方案，作为密钥对$(p_k,s_k)$，对于两个消息$m1和m_2$，可以使用$Enc(m_1,p_k)和Enc(m_2,p_k)$计算$Enc(m_1+m_2,p_k)$，并且不泄露$m_1和m_2$。

根据对加密数据进行算术运算的次数进行分类：部分同态加密(支持一种类型的运算)、有限同态加密、完全同态加密(无限类型算术运算和次数)

### 3.Secure Multi-Party Computation

##### Shamir Share

Shamir秘密共享利用非线性映射来重建秘密。具体来说对于$a(t,n)$的Shamir方案，要共享一个秘密s，需要随机选择$t-1$个元素$a_1,a_2,a_3,..a_{t-1}从有限域F_p$，其中$a_0 = s$。
$$
f(x)=a_0+a_1x+a_2x^2+....+a_{t-1}x^{t-1} \pmod p
$$
那么在由拉格朗日多项式定义的曲线上除了点$(0,s)$，取不同的x代入计算得到$(x_i,y_i)$分发给n个参与方，为了重建秘密s，一旦收集到至少$t$个Shamir股份$(x_i,y_i)$，计算上述拉格朗日多项式的常数项
$$
s=f(0)=\sum_{j=0}^{t-1}y_j \prod_{m=0,m\neq j}^{t-1} \frac{x_m}{x_m-x_j}
$$
Shamir方案的阈值结构使得它可以很自然地用于处理丢失用户和构造验证协议。

### 4.Differential Privacy



### 5.Blockchain

区块链起源于加密货币，即比特币的概念，作为一种防篡改和去中心化的分类账，以透明和不可变的方式记录一组有序的交易。具体来说，这些交易通过去中心化的共识协议由无信任的区块链节点进行验证，并在附加到区块链之前构建成块。

除了交易，一个块还包含前一个块的加密散列，这提供了可链接性和可追溯性。在这里，我们总结了区块链网络可以提供的主要优势如下:

- 去中心化：除了交易，一个块还包含前一个块的加密散列，这提供了可链接性和可追溯性。在这里，我们总结了区块链网络可以提供的主要优势如下
- 不变性:存储在区块链分类账中的交易不能被改变或篡改，除非大多数节点受到损害。区块链中使用的加密技术保证了这种安全性，所有区块链节点都可以观察到交易数据的任何变化
- 透明度和可审计性:存储在区块链分类账中的交易对所有区块链节点都是可见的，并且可以追溯到验证
- 匿名性：通过使用数字签名技术，区块链允许节点以匿名的方式执行交易

智能合约：可以将其写入代码行，并在满足预定义条件时自动执行，可以实现更复杂的功能评估(聚合操作)。智能合约一旦部署在区块链上就无法修改，其执行也是去中心化的，保证了控制功能的稳定可靠。

### 6. Trusted Execution Environment(TEE可信执行环境)

可信执行环境，由GlobalPlatform所定义，是主处理器的一个安全区域，允许敏感数据和代码在一个隔离和可信的环境中进行存储、处理和保护。TEE和纯软件环境隔离。

TEE的实现，例如[68]，[69]，由硬件enclave支持，例如Intel SGX[70]和ARM TrustZone[71]，其中存在计算资源(例如有限的内存大小)和提供的安全级别之间的权衡。请注意，与基于REE的应用程序相比，TEE通常涉及硬件方面的额外成本，这可能会阻碍其大规模部署。

---

## PRIVACY-PRESERVING AGGREGATION PROTOCOLS IN FEDERATED LEARNING

### Masking-based Aggregation

###  HE-based Aggregation



阈值同态加密(threshold addtive HE crypto-system)

![image-20230916173731788](<assets\Privacy-Preserving Aggregation in Federated Learning A Survey\image-20230916173731788.png>)

阈值同态加密算法中存在多个私钥、一个公钥，使用该公钥系统加密的密文之间可以相互计算，并且只有当参与解密的私钥数量达到一定阈值时，才能成功解密密文



### MPC-based Aggregation



### DP-based Aggregation

