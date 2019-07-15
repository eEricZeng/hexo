---
title: hexo博客系统分类和标签的使用
date: 2019-07-14 17:45:53
categories: hexo
tags:
  - hexo
  - tags
  - categories
  - next
---
## 版本说明
* hexo v3.9.0
* hexo-theme-next v7.1.2

## 写在前面
以next主题为例，修改主题的配置文件themes/next/_config.yml，生效分类和标签：
```yml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  # sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```
新创建的hexo博客没有分类和标签，点击分类或标签会提示"Cannot GET /categories/"和"Cannot GET /tags/"。
## 分类
进入hexo博客所在的根目录，输入如下命令：
```bash
hexo new page categories
```
成功后提示：
```bash
INFO  Created: HEXO_ROOT\source\categories\index.md
```
index.md的内容如下：
```md
---
title: categories
date: 2019-07-14 17:51:10
---
```
这个index.md可以认为是存放分类的一个文档，只是type不同。修改为如下内容：
```md
---
title: 分类
date: 2019-07-14 17:51:10
type: "categories"
---
```
找到source/_posts路径下的markdown文章，并修改其categories属性。以hexo自带的hexo-world.md为例，文件头修改为：
```md
---
title: Hello World
date: 2019-06-28 22:36:00
categories: hexo
---
```
此时，点击菜单栏中的“分类”，即可看到新建的“hexo”分类。
## 标签
标签创建与分类类似，在控制台中输入如下命令：
```bash
hexo new page tags
INFO  Created: HEXO_ROOT\source\tags\index.md
```
tags/index.md内容与分类类似，修改为如下内容：
```md
---
title: 标签
date: 2019-07-14 18:19:31
type: "tags"
---
```
同样在hello-world.md增加标签内容：
```md
---
title: Hello World
date: 2019-06-28 22:36:00
categories: hexo
tags:
  - hexo
  - others
---
```
