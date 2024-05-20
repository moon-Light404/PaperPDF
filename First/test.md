
$\frac{\partial{\mathcal{L}}}{\partial{\mathbf{W}}}=-\frac{1}{N}\sum_{i=1}^N(y_i-f(x_i))x_i$

$\frac{\partial{\mathcal{L}}}{\partial{\mathbf{\Theta_j}}}=-\frac{1}{N}\sum_{i=1}^N(y_i-f(x_i))x_i^j$

$g_1^B X_S^B = \frac{1}{4} \Theta_1^B X_S^B-\frac{1}{4} \Theta_0^BX_S^B$

$\nabla_{b_1}L(\Theta,D(s^t))=\sum_{n=1}^Ns^t[n]\nabla_{u_n}L(\Theta,D(s^t))$

$min_{\Theta}\mathcal{L}(\Theta,D) =\frac{1}{N}\sum_{i=1}^N\mathbb{l}(o_i^K,y_i^K)+\beta\sum_{k=1}^K \gamma(\theta_k)$

$U=\frac{\partial{l_B}}{\partial{H_B}}$

$W^{FC} \in \mathbb{R}^{M \times N}$，其输入为一个M维向量$v \in \mathbb{R} ^M$，
对于一个特定的类别$n$，($z$为全连接层输出的logits)，其输出$z_n=v \cdot W[:,n]=\sum_{i=1}^M v_iw_{i,n}$，那么有$\frac{\partial{z_{n,k}}}{\partial{w_{m,n}}}=\frac{\partial{\sum_{i=1}^M v_iw_{i,n}}}{\partial{w_{m,n}}}=v_m$，
$v_m$也就是$o_m,k$，即全连接层的第$m$个输入。这里的m和n其实是W参数的索引。

设全连接层输入是$[v1,v2,v3]$，参数为$\begin{bmatrix}
    w_{11} \cdots w_{1N} \\
    w_{21} \cdots w_{2N} \\
    \cdots \\
    w_{M_1} \cdots w_{MN}
\end{bmatrix}$，设样本的类别是$n$，则与$n$对应的等式为$\partial\{v_1w_{1n}+v_2w_{2n}+v3w_{3n}\}/\partial\{w_{m,n}\}=v_m$，其中n是固定的。

$H_k=G_i(x_k,\theta_k)$

$\frac{\partial L}{\partial W}=-\frac{1}{N}\sum_{i=1}^N(y_i-f(x_i))x_i$

$\nabla l'=\sum_{i \in B}\partial l(softmax(H_i^p+H_i^{a'}),y_{i'})/\partial{\theta_p}$

$min_{H_a',y'} \mathcal{D} = \|\nabla l'=\nabla l\|^2$

### 图片居中等操作

<!-- <image width=200px height=200px src="sss"> -->

<!-- <div align="center">
    <image src="">
</div> -->

<div align="center" >
<img width=600px  height=auto src="">
</img>
</div>


