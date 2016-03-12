---
layout: post
category: "env"
title:  "编译caffe遇到的问题汇总"
tags: [CAFFE]
description:  "编译caffe遇到的问题汇总"
---
#### 2016.3.4更新
### K80上一直忘记安装了cudnn，现在记录如下：

## 环境是cuda7.5，对应cudnn v3，即7。0。

解压之后，
{% highlight python linenos %}
$ sudo cp include/cudnn.h /usr/local/include  
$ sudo cp lib64/libcudnn.* /usr/local/lib  
{% endhighlight %}

链接库文件，
{% highlight python linenos %}
$ sudo ln -sf /usr/local/lib/libcudnn.so.7.0.64 /usr/local/lib/libcudnn.so.7.0  
$ sudo ln -sf /usr/local/lib/libcudnn.so.7.0 /usr/local/lib/libcudnn.so  
$ sudo ldconfig -v 
{% endhighlight %}

在caffe的MakeFile中打开CUDNN即可。

# 2016.1.26更新

### K80来了一周了，为了大面积实验，再次安装caffe。好在现在可以在几小时内安装完成。想想去年这个时候，花了一周多时间搭建环境，还真是有毅力：）

## 问题描述

突然在编译caffe到最后一步时，出现了core dumped。但是runtest的所有测试均已通过，汗！报错如下:
<div align="center"><img src='../imgs/caffe_1.png' /></div>
我自己也是挺郁闷的。

## 原因

最终Google之后，发现问题的原因是：
由于上次使用了一个节省显存的方法，所以参见[#2016](https://github.com/BVLC/caffe/pull/2016) 修改了文件，引起了这一个现象。

但后期的测试以及实验，并未发现大的问题。如有问题，再做记录。



#### 2016.1.13更新

## 问题描述

在编译caffe最后一步：```make runtest -j40``` 时，会出现如下错误：

{% highlight python linenos %}
$ make runtest -j40
.build_release/tools/caffe
.build_release/tools/caffe: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by /home/anothergjy/Desktop/caffe-master-matlab-python-gpu02-251/
.build_release/tools/../lib/libcaffe.so)
.build_release/tools/caffe: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by /usr/local/lib/libopencv_highgui.so.2.4)
make: *** [runtest] Error 1
{% endhighlight %}

原因是版本太低。需要更高的版本。关于版本的查看可以用下面的命令：
{% highlight python linenos %}
strings libstdc++.so.6.0.18| strings /usr/lib64/libstdc++.so.6|grep GLIBCXX
{% endhighlight %}
## 解决办法

这里已经安装好了GCC-4.8.2，因此需要修改软连接即可。

需要连接的文件存在于```gcc-build-4.8.2/x86_64-unknown-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.18```下面。
但需要注意的是src目录下的.lib文件夹是隐藏的，需要使用root权限才可以看到，将其复制到/usr/lib64/下。
接下来，就是要修改连接，让原本的libstdc++.so.6指向ibstdc++.so.6.0.18。
{% highlight python linenos %}
# cd /usr/lib64
# rm -r libstdc++.so.6
rm: remove symbolic link `libstdc++.so.6'? y
# ln -s libstdc++.so.6.0.18 libstdc++.so.6
{% endhighlight %}

查看版本结果如下：
{% highlight python linenos %}
# strings libstdc++.so.6.0.18| strings /usr/lib64/libstdc++.so.6|grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_FORCE_NEW
GLIBCXX_DEBUG_MESSAGE_LENGTH
{% endhighlight %}

可以看到最新版本已经到了3.4.19。

#### 2016.1.11更新

## 问题描述

由于最新版的caffe支持多卡，因此，在编译过程中，如果检测到机器是多卡，便会在runtest阶段出现报错：
{% highlight python linenos %}
	The difference between XXX and XXX...
{% endhighlight %}

## 解决方法
对于这样的问题，只需要加入一行代码，```export CUDA_VISIBLE_DEVICES=0```

这样以来，runtest就可以顺利通过了。

####2015.3.25 更新

## 问题

CuDNN是专门针对Deep Learning框架设计的一套GPU计算加速方案，目前支持的DL库包括Caffe，ConvNet, Torch7等。

CuDNN可以在官网免费获得，注册帐号后即可下载。官网没有找到安装说明，下载得到的压缩包内也没有Readme. 不过google一下就会找到许多说明。基本原理是把lib文件加入到系统能找到的lib文件夹里， 把头文件加到系统能找到的include文件夹里就可以。这里把他们加到CUDA的文件夹下（[参考这里](https://groups.google.com/forum/#!searchin/caffe-users/cudnn/caffe-users/nlnMFI0Mh7M/8Y4z1VCcBr4J)）

## 解决
{% highlight python linenos %}
tar -xzvf cudnn-6.5-linux-R1.tgz
cd cudnn-6.5-linux-R1
sudo cp lib* /usr/local/cuda/lib64/
sudo cp cudnn.h /usr/local/cuda/include/
{% endhighlight %}

执行后发现还是找不到库， 报错
```error while loading shared libraries: libcudnn.so.6.5: cannot open shared object file: No such file or directory```,而lib文件夹是在系统路径里的，用ls -al发现是文件权限的问题，因此用下述命令先删除软连接
{% highlight python linenos %}
cd /usr/local/cuda/lib64/
sudo rm -rf libcudnn.so libcudnn.so.6.5
{% endhighlight %}

然后修改文件权限，并创建新的软连接

{% highlight python linenos %}
sudo chmod u=rwx,g=rx,o=rx libcudnn.so.6.5.18 
sudo ln -s libcudnn.so.6.5.18 libcudnn.so.6.5
sudo ln -s libcudnn.so.6.5 libcudnn.so
{% endhighlight %}












