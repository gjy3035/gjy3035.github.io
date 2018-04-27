---
layout: post
title: 2018-4-27-PCC Net readme
category: "reading"
tags: [Code, Paper]
description: ""
---

This is an official implementaion od the paper "PCC net".

In the paper, the experiments are conducted on the three populuar datasets: Shanghai Tech, UCF_CC_50 and WorldExpo'10. To be specific, Shanghai Tech Part B contains crowd images with the same resolution.For easier data prepareation, we only release the pre-trained model on ShanghaiTech Part B dataset in this repo.

# Requirements
- Python 2.7
- Pytorch 0.2.0
- TensorboardX

# Data preparation
1. Download the original ShanghaiTech Dataset [Link: [Dropbox ](https://www.dropbox.com/s/fipgjqxl7uj8hd5/ShanghaiTech.zip?dl=0)/ [BaiduNetdisk](https://pan.baidu.com/s/1nuAYslz)]
2. Resize the images and the locations of key points. 
3. Generate the density maps by using the [code](https://github.com/aachenhang/crowdcount-mcnn/tree/master/data_preparation).
4. Generate the segmentation maps.

We also provide the processed Part B dataset. [[Link]()]

# Code preparation


# Training model
1. Run the train_lr.py: ```python train_lr.py```.
2. See the training outputs: ```Tensorboard --logdir=exp --port=1234```.

We provide the **trained PCC Net model** on Part B dataset, which can achieve MAE of 11.9 and MSE of 19.0. [[Download Link]()]

# Expermental results

## Quantitative results

We show the Tensorboard visualization results as below:
![Detialed infomation during the traning phase.](./images/1524789498744.jpg).
The mae and mse are the results on test set. Others are triaining loss. 

## Visualization results
Visualization results on the test set as below:
![Visualization results on the test set as below:](./images/1524789935027.jpg)
Column 1: input image; Column 2: density map GT; Column 3: density map prediction; Column 4: segmentation map GT; Column 5: segmentation map prediction. 



