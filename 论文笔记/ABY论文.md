

$(pK_0)^a=(g^k)^a=(g^a)^k$,

$(PK_1)^a=C^a/(g^k)^a=C^a/(g^a)^k$
$$
c_{0,0}=En_{k_1^0,k_2^0}(k_3^0)=En_(k_1^0)(En_{k_2^0}(k_3^0))
$$

$$
c_{0,1}=En_{k_1^0,k_2^1}(k_3^0)=En_(k_1^0)(En_{k_2^1}(k_3^0))
$$

$$
c_{1,0}=En_{k_1^1,k_2^0}(k_3^0)=En_(k_1^1)(En_{k_2^0}(k_3^0))
$$

$$
c_{1,1}=En_{k_1^1,k_2^1}(k_3^1)=En_(k_1^1)(En_{k_2^1}(k_3^1))
$$

![image-20230726005709909](./assets\ABY论文\image-20230726005709909.png)

$恢复布尔秘密：a = a_1 \oplus a_2, b = b_1 \oplus b_2$

$AB共同计算,A本地计算a_1 \oplus b_1，B本地计算a_2 \oplus b_2，$
$$
(a_1 \oplus b_1) \oplus (a_2 \oplus b_2)= (a_1 \oplus r_12 \oplus r_21) \oplus (a_2 \oplus r_21 \oplus r_21) = a1 \oplus a_2
$$
$对于a \wedge b，这里使用OT来完成，$

$a \wedge b = (a_1 \oplus a_2) \wedge (b_1 \oplus b_2)=(a_1 \wedge b_1)\oplus(a_1 \wedge b2)\oplus(a_2 \wedge b1)\oplus(a_2 \wedge b_2)$ 

$(a_1 \wedge b_2) \oplus (a_2 \wedge b_1)$需要两者协作完成计算，可以通过四选一不经意传输协议完成，对于A来说，B手中的$a_2和b_2$都是单比特值，有四种情况$(0,0),(0,1),(1,0),(1,1)$。

A手中有$a_1和b_1$，A不知道$a_2和b_2$,因此A可以分别计算这4种情况下$(a_1 \wedge b2)\oplus(a_2 \wedge b1)$的值，假设为$c_{00},c_{01},c_{10},c_{11}(注意c是a\wedge b的值)$，

![image-20230726122843716](assets\ABY论文\image-20230726122843716.png)

Alice 再产生一个随机比特𝑟，并将计算出的$𝑐_{00},𝑐_{01},𝑐_{01},𝑐_{11}$异或上随机比特𝑟，将$𝑐_{00}⨁𝑟, 𝑐_{01}⨁𝑟, 𝑐_{01}⨁𝑟, 𝑐_{11}⨁𝑟$为四选一不经意传输协议的输入，Bob将其所掌握的$a_2,b_2$作为四选一不经意传输协议的输入，双方执行该OT协议，Bob得到对应的$$𝑐_{a_2b_2}⨁𝑟$$。


Alice将𝑟作为该与门的输出的子秘密，Bob将$𝑐_{a_2b_2}⨁𝑟$作为该与门的输出的子秘密。只有在最后双方都公布各自手中的计算结果，双方才能合力计算出正确的结果。



将GMW协议扩展到n方情况下：

1、参与者为$p_1,p_2,..p_n$，假设输入为m个比特，那么每个参与者$p_i$产生m组，每组$n-1$个比特。针对一个比特来说，将$n-1$个随机比特发送给其他$n-1$个参与者。

2、假设输入数据为1个比特，每个参与者$p_i$向参与者$p_j$发送$r_{ij},1\leq j\leq n 且i \neq j$，假设$p_i$的输入为$a$,那么$p_i$将$a \oplus r_{i1} \oplus r_{i2} \oplus ... \oplus r_{in}$作为$a$的子秘密$a_i$。

3、对应的收到$p_i$发送的随即比特$r_{ij}$的参与者$p_j$，将$r_{ij}$作为$a$的子秘密$a_j$，因此$a=a_1 \oplus a_2 \oplus... \oplus a_n$

![image-20230726125952641](assets\ABY论文\image-20230726125952641.png)

每个$bit$被拆成了n份，恢复秘密：

1、$a \oplus b =(a_1 \oplus a_2 \oplus...\oplus a_n) \oplus (b_1 \oplus b_2 \oplus...\oplus b_n)=(a_1 \oplus b_1)\oplus(a_2 \oplus b_2)\oplus.... \oplus(a_n \oplus b_n)$ 

2、$a \wedge b =(a_1 \oplus...\oplus a_n)\wedge(b_1 \oplus..\oplus b_n)=(\mathop{\oplus}\limits_{i \neq j}a_i \wedge b_j) \oplus(\mathop{\oplus}\limits_{i=1}^n a_i \wedge b_i)$

