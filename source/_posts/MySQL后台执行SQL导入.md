---
title: MySQL后台执行SQL导入
categories: MySQL
tags:
  - MySQL
  - CentOS
  - shell
date: 2019-09-12 18:28:56
---


## 背景介绍

需要向MySQL数据库中导入大量SQL，而且多个SQL文件到不小，最大的有5.47GB。无论使用Navicat还是命令行导入SQL脚本，时间的等待是不可接受的。因此需要写一个导入SQL的脚本。

## 脚本介绍

创建一个.sh脚本文件import.sh，并输入如下内容：

```bash
#!/bin/bash
mysql -u root -p[password] --database=db <test.sql
```

说明：-u参数指定使用的数据库用户；-p参数是输入数据密码，-p和password之间不能有空格，否则会被识别为数据库；--database参数指定数据库为db；test.sql是指与脚本同级目录下的SQL文件，如果不是请使用绝对路径。远程数据库可使用-h参数指定，如：

```bash
/#!/bin/bash
mysql -h [ip] -u root -p[password] --database=db <test.sql
```

给脚本添加可执行权限，控制台执行如下命令：

```bash
chmod u+x import.sh
```

用nohup命令执行脚本：

```bash
nohup ./import.sh &
```

说明：一定要加“&”，才能脚本放到后台运行。

nohup执行后系统会打印出命令执行的PID，类似如下打印信息：

```console
[1] 22215
```

根据PID使用ps命令可以查看命令执行请求：

```bash
ps -aux | grep 22215
```

打印信息如下：

```console
root     22215  0.0  0.0 113120  1184 pts/1    S    21:25   0:00 /bin/bash ./import.sh
root     21895  0.0  0.0 112648   964 pts/1    S+   21:27   0:00 grep --color=auto 22215
```
