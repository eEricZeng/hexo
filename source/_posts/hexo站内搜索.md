---
title: hexo站内搜索
date: 2019-07-14 21:14:58
categories: hexo
tags:
  - hexo
  - search
  - 站内搜索
---
### 版本说明
* hexo v3.9.0
* hexo-theme-next v7.1.2

### 安装插件
安装 hexo-generator-searchdb，在站点的根目录下执行以下命令：
```bash
$ npm install hexo-generator-searchdb --save
```
### 修改配置文件
编辑__站点配置文件__，新增以下内容到任意位置：
```yml
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
编辑__主题配置文件__，启用本地搜索功能：
```yml
# Local search
local_search:
  enable: true
```
执行如下命令进行本地展示：
```bash
hexo g
hexo s
```
参考[next官方说明](https://theme-next.iissnan.com/third-party-services.html)