- 对于$i \neq j$，$p_i$和$p_j$利用不经意传输协议共同计算$a_i \wedge b_j$。

- $p_i$本地随机生成$c_{ij} \in \{0,1\}$，$f(x)=c_{ij} \oplus(a_i \wedge x)生成f(0),f(1)$

- $p_i与p_j$进行${OT}^2_1$，$P_i$提供输入$\{f(0,f(1)\}$，$P_j$根据$b_j$的真实值选择$f(b_j)$，记作$d_{ji}$，不难发现$c_{ij} \oplus d_{ji}=a_i \wedge b_j$。

- $P_i$本地计算$e_i= \mathop{\oplus}_{1 \leq j \leq n, j \neq i}c_{ij}$和$f_i= \mathop{\oplus}_{1 \leq j \leq n, j \neq i}d_{ij}$，本地计算$(a_i \wedge b_i) \oplus e_i \oplus f_i$。在此过程种$ai和b_i$的子秘密一直没有泄露，在保证隐私的情况下算出来AND。

公式：
$$
a \wedge b = (\mathop{\oplus}\limits_{i \neq j}a_i \oplus b_j)\oplus(\mathop{\oplus}\limits_{i=1}^n a_i \wedge b_i)\\
=(\mathop{\oplus}\limits_{i \neq j}c_{ij} \oplus d_{ij})\oplus(\mathop{\oplus}\limits_{i=1}^n a_i \wedge b_i)\\
= (\mathop{\oplus}\limits_{i =1}^n e_i) \oplus(\mathop{\oplus}\limits_{i =1 }^n f_i)\oplus(\mathop{\oplus}\limits_{i=1}^n a_i \wedge b_i)\\
=\mathop{\oplus}\limits_{i=1}^n(a_i \wedge b_i) \oplus e_i \oplus f_i
$$


---

作者提出了ABY框架，支持三种不同类型的分享（**A**rithmetic, **B**oolean, **Y**ao），并允许三者之间的有效转换

每种安全计算技术都使用最新的优化和最佳实践来完成（Paillier打包、DGK生成乘法三元组、OT等）

并对框架进行测试，使用基于OT的转换协议，不同共享之间的转换更加便宜且更好扩展。



# ABY – A Framework for EfficientMixed-Protocol Secure Two-Party Computation

## ***Preliminaries\***

#### **Two-Party Setting**

双方都提供自己的私人输入然后计算出最后的结果，过程中不学习任何中间信息

#### **Security Against Semi-Honest Adversary**

<u>协议使用半诚实对手模型，对手不允许偏离协议，需要正确的执行协议，但是试图在协议执行过程期间看到的信息中学习额外的信息。</u>虽然比恶意模型更具有限制性，但半诚实模型有许多应用，可以信任各方不会主动行为不当，半诚实模型使得开发高效的安全计算协议成为可能，因此被广泛用于实现隐私保护应用。

我们的工作重点是在半诚实对手模型中设计和实现高效的混合协议安全两方计算。

| $P_0,P_1$          | 参与方                                       |
| :----------------- | -------------------------------------------- |
| $x \oplus y$       | XOR                                          |
| $x \wedge y$       | AND                                          |
| $x[i]$             | $x中的第i个比特，x[0]最低有效位$             |
| $\kappa,\phi,\sigma$ | 系统安全参数 ($\kappa \in\{20,112,128\}$) |
|  | 公钥安全参数($\phi \in{1024,2048,3072}$) |
|  | 统计安全参数($\sigma=40$) |
| $\lang x\rang^t_i$         | $t\in(A,B,Y)$,其中$\lang x\rang_i^t表示P_i持有的份额$ |
| $\lang x\rang^t=Shr^t_i(x)$ | $P_i将自己的输入x分享给p_{1-i}$              |
| $x=Rec^t(\lang x\rang^t)$  | $P_i得到最后计算结果x$                       |

![image-20230726180047169](assets\ABY论文\image-20230726180047169.png)

#### Arithmetic Sharing

一个长度为$l-bit$的值$x$被分成两个在环$Z_{2^l}$上的值的和。假设所有算术运算都在环$2^l$上进行，$mod(2^l)$。

算术共享是基于在双方之间加性地共享私有值

$Share \_ Value$：对于一个长度为$l-bit$的$x$的Arithmetic Sharing$\lang x \rang^A$，有$\lang x \rang _0^A+\lang x\rang_1^A=x(mod 2^l)$，$P_0，P_1$分别持有两个份额。

$Share$：$Shr_i^A(x):P_i$选择随机数$r\in _R Z_{2^l}$，$令\lang x \rang_i^A=x-r,将r发送给P_{1-i},即\lang x\rang_{1-i}^A=r$

$Reconstruction$：$Rec_i^A(x):P_{1-i}将自己的值\lang x\rang_{1-i}^A发送给P_i,计算x=\lang x\rang_0^A+\lang x\rang_1^A$

$Addition:$$\lang z\rang^A=\lang x\rang^A+\lang y\rang^A$，$P_i$本地计算$\lang z\rang^A_i=\lang x\rang^A_i+\lang y\rang^A_i$
$$
\lang z\rang^A=\lang z\rang^A_0+\lang z\rang^A_1=(\lang x\rang_0^A+\lang y\rang_0^A)+(\lang x\rang_1^A+\lang y\rang_1^A)=x+y
$$
$Multipication$:$\lang z\rang^A=\lang x\rang^A \cdot \lang y\rang^A$，使用预先计算好的乘法三元组$\lang c\rang^A=\lang a\rang^A \cdot\lang b\rang^A$

生成随机的三元组$\lang a,b,c=ab\rang$，$a和b$对双方保密，两方分别持有$a和b$的秘密

- 双方$P_i$计算$\lang e\rang_i^A=\lang x\rang_i^A-\lang a\rang_i^A$和$\lang f\rang_i^A=\lang y\rang_i^A-\lang b\rang_i^A$
- 双方计算$e=\lang x\rang_0^A+\lang x\rang_1^A-(\lang a\rang_1^A+\lang a\rang_0^A)=x-a$

$f=\lang y\rang_0^A+\lang y\rang_1^A-(\lang b\rang_0^A+\lang b\rang_1^A)=y-b$

- $p_i计算\lang z\rang_i^A=i \cdot e \cdot f + f \lang a\rang_i^A+e\lang b\rang_i^A+\lang c\rang_i^A$
- 重构$z=\lang z\rang_0^A+\lang z\rang_1^A$

$$
\lang z \rang_0^A=f \lang a \rang_0^A +e\lang b \rang_0^A+\lang c \rang_0^A\\
\lang z \rang_1^A = ef + f\lang a \rang_1^A + e\lang b \rang_1^A+\lang c \rang_1^A\\
\begin{aligned}
z=\lang z \rang_0^A+\lang z \rang_1^A
&=ef+f\lang a \rang^A+e\lang b \rang^A+\lang c \rang^A\\
&=(x-a)(y-b)+(y-b)a+(x-a)b+c\\
&=xy
\end{aligned}
$$

---

**生成乘法三元组**
$$
\begin{aligned}
\lang c \rang^A&= \lang a \rang^A \lang b \rang^A\\
&=(\lang a\rang_0^A+\lang a\rang_1^A)(\lang b\rang_0^A+\lang b\rang_1^A)\\
&=\lang a\rang_0^A\lang b\rang_0^A+\lang a\rang_0^A\lang b\rang_1^A+\lang a\rang_1^A\lang b\rang_0^A+\lang a\rang_1^A\lang b\rang_1^A
\end{aligned}
$$
1、同态加密

![image-20230726141442639](assets\ABY论文\image-20230726141442639.png)

$Paillier:D(E(m_1) \cdot E(m_2))=m_1+m_2$

$E(m_1)E(m_2)=E(m_1+m_2)$

可以推出公式：$E(a)^b=E(b \cdot a)$

其中
$$
\begin{aligned}
d&=Enc_0(\lang a\rang_0^A)^{(\lang b\rang)_1^A} \cdot Enc_0(\lang b\rang_0^A)^{(\lang a\rang)_1^A} \cdot Enc_0(r)\\
&=Enc_0(\lang a\rang_0^A\lang b\rang_1^A+\lang b\rang_0^A\lang a\rang_1^A+r)\\
Dec_0(d)&=\lang a\rang_0^A\lang b\rang_1^A+\lang b\rang_0^A\lang a\rang_1^A+r\\
\lang c\rang_0^A&=\lang a \rang_0^A \lang b \rang_0^A+\lang a\rang_0^A\lang b\rang_1^A+\lang b\rang_0^A\lang a\rang_1^A+r\\
\lang c\rang_1^A&=\lang a\rang_1^A  \lang b\rang_1^A - r
\end{aligned}
$$


2、COT（correlated OTs）需要生成$2l个l位字符串的cOT，即c{OT}^{2l}_l$

$P_0随机生成\lang a \rang_0^A,\lang b\rang_0^A$，$P_1生成\lang a\rang_1^A ,\lang b\rang_1^A$，$\lang a\rang_i^A \lang b\rang_i^A$可以在本地完成计算。

- 计算$\lang a\rang_0^A\lang b\rang_1^A$：在$P_0和P_1之间进行cOT^l_l$，其中$P_0为S$，$P_1为R$。

总共进行$l$轮(秘密分享数值的二进制长度),第$i$轮中：

- $P_1$输入$\lang b\rang_1^A[i]$作为自己的选择比特，$P_0$输入相关函数$f(x)=(\lang a\rang_0^A \cdot 2^i-x)mod 2^l$

- $P_0$获得$s_{i0},s_{i1}$，其中$s_{i0}\in_RZ^{2^l},s_{i1}=f(s_{i0})=(\lang a \rang_0^A \cdot2^i-s_{i0})mod\, 2^l$
- $P_1$获得$s_{i,\lang b\rang_1^A[i]}=(\lang b\rang_1^A[i]\cdot\lang a\rang_0^A\cdot2^i-s_{i0})mod \,2^l$
- $\lang u\rang_0^A=(\sum_{i=1}^ls_{i0})mod \,2^l$
- $\lang u\rang_1^A=(\sum_{i=1}^ls_{i\lang b\rang_1^A[i]})mod \,2^l$

![image-20230726144046979](assets\ABY论文\image-20230726144046979.png)
$$
\begin{aligned}
\lang a\rang_0^A\lang b\rang_1^A&=\lang u\rang_0^A+\lang u\rang_1^A\\
&=\sum_{i=1}^l(s_{i0}+\lang b\rang_1^A[i] \cdot\lang a\rang_0^A\cdot2^i-s_{i0})\\
&=\sum_{i=1}^l(\lang b\rang_1^A[i] \cdot\lang a\rang_0^A\cdot2^i)\\
&=\lang a\rang_0^A \cdot \lang b\rang_1^A
\end{aligned}
$$


同理可得：$\lang v\rang^A=\lang \lang a\rang_1^A\lang b\rang_0^A\rang^A$

$\lang c\rang_i^A=\lang a\rang_i^A\lang b\rang_i^A+\lang u\rang_i^A+\lang v\rang_i^A$



#### Boolean Sharing

Bool Sharing与Arithmetic Sharing的实现相似

$Sharled \, Values:$$\lang x\rang_0^B,\lang x\rang_1^B$两方分别持有**某个比特**的分享，有$x=\lang x\rang_0^B \oplus \lang x\rang_1^B(\lang x\rang_i^B \in Z_2)$

$Sharing:$ $Shr_i^B(x):P_i$随机$r\in _R \{0,1\}$,计算$\lang x\rang_i^B=x\oplus r$,将$r$发送给$P_{1-i}$，即$\lang x\rang_{1-i}^B=r$

$Reconstruction:$$Rec_i^B(x):$ $P_{1-i}$将自己的$\lang x\rang_{1-i}^B$发送给$P_i$,$P_i$计算$x=\lang x\rang_0^B \oplus\lang x\rang_1^B$

$XOR:$$本地计算\lang z\rang_i^B=\lang x\rang_i^B \oplus \lang y\rang_i^B$,$\lang z\rang^B=\lang z\rang_0^B \oplus \lang z\rang_1^B=(\lang x\rang_0^B \oplus \lang y \rang_0^B) \oplus (\lang x\rang_1^B \oplus \lang y\rang_1^B)=x \oplus y$

$AND:$$\lang z\rang^B = \lang x\rang^B \wedge \lang y\rang^B$：使用预先计算好的布尔乘法三元组(Boolean multiplication triple),$(a,b,a\wedge b)$

$\lang c\rang^B=\lang a\rang^B \wedge \lang b\rang^B=(\lang a\rang_0^B \oplus \lang a\rang_1^B)\wedge(\lang b\rang_0^B \oplus \lang b\rang_1^B)=(\lang a\rang_0^B \wedge \lang b\rang_0^B)\oplus(\lang a\rang_0^B \wedge \lang b\rang_1^B)\oplus(\lang a\rang_1^B \wedge \lang b\rang_0^B)\oplus(\lang a\rang_1^B \wedge \lang b\rang_1^B)$

这里一样可以使用不经意传输，四选一、二选一,A先预处理结果，根据B的秘密来选择结果。

- $P_i$计算$\lang e\rang_i^B=\lang x\rang_i^B \oplus \lang a\rang_i^B$和$\lang f\rang_i^B=\lang y\rang_i^B \oplus \lang b\rang_i^B$
- 双方计算$e=Rec^B(e)=x\oplus a$和$f=Rec^B(f)=y\oplus b$
- $P_i计算\lang z\rang_i^B=ief \oplus f\lang a\rang_i^B \oplus e\lang b\rang_i^B \oplus \lang c\rang_i^B$

- 重构$z=\lang z\rang_0^B \oplus \lang z\rang_1^B$

$$
\begin{aligned}
z &= \lang z\rang_0^B \oplus \lang z\rang_1^B\\
&=f \cdot \lang a\rang_0^B \oplus e\cdot\lang b\rang_0^B \oplus\lang c\rang_0^B \oplus (e \cdot f \oplus f\cdot\lang a\rang_1^B \oplus\ e\cdot\lang b\rang_1^B\oplus \lang c\rang_1^B)\\
&=e\cdot f \oplus f\cdot(\lang a\rang_0^B \oplus \lang a\rang_1^B)\oplus e\cdot(\lang b\rang_0^B \oplus \lang b\rang_1^B)\oplus(\lang c\rang_0^B \oplus\lang c\rang_1^B)\\
&=(x \oplus a) \wedge (y \oplus b) \oplus((y \oplus b) \wedge a)\oplus((x\oplus a)\wedge b)\oplus(a \wedge b)\\
&=x \wedge y \oplus x \wedge b \oplus a \wedge y \oplus a \wedge b \oplus y \wedge a \oplus b \wedge a \oplus x\wedge b \oplus a \wedge b \oplus a \wedge b \\
&=x \wedge y
\end{aligned}
$$

- ROT生成布尔乘法三元组（More Efficient Oblivious Transfer and
  Extensions for Faster Secure Computation*）

乘法三元组$c_0 \oplus c_1=(a_0 \oplus a_1)\wedge (b_0 \oplus b_1)=(a_0 \wedge b_0)\oplus(a_0 \wedge b_1)\oplus(a_1 \wedge b_0)\oplus(a_1 \wedge b_1)$

![image-20230726154546190](assets\ABY论文\image-20230726154546190.png)

![image-20230726155053943](assets\ABY论文\image-20230726155053943.png)

算法中，a是随机生成的，作为ROT的输入，R没有获得关于$b=x_0 \oplus x_1$的信息,因为$x_0和x_1$是有ROT随机生成的，过程中S和R都不知道双方的信息，但持有了$a和b$的秘密(还有生成的$u和v$的秘密)。

算法中$ab=a(x_0 \oplus x_1)=(a(x_0 \oplus x_1)\oplus x_0)\oplus x_0=x_a \oplus x_0 = u \oplus v$

引入一个$f^{ab}功能$，即算法1中的ROT实现，双方不持有输入接收随即比特$(a,u),(b,v)$，约束$ab=u\oplus v$。让两方充当R和S，第一次调用中，$P_0$当作R接收$(a_0,u_0)$，$P_1$作为S得到$(b_1,v_1)$，有$(a_0b_1)=u_0\oplus v_1$。最后$p_1(a_0,u_0,b_0,v_0,c_0), p_2(a_1,u_1,b_1,v_1,c_1)$,其中$c_i=a_ib_i \oplus u_i \oplus v_i$。

从上我们得出$\lang c\rang_0^B=\lang a \rang_0^B\lang b\rang_0^B \oplus\lang u\rang_0^B \oplus\lang v\rang_0^B$和$\lang c\rang_1^B=\lang a \rang_1^B\lang b\rang_1^B \oplus\lang u\rang_1^B \oplus\lang v\rang_1^B$

从而$c=a\wedge b=\lang a\rang_0^B\lang b\rang_0^B \oplus(\lang u\rang_0^B\oplus \lang v\rang_1^B)\oplus(\lang u_1^B\oplus \lang v\rang_0^B)\oplus\lang a\rang_1^B\lang b\rang_1^B$

$=\lang a\rang_0^B\lang b\rang_0^B \oplus \lang a\rang_0^B\lang b\rang_1^B \oplus \lang a\rang_1^B\lang b\rang_0^B \oplus \lang a\rang_1^B\lang b\rang_1^B$



$MUX:$选择门$MUX(x,y,b):$如果比特b=0，则返回x，否则返回y，可以使用R-OT实现





#### Yao Sharing

$P_0作为garbler(加码器),P_1作为evaluator(评估方),P_0利用free-XOR和point-permute技术随机选择一个全局的k-bit的字符串R，其中R[0]=1$

对于每一条线路$w$,对应的$key$是$k_0^w\in _R \{0,1\}^k, k_1^w=k_0^w \oplus R,$最低有效位也称作置换为作为标签(permutation bit)。$k_1^w[0]=1-k_0^w[0]$

![image-20230723000616027](assets\ABY论文\image-20230723000616027.png)

$P_0$持有每一条线路$w$的两个密钥$k_0^w,k_1^w$，$P_1$持有其中的一个但不知道它对应的明文值(0,1)，对于l-bit的值，并行执行$l$次。

$Share \, Value:$ $\lang x\rang^Y$对$x$进行分享，$\lang x\rang_0^Y=k_0, \lang x\rang_1^Y=k_x=k_0 \oplus xR$

$Sharing:$ $Shr_0^Y(x):P_0令\lang x\rang_0^Y=k_0,然后将k_x = k_0\oplus xR发送给P_1$

$Shr_1^Y(x):$双方执行$C-OT_k^l$，其中$P_0$作为sender，输入相关函数$f_R(x)=(x \oplus R)$,得到$(k_0,k_1=k_0 \oplus R)$，$P_1$作为Receiver，将$x$作为选择比特，得到$\lang x\rang_1^Y=k_x$,可以发现当$x=0时，k_0\oplus xR=k_0;x=1时,为k_0 \oplus R=k_1$。$(k_0 \oplus R)x=k_0 \oplus xR$。

$Reconstruction:$$Rec_i^Y(x):$$P_1$将$\pi=\lang x\rang_1^Y[0]$发送给$P_0$，$P_0$计算$x=\lang x\rang_0^Y[0]\oplus\lang x\rang_1^Y[0]$

$XOR:$ $\lang z\rang^Y=\lang x\rang^Y \oplus \lang y\rang^Y$, $P_i$本地计算$\lang z\rang_i^Y=\lang x\rang_i^Y \oplus \lang y\rang_i^Y$，$\lang z\rang^Y=\lang z\rang^Y_0 \oplus\lang z\rang_1^Y$

$AND:$$\lang z\rang^Y=\lang x\rang^Y \wedge\lang y\rang^Y$

- $P_0$生成garbled table $Gb_{\lang z\rang_0^Y}(\lang x\rang_0^Y,\lang y\rang_0^Y)$，$P_0$将table发送给$P_1$，
- $P_1$使用密钥$\lang x\rang_1^Y,\lang y\rang^Y_1$解密



### Sharing Conversions

#### Y2B

Yao重构秘密时只需要用到permutation bit两个位的异或，处理方式是直接利用这个最低有效位。

$\lang x\rang_i^B=Y2B(\lang x\rang_i^Y)=\lang x\rang_0^Y[0]$



#### B2Y

和$Shr_1^Y$相似：对于单个bit，令$x_0=\lang x\rang_0^B,x_1=\lang x\rang_1^B$，$P_0$随机选择$\lang x\rang_0^Y=k_0 \in_R\{0,1\}^k$，然后双方执行OT，$P_0(sender)$将消息对$k_0\oplus x_0 R),k_0 \oplus(1-x_0)R$作为输入，$P_1(receiver)$将$x_1$作为选择比特，然后就可以得到$\lang x\rang_1^Y=k_0\oplus (x_0 \oplus x_1)R=k_x$。

