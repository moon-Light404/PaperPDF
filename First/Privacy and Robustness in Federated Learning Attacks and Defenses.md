# Privacy and Robustness in Federated Learning: Attacks and Defenses

最近，联邦学习(FL)作为一种替代解决方案出现，并在这种新的现实中继续蓬勃发展。现有的FL协议设计已被证明容易受到系统内外对手的攻击，损害数据隐私和系统健壮性。除了训练强大的全局模型外，设计具有隐私保证并能抵抗不同类型对手的FL系统至关重要。在本文中，我们对过去五年来FL的隐私和鲁棒性进行了全面的调查。通过对FL概念的简要介绍和独特的分类涵盖:1)威胁模型;2)隐私攻击与防御;3)中毒攻击和防御，我们对这个重要的话题提供了一个容易理解的回顾。我们重点介绍了各种攻击和防御所采用的直觉、关键技术和基本假设，最后，我们讨论了鲁棒性和隐私保护FL的未来研究方向，以及它们与FL多学科目标的相互作用。

## Introduction

> 基于数据分布的FL分类

根据数据特征和数据样本在参与者中的分布，FL大致可分为HFL、VFL和FTL(横向联邦学习、纵向联邦学习、联邦迁移学习)。

在HFL下，每个参与者拥有的数据集具有相似的特征，但涉及不同的用户[20]。例如，几家医院可能各自存储关于不同患者的类似类型的数据(例如，人口统计、临床和基因组学)。如果他们决定使用FL一起构建ML模型，我们将这种场景称为HFL。在本文中，我们进一步将HFL分为H2B(HFL to businesses)和H2C(HFL to consumers.)。H2B和H2C的主要区别在于参与者的数量、FL培训参与水平和技术能力，这些因素会影响攻击者试图破坏FL系统的方式。在H2B项目下，<u>通常只有少数参与者。他们可以在FL训练中经常选择。参与者往往具有显著的计算能力和复杂的技术能力</u>[3]。在H2C下，<u>可能有数千甚至数百万的潜在参与者。在每一轮训练中，只选择其中的一个子集。由于他们的数据集往往很小，参与者被反复选择进行FL训练的机会很低。它们通常具有有限的计算能力和较低的技术能力</u>。H2C的一个例子是谷歌的GBoard应用程序

VFL适用于<u>参与者在样本空间中有较大的重叠，但在特征空间上不同的情况</u>，即不同的参与者对同一条记录持有不同的属性[21]。VFL主要针对商业参与者。因此，VFL参与者的特征与H2B参与者相似。

目前，联邦迁移学习在金融、医药、医疗保健等行业受到越来越多的关注。FTL<u>处理的是FL参与者在样本空间和特征空间上几乎没有重叠的场景</u>[3]，[19]。


在这种情况下，迁移学习[22]技术可以为整个样本和特征空间提供解决方案，FTL使数据联邦中的互补知识能够跨域传递，从而使目标域方能够利用源域的丰富标签构建灵活有效的模型[23]。

> 基于体系结构的FL分类

1)具有同构架构的FL

共享梯度通常仅限于同构FL架构，即所有参与者共享相同的模型。参与者的目标是协作学习一个更准确的模型。具体来说，模型参数w通常是通过求解以下优化问题得到的:$min_w\sum_{i=1}^nF(w,D_i)$，其中$F(w,D_i)$是第i个参与者的局部训练数据集的目标函数，表征参数w对第i个参与者的局部训练数据集Di的建模程度。不同的分类器(例如，逻辑回归和深度神经网络)使用不同的目标函数。

2)具有异构架构的FL

最近的努力将FL扩展到具有异构架构的协同训练模型[24]，[25]，[26]。传统的联邦模型训练，直接平均模型权重，<u>只有在所有的局部模型具有相同的模型结构</u>时才有可能。自然，它限制了具有异构模型体系结构的数据所有者之间的协作。一项称为联邦模型蒸馏(federatedmodel distillation, FedMD)的代表性工作[25]并没有将单个全局模型强制应用到局部模型上。相反，它是以一种简洁、黑盒和模型不可知的方式进行的。每个局部模型分别更新;参与者通过他们在一个未标记的公共集合上的预测分享他们当地模型的知识。共享logits的一个明显好处是降低了通信成本，而不会显著影响效用[25]。


