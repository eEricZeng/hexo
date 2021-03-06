---
title: hexo博客站点sitemap的使用
date: 2019-07-14 18:48:58
categories: hexo
tags:
  - hexo
  - sitemap
  - 检索
  - 搜索引擎
  - google search
  - seo
---
## 版本说明
* hexo v3.9.0
* hexo-theme-next v7.1.2

## 启用sitemap功能
为了让博文被google或百度检索，需要使用hexo的sitemap功能。修改themes/next/_config.yml：
```yml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```
## 安装插件
切换到hexo根目录下，安装搜索引擎插件，插件根据自己的需要安装其中一个或者都安装：
```bash
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```
如何此时你跟我一样使用的是next主题，直接执行如下命令即可访问站点地图：
```bash
hexo g
```
此时，在public的静态文件根目录中会多出一个sitemap.xml文件，表示谷歌检索的sitemap安装成功。
## 谷歌检索
### 检测网站是否被检索
以GitHub Pages为例，新建的GitHub Pages是没有被谷歌检索的。在谷歌搜索栏中输入如下信息进行检索：
```md
site: xxx.github.io
```
xxx为你的GitHub账号，如果没有被检索，不会显示hexo中的任何博文，如果有下面的步骤就可以省略了。
### 验证站点
需要谷歌账号，没有的可以申请一个。登录[GoogleSearchConsole](https://www.google.com/webmasters/tools)，点击立即使用，输入需要验证所有权的网站地址，例如本站地址
```md
https://eericzeng.github.io
```
对于hexo博客网站，最好使用__HTML标记__法验证网站所有权，~~复制Google给的标记内容粘贴到themes/next/layout/_partials/head/head.swig文件meta标签最后面。这里使用的是next主题，其他主题类似。~~我发现next主题有更好的配置方式，千万不要采用修改head.swig文件的方法，不然在升级NexT版本时会遇到很多不必要的麻烦。修改themes/next/_config.yml文件：
```yml
# Google Webmaster tools verification.
# See: https://www.google.com/webmasters
google_site_verification: xxxxxxxxxx
```
然后生成静态文件并发布：
```bash
hexo g
hexo d
```
回到谷歌搜索控制台继续进行验证，点击__完成__。
### 添加sitemap.xml
打开谷歌控制台选择上一步添加的网站->__索引__->__站点地图__，在添加新的站点地图中填入"sitemap.xml"即可，添加成功会在__已提交的站点地图__中显示，过一段时间在__概述__中会显示被索引的情况。
## 百度检索
### 添加站点
登录[百度资源管理平台](https://ziyuan.baidu.com/)，依次点击__用户中心__->__站点管理__->__添加网站__（网站会随时更新，路径可能不同，只要找到添加网站的位置就可以）。
1. 输入网址
```
https//eericzeng.github.io
//（注意协议头，gitpage在http基础上要加s）
```
2. 站点属性
最多可以选择三项，建议相关的都选上，提高曝光度
3. 验证网站
NexT主题选择__HTML标签验证即可__，复制<meta>标签中的content值，修改next配置文件_config.yml：
```yml
# Baidu Webmaster tools verification.
# See: https://ziyuan.baidu.com/site
baidu_site_verification: xxxxxxxxxx
# Enable baidu push so that the blog will push the url to baidu automatically which is very helpful for SEO.
baidu_push: true
```
百度资源页面先不要关闭，执行下面的步骤之后再点击__完成验证__。

### 发布并验证
hexo根目录下执行如下命令：
```bash
hexo g
hexo d
```
回到百度资源网站，点击__完成验证__。
## 参考资料
1. [让Google搜索到搭建在Github Pages上的博客](https://jactor-sue.github.io/zh-CN/how-blog-on-githubpages-can-be-searched-by-google/)
2. [生成sitemap站点地图](https://www.jianshu.com/p/9c2d6db2f855)