当$x_1=0$时，$(k_0\oplus x_0R)x_1=k_0 \oplus(x_0 \oplus x_1)R$;

当$x_1=1$时，$(k_0\oplus (1-x_0)R)x_1=k_0 \oplus(x_0\oplus x_1)R$



#### A2Y

令$x_0=\lang x\rang_0^A,x_1=\lang x\rang_1^A$，然后得到$\lang x\rang_0^Y=Shr_0^Y(x_0),\lang x\rang_1^Y=Shr_1^Y(x_1)$,得到$\lang x\rang^Y=\lang x\rang_0^Y +\lang x\rang_1^Y$

论文中的"for l-bit values each operation is performed l times in paraller", 我的理解是Yao GC是针对单bit生成电路的，如果是l-bit就并行处理$l$次。所以，这里的$Shr_0^Y(x_0)代表是对x_0=\lang x\rang_0^A\in Z_{2^l}$的$l$次处理。



#### B2A

使用类似算术乘法三元组的方法，对**每个位**进行不经意传输，然后receiver将获得的值相加获得算术值

$P_0$作为sender,$P_1$作为receive一起执行OT，在第$i$轮OT中，$P_0$随机选择$r_i \in_R\{0,1\}^l$并得到消息对$(s_{i0},s_{i1})$,其中

