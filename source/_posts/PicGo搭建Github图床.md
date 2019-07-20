---
title: PicGo搭建Github图床
tags:
  - PicGo
  - 图床
  - hexo
date: 2019-07-20 14:52:50
categories:
---

## 版本说明
* hexo v3.9.0
* node.js v6.4.1
* PigGo v2.1.2

## 安装
安装PicGo需要node.js环境，没有的看[这里](https://nodejs.org/en/ "node.js")。[下载](https://github.com/Molunerfinn/picgo/releases)PicGo最新版本安装包，根据操作系统下载相应的安装包，以Windows为例，在Assets中点击picgo-setup-2.1.2.exe下载。本地双机安装到指定的目录。安装完成如下图所示：
![](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190720125229.png)
## GitHub图床
PicGo支持SM.MS图床、腾讯云COS、微博图床、GitHub图床、七牛图床、Imgur图床、阿里云OSS、又拍云等各种图床。各家图床各有利弊，但是最好找一个稳定靠谱点的。虽然不是每一个都研究过，但是也是耳闻一些消息。像微博图床2019/4添加了防盗链，微博图床的老粉几多欢喜几多愁；CDN加速？薅羊毛还是要还的。个人独钟GitHub（主要是免费），当然哪天GitHub不能访问了就告别代码吧。

GitHub图床设置还是蛮简单的，[PicGo官网配置说明](https://picgo.github.io/PicGo-Doc/zh/guide/config.html#github%E5%9B%BE%E5%BA%8A)。Windows点击右下角PicGo小图标打开软件，依次点击__图床设置__->__GitHub图床__，需要配置的有以下三个必须参数：
* 仓库名
* 分支名
* token

仓库名要带github用户名的，如我的配置：__eEricZeng/imgbed__。分支名无特殊需求使用master即可。token稍微麻烦一点，需要__登录GitHub__->__Settings__->__Developer settings__->__Personal access tokens__->__Generate new token__，之后添加__Note__描述，勾选scopes中的repo（包含repo:status、repo_deployment、public_repo、repo:invite四项），点击__Generate token__生成token，复制到PicGo的token表单中，点击确定即可。返回到上传取即可体验PicGo。
![](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190720144537.png)

[__更多更新__](https://eericzeng.github.io)

### 参考资料
1. [PicGo官网](https://molunerfinn.com/PicGo/)
2. [Markdown 图片粘贴工具 PicGo](https://blog.rxliuli.com/p/6138bec/)
