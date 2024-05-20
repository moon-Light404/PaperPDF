# Active Data Reconstruction Attacks in Vertical Federated Learning

### 摘要
尽管近年来对 VFL 的隐私漏洞和防御策略进行了大量研究，但重点主要集中在攻击者遵守协议的被动场景上。这种观点削弱了实际威胁，因为攻击者可以偏离协议来提高其推理能力。为了解决这一差距，我们的研究引入了两种创新的数据重建攻击，旨在损害活动环境中的数据隐私。本质上，这两种攻击都会修改 VFL 训练阶段计算的梯度，从而侵犯隐私。我们的第一次攻击使用主动反转网络，利用训练集中的一小部分已知数据来强制被动参与者训练自动编码器以重建其私有数据。第二种攻击是主动生成网络，利用训练数据分布的知识来引导系统训练条件生成网络（C-GAN）以进行特征推断。我们的实验证实了这两种攻击在从现实世界数据集中推断私有特征方面的有效性。
![20240527164640](https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/20240527164640.png)

**主动攻击的设置**。在主动攻击中，主动参与者计算篡改的梯度并将其传输给其他参与者以操纵其模型的更新。例如，我们提出的方法利用自动编码器重建损失 LAE 和生成损失 LGAN 而不是正常损失来计算梯度，如图 1b 和 1c 所示。值得注意的是，主动对手还有能力调整全局模型的后续层以增强隐私推理。由于被动参与者不知道对手拥有的编码器 $f_0$ 和后续层，因此他们没有简单的方法来检测梯度是否已被篡改。

>We now discuss our black-box data reconstruction attacks. Our methods leverage an under-exploited capability of the active participants in VFL, which is its control over the gradients transmitted toward the passive participants. As outlined in the threat models, each of our attacks operates with minimal assumptions about the private training data. A fundamental distinction in our active adversaries, as opposed to prior passive approaches, lies in the engagement of the passive local models. Unlike passive methods that take the local models as fixed, our approach involves the exploitation on their gradients to breach privacy.
我们现在讨论黑盒数据重建攻击。我们的方法利用了 VFL 中主动参与者未充分利用的能力，即它对传输给被动参与者的梯度的控制。正如威胁模型中所述，我们的每次攻击都在对私人训练数据进行最小假设的情况下进行。与之前的被动方法相比，我们主动对手的一个根本区别在于被动本地模型的参与。与将本地模型视为固定的被动方法不同，我们的方法涉及利用其梯度来侵犯隐私

### Active Inversion Network(AIN)
第一种是当对手知道训练数据的一些样本时，称为辅助数据 $D_{AUX} \in D$。假设意味着攻击者知道辅助数据中的 $x_{n,k}∀k$，但只知道 $\mathcal{D}\setminus\mathcal{D}_{\mathrm{AUX}}$(除辅助数据以外的数据，也就是说辅助数据对主动方是透明的)中 $x_n$ 的 $x_{n,0}$ 。该场景描述了攻击者与本地数据所有者进行协作 VFL 模型训练的情况，本地数据所有者专门拥有某些特定功能的数据。然而，主动参与者可能知道 VFL 中使用的某些样本的那些特征。

主动反转网络，可以从中间激活中恢复私有特征，如图1b，通过篡改返回的梯度，主动攻击者迫使被动参与者参与 AIN 的联合训练。特别是，对手使用训练前已知的一组辅助数据和重建损失（例如均方误差（MSE）损失）来篡改梯度并将其传输给被动用户。当被动用户使用该梯度更新其局部参数时，他们的局部模型实际上是针对对抗性重建任务进行训练的。

在训练期间收到来自被动参与者的中间激活 $z$ 后，AIN 对手执行三项活动：过滤数据以计算对抗性损失，更新其权重和偏差，以及将篡改的梯度传输给被动参与者。这些活动强制逆网络 h 的训练损失成为重建 MSE 损失：
$$\mathcal{L}_{AE}(\hat{X},X)=\frac1{|X|}\sum_{n=1}^{|X|}\|\hat{x}_n-x_n\|^2$$