$s_{i0}=(1-\lang x\rang_0^B \cdot 2^i -r_i)$

$s_{i1}=\lang x\rang_0^B[i]-r_i$

$P_1$用$\lang x\rang_1^B[i]$作为选择比特,并得到$s_{\lang x\rang_1^B[i]}=(\lang x\rang_0^B[i] \oplus\lang x\rang_1^B[i])\cdot 2^i-r_i$,

可以看出上面的$s_{i0},s_{i1}$就对应$\lang x\rang_1^B[i]$分别取0和1的情况，然后让$P_1$进行OT传输选择。

最后$P_0$计算$\lang x\rang_0^A=\sum_{i=1}^lr_i$,

$P_1$计算
$$
\begin{aligned}
\lang x\rang_1^A&=\sum_{i=1}^ls_{\lang x\rang_1^B[i]}\\
&=\sum_{i=1}^l(\lang x\rang_0^B[i] \oplus\lang x\rang_1^B[i]\cdot 2^i-\sum_{i=1}^lr_i)\\
&=\sum_{i=1}^lx[i]\cdot2^i-\sum_{i=1}^lr_i\\
&=x-\lang x\rang_0^A
\end{aligned}
$$


#### Y2A

$\lang x\rang^A=Y2A(\lang x\rang^Y)=B2A(Y2B(\lang x\rang^Y))$





