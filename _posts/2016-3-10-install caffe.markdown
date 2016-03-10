---
layout: post
category: "env"
title:  "实验室安装caffe小记"
tags: [caffe]
description: "实验室安装caffe小记"
---

#### caffe安装小记：目的为了实验室以后更快地安装caffe。

# 环境配置
硬件环境：CentOS 6.5，GPU：K80
已完成安装：CUDA7.5，OPENCV2.4.10，GCC4.8.2，OpenBLAS，并且包含所有依赖在其他节点“遗留”的安装包。

## 1：安装Anaconda2 2.5，

下载地址：https://www.continuum.io/downloads

```
sudo sh Anaconda2-2.5.0-Linux-x86_64.sh
```

阅读协议后，```yes```同意，输入安装路径```/opt/anaconda2-2.5```(为了防止和之前Anaconda冲突)。

最后一步，将新安装的Anaconda写入PATHA.之后各个节点可以直接使用2.5版本的Anaconda。

## 2：基本依赖安装：

```
sudo yum -y groupinstall "Development Tools"
```

添加EPEL repositories：

```
sudo rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```

接着安装protobuf，leveldb，snappy，hdf5等：

```
sudo yum install atlas-devel protobuf-devel leveldb-devel snappy-devel opencv-devel hdf5-devel lmdb-devel
```

## 3：安装Boost：

```
sudo yum install boost-devel
```

这个版本可能过低，为以后使用，安装boost1.59。

```
$ 解压
$ 进入目录
$ ./bootstrap.sh
$ ./b2
$ sudo ./b2 install
```

注：可能需要sudo权限。

## 4： 手动安装glog，gfalgs，protobuf

注：由于最新版本的gflags 2.1下，并不能编译glog，因此必须先行安装glog！！！

###glog:

```
$ 进入目录
$ make clean #清楚之前编译结果
$ ./configure 
$ make
$ make install
```

### gflags：

```
$ 进入目录
$ rm -rf build 清除之前编译结果
$ mkdir build && cd build
$ export CXXFLAGS="-fPIC" && cmake .. && make VERBOSE=1
$ make && make install
```

### protobuf：

```
$ 进入目录
$ make clean （清除上次编译结果）
$ ./configure
$ sudo make
$ sudo make install
```

## 5：使用cuDNN加速

已经完成下载放于/opt/cuda/下。
```
$ cd /opt/cuda
$ sudo cp include/cudnn.h /usr/local/include
$ sudo cp lib64/libcudnn.* /usr/local/lib
$ # 链接库文件：
$ sudo ln -sf /usr/local/lib/libcudnn.so.7.0.64 /usr/local/lib/libcudnn.so.7.0
$ sudo ln -sf /usr/local/lib/libcudnn.so.7.0 /usr/local/lib/libcudnn.so
$ sudo ldconfig -v
```

## 6： gflags 重装

gflags安装失败，编译caffe时提示应该按照```CXXFLAGS="-fPIC"```来编译，但无论如何尝试也不行。
迫不得已，只能将其他节点以前安装成功的gflags拷贝过来。

环境配置到此结束。

# 安装Caffe

	```
	>>>make all -j40
	>>>make test -j40
	#选择GPU
	>>>export CUDA_VISIBLE_DEVICES=0
	>>>make runtest -j40
	>>>make pycaffe
	#如果打开了matlab wrapper，请编译matlab接口
	>>>make matcaffe
	>>>make pycaffe
	```
















## 参考：
[1]：http://caffe.berkeleyvision.org/install_yum.html
[2]：http://my.oschina.net/speedinghzl/blog/464142
