**Tensor，即张量，是PyTorch中的基本操作对象，可以看作是包含单一数据类型元素的多维矩阵**

## Tensor数据类型

**数据类型** | **CPU Tensor** | **GPU Tensor**
32位浮点 | torch.FloatTensor | torch.cuda.FloatTensor
64位浮点 | torch.DoubleTensor | torch.cuda.DoubleTensor
16位半精度浮点 | N/A | torch.cuda.HalfTensor
8位无符号整型 | torch.ByteTensor | torch.cuda.ByteTensor
8位有符号整型 | torch.CharTensor | torch.cuda.CharTensor
16位有符号整型 | torch.ShortTensor | torch.cuda.ShortTensor
32位有符号整型 | torch.IntTensor | torch.cuda.IntTensor
64位有符号整型 | torch.LongTensor | torch.cuda.LongTensor


- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item