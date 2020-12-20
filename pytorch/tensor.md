**Tensor，即张量，是PyTorch中的基本操作对象，可以看作是包含单一数据类型元素的多维矩阵**

## Tensor数据类型

**数据类型** | **CPU Tensor** | **GPU Tensor**
------------|----------------|-----------------
32位浮点 | torch.FloatTensor | torch.cuda.FloatTensor
64位浮点 | torch.DoubleTensor | torch.cuda.DoubleTensor
16位半精度浮点 | N/A | torch.cuda.HalfTensor
8位无符号整型 | torch.ByteTensor | torch.cuda.ByteTensor
8位有符号整型 | torch.CharTensor | torch.cuda.CharTensor
16位有符号整型 | torch.ShortTensor | torch.cuda.ShortTensor
32位有符号整型 | torch.IntTensor | torch.cuda.IntTensor
64位有符号整型 | torch.LongTensor | torch.cuda.LongTensor


PyTorch通过`set_default_tensor_type`函数设置默认使用的Tensor类型，在局部使用完后如果需要其他类型，则还需要重新设置回所需的类型。
```python
torch.set_default_tensor_type('torch.DoubleTensor')
```

对于Tensor之间的类型转换，可以通过`type(new_type)`，`type_as()`等多种方式进行操作。当想保持Tensor间类型一致时，使用`type_as()`即可，不需要明确是哪种类型。

## Tensor创建

Tensor的创建方法包括：构造函数`Tensor()`，与NumPy类似的方法，如`ones()`，`eye()`，`zeros()`，`randn()`等
```python
# 基础的Tensor函数：
torch.Tensor(2, 2)
# 指定类型：
torch.DoubleTensor(2, 2)
# 使用Python的list序列：
torch.Tensor([[1, 2], [3, 4]])
# 默认值为0：
torch.zeros(2, 2)
# 默认值为1：
torch.ones(2, 2)
# 对角张量：
torch.eye(2, 2)
# 随机张量：
torch.randn(2, 2)
# 随机排列张量：
torch.randperm(4)
``` 

Tensor的维度，可使用`Tensor.shape`或`size()`查看每一维的大小

Tensor中元素的总个数，可使用`Tensor.numel()`或`Tensor.nelement()`函数。

## Tensor组合和分块