### Design and Implementation

我们的ABY框架的主要设计目标是<u>实现高效的在线阶段</u>，这就是为什么我们在设置阶段并行批量预计算所有加密操作的原因(<u>在线阶段中唯一剩余的加密操作是用于评估乱码电路的对称加密)</u>。如果无法进行预计算，则可以将设置阶段和联机阶段交叉进行，以减少总计算时间。

我们的框架采用模块化设计，可以很容易地扩展到额外的安全计算方案、计算体系结构和新的操作，同时还允许在实现的所有级别上进行特殊目的的优化。我们的框架通过对共享和协议细节的内部表示进行抽象来关注应用程序。

我们在c++ GMW和Yao的乱码电路实现[19]的基础上，采用优化的两方GMW例程[69]、固定密钥AES乱码例程[7]和OT扩展实现[1]。然而，我们<u>没有使用散列函数实例化相关鲁棒单向函数H</u> (cf. xII-D)，而是使用固定密钥AES。特别地，我们计算:H(x;t) = AESK(x⊕t)⊕x⊕t，其中K是一个固定的AES密钥，t是一个(唯一的)单调递增计数器(类似于[7])。使用Paillier和DGK生成算术乘法三元组是使用GNU多精度算术库(GMP)用C编写的，并受到libpaillier的启发。

