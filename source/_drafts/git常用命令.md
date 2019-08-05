---
title: git常用命令
categories: git
tags:
  - git
date:
---

## git tag
* 打标签

```bash
git tag <tagname>
```
* 查看标签

```bash
git tag
```
* 给以前的提交记录打标签

```bash
git tag <tagname> <commit id>
```
* 带有注释标签

```bash
git tag -a <tagname> -m "message" <commit id>
```
* 将本地tag推送到远程分支

```bash
# 单个tag
git push origin <tagname>

# 所有tag
git push --tags
# 或
git push origin --tags
```
