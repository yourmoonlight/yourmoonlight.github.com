---
layout: post
title: Golang服务的性能调优与问题定位
category: Golang
tags: [Golang,Profile,pprof]
---




前段时间做了一个技术分享，关于服务性能调优的，再简要总结一下。

大家用Golang写服务还是比较顺手的，语法简洁，开发速度快，又天然并发。

但是仅仅这样，还不足以让Golang进入编程语言Top10。

写“bug”跟定位bug是俩码事，好像后者要更难一点。不过不用担心，Golang给我们提供了很多实用工具，来帮助我们定位问题。

今天要介绍的就是**pprof**。

#### 原理

市面上有几款Golang的性能调优工具，但是核心都是Golang自带的这个pprof。

它主要分成两部分：

​	1.runtime/pprof

​	2.go tool pprof 

pprof的原理，其实就是对运行时的程序进行**取样**，然后**统计**绘图

当前运行的系统线程，每隔段时间就会收到来自操作系统的中断信号，这时候pprof就会进行取样，然后记录在文件里，取样完成，我们使用go tool pprof工具对样本进行统计分析就OK了。

#### 使用场景

我们需要使用pprof的场景：

​	1.定位内存泄漏

​	2.程序效率瓶颈

​	3.或者就是为了看一下程序的调用图？

经常要做的就两类：内存分析、CPU分析，

照着文档先看一遍，我们常用的命令其实就这么几个：top、peek、list、web。

其中web命令会生成一张调用图，

![pprof-web](https://github.com/yourmoonlight/yourmoonlight.github.com/blob/master/img/pprof-web.png?raw=true)



这张图是CPU profile，我们可以很清楚的看到程序运行时的调用状态，当然主要是看各个方法的耗时。

为什么有的地方是虚线？因为有些耗时特别少的节点没在图上体现出来，要把图连起来，有的地方就使用虚线了。

#### Go-Torch

这张图其实看着有点乱，那有没有能让我们看着舒服一点的工具？

当然有! Uber开源的[火焰图](https://github.com/uber/go-torch)绝对让你high～ 
话不多说，我们上图:

![flame](https://github.com/yourmoonlight/yourmoonlight.github.com/blob/master/img/flame.png?raw=true)简要说一下，图上的X轴代表时间，Y轴代表程序的调用顺序。

所以小方块越长耗时越多，这种瘦瘦长长的就没啥问题，相反，比较扁平的就稍微注意一下～

至于pprof内存分析，也非常有用，之前写的服务有内存泄漏（因为thrift-0.9.1的一个bug，升级到0.9.2就OK了，有空写写thrift）就靠这个搞定的，用起来还挺方便。

呐，有问题欢迎讨论，就酱。