---
layout: post
title: "关于OOM问题（提高系统分配内存）"
categories: ANDROID
tags: [ANDROID]
---

在网上找了很多防止OOM的方法，在此收藏一下

我之前试过软引用、弱引用，避免上下文内存溢出，Bitmap及时recycle()这些避免OOM的方式，但是这对一些如"唯品会"这种全大图的app没什么效果，所以在网上找到了提高系统分配内存来防止OOM的方法

1、API 11及以上的Android版本可以在Mainifest清单文件中application属性设置加上android:largeHeap="true"，可以使用的最大内存值，不过一般情况下不需要这么多内存分配，如果是全大图的APP，上面提到的方法都不可避免的OOM情况下，可以用这个方式。

2、用android:process 定义activity运行所在的进程名称，以“：”开头，则一个新的专属于该应用的进程将会被创建。以小写字母开头，则为该activity提供权限以让其在一个全局的进程中运行。这样会允许多个应用的不同组件共用一个进程，以便节省资源。Android是支持多进程的，每个进程的内存使用限制一般为24MB的内存，所以当完成一些很耗费内存的操作如处理高分辨率图片时，需要单独开一个进程来执行该操作

参考资料：[http://blog.csdn.net/hustpzb/article/details/7785722](http://blog.csdn.net/hustpzb/article/details/7785722)