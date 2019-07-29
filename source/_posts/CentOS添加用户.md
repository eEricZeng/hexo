---
title: CentOS添加用户
categories: CentOS
tags:
  - CentOS
  - useradd
date: 2019-07-29 19:06:34
---


## 添加用户

```bash
useradd username
```

添加用户的同时将该用户指定到了同名的用户组中，如果将新添加的用户放到root组中，执行如下命令即可：

```bash
useradd -g root username
```

root是用户组名，username是用户名。

添加完用户要及时修改密码：

```bash
passwd zengguang
```

密码输入两次。

切换用户：

```bash
su username
```
