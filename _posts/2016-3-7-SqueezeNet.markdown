---
layout: post
category: "reading"
title:  "论文笔记--SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and < 1MB model size"
tags: [论文学习,笔记,CV,CNN]
description: "论文笔记--SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and < 1MB model size"
---

#### 最近正好在做关于神经网络剪枝的实验，正好看到这篇文章，感觉效果不错，特此分享一下。


## 摘要

作者提出了一个SqueezeNet，其图像识别性能可以达到AlexNet的水平，而网络参数可以缩减50倍，如果进一步采用Deep Compression的方法，能将压缩率提高到400多倍。

## 主要思想

### 结构设计策略

作者有三个核心的策略，分别如下：

- 将3x3的卷积核替换为1x1：

    这样一来就可以将参数缩减9倍。

- 减小输入数据的通道大小：
  
    我们都知道，某一层的参数数量这样计算：输入通道数 x 滤波器数量 x 核大小（3x3等）。如果能减少输入通道数，也就能大幅减少参数。
    
- 降采样后续网络，使得卷积层能有大的激活图。

前两者是为了减少参数，第三个则是为了最大化准确率。

### Fire Module

作者如下图定义了一个Fire Module：

<div align="center"><img src='../imgs/sq1.png' /></div>

一个压缩卷积模型包括1x1的卷积层和一个后续的expand模型（由两个并行的卷积层构成）。

整个网络结构如下所示：
<div align="center"><img src='../imgs/sq2.png' /></div>

除此之外，为了减小参数，作者并没有采用全链接的方法，而是采用了Network in Network的方法。



## 参考文献：