总之，上述所有共享方法本身并不能提供对隐私和中毒攻击的防御——这是对FL的两个主要威胁来源。

> 联邦学习的威胁

现有的FL协议设计容易受到:1)恶意服务器的攻击，该服务器旨在从个体更新中推断敏感信息，篡改训练过程，或控制参与者对全局参数的看法;2)任何敌对参与者可以推断其他参与者的敏感信息，篡改全局参数聚合，或毒害全局模型。

在**隐私**方面，整个训练过程中的通信梯度可能会<u>泄露敏感信息</u>[27]，[28]，甚至导致深度泄漏[29]，无论是对第三方还是对中央服务器[7]，[30]。即使是一小部分梯度也能揭示相当数量的关于局部数据的敏感信息[31]。最近的研究进一步表明，恶意攻击者可以通过简单地观察梯度，成功地窃取训练数据[29]，[32]。

在**鲁棒性**方面，FL系统容易受到数据中毒[33]、[34]和模型中毒攻击[35]、[36]、[37]、[38]。恶意参与者可以通过故意改变他们的局部数据(数据中毒)或他们的梯度上传(模型中毒)来攻<u>击全局模型的收敛性</u>或在全局模型中植入后门触发器。


更广泛地说，中毒攻击可以分为:1)<u>非目标攻击</u>，如拜占庭攻击，攻击者的目标是破坏全局模型的收敛性和性能[39]，[40];2)<u>目标攻击</u>，如后门攻击，攻击者的目标是在全局模型中植入后门触发器，从而欺骗模型不断预测子任务上的对抗类，同时保持主任务的良好性能[34]，[35]，[36]。

<div align="center" >
<img width=800px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-62.png">
</img>
</div>

> 安全联邦学习

为了保护FL免受隐私攻击，已经在FL中尝试了集中式ML中现有的隐私保护方法，包括HE, SMC和DP。然而，HE和SMC可能不适用于大规模的FL，因为它们会产生大量的通信和计算开销。在基于聚合的任务中，DP要求聚合值包含一定大小的随机噪声以确保$(\epsilon,\delta)-DP$，因此对于FL来说也不理想。DP要求的噪声添加在FL中也很难执行。在服务器(聚合器)受信任的理想场景中，服务器可以将噪声添加到聚合梯度中[7]。然而，在许多现实场景中，<u>参与者可能不信任中央服务器或彼此</u>。在这种情况下，参与者会相互竞争，并且都希望通过在其局部梯度中<u>添加尽可能多的噪声</u>来确保LDP[30]，[44]，[46]。这往往会在服务器端积累严重的错误。DDP[30]，[44]，[46]可以在一定程度上缓解这一问题，至少有一定比例的参与者是诚实的，不进行这种恶意竞争。

保护FL免受各种鲁棒性攻击(例如，无针对性的拜占庭攻击和有针对性的后门攻击)是一项极具挑战性的任务。首先，<u>防御只能在只有本地梯度可用的服务器端执行</u>。这使得在集中式机器学习中开发的许多后门防御方法无效，例如去噪(预处理)方法[47]，[48]，[49]，[50]，[51]，后门样本/触发检测方法[52]，[53]，[54]，[55]，[56]，[57]，鲁棒数据增强[58]，微调方法[58]，基于神经注意蒸馏(NAD)的方法[59]，以及最近基于复杂学习过程的反后门学习(ABL)方法[60]。其次，该防御方法对数据投毒和模型投毒都必须具有鲁棒性。大多数现有的鲁棒性防御都是梯度聚合方法，主要用于防御非目标的拜占庭攻击者，如Krum/ multikrum[40]、AGGREGATHOR[61]、Byzantine梯度下降(BGD)[62]、基于中位数的梯度下降[63]、基于裁剪均值的梯度下降[63]和SIGNSGD[39]。这些防御方法从未在针对性的后门攻击中进行过测试[33]、[34]、[35]、[36]、[38]。

