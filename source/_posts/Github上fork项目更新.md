---
title: Github上fork项目更新
categories: git
tags:
  - git
  - github
  - fork
date: 2019-07-22 13:07:40
---
场景：很早之前fork了github/gitee/gitlab等等的项目，但是项目经过多次迭代提交了更多更新，而自己账号下面被fork过来的项目并没有及时更新。此时，需要把提交过的内容更新到自己账号下的远程分支上。以我自己fork过的一个项目为例，主要分添加上游仓库和同步两个步骤。
## 添加上游仓库
查看远程分支状态：
```bash
git remote -v
```
![git remote -v](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190722112159.png)
添加需要同步的上游仓库：
```bash
git remote add upstream https://github.com/tywo45/t-io
```
再次查看远程状态：
```bash
$ git remote -v
origin  git@github.com:eEricZeng/t-io.git (fetch)
origin  git@github.com:eEricZeng/t-io.git (push)
upstream        https://github.com/tywo45/t-io (fetch)
upstream        https://github.com/tywo45/t-io (push)
```
## 同步
将上游仓库内容拉到本地：
```bash
git fetch upstream
```
此时，并没有将上游仓库的内容合并到本地中，可以使用status查看，也可以对比更新：
```bash
git diff master upstream/master --stat
```
若要同步哪个分支就切换到对应的分支，例如master分支：
```bash
git checkout master
```
同步并推送到自己账号下的远程分支，这里的同步本质是把upstream中master分支的内容合并到当前的master分支中：
```bash
git merge upstream/master
git push origin master
```
