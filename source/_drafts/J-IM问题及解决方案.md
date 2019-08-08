---
title: J-IM问题及解决方案(持续更新)
date:
categories: 即时通讯
tags:
  - 即时通讯
  - IM
  - J-IM
  - t-io
  - java
---

### ConstructorAccessor/FieldAccessor类飘红

IDEA导入项目后，jim-common下的工具类DynamicEnumUtil飘红，如下图所示：
![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190808204132.png)
**原因：** ConstructorAccessor/FieldAccessor是JDK里面的类，我IDEA启动配置的是JDK12，该版本包中可能不带sun公司的包。
**解决办法：** Ctrl+Shift+Alt+S修改Project中的JDK，该为JDK1.8就可以了。

### maven-javadoc-plugin/maven-gpg-plugin not found

查了下pom里配置的maven公共仓库<https://oss.sonatype.org找不到maven插件，尝试换个maven仓库。>
![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190808212436.png)