#### Efficient Multiplication Triple Generation

1、我们包含了[26]中提出的对Paillier密码系统的几种算法优化，并使用打包[62]，<u>[66]将多个乘法三元组组合成一个Paillier密文</u>。我们对Paillier和DGK的实现优化包括使用固定基数幂和中国剩余定理(CRT)进行加密，以及使用CRT进行解密。在乘法三重协议中，我们使用双基幂。

我们使用了两种部署场景:本地设置(具有低延迟、高带宽的网络)和洲际云设置(具有高延迟的网络)。这两种场景涵盖了设计空间中的两种极端情况:影响布尔和算术共享性能的延迟

---

本地设置。在本地设置中，我们在两台台式机上运行基准测试，每台台式机都配备了英特尔Haswell i7-4770K CPU, 3.5 GHz和16gb RAM，通过千兆局域网连接。本地设置中的平均运行时方差为15%。对于使用流水线计算过程的算法(例如，乘法三代算法)，我们发送大小为50kb的数据包。

云设置。在云环境中，我们在两台Amazon EC2 c3上运行基准测试。大型实例具有64位Intel Xeon双核CPU, 2.8 GHz和3.75 GB RAM。一个虚拟机位于美国东海岸，另一个位于日本。该场景的平均带宽为70mbit /s，延迟为170ms。在我们的测量中，我们很少遇到超过平均运行时间两倍的异常值，这可能是由网络引起的，我们从结果中省略了这一点。在云设置中得到的平均运行时方差为25%。对于使用流水线计算过程的算法，我们在比本地设置更大的块上操作，并发送大小为32 MB的数据包，以实现更少的通信轮数。