已经研究了针对数据中毒和模型中毒攻击的专用防御方法，如norm clipping[38]、基于几何中位数的RFA[64]和鲁棒学习率[65]。对于Sybil攻击的合谋，贡献相似性[37]可以作为防御策略加以利用。

在本文中，我们对FL及其防御的隐私和健壮性威胁的最新进展进行了广泛的调查。特别是，我们专注于FL系统中内部人员发起的两个特定威胁:1)试图推断受害的参与者私人信息的隐私攻击和2)试图阻止全局模型学习或植入触发器以控制全局模型行为的中毒攻击。本文主要综述了近五年来关于FL中的隐私和鲁棒性的文献;它可以成为现有文献的一个值得注意的内容，帮助社区更好地理解FL中最先进的隐私和鲁棒性进展。还提供了文献中现有作品的局限性和有前途的用例以及未来研究的开放方向，以确定研究差距，以应对FL中隐私和鲁棒性的挑战。

1)对FL进行了全面的分类，系统地总结了对FL的威胁及相应的保护措施。

2)对现有的隐私和鲁棒性攻击和防御进行了很好的探讨，以帮助读者更好地理解FL的隐私和鲁棒性领域当前进展的假设、原则、原因和差异。

3)识别隐私与鲁棒性之间的冲突，以及多个设计目标之间的冲突;总结了当前研究与FL实际场景之间的差距。

4)未来的研究方向将帮助社区重新思考和改进他们目前的设计，以实现真正实用和影响的健壮和保护隐私的FL。同时，建议在FL系统设计中整合多学科目标。

## Threat Model 	  	

> 内部与外部

1)内部攻击：内部攻击可以由FL服务器或者参数者发起，中央服务器可以随着时间的推移观察单个更新，并可以控制参与者对全局参数的看法;任何参与者都可以观察全局参数的更新，并可以控制自己的参数上传[71]。

2)外部攻击：外部攻击包括窃听者在参与者和FL服务器之间的通信通道上发起的攻击，以及最终FL模型作为服务部署时的用户发起的攻击。内部攻击通常比外部攻击更危险，因为它严格地增强了对手的能力。因此，我们对FL攻击的讨论将主要集中在内部攻击上。

> 半诚实与恶意

1)半诚实：对手被认为是被动的或诚实但好奇的，他们试图在不偏离FL协议情况下了解其他参与者的私人信息。攻击者只能观察到接收到的信息，即全局模型的参数。

2)恶意设置：一个活跃或恶意的对手试图了解其他诚实参与者的隐私信息，并通过修改、重播或删除消息来任意偏离FL协议，这种设置允许对手进行具有破坏性的攻击。

> 训练阶段与推理阶段

1)训练阶段:在训练阶段，内部攻击者可以访问完整的模型，特别是其架构和参数，以及使用模型进行预测所需的任何超参数[71]。在训练阶段进行的攻击试图学习、影响或破坏FL模型本身[74]。

在训练阶段的隐私漏洞方面，攻击者还可以对个体参与者的共享信息或聚合信息进行一系列推理攻击，以达到侵害隐私的目的[28]、[29]、[71]、[75]。例如，从共享的局部模型参数中，内部攻击者(参与者或服务器)可以推断出<u>类代表</u>[75]、<u>记录隶属关系和属性</u>[28]、[71]，甚至原始训练输入和标签[29]。详情可参阅第三节。

就训练阶段的鲁棒性漏洞而言，攻击者可以运行数据中毒攻击来破坏训练数据集的完整性[35]、[37]、[76]、[77]、[78]、[79]，或者运行模型中毒攻击来破坏学习过程的完整性[36]、[80]。具体攻击可以是有针对性的标签翻转攻击[37]、[81]和后门攻击[35]，也可以是无针对性的拜占庭攻击[40]、[63]。更多细节可参阅第四节。

2)测试推断阶段：在这个阶段，攻击者不改变目标模型；他们会查询目标模型以泄露一些私人信息，或者通过产生错误的预测来欺骗目标模型以损害鲁棒性。这种攻击的有效性在很大程度上取决于攻击者可以获得的关于模型的信息。

