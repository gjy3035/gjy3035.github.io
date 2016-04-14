---
layout: post
category: "reading"
title:  "深度学习之目标检测：R-CNN/Fast RCNN/Faster RCNN(2)"
tags: [论文学习,目标检测,CV,CNN]
description: "深度学习之目标检测：R-CNN/Fast RCNN/Faster RCNN(2)"
---

#### 最近又转型做目标检测了，所以目标检测方向将最著名的RCNN做一下笔记。

## Fast RCNN

## 动机

有了RCNN，我们发现其速度并不快：检测速度在40多秒，主要时间消耗在了2000个region proposal的CNN特征提取上。如何一次性对这些region proposal一起处理是本文要解决的问题。

除此之外，并且定位和分类是独立的，如何将这样的multi-task结合在一起同时训练也是另一个问题。

### 思想

作者的核心思想是引入了一个ROI pooling layer，在这一层中，会接收N个Feature Map和ROI列表，然后统一进行一次Max pooling，最终得到7x7的feature map。

对于第二个问题：multi-task， 作者的loss函数定义如下：

<div align="center"><img src='../imgs/fast2.png' /></div>

### 主要框架

<div align="center"><img src='../imgs/fast1.png' /></div>



## Faster RCNN

[详见]()




## 参考文献：
[1] Girshick R. Fast r-cnn[C]//Proceedings of the IEEE International Conference on Computer Vision. 2015: 1440-1448.