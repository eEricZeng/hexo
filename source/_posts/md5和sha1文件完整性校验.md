---
title: md5和sha1文件完整性校验
categories: CentOS
date: 2019-09-12 18:49:12
tags:
  - md5
  - sha1
---

简单而强大的命令：md5sum/sha1sum。

## 背景介绍

下载多个大文件，中间网络出现过多次断点续传、下载工具报错等问题，自己也不知道文件到底是否是完整的，依次比较是否下载完所有文件略显费力。官方提供了md5/sha1文件完整性校验码，如何使用这两个文件进行校验是本文的目的。

## md5sum校验

md5sum是Linux系统自带的命令，windows系统安装了bash shell也可以使用该命令。将校验文件md5sums.txt复制到下载的文件同级目录下，只需执行如下命令：

```bash
md5sum -c md5sums.txt
```

执行结果示例如下：

```console
file1.zip: OK
file2.zip: OK
file3.zip: OK
file4.zip: FAILED
```

说明：-c参数为--check的缩写，意思是从文件中读取md5校验码并校验文件中指定文件。OK代表校验通过，FAILED代表文件不完整。

## 生成md5校验码

当我们共享给其他人文件资源时，也可以自己生成md5校验码供其他用户校验。生成md5校验码命令：

```bash
md5sum *.zip > md5sums.txt
```

该命令会给当前目录下的每一个zip文件生成一个对应的md5校验码，文件内容示例如下：

```console
d934615427115004a17771afc9d294e7 file1.zip
74cf07ce15cdf3f0639d69093661c26a file2.zip
d3dc5b39344a8f861741398e8ecb96fd file3.zip
ff7922f5c3b02c2cf99203912660e735 file3.zip
```

sha1sum命令与md5sum类似，因此不再赘述。