在推理阶段的隐私漏洞方面，训练好的全局模型在作为服务器部署时可能会泄露模型预测中的敏感信息，从而导致隐私泄露。在这种情况下，<u>攻击者无法直接访问模型参数，但可以查看输入输出对，从而发起模型窃取攻击，其中模型参数可以被重建</u>[82]、[83]、[84]、[85]，或者发起MIAs攻击，目的是确定是否使用了特定记录来训练模型[71]、[86]。

在推理阶段的鲁棒性漏洞方面，当目标模型作为服务部署时，服务器维护的全局模型遭受与传统ML设置相同的逃避攻击[87]。逃<u>避攻击的一种被充分研究的形式是所谓的对抗性示例，它似乎与人类的原始测试输入几乎没有区别，但是可以欺骗训练好的模型</u> [88], [89]，最近的研究[90]，[91]，[92]表明，在推理阶段，FL也容易受到精心制作的对抗性示例的影响。针对鲁棒性攻击的一种典型防御是AT，其中使用对抗性示例训练鲁棒模型，从而为白盒逃避攻击提供一定的鲁棒性[93]。然而，将AT方法适应于FL带来了许多悬而未决的问题，我们将在第七节中讨论。

## Privacy Attacks

在FL中交换梯度也可能会泄露隐私数据，例如，梯度或两个连续的FL模型参数快照可能会将参与者训练数据意外的特征泄露给敌对参与者($w_{t+1}-w_t$)。

梯度之所以会导致隐私泄露，是因为梯度来源于参与者的私人训练数据，学习模型可以被认为是训练数据集的高级统计数据的表示[43]。在深度学习中，给定层的梯度是基于层的特征和之后层的误差(反向传播)计算的。在顺序全连接层的情况下，权值的梯度是当前层的特征与后一层的误差的内积。类似地，对于卷积层，权值的梯度是该层特征的卷积和后一层的误差。因此，对梯度的观察可以用来推断大量私有信息，如类代表、成员资格和训练数据子集的属性。更糟糕的是，攻击者可以从共享梯度中推断出标签，并在没有任何先验知识的情况下恢复原始训练样本[29]。

> Inferring Class Representatives

Hitaj等人[75]首先设计了一种针对深度FL模型的主动推理攻击，称为GAN攻击。在这种攻击中，恶意参与者可以故意危及任何其他参与者。GAN攻击利用了FL学习过程的实时性，这使得敌对参与者可以训练GAN来生成目标私人训练数据的原型样本。生成的样本似乎来自与训练数据相同的分布。

因此，<u>GAN攻击的目标不是重建精确的训练输入，而是只重建类代表</u>。值得注意的是，GAN攻击假设给定类的整个训练语料库来自单个参与者，这意味着只有当所有类成员相似时，GAN构造的代表才与训练数据相似。

这类似于集中式ML设置中的模型反转攻击[96]。请注意，这些假设在FL中不太实用。由于GAN攻击需要大量的计算资源来训练GAN模型，因此它不太适合H2C场景

>  Inferring Membership

MIAs的目的是确定该数据点是否用于训练模型[86]，在FL中，<u>攻击者可以推断特定样本是否属于特定参与者的私有训练数据</u>。攻击者可分为主动和被动的MIAs[28],[71]。在被动情况下，攻击者在不修改学习过程的情况下观察更新的模型参数并推理。在主动情况下，攻击者可以篡改模型训练协议并对其他参与者执行更强大的攻击。

> Inferring Properties

<u>推断与训练数据集相关的属性信息</u>

> Inferring Training Inputs and Labels

<u>重建原始训练数据样本和相应标签</u>。最近一项名为梯度深度泄漏(deep leak from gradient, DLG)的研究提出了一种同时提取训练输入和标签的优化算法[29]。这种攻击比以前的方法强得多。它可以准确地恢复用于训练深度学习模型的原始图像和文本。在后续工作中[32]，提出了一种称为改进DLG (iDLG)的分析方法，基于共享梯度提取标签，并探索标签与梯度符号之间的相关性。iDLG可以应用于攻击任何用交叉熵损失和单热标签训练的可微模型，这是分类任务的典型设置。

