```markdown
import torch
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module)    # 这里的 nn.Module 是继承
  def __init__(self):
    super(Net, self).__init__()
    pass
    
  def forward(self):    # 前向传递
    pass

# 初始化一个类实例，将x参数传给forward函数并运行
net = Net( ， ，)

    
# 定义优化器
# SGD：随机梯度下降
# net.parameters():传入网络参数; lr:学习效率 < 1,学习率太高可能会导致不收敛
optimizer = torch.optim.SGD(net.parameters(), lr=0.5)

# 定义损失函数
# MSELoss:均方差损失函数
# CrossEntropyLoss:交叉熵损失函数
loss_func = torch.nn.MSELoss()

for t in range(100):
  prediction = net(x)
  
  # 预测值和真实值通过损失函数计算误差
  loss = loss_func(prediction, y)
  
  # 把所有参数的梯度先全部降为0
  # 清零是由于pytorch的梯度是累加的，因此要对每一次的梯度降为0
  optimizer.zero_grad()
  loss.backward()
  optimizer.step()
