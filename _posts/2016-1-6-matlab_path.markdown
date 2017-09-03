---
layout: post
category: "coding"
title:  "在matlab中如何将文件夹下所有子路径加入到path中"
tags: [MATLAB]
description:  "在matlab中如何将文件夹下所有子路径加入到path中"
---

#### 整个12月都在考试，现在终于有时间做实验了。。。


## 问题描述

很多时候，我们都会需要在matlab中将一些开源库或者代码加入到path中，以便调用。但有些较大的库，子路径众多，这就需要让matlab自行发现这些子路径并添加进来了。

## 方法

具体代码也很简单。
	{% highlight matlab linenos %}
    addpath(genpath('/toolbox-master'),'-begin'); 
    savepath;
	{% endhighlight %}

官方文档对genpath函数的解释如下：
> p = genpath(folderName) returns a path string that includes folderName and multiple levels of subfolders below folderName. The path string does not include folders named private, folders that begin with the @ character (class folders), folders that begin with the + character (package folders), or subfolders within any of these.

该函数会返回一个path string，其中包括了文件夹名称和多级子文件夹名称。