## Poisoning Attacks

与针对数据隐私的隐私攻击不同，中毒攻击的目的是破坏系统的鲁棒性，大致可分为两类：无目标投毒攻击和目标投毒攻击。从图中可以看出，中毒更新可以来源于：1)局部数据收集过程中的数据中毒攻击和2)局部模型训练过程中的模型中毒攻击，数据中毒攻击不如模型中毒攻击有效[36],[37],[38]。

<div align="center" >
<img width=600px  height=auto src="https://raw.githubusercontent.com/moon-Light404/read_paper_notes/master/note_image/image-63.png">
</img>
</div>

**A.无目标攻击**

无目标中毒攻击的目的是任意破坏目标模型的完整性，拜占庭攻击是一种无目标的投毒攻击，通过向服务器上传恶意(任意)梯度，导致全局模型失效。Blanchard等[40]表明，如果FL中没有防御，FL的聚集可以完全由单个Byzantine参与者控制。特别地，假设有$n-1$个良性参与者和一个Byzantine参与者;服务器通过$\Delta w_n=nu-\sum_{i=1}^{n-1}\Delta w_i$聚合梯度，其中w为聚合梯度。假设第n个参与者是拜占庭人;通过上传以下梯度，总能使聚合梯度变为任意向量u。

Baruch等[109]表明梯度下降算法的核心部分是下降的方向。具体来说，对于梯度下降算法，为了保证损失下降，<u>其真梯度与鲁棒聚合梯度之间的内积必须是非负的</u>。$\lang \Delta w,\Delta w'\rang \geq 0$。为了使聚合失败，他们提出了一种“内积操纵攻击”，可以使基真梯度与鲁棒聚合梯度之间的内积为负。为了做到这一点，每个拜占庭参与者上传了平均良性梯度的负值，他们提出的攻击可以成功绕过坐标median[63]和Krum[40]。

Xie等人[100]声称，通过持续地对许多参数施加微小的变化，拜占庭参与者会干扰模型的收敛。首先，他们使用拜占庭参与者的本地数据来估计分布的平均值和标准差。然后，他们分析了参数变化不会被防御检测到的范围，并在选择该范围的最大值时，避免了收敛。



**B.目标攻击**

在有针对性的中毒攻击中，学习模型为特定的测试示例输出攻击者指定的标签，但是其他的样本预测不受影响。一般来说，有针对性的攻击比无针对性的攻击更难以实施，因为攻击者有特定的目标要实现。

1)标签翻转攻击：标签翻转攻击:有针对性的投毒攻击的一个常见例子是标签翻转攻击[37]，[81]。将一类诚实训练样例的标签翻转到另一类，同时保持数据的特征不变。例如，系统中的恶意参与者可以通过将所有1类变成7类来毒害他们的本地数据。一次成功的攻击会产生一个模型，该模型无法正确地对1类进行分类，并错误地将它们预测为7类。

2)后门攻击:另一种现实的针对性中毒攻击是后门攻击，攻击者可以修改原始训练数据集的单个特征或小区域，将后门触发器植入模型中。该模型在干净的数据上会正常工作，但只要触发器(例如，图像上的戳戳)出现，它就会不断预测目标类。例如，后门攻击可以使FL模型在后门任务上达到100%的准确率，例如，在图像分类任务中控制图像分类器为具有某些特征的图像分配攻击者选择的标签，或者在下一个单词预测器在单词预测任务中使用攻击者选择的单词完成某些句子[35]。

后门攻击可以进一步分为两类：脏标签攻击和干净标签攻击。干净标签攻击假设攻击者无法改变任何训练数据的标签，并且数据样本的中毒必须是难以察觉的。相反，脏标签中毒攻击中，攻击者可以将许多数据样本引入训练数据中，这些样本预计会被具有期望目标标签的模型错误分类，干净标签攻击可以说是隐形的，因为它不改变标签。
