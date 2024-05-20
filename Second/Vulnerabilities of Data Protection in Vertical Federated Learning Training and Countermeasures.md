# Vulnerabilities of Data Protection in Vertical Federated Learning Training and Countermeasures

### 摘要
在本文中，提出了一种基于后验差异的数据攻击VFLRecon，通过重建标签和特征来检查这个问题，而且在加密的VFL中依然存在。为了减轻这种风险，设计了一种新颖的防御机制VFLDefender，专门用于在训练过程中混淆底层模型变化和标签(特征)之间的相关性。

### 主要思路(数据重建攻击)
- NN-VFL模型定义：
$$y=h(g(x^A;\theta_A),g(x^B;\theta_B);\theta_t)
$$

在VFL设置中，每个参与者无法直接从其他参与者那里获取具有相同样本ID的记录的特征或标签。然而，这并不意味着一个参与者不可能在模型训练阶段重建其他参与者的特征或标签。$\mathcal{L}$是基于NN的VFL模型的损失函数，而对抗参与者持有特征$X^{adv}$和参数为$\theta_{adv}$的底部模型$g$，下面等式代表对抗参与者底层模型的梯度计算。

$$\nabla_{\theta_{\mathrm{adv}}}\mathcal{L}=\frac{\partial\mathcal{L}}{\partial h}\nabla_{\theta_{\mathrm{adv}}}h(b_{\mathrm{adv}},b_{\mathrm{vict}};\theta_{\mathrm{top}})|_{b_{\mathrm{adv}}=g(\mathcal{X}^{\mathrm{adv}};\theta_{\mathrm{adv}});b_{\mathrm{vict}}=g(\mathcal{X}^{\mathrm{vict}};\theta_{\mathrm{vict}})}$$
>It clearly shows that those gradients, i.e., ∂L ∂ θadv with respect to adversarial participants’ bottom model, are associated with the other participants’ bottom model output (b2), top model output and ground-truth label. In other words, the distribution (model parameters) changes in the bottom model are correlated with the features and labels from other participants. This offers an attack surface for the adversarial participants to reconstruct other participants’ data samples (features or labels). Therefore, this may lead to serious training data leakage in the VFL training stage

上式表明，$\frac{\partial\mathcal{L}}{\partial\theta_{\mathrm{adv}}}$和其他参与者底部模型的输出($b_2$)，顶部模型输出和真实标签有关，换句话说，底层模型的分布（模型参数）变化与其他参与者的特征和标签相关联，这为敌对参与者提供了一个攻击面来重建其他参与者的数据样本（特征或标签）。因此，这可能会导致VFL训练阶段出现严重的训练数据泄漏。

### 算法
基于神经网络的重建模型$R(\cdot)$，用于重建来自其他参与者的标签或特征。在 VFL 模型训练期间，对抗性参与者通过测量底部模型分布的后验差异来运行 $R(\cdot)$。使用底部模型输出的梯度$\delta_g^{adv}$以及模型更新前后的参数和底部模型输出表示底部模型分布的后验差异，分别是$(\theta_{\mathrm{adv}},\mathrm{~g}(\mathcal{X}^{adv};\theta_{\mathrm{adv}}))$和$(\theta_{\mathrm{adv}}^{\prime},g(\mathcal{X}^{adv};\theta_{\mathrm{adv}}^{\prime}))$。在两个连续的训练步骤中对这些后验差异与其训练样本之间的相关性进行建模。在影子模型上模拟VFL训练过程，收集数据来描述训练样本的特征或标签之间的相关性以及VFL训练过程中底层模型的分布变化。使用收集到的数据来训练基于神经网络的重建模型$R(\cdot)$作为攻击者，重建损失为：
$$\mathcal{L}_{r}^{\mathrm{f}}=\|\mathcal{R}(\delta_{g}^{\mathrm{adv}},g(\mathcal{X}^{\mathrm{adv}};\theta_{\mathrm{adv}}),g(\mathcal{X}^{\mathrm{adv}};\theta_{\mathrm{adv}}^{\prime}),\theta_{\mathrm{adv}},\theta_{\mathrm{adv}}^{\prime},\mathcal{X}^{\mathrm{adv}})-\mathcal{X}^{\mathrm{vict}}\|_{2}^{2}$$
- R可以为任意NN模型
对于标签重建任务，损失为：
$$\begin{aligned}\mathcal{L}_{r}^{1}&=-\mathbb{E}_y(y\log\mathcal{R}(\delta_g^{\mathrm{adv}},g(\mathcal{X}^{\mathrm{adv}};\theta_{\mathrm{adv}}),g(\mathcal{X}^{\mathrm{adv}};\theta_{\mathrm{adv}}^{\prime}),\theta_{\mathrm{adv}},\theta_{\mathrm{adv}}^{\prime},\mathcal{X}^{\mathrm{adv}}))\end{aligned}$$

过程：在影子数据和影子模型上进行训练重建NN模型，然后记录这些数据，特征和标签重建的数据不同，根据对应的损失函数进行重建模型训练。

<div align="center" >
<img width=600px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/20240527162729.png">
</img>
</div>

>As VFLRecon exploits the changes in the bottom model during training, the adversarial participants are capable of reconstructing training data samples, including features and labels from other partic- ipants after participating only in one epoch of training.

由于 VFLRecon 在训练过程中利用对抗参与方的底层模型的变化，对抗参与者能够在仅参与一个训练周期后重建训练数据样本，包括来自其他参与者的特征和标签。

### 防御VFLDefender(梯度模糊)
**如果两个或多个样本产生相同的梯度，则模型变化与训练样本之间的相关性就会变弱**。因此，我们的目标是扰乱反向传播的梯度，以降低底部模型的分布变化与训练样本之间的相关性。向梯度添加随机噪声是保护梯度中包含的信息的最常见方法之一[19]、[58]。然而，噪声尺度的大小对模型效用有显着影响[13]、[58]。为了确保模型实用性，我们设计了一个简单的机制 VFLDefender，以尽可能少地向输出层的梯度添加噪声。我们的方法是随机化梯度的范数，而不显着改变它们的方向。

顶部模型计算对底部模型输出的梯度后，顶部模型对$\delta_o$进行裁剪并进行$l_2$范数规范得到$\hat{\delta}_{o}$，然后，顶部模型随机化 $\hat{\delta}_{o}$ 的范数，同时保持梯度方向不变（第 8-14 行）。之后，随机梯度 $\hat{\delta}_{o}$被反向传播到每个模型层。底部和顶部模型使用扰动梯度更新其参数。(裁剪是对梯度的范围进行裁剪，基于$t^{max}$和$t^{min}$)

<div align="center" >
<img width=600px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/20240527164047.png">
</img>
</div>
