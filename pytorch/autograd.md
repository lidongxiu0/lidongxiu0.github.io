PyTorch提供了autograd来自动求导，将前向传播的计算记录成计算图，自动完成求导。

## Tensor的自动求导：Autograd

自动求导机制记录了Tensor的操作，以便自动求导和反向传播。

```python
import torch
a = torch.randn(2, 2, requires_grad=True)
```

这里的requires_grad参数表示是否需要对该Tensor进行求导，默认为False；设置为True则需要求导，并且依赖于该Tensor的之后的所有节点都需要求导。

Tensor有两个重要属性，分别记录Tensor的梯度和经历的操作。
- grad: 该Tensor对应的梯度，类型为`Tensor`，与Tensor同维度