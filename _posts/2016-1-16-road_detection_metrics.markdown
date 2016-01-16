---
layout: post
category: "ml"
title:  "Precision, Recall, F-measure, Accuracy, maxF 和 AP 介绍"
tags: [机器学习]
description:  "Precision, Recall, F-measure, maxF 和 AP 介绍"
---
### 最近在做道路检测方面的实验，在这里记录一下道路检测的评价准则。

## Intro
### 在KITTI的道路检测任务中，一共提到了6个评价指标，从不同角度来评价算法性能。在介绍之前先需要对几个基本概念做以说明：

TP：True Positive,　被识别为正样本，实际上是正样本。
TN：True Negative,　被识别为负样本，实际上是负样本。
FP：False Positive,　被识别为正样本，但实际上是负样本。
FN：False Negative,　被识别为负样本，但实际上是正样本。

上面的描述可能比较绕口，其实可以通过直译帮助区分：例如TP/TN：“真的正/负样本”，首先就是被识别正确了，其次是测试样本是“正/负样本”。

## Metrics

### Precision
公式定义如下：
<div align="center"><img src='../imgs/road_metrics1.png' /></div>

正确率代表的是在识别为正样本的数量中，正确识别为正样本所占的比率。

### Recall

公式定义如下：
<div align="center"><img src='../imgs/road_metrics2.png' /></div>

召回率表示在测试集中所有正样本中，正确识别为正样本所占的比率。

### F-measure

公式定义如下：
<div align="center"><img src='../imgs/road_metrics3.png' /></div>

F-measure，在一些其他地方也称作F-score。他是对正确率和召回率的一个加权平均。当β=1时，称为F1 score，此时认为正确率和召回率权重一样。

### Accuracy

公式定义如下：
<div align="center"><img src='../imgs/road_metrics4.png' /></div>

这个就是我们常说的准确率或者精确率了。在道路检测中，正确识别的样本与全部样本的比。

### max F-measure

公式定义如下：
<div align="center"><img src='../imgs/road_metrics5.png' /></div>

τ是一个阈值。在道路检测任务中，每一个像素都会给一个score（归一化到0和1之间）。在分类阈值τ下，其分类结果也会有所不同。这个指标其实有调参包含其中。

### AP

公式定义如下：
<div align="center"><img src='../imgs/road_metrics6.png' /></div>

AP：average precision，即平均正确率。这个指标来自于PASCAL VOC 比赛中的评价准则。本质来看，其实是对正确率/召回率曲线的一个评估。

## 参考文献

Fritsch J, Kuhnl T, Geiger A. A new performance measure and evaluation benchmark for road detection algorithms[C]//Intelligent Transportation Systems-(ITSC), 2013 16th International IEEE Conference on. IEEE, 2013: 1693-1700.











