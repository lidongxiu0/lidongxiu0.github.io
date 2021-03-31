## UNet

UNet的encoder和decoder是两个对称的结构，在encoder和decoder之间加入了skip connection，通过叠操作将encoder过程与decoder过程连接起来。

![unet_in_paper](../image/UNet/unet.png)

将原始的UNet网络画成点的形式：