---

所有机器都**使用AES新指令集(AES- ni)来实现对称加密操作的最大效率**。除非另有说明，所有实验均为平均执行10次。

我们针对遗留、中期和长期安全参数(参见xIII-A，文中指的是公钥安全参数)对算术乘法三元组(用于算术共享中的乘法)的生成进行基准测试。

xII-C)和编程语言中使用的典型数据类型大小($l\in {8;16;32;64}$)，使用两个线程。我们测量了100,000个乘法三元组的生成，<u>不包括base- ot和公钥和私钥的生成时间</u>，我们在附录xA中分别描述了它们

表中描述了生成一个乘法三元组的通信成本和平均运行时间

![image-20230726181052168](assets\ABY论文\image-20230726181052168.png)

基于OT的协议总是比基于Paillier和基于DGK的协议更快。在乘法三元组的构造中，基于OT的构造协议比基于同态的构造协议，由于加密指数更短，密文大小更小，DGK在所有参数上都比Paillier更有效，但是DGK的运行时间很大程度上取决于乘法三元组的位长，所以对于非常大的之Paillier更可取

在通信方面，基于DGK的协议比基于OT的协议在更长的比特长度（l=32,64）上更好，<u>而对于短的比特长度，OT表现更好</u>

----

论文通过三个示例应用程序，演示了ABY框架可以用于多个隐私保护应用程序

#### Modular Exponentiation

我们通过使用平方乘算法实现模幂，给出ABY框架中功能描述的示例。我们将功能实例化为混合(a +B+Y)和纯Yao(仅Y)协议，并对两者进行基准测试。

