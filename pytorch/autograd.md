PyTorch提供了autograd来自动求导，将前向传播的计算记录成计算图，自动完成求导。

## Tensor的自动求导：Autograd

自动求导机制记录了Tensor的操作，以便自动求导和反向传播。

```python
import torch
a = torch.randn(2, 2, requires_grad=True)
```

这里的`requires_grad`参数表示是否需要对该Tensor进行求导，默认为False；设置为True则需要求导，并且依赖于该Tensor的之后的所有节点都需要求导。

Tensor有两个重要属性，分别记录Tensor的梯度和经历的操作。
- grad: 该Tensor对应的梯度，类型为`Tensor`，与Tensor同维度
- grad_fn: 指向`function`对象，即该Tensor经过了什么样的操作，用作反向传播的梯度计算，如果该Tensor由用户自己创建，则该grad_fn为None。

## 计算图

计算图是PyTorch对于神经网络的具体实现形式，包括每一个数据Tensor及Tensor之间的函数function。以![i1](https://latex.codecogs.com/png.latex?z=wx+b)为例，通常在神经网络中，![i2](https://latex.codecogs.com/png.latex?x)为输入，![i3](https://latex.codecogs.com/png.latex?w)与![i4](https://latex.codecogs.com/png.latex?b)为网络需要学习的参数，![i5](https://latex.codecogs.com/png.latex?z)为输出，计算图构建方法如图：

![i6](../image/autogradgraph.png)

图中，![i2](https://latex.codecogs.com/png.latex?x)、![i3](https://latex.codecogs.com/png.latex?w)和![i4](https://latex.codecogs.com/png.latex?b)都是用户自己创建的，因此作为叶节点，![i7](https://latex.codecogs.com/png.latex?wx)首先经过乘法算子产生中间节点![i8](https://latex.codecogs.com/png.latex?y)，然后与![i4](https://latex.codecogs.com/png.latex?b)经过加法算法产生最终输出![i5](https://latex.codecogs.com/png.latex?z)，并作为根节点。

Autograd的基本原理是随着每一步Tensor的计算操作，逐渐生成计算图，并将操作的`function`记录在`Tensor`的`grad_fn`中，在前向计算完后，只需对根节点进行`backward`函数操作，即可从当前根节点自动进行反向传播与梯度计算，从而得到每一个叶子节点的梯度，梯度计算遵循链式求导法则。

## Autograd使用注意事项

### 动态图特性

PyTorch建立的计算图是动态的，是PyTorch的一大特点。动态图是指程序运行时，每次前向传播时从头开始构建计算图，这样不同的前向传播就可以有不同的计算图，也可以在前向时插入各种Python的控制语句，不需要事先把所有的图都构建出来，且可以方便地查看中间过程变量。

### backward()函数

`backward()`函数有一个需要传入的参数`grad_variables`，代表根节点的导数，也可以看做跟节点各部分的权重系数。因为PyTorch不允许`Tensor`对`Tensor`求导，求导时都是标量对于`Tensor`进行求导。因此，如果跟节点是向量，则应配以对应大小的权重，并求和得到标量，再反传。如果根节点的值是标量，则该参数可以忽略，默认为1。

### 梯度累加

当有多个输出需要同时进行梯度反传时，需要将`retain_graph`设置为`True`，从而保证在计算多个输出的梯度时互不影响。


## PyTorch网络代码中的自动求导

在PyTorch中的深度学习计算时，一般的模式是：
```python
# 正向传播
out = model(x)
loss = criterion(out, y)

# 反向传播
optimizer.zero_grad()
loss.backward()
optimizer.step()
```

这里的`loss`一般是梯度下降要优化的目标函数，`loss.backward()`表示自动求梯度。

但后面的`optimizer`优化器清除了梯度后，并没有与`loss`建立任何关系。自动求导完成后，直接调用`optimizer.step()`去更新参数。

`optimizer`更新参数的方法即是PyTorch的自动求导机制，函数做`backward()`时会自动更新自变量的`grad`值，因此不需要知道函数是什么。`optimizer`不用关心`loss`是什么，只要知道应更新的变量即可。


- 内容参考自：

董洪义.深度学习之PyTorch物体检测实战[M].北京:机械工业出版社, 2020.48-51.#1
[2] 刘子瑛.TensorFlow+PyTorch深度学习从算法到实战[M].北京:北京大学出版社,2019.155-157.