---
title: CentOS7服务开机自动启动
categories: CentOS
tags:
  - CentOS
  - 笔记
  - systemctl
date: 2019-09-07 21:44:57
---

专治健忘症

以Apache HTTP服务为例：

### 添加开机启动

开机启动http服务

```bash
systemctl enable httpd
```

添加手动安装的服务，如nginx，可参考[该文](https://www.jianshu.com/p/ca5ee5f7075c "CentOS7设置nginx开机自启动
")。

### 删除开机启动

删除开机启动http服务

```bash
systemctl disable httpd
```

### 查看所有开机启动的服务

```bash
systemctl list-unit-files | grep enabled
```