>where $X$ and $\hat{X}$ are the set of inputs that are in both the auxiliary data and the training batch, and the output of $h$ on the intermediate activations, respectively
$X$是输入的特征(在辅助数据和训练数据中)，$\hat{X}$是顶部模型$h$的输出结果，相当于主动方训练的是一个将各参与方中间激活转化为真实输入特征的模型，回传损失函数强制要求其他参与方训练恢复输入特征的生成模型。

$$
\text{Active user: arg}\min_{\boldsymbol{\Theta_0,h}}\mathcal{L}_{AE}(\hat{X},X)\\\text{Passive user }k{:}\arg\min_{\boldsymbol{\Theta_k}}\mathcal{L}_{AE}(\hat{X},X)
$$

**数据重建** 当AIN训练的$h$收敛时，主动方可以冲中间激活重建任何输入数据：$x_n \approx h(z_n)$

### Active Generative Network(AGN)
第二个设置研究对手只知道训练数据分布的场景，即 $|D_{AUX}| = 0$。该威胁模型描述了主动参与者拥有大量数据来覆盖特征分布[19]或数据可以轻松被对手采样的情况。例如，它可以描述前面设置中提到的例子，当中心医院有一个包含所有特征的大记录数据库时。只要训练数据来自相同的分布，该设置中引入的方法就可以由该中心医院执行。

第二种方法由主动生成网络（AGN）组成，专为对手无法访问辅助数据的情况而设计。 AGN 基于生成对抗网络（GAN）[20] 的思想，由生成器和鉴别器组成。生成器负责生成模仿分布中的数据样本的新数据样本，而鉴别器则尝试区分真实数据和假样本，即生成器生成的样本。由于 GAN 生成器的输出通常源自随机噪声，因此它不适用于重建/推理任务。**为了解决这个问题，AGN 利用主动参与者了解的有关目标样本的信息，即 $x_{n,0}$ 和潜在的 $y_n$ 来鼓励生成器返回 $x_n$ 而不是真实分布中的任意数据点。**从这个方面来看，AGN 与条件 GAN [21] 有一些相似之处，条件 GAN 是一种改进的 GAN，用于根据旁路信息来调节数据生成。

**AGN的训练**。 在一次 VFL 训练迭代中，AGN 对手进行 4 项主要活动。第一个活动生成数据批次来训练鉴别器。由于真实分布已知，**标记为 1 的真实数据是从该分布中提取的，而标记为 0 的假数据是使用中间激活创建的($h$函数根据中间激活的输出)。**
第二个活动涉及按照传统的GAN训练更新判别器，鉴别器使用二元交叉熵损失函数：
$$\begin{aligned}&\mathcal{L}_{BCE}(Y,1)=\frac1{|X|}\sum_n\log(y_n)\\&\mathcal{L}_{BCE}(Y,0)=\frac1{|X|}\sum_n\log(1-y_n)\end{aligned}$$

第三个活动是更新生成器，与判别器不同，判别器被训练为使用损失 $L_{BCE}(D(X_{fake}), 0)$ 来识别生成的数据，而生成器则被鼓励使用损失 $L_{BCE}(D(X_{fake}), 1)$ 从真实分布中生成数据。
>上面的步骤只是在训练判别器，回传的损失函数$L_{BCE}(D(X_{fake}), 1)$是让其他参与方训练过程中更多将假的数据接近真实标签1。所以回传给被动方的梯度是$\nabla_{z_{n,k}}\mathcal{L}_{BCE}(D(X_{fake}),1)$

**数据重建**. 在推理过程中，攻击者使用生成器并将重构信号计算为：
$$\hat{x}_n=G([z_{n,0},\cdots,z_{n,K+1}]),\quad\hat{x}_{n,0}=x_{n,0}$$
传入真实的$x_{n,0}$，以便生成器返回对应的$x_n$。