---
title: kettle插件开发pom配置
categories: kettle
tags:
  - ETL
  - kettle
  - kettle插件
  - pom
  - maven plugin
date: 2019-10-21 22:42:03
---

## 背景介绍

看过网上很多介绍kettle插件开发的博文，在真正开发之前存在很多繁琐的工作，例如：将kettle工具中的lib包安装到本地maven仓库。通常会执行如下操作：

1.  打开kettle/data-integration/lib目录，执行如下命令

```bash
mvn install:install-file -Dfile=./kettle-core-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=kettle-core -Dversion=6.1.0.1-196 -Dpackaging=jar
mvn install:install-file -Dfile=./kettle-dbdialog-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=kettle-dbdialog -Dversion=6.1.0.1-196 -Dpackaging=jar
mvn install:install-file -Dfile=./kettle-engine-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=kettle-engine -Dversion=6.1.0.1-196 -Dpackaging=jar
mvn install:install-file -Dfile=./kettle-ui-swt-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=kettle-ui-swt -Dversion=6.1.0.1-196 -Dpackaging=jar
mvn install:install-file -Dfile=./pentaho-metadata-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=pentaho-metadata -Dversion=6.1.0.1-196 -Dpackaging=jar
mvn install:install-file -Dfile=./metastore-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=metastore -Dversion=6.1.0.1-196 -Dpackaging=jar
```

2.  打开kettle/data-integration/libswt/win64目录，执行如下命令

```bash
mvn install:install-file -Dfile=./swt-6.1.0.1-196.jar -DgroupId=org.pentaho.di -DartifactId=swt -Dversion=6.1.0.1-196 -Dpackaging=jar
```

执行这一堆命令，我们可能稍微能忍受一下（毕竟都是搬运工嘛，复制粘贴一下就好了）。然而，往往我们自己使用的又不是6.1.0.1-196这个版本，或者kettle升级了，或者本地仓库重置了，亦或接手这个项目的后来者（比如我），我们这个脚本还要手动替换一下。

为了实现插件开发流程的简化，结合已有的maven插件对pom文件进行了简单的修改，毕竟maven的天职就是对依赖包的统一管理嘛！！！

## pom配置

在打开IDE之前，需要配置一下kettle的系统环境变量KETTLE_HOME，这个环境变量会在pom中使用。举个例子，在windows系统中，KETTLE_HOME可以配置为D:\\Program Files\\kettle71，该目录为kettle的安装目录，其文件结构如下：

```bash
.
└── kettle71
    └── data-integration
        ├── classes
        │       └── ...
        ├── lib
        │       └── *.jar
        ├── libswt
        │   ├── linux
        │   │   ├── x86
        │   │   │   └── swt.jar
        │   │   └── x86_64
        │   │       └── swt.jar
        │   ├── osx64
        │   │   └── swt.jar
        │   ├── win32
        │   │   └── swt.jar
        │   └── win64
        │       └── swt.jar
       ....
```

打开IDE或直接打开maven管理的kettle插件工程，修改pom.xml文件：

1.  properties标签配置

```xml
    <properties>
        <kettle.version>7.1.0.0-12</kettle.version>
        <kettle.home.lib>${env.KETTLE_HOME}/data-integration/lib</kettle.home.lib>
        <kettle.home.libswt>${env.KETTLE_HOME}/data-integration/libswt/win64</kettle.home.libswt>
    </properties>
```

2.  plugin标签配置
    添加maven-install-plugin插件：

```xml
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-install-plugin</artifactId>
        <version>2.4</version>
        <executions>
            <execution>
                <id>kettle-core</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>kettle-core</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\kettle-core-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>kettle-dbdialog</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>kettle-dbdialog</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\kettle-dbdialog-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>kettle-engine</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>kettle-engine</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\kettle-engine-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>kettle-ui-swt</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>kettle-ui-swt</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\kettle-ui-swt-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>pentaho-metadata</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>pentaho-metadata</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\pentaho-metadata-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>metastore</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>metastore</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.lib}\metastore-${kettle.version}.jar</file>
                </configuration>
            </execution>
            <execution>
                <id>swt</id>
                <goals>
                    <goal>install-file</goal>
                </goals>
                <phase>validate</phase>
                <configuration>
                    <groupId>org.pentaho.di</groupId>
                    <artifactId>swt</artifactId>
                    <version>${kettle.version}</version>
                    <packaging>jar</packaging>
                    <file>${kettle.home.libswt}\swt.jar</file>
                </configuration>
            </execution>
        </executions>
    </plugin>
```

现在，只需要执行mvn validate命令即可将kettle依赖的jar包安装到本地的maven仓库。若更换kettle版本，只需更换properties标签中kettle.version的值即可。当然配置中还是存在依赖系统环境的缺陷，即${kettle.home.libswt}，在windows64中使用win64，若换linux需要将win64改为liunx/x86_64，目前还没找到更好的解决办法，如果有谁知道欢迎[告知](https://eericzeng.github.io)！

## 参考资料

1.  [kettle通用插件\[kettlePlugins\]使用说明](https://my.oschina.net/nivalsoul/blog/1620664 "https&#x3A;//my.oschina.net/nivalsoul/blog/1620664")
2.  [Apache install:install-file](http://maven.apache.org/plugins/maven-install-plugin/install-file-mojo.html "http&#x3A;//maven.apache.org/plugins/maven-install-plugin/install-file-mojo.html")
3.  [Maven安装jar文件到本地仓库](https://www.cnblogs.com/xguo/archive/2013/06/04/3117894.html "https&#x3A;//www.cnblogs.com/xguo/archive/2013/06/04/3117894.html")
4.  [Maven中的几个重要概念(二)：lifecycle, phase and goal](https://blog.csdn.net/OnlyQi/article/details/6801318 "https&#x3A;//blog.csdn.net/OnlyQi/article/details/6801318")
5.  [Maven 生命周期](https://www.jianshu.com/p/fd43b3d0fdb0 "https&#x3A;//www.jianshu.com/p/fd43b3d0fdb0")