在我们的协议描述中，我们需要为所使用的每个共享显式实例化一个对象:算术共享as、布尔共享b和Yao共享ys。这些对象为每个共享中的原子操作提供接口，并从底层表示中抽象出来。对应的共享类型分别为ashr、bshr和yshr。模幂函数的输入是一个底数$base$，一个指数$exp$，模数$mod$和输入$len$的长度。在Arithmetic算数共享中执行乘法(MUL)，在Yao共享中执行化简(rem)，在Boolean中执行条件乘法(根据共享的指数位0和1进行选择相乘)。也就是说算术共享中共享基数、布尔共享中共享指数、在姚共享中共享模数。

- 在本地和云场景中，对' l-bit= 32位值进行单个原子操作的设置时间(以µs为单位)和通信(以字节为单位)，使用长期安全参数平均超过1000次操作。

![image-20230727144638122](assets\ABY论文\image-20230727144638122.png)

![image-20230727145843354](assets\ABY论文\image-20230727145843354.png)

模块化求幂:清单1中关于len= 32位输入和长期安全性的模块化求幂的设置、在线和总运行时间(单位)、通信和消息数。最小的条目用粗体标出。

我们可以观察到A+B+Y协议有大量的通信轮，这是由于大量的A2Y和y2a转换。因此，它在本地设置中比Y-only协议性能更好，但在具有更高网络延迟的云设置中性能更差。两种协议的通信复杂度也相似，这是由于在Yao共享中评估的rem操作造成的，是主要的通信瓶颈。

#### Private Set Intersection

在私有集合交集应用程序中，双方<u>希望确定它们的n个元素集合的交集</u>，而<u>不透露交集中不包含的元素</u>。计算私有集交集功能的布尔电路在[37]中有描述，并使用Yao的乱码电路协议进行评估。对于具有较长位长度元素的较大集，**排序-比较-洗牌集交叉电路**是最有效的;对于n位元素的集合，该电路有O(n log2 n)个与门。文献[64]对现有基于各种技术的PSI协议进行了比较。其中，他们使用纯Yao的乱码电路和纯GMW执行PSI，并比较它们在不同设置下的性能。

ABY框架中实现了[37]的排序-比较-洗牌电路，并将其<u>实例化为三个版本</u>:<u>仅Yao实例化</u><u>(Y-only)</u>，<u>仅布尔实例化(B-only)</u>和<u>混合实例化(B+Y)</u>，该实例化使用Yao共享评估排序和比较部分，使用<u>布尔共享评估洗牌部分</u>布尔共享得益于对频繁出现在电路shuffle部分的MUX操作的改进评估。

- 在本地和云场景中，对' l-bit= 32位值进行一次原子操作的在线时间(以µs为单位)和通信(以字节为单位)，使用长期安全参数平均超过1000次顺序/并行操作。

![image-20230727150641343](assets\ABY论文\image-20230727150641343.png)

![image-20230727150745725](assets\ABY论文\image-20230727150745725.png)

Private Set Intersection应用程序在**n = 4096个长度为32位**和长期安全性的元素上的设置、在线和总运行时间(单位为秒)、通信和消息数量。最小的条目用粗体标出。

延迟(Seq)：延迟取决于部署场景的延迟，虽然在本地中从Y到A比单纯在Y中运行乘法更有效，但是在云端中，用Y执行乘法更有效，这是因为在A和B中有较多的通信轮次，相比之下，Y具有恒定数量的交互轮次，因此更适合高延迟的网络

吞吐量(Par)：电路中运算的并行实例大大跳了A和B的在线运行时间，虽然Y也受益于并行的电路实例化，但是Y只需要对一个电路进行构建和多次并行的评估，所以A和B比Y更有利

从图中可以看出，共享之间的转换成本很小，甚至允许单个操作进行一轮完整的转换，以乘法为例，表现最好的是Arithmetic Sharing，从Y转换为A再转换回来比在Y中执行乘法更好

#### **Biometric Matching**

一方想要确定其生物特征样本是否与存储在另一方持有的数据库中的多个生物特征样本匹配，计算查询与数据库中所有生物特征之间的欧氏距离的平方，然后确定这些距离中的最小值。

每个特征样本有4个维度且长度均为32bit，数据库大小$n=512$，其中$P_0$输入数据库$S_{i,j}$，$P_1$输入样本$C_i$，然后双方共同计算$min(\sum_{i=1}^d(s_{i,1}-C_i)^2,....\sum_{i=1}^d(s_{i,n-C_i})^2)$

![image-20230727153455103](assets\ABY论文\image-20230727153455103.png)

混合协议比单个协议表现要好。通信降低了近20倍，A+Y在在线阶段和总运行时间里表现最佳，比单纯的协议快了近7倍。

使用混合协议要比单个协议更好。
