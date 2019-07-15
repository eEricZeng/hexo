---
title: hexo添加阅读统计
date: 2019-07-15 12:29:48
categories: hexo
tags:
  - hexo
  - 阅读统计
  - 百度统计
---
## 版本说明
* hexo v3.9.0
* hexo-theme-next v7.1.2

## 不蒜子
NexT内置了leancloud、firebase、busuanzi三种访客统计插件，前两种需要到官网注册获取网站颁发的appKey，相对麻烦，有兴趣的请访问[leancloud](https://leancloud.cn)、[firebase](https://console.firebase.google.com/u/0/)。而不蒜子配置只需要将false改为true即可：
```yml
busuanzi_count:
  enable: true
```
更多用法请参考官网说明[不蒜子官网](http://ibruce.info/2015/04/04/busuanzi/)。
## 百度统计
起初我以为阅读统计是通过百度统计进行计数的，后来发现百度统计、GA等只是分析工具，并不会把统计信息显示在博客页面上，所以是否需要百度统计看个人需求。打开[__百度统计__](https://tongji.baidu.com/)，登录并进入__网站列表__，点击__新增网站__。以我的GitPage为例，填写新增网站表单，添加必要字段__网站域名__：eericzeng.github.io，__网站首页__：https://eericzeng.github.io， __网站名称__ 、__行业类别__选填。点击“确定”后，会出现包含如下信息的提示：
```JavaScript
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "https://hm.baidu.com/hm.js?324a1c41343d123482f031976f9";
  var s = document.getElementsByTagName("script")[0];
  s.parentNode.insertBefore(hm, s);
})();
</script>
```
复制"https://hm.baidu.com/hm.js?" 后面的Id字符串，粘贴到主题配置文件中，如themes/next/_config.yml
```yml
# Baidu Analytics ID
baidu_analytics: 324a1c41343d123482f031976f9
```
## 发布
重新打包静态文件并发布：
```bash
hexo g
hexo s
```
点击__百度统计__控制台__，代码管理__->__代码安装检查__进行安装校验。
