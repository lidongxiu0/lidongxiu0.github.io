## ResNet

This part will be update recently.

## Dilated Residual Network (DRN)

This part will be update recently, too.


## Voxelwise Dilated Residual Network (VoxDRN)

This part will also be update recently.


## Bayesian VoxDRN [4-0]

### This part will be update recently.

### 网络结构

 Gal 和 Ghahramani [*4-1] 中证明，贝叶斯CNN在小数据的过拟合上提供了更好的鲁棒性。这一网络是目的是找到后验概率![p1]https://latex.codecogs.com/png.latex?p(\mathbf{W}|\mathbf{X},\mathbf{Y})在网络中用variational dropout来解决这一问题。

 Bayesian VoxDRN基于这种思想设计

 ![Bayesian_VoxDRN](./image/Bayesian_VoxDRN.png)

这其中的dropout层训练时的丢弃概率为0.5，以此避免过拟合。

利用dropout对权重的后验概率进行抽样，得到softmax类概率的后验分布。最终分割结果由这些样本进行多数投票获得。在这篇论文的全心分割中，使用10个样本来进行多数投票以得到更好的准确性和处理效率的权衡。

### 损失函数






[4-0] Shi Z, Zeng G, Zhang L, et al. Bayesian voxdrn: A probabilistic deep voxelwise dilated residual network for whole heart segmentation from 3d mr images[C]//International Conference on Medical Image Computing and Computer-Assisted Intervention. Springer, Cham, 2018: 569-577.

 [*4-1] Gal Y, Ghahramani Z. Bayesian convolutional neural networks with Bernoulli approximate variational inference[J]. arXiv preprint arXiv:1506.02158, 2015.