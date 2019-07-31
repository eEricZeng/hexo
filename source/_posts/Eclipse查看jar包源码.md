---
title: Eclipse查看jar包源码
categories: 开发工具
tags:
  - 开发工具
  - eclipse
  - 源码
  - jar
  - decomplier
  - java
date: 2019-07-31 19:50:36
---


还在使用Eclipse的同学真是伤不起，点开一个类报一个**Source not fournd**。两步完全解决查看jar源码的问题。

## Maven配置

如图，打开**Window->Preferences->Maven**，勾选“**Download Artifact Sources**”，所有Maven公共仓库提供Source的jar包都可以下到源码，而且带注释。
![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190731190850.png)

## Java Decomplier配置

1.  下载反编译插件[jd-eclipse-2.0.0](https://github.com/java-decompiler/jd-eclipse/releases/download/v2.0.0/jd-eclipse-2.0.0.zip "jd-eclipse-2.0.0.zip")（其他版本看[这里](https://github.com/java-decompiler/jd-eclipse/releases)）
2.  Eclipse安装（**Help->Install New Software->Add**，Archive选择“**jd-eclipse-2.0.0.zip**”）
3.  安装完后重启Eclipse
4.  如下图所示，打开**Window->Preferences->Gneral->Editors->File Associations**，选中“**\*.class without source**”，在下方的Associated editors中选中“**JD Class File Viewer**”点击“**Default->Apply and Close**”。

![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190731193141.png)

## 点评

\*.class不用配置成JD，用原来的Class File Viewer就可以。反编译出来的毕竟不是源码，有可能行不对齐，所以会看到JD用注释标注的行号。还有反编译出来的并不一定准确，而且没注释。

## 建议

换IDEA吧，IDEA自带反编译工具。
