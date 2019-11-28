---
title: J-IM问题及解决方案(持续更新)
categories: 即时通讯
tags:
  - 即时通讯
  - IM
  - J-IM
  - t-io
  - java
date: 
---


### ConstructorAccessor/FieldAccessor类飘红

IDEA导入项目后，jim-common下的工具类DynamicEnumUtil飘红，如下图所示：
![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190808204132.png)

**原因：** ConstructorAccessor/FieldAccessor是JDK里面的类，我IDEA启动配置的是JDK12，该版本包中可能不带sun公司的包。

**解决办法：** Ctrl+Shift+Alt+S修改Project中的JDK，该为JDK1.8就可以了。

### maven-javadoc-plugin/maven-gpg-plugin not found

问题如下：
![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190808212436.png)

这两个包一个用于生成javadoc，一个用于给maven构建加密，既然没有上传公共仓库的必要，所以可以暂时将这两个插件注释掉。

### slf4j与logback日志框架冲突

![img](https://raw.githubusercontent.com/eEricZeng/imgbed/master/20190815205451.png)

**解决办法：** 移除slf4j-simple包

修改jim-server-demo的pom：
```xml
<dependency>
	<groupId>org.j-im</groupId>
	<artifactId>jim-server</artifactId>
	<exclusions>
		<exclusion>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-simple</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```
或
修改jim-common的pom：
```xml
<dependency>
	<groupId>net.oschina.j2cache</groupId>
	<artifactId>j2cache-core</artifactId>
	<exclusions>
		<exclusion>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-simple</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

### Empty watch file list. Disabling
jar包启动后，控制台每隔10秒打印如下信息：
```
21:06:23,179 |-INFO in ReconfigureOnChangeTask(born:1565960503172) - Empty watch file list. Disabling
```
这条信息是logback日志框架打印的，解决办法参考[此文](https://blog.csdn.net/wsy2846513/article/details/78897904)。
