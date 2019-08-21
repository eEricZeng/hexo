---
title: 使用IDE远程调试
categories: 开发工具
tags:
  - eclipse
  - IDEA
  - java
  - debug
  - 开发工具
date: 2019-08-21 19:49:00
---

以被调试jar包项目为例，使用IDEA/Eclipse对jar包进行远程调试。

## IDEA

> 版本：IntelliJ IDEA 2019.1.2 (Ultimate Edition)

启动被调试jar包时使用如下命令：

```bash
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 -jar XXX.jar
```

其中XXX.jar是被调试项目的jar包。
打开IDEA，依次点击**Run->Debug Configurations**，点击Run/Debug Configurations对话框左上角的“+”号，选择**Remote**。根据需要修改Host和module如下图所示：

![](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190821194512.png)
保存配置之后，点击Debug即可。

## Eclipse

> 版本：Eclipse Oxygen 2.2.100.v20170612-0950

启动时使用如下命令：

```bash
java -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8000 -jar XXX.jar
```

打开Eclipse，依次点击**Run->Debug Configurations**，鼠标右击**Remote Java Application->New**。如下图所示，表单必填项有Connect中的Project和Host，其余的Name、Port根据需要进行修改，端口号与被调试项目一致即可。

![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190821191425.png)

启动Debug运行刚刚创建的“Remote Debug”即可。
