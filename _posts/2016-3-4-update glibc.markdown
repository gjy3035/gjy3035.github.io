---
layout: post
category: "env"
title:  "/lib64/libc.so.6: version ‘GLIBC_2.14' not found问题解决"
tags: [Glibc]
description:  "/lib64/libc.so.6: version ‘GLIBC_2.14' not found问题解决"
---


## 问题描述

在运行Faster R-CNN代码demo时，报错如下：
{% highlight python linenos %}
ImportError: /lib64/libc.so.6: version ‘GLIBC_2.14' not found (required by /opt/anaconda/lib/python2.7/site-packages/cv2.so)
{% endhighlight %}

利用```strings /lib64/libc.so.6 |grep GLIBC```命令查看glibc版本,结果如下：

{% highlight python linenos %}
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_PRIVATE
{% endhighlight %}

## 解决

由于Glibc的使用比较广泛，为了不污染当前的系统环境，决定自定义安装目录。后续使用时，自行加上环境变量即可。

{% highlight python linenos %}
[root@localhost ~]# tar xvf glibc-2.14.tar.gz
[root@localhost ~]# cd glibc-2.14
[root@localhost glibc-2.14]# mkdir build
[root@localhost glibc-2.14]# cd ./build
[root@localhost build]# sudo ../configure --prefix=/opt/glibc-2.14
[root@localhost build]# sudo make -j4
[root@localhost build]# sudo make install
{% endhighlight %}

接下来修改环境变量（```export LD_LIBRARY_PATH=/opt/glibc-2.14/lib:$LD_LIBRARY_PATH```）后，我们查看一下版本号（```strings /opt/glibc-2.14/lib/libc.so.6 |grep GLIBC```)，如下显示：

{% highlight python linenos %}
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_PRIVATE
{% endhighlight %}

OK，大功告成！

##<font color=red>注！！！：如果想更换系统的libc版本，一定要在root权限下操作，否则，在删除或者重命名原来的链接后，大部分命令包括（sudo，su）将无法使用。</font>











