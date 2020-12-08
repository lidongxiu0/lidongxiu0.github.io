## ResNet

This part will be update.

## Dilated Residual Network (DRN)

This part will be update, too.


## Voxelwise Dilated Residual Network (VoxDRN)

This part will also be update.


## Bayesian VoxDRN [4-0]


### 网络结构

Gal 和 Ghahramani [4-1] 中证明，贝叶斯CNN在小数据的过拟合上提供了更好的鲁棒性。这一网络是目的是找到后验概率 ![p1](https://latex.codecogs.com/png.latex?p(\mathbf{W}\mid\mathbf{X},\mathbf{Y})) 在网络中用variational dropout来解决这一问题。

Bayesian VoxDRN基于这种思想设计，在上面的VoxDRN的基础上加入了4个额外的dropout层。并且最后的输出变成了两个DUC层输出两类结果，这两类输出会在下文训练中提到。

![Bayesian_VoxDRN](./image/Bayesian_VoxDRN.png)

这其中的dropout层训练时的丢弃概率为0.5，以此避免过拟合。

利用dropout对权重的后验概率进行抽样，得到softmax类概率的后验分布。最终分割结果由这些样本进行多数投票获得。在这篇论文的全心分割中，使用10个样本来进行多数投票以得到更好的准确性和处理效率的权衡。

### 损失函数

这篇论文中提到的损失函数结合了加权聚焦损失 (weighted focal loss) 和Dice损失 (Dice loss) ，论文希望以此来解决分类不均衡的问题。

其中，加权聚焦损失：

![p2](https://latex.codecogs.com/png.latex?L_{wFL}=\sum_{c\in{C}}-\alpha_c\left (1-p_c \right )^\lambda log\left ( p_c \right ))

其中![p3](https://latex.codecogs.com/png.latex?\vert\mathit{X}\vert) 和 ![p4](https://latex.codecogs.com/png.latex?\vert\mathit{X}_c\vert) 分别表示所有类的频率和 _c_ 类的频率，因此![p5](https://latex.codecogs.com/png.latex?\alpha_c=1-\frac{\vert\mathit{X}_c\vert}{\vert\mathit{X}\vert})用来自适应地平衡心脏子结构大小的重要性。

![p6](https://latex.codecogs.com/png.latex?p_c)表示 _c_ 类的概率，![p7](https://latex.codecogs.com/png.latex?\left (1-p_c\right )^\lambda) 是一个尺度因子(scaling factor)，用来减少好的分类样本的相对损失。通过这样来更关注难分类和错分类的样本。

Dice损失：

![p8](https://latex.codecogs.com/png.latex?L_{dice}=1-\frac{2\vert\mathit{X}\cap\mathit{Y}\vert}{\vert\mathit{X}\vert+\vert\mathit{Y}\vert})

其中![p9](https://latex.codecogs.com/png.latex?\vert\mathit{X}\cap\mathit{Y}\vert)表示 _X_ 和 _Y_ 的交集，![p10](https://latex.codecogs.com/png.latex?\vert\mathit{X}\vert) 和 ![p11](https://latex.codecogs.com/png.latex?\vert\mathit{Y}\vert) 分别表示 _X_ 和 _Y_ 的元素个数。就语义分割问题而言， _X_ 表示金标准，_Y_ 表示分割图像。

两个损失函数更具体的内容参看
- [加权聚焦损失](../Loss_function/focal_loss)
- [Dice损失](../Loss_function/dice_loss)

这篇论文中，将聚焦损失和Dice损失结合，由于聚焦损失会导致网络保留复杂的边界细节，但会带来噪声，Dice损失会产生更平滑的分割。


### 迭代开关训练(Iterative Switch Training)

这篇论文使用一种渐进学习策略(progressive learning strategy)来对所提出的Bayesian VoxDRN网络进行训练。

这种策略的主要目的与其他的深度学习的全心分割的思想类似，即，将前景和背景分开，再将前景继续分割成心脏的不同子结构。网络被训练成每一步都解决比原来更简单的问题。网络的最后一层改为两个分支，一个分支由二值化标签估计前景背景，一个分支由多分类标签估计心脏多个子结构。在训练时，每次只训练一个分支。具体而言，在每个训练阶段，首先训练二进制分支，将前景背景分开，然后训练多分类分支，将心脏子结构分开。在测试时，只对多分类感兴趣。



[4-0] Shi Z, Zeng G, Zhang L, et al. Bayesian voxdrn: A probabilistic deep voxelwise dilated residual network for whole heart segmentation from 3d mr images[C]//International Conference on Medical Image Computing and Computer-Assisted Intervention. Springer, Cham, 2018: 569-577.

[4-1] Gal Y, Ghahramani Z. Bayesian convolutional neural networks with Bernoulli approximate variational inference[J]. arXiv preprint arXiv:1506.02158, 2015.