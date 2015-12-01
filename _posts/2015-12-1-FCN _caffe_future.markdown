---
layout: post
category: "env"
title:  "使用caffe-future完成FCN网络的训练"
tags: [深度学习,CAFFE,FCN]
description:  "使用caffe-future完成FCN网络的训练"
---

#### FCN算是当前最火的一个深度网络了，目的在与解决密集估计（Dense prediction）（例如语义分割/显著性检测等任务）问题。自从去年11月提出之后，就广受欢迎，并且获得了CVPR2015最佳论文奖（提名）。具体论文大家可以参见[arViv论文](http://arxiv.org/abs/1411.4038)，caffe-future代码可以从[caffe-future](https://github.com/longjon/caffe/tree/future) 获得。
#### 本篇中简要说说如何在自己的数据上训练自己的FCN网络，具体的理论知识，等过了考试月我再加入进来。



## 准备数据
### 网络结构数据（.caffemodel & .prototxt）

本篇文章我们采用VGG 16模型，原模型文件大家可以从Caffe的[Model Zoo](https://gist.github.com/ksimonyan/211839e770f7b538e2d8#file-readme-md) 下载。

由于这一模型是做图像分类的，因此，我们需要将其从全连接层（fc6和fc7）往上的层全部“砍掉”，。其实在FCN的Model Zoo中，论文作者已经为我们写好了他修改的[模型定义文件](https://gist.github.com/shelhamer/08652f2ba191f64e619a) (该模型是FCN-16s，其他的8s/32s也可在Model zoo中找到下载链接）。作者是在Pascal VOC Context Segmentation上进行的。但是对于这一定义文件仅仅是针对于一个59类别的语义分割任务展开的，因此，我们还需要进一步修改prototxt文件，但这已经远远要比自行从VGG 16的定义文件修改容易得多。

### 修改定义文件
#### 修改deploy.prototxt
deploy.prototxt是用于模型测试时的模型定义文件。我们需要将作者所有的score层中的```num_output: 60```修改为自己的输出值。在作者的数据集中，共有59类别和一个背景类别，因此是60.

之前刚刚说过，我们需要将模型的后几层“砍掉”，其实，在实际操作中，并不是将其砍掉不顾。而是：我们在网络定义文件中新建自己的层，将原来的fc6/7弃之不用即可。那么对于Jonathan Long提供的[deploy文件](https://gist.github.com/shelhamer/08652f2ba191f64e619a#file-deploy-prototxt) 我们只需要将fc6/7改个名字即可，因为他已经帮我们改好了layer类型和参数。

#### 修改train_val.prototxt

train_val.prototxt文件是我们在训练网络时的网络定义文件。也就是说它包含了输入数据的信息在里面，要比deploy仅仅做test的内容更丰富。在Jonathan Long提供的[文件](https://gist.github.com/shelhamer/08652f2ba191f64e619a#file-train_val-prototxt) 中，完全按照deploy文件的方式修改即可。除此之外，将LMDB路径换为自己的（关于这里有两个输入的疑问，下面一部分会说到），修改RGB的mena值为自己训练集的mean值。

#### solver.prototxt
这个根据自己需求修改，在这里，我们可以完全利用Jonathan Long所提供的文件。

## 生成数据

和图像分类不同的是，每一个图像并不是一个标量标签，而是一个Map，或者叫做矩阵。因此，我们需要分别生成图像和GroundTruth（以下简称GT）的LMDB。然后输入到网络中去进行训练。之前我们在修改train_val.prototxt时，大家可能都已经注意到训练集和验证集分别有两个输入（我仅仅列了训练集的输入）：
	```
    layer {
      name: "data"
      type: "Data"
      top: "data"
      include {
        phase: TRAIN
      }
      transform_param {
        mean_value: 106.08069
        mean_value: 103.75618
        mean_value: 100.05657
      }
      data_param {
        source: "/home/gjy/Documents/caffe-master-newest/data/road_camvid/LMDB/img/train"
        batch_size: 1
        backend: LMDB
      }
    }
    layer {
      name: "label"
      type: "Data"
      top: "label"
      include {
        phase: TRAIN
      }
      data_param {
        source: "/home/gjy/Documents/caffe-master-newest/data/road_camvid/LMDB/gt/train"
        batch_size: 1
        backend: LMDB
      }
    }
	```
为了完成end-to-end的训练，分开输入的情况完美满足了我们的需求。

接下来就是数据的生成了，参考[Issue1698](https://github.com/BVLC/caffe/issues/1698) 和其他人的代码，我的数据生成代码如下：

{% highlight python linenos %}
    import os
    import numpy as np
    from scipy import io
    import lmdb
    import caffe
    from PIL import Image

    NUM_IDX_DIGITS = 10
    IDX_FMT = '{:0>%d' % NUM_IDX_DIGITS + 'd}'

    def img_to_lmdb(paths_src,path_dst):
        in_db = lmdb.open(path_dst, map_size=int(1e12))
        with in_db.begin(write=True) as in_txn:
            for in_idx, in_ in enumerate(paths_src):
                # load image:
                # - as np.uint8 {0, ..., 255}
                # - in BGR (switch from RGB)
                # - in Channel x Height x Width order (switch from H x W x C)
                im = np.array(Image.open(in_)) # or load whatever ndarray you need
                im = im[:,:,::-1]
                im = im.transpose((2,0,1))
                im_dat = caffe.io.array_to_datum(im)
                in_txn.put('{:0>10d}'.format(in_idx), im_dat.SerializeToString())
        in_db.close()

    def matfiles_to_lmdb(paths_src, path_dst, fieldname,lut=None):

        db = lmdb.open(path_dst, map_size=int(1e12))

        with db.begin(write=True) as in_txn:

            for idx, path_ in enumerate(paths_src):
                print idx
                content_field = io.loadmat(path_)[fieldname]
                # get shape (1,H,W)
                while len(content_field.shape) < 3:
                    content_field = np.expand_dims(content_field, axis=0)
                content_field = content_field.astype(int)

                if lut is not None:
                    content_field = lut(content_field)

                img_dat = caffe.io.array_to_datum(content_field)
                in_txn.put(IDX_FMT.format(idx), img_dat.SerializeToString())

        db.close()

        return 0
{% endhighlight %}

我们的GT格式为.mat文件，当然，这和我上一个工作有关，上一个工作用的matlab实现的。

## 训练

这一部分就和往常的训练（严格来讲，叫做fine-tune)一样，可以在终端采用或者利用python接口训练。

