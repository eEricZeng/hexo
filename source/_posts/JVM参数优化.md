---
title: JVM参数优化
categories:
  - java
  - jvm
tags:
  - java
  - jvm
  - GC
  - OOM
date: 2019-09-28 22:53:46
---


## 背景介绍

最近，将应用手动部署到新的CentOS环境上，JRE/Tomcat都是新安装的，所以还没对JVM进行优化。习惯了本地开发和持续集成系统上现成的环境，往往会忽略JVM参数的配置。所以在启动没多久后就OOM了。。。

## JVM优化

### 优化前

应用启动后GC状态如下图所示，从老年代可以看到应用已经执行了9次Full GC，而且消耗了14.418s，可以说这种状态应用是持续不了多久的。

![https://eericzeng.github.io](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190924201746.png)

> 图1 优化前初始运行

JVM为了获取足够的可用空间还会执行Full GC，此时垃圾收集器不是并行执行Full GC，所以Stop The World(STW)到来了。再看看第二张图的变化，老年代达到1.273G，已经完全占满，没有额外的空间可供分配的，就会出现OOM。如果是一个Web应用，我们就会发现前端各种无响应、各种超时。

![https://eericzeng.github.io](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190924204328.png)

> 图2 优化前运行一段时间后

### 执行优化

JVM添加哪些参数会优化应用呢？不妨再从第二张图看看，我们的应用缺什么。

1.  老年代都不够分配，当然需要增大堆空间了。

    所以需要添加-Xmx设置最大堆空间，以8G服务器为例，最大可以设置7G+，最好不要设置满，从调优角度来考虑，可以逐步网上加，暂且设置4G。

2.  再看看新生代，Eden区与Survivor区（S0/S1）比例十分不合理。

    图2中Eden: S0: S1 = 1:1:1，对于采用标记复制的新生代推荐的比例是8:1:1，所以需要修改SurvivorRatio参数为8。

3.  老年代堆空间增长很快。

    老年代堆空间增长很快，所以我们需要延长新生对象到老年代的代数（MaxTenuringThreshold）。

4.  垃圾收集器

    由于本应用与用户的交互性很强，所以老年代的垃圾收集器采用并发执行的CMS垃圾收集器，同时，需要选择合适的新生代垃圾收集器ParNewGC

综合以上四点设置的JVM优化参数如下：

```bash
-Xms4g
-Xmx4g
-XX:SurvivorRatio=8
-XX:MaxTenuringThreshold=15
-XX:+UseParNewGC
-XX:+UseConcMarkSweepGC
-XX:ParallelGCThreads=4
```

这个时候的GC状态见图3所示：

![https://eericzeng.github.io](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190928215837.png)

> 图3 初步调优GC状态

这个时候JVM状态比没调优之前要好的多，但是还是刚刚运行就发生了几次Full GC。再根据图3GC状态进行分析：

1.  新生代太小了。

    即使有MaxTenuringThreshold参数加持，但是多少对象都熬不过两代就因为S0/S1太小了被复制到了老年代，需要适当增大新生代空间（NewSize和MaxNewSize）。

2.  堆空间调整

    增大新生代必然会挤占老年代，而且，对于刚刚运行没多久的应用已经发生了4次Full GC也说明堆空间还是不够用，因此还需要调整-Xmx参数。

此时，JVM优化参数如下：

```bash
-Xms6g
-Xmx6g
-XX:SurvivorRatio=8
-XX:MaxTenuringThreshold=15
-XX:+UseParNewGC
-XX:+UseConcMarkSweepGC
-XX:ParallelGCThreads=4
-XX:NewSize=1g
-XX:MaxNewSize=1g
```

这个时候的GC状态见图4所示：

![https://eericzeng.github.io](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190928223028.png)

> 图4 二次优化后GC状态图

从图中不难发现Minor GC次数从97减少到了31次，明显少了很多，而且比较好使的MinorGC基本上都发生在程序启动阶段。再加上GC日志和其他辅助参数，用压力工具Loadrunner压测一下，JVM参数如下：

```bash
-Xms6g
-Xmx6g
-XX:SurvivorRatio=8
-XX:MaxTenuringThreshold=15
-XX:+UseParNewGC
-XX:+UseConcMarkSweepGC
-XX:ParallelGCThreads=4
-XX:NewSize=1g
-XX:MaxNewSize=1g

-server
-Xss512k
-XX:+DisableExplicitGC
-verbose:gc
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+PrintGCApplicationConcurrentTime
-XX:+PrintHeapAtGC
-Xloggc:logs/gc.log
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=logs/HeapDumpOnOutOfMemoryError
```

![https://eericzeng.github.io](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190928223946.png)

> 图5 测试一段时间后GC状态

新生代Minor GC 499次，排除启动开始比较耗时的31次，之后的每次Minor GC耗时为：(42.896-14.809)/(499-31)≈60ms，目前单次Full GC是最耗时的，达到210.861ms，对于用户交互来说还是可以接受的。当然这不一定是最优的结果，可以持续优化下去，达到最理想的状态——没有Full GC、少量Minor GC。

## 总结

之前看过很多JVM的知识，但是一直没有实际操作过，这个也算本人第一次优化JVM参数吧，如有不当之处欢迎指正。

## 参考资料

1.  [jvisualvm远程连接](https://eericzeng.github.io/2019/09/20/jvisualvm%E8%BF%9C%E7%A8%8B%E8%BF%9E%E6%8E%A5Tomcat/ "jvisualvm远程连接")
2.  [jvm参数-verbose:gc和-XX:+PrintGC的区别](https://segmentfault.com/q/1010000004348215 "segmentfault:jvm参数-verbose:gc和-XX:+PrintGC有区别？")
