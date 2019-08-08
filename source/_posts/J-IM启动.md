---
title: J-IM启动
categories: 即时通讯
tags:
  - 即时通讯
  - IM
  - J-IM
  - t-io
  - java
date: 2019-08-05 21:05:55
---

> 开源社区：[Gitee](https://gitee.com/xchao/j-im "https&#x3A;//gitee.com/xchao/j-im")

下载J-IM源码，目前（2019.8.1）最新版本（v2.6.0)的J-IM没有上传到Maven仓库中，需要自己打包。作为一个Java开发人员，我相信你本地安装了Maven而且配置了环境变量。下载源码后进入jim-parent文件夹执行如下命令：

```cmd
mvn clean package -U -Dmaven.test.skip=true
# Maven命令解释: 打包并跳过测试类的编译
```

或者直接双击jim-parent/install.bat，该脚本实际执行如下命令：

```cmd
call mvn clean install
# 该命令会将打好的包存放到本地Maven仓库里，请悉知
```

J-IM提供了简单的client/server端演示Demo，当然本文主要关心的是服务端如何启动。该Demo项目名为jim-server-demo，进入该项目的target目录找到刚刚打好的jar包，如：jim-server-demo-2.6.0v20190114-RELEASE.jar。

## 启动

jar包启动的通常做法是执行如下命令：

```sh
java -jar jim-server-demo-2.6.0v20190114-RELEASE.jar
```

但是执行后会报如下异常：

```sh
jim-server-demo-2.6.0.v20190114-RELEASE.jar中没有主清单属性
```

打开jar包里的META-INF/MANIFEST.MF文件会发现缺失启动类。解决办法是在pom里添加插件：

### jar/dependency插件

配置如下：

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.6</version>
    <configuration>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <classpathPrefix>lib/</classpathPrefix>
                <mainClass>org.jim.server.demo.ImServerDemoStart</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>2.10</version>
    <executions>
        <execution>
            <id>copy-dependencies</id>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <outputDirectory>${project.build.directory}/lib</outputDirectory>
            </configuration>
        </execution>
    </executions>
</plugin>
```

再次启动就可以啦：

```sh
java -jar jim-server-demo-2.6.0.v20190114-RELEASE.jar
```

这样使用有个限制，拷贝jar包时需要带lib文件夹。

### assembly插件

配置如下：

```xml
<plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <configuration>
        <archive>
            <manifest>
            <mainClass>org.jim.server.demo.ImServerDemoStart</mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <!--下面是为了使用 mvn package命令，如果不加则使用mvn assembly-->
    <executions>
        <execution>
            <id>make-assemble</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

mvn打包后生成jim-server-demo-2.6.0.v20190114-RELEASE.jar和jim-server-demo-2.6.0.v20190114-RELEASE-jar-with-dependencies.jar两个jar包，后面那个包将依赖的jar一起打包，任何位置直接使用java -jar即可。

## IDE启动

直接启动main方法。

## SpringBoot集成

J-IM启动类继承org.springframework.boot.CommandLineRunner接口，实现run()方法:

```java
public class JimStarter implements CommandLineRunner {

    @Override
    public void run(String... args) {
      // J-IM启动代码
    }
}
```

## 参考资料

1.  [jar包中META-INF文件作用](https://blog.csdn.net/liuxiao723846/article/details/79364922 "CSDN")
2.  [java -cp & java jar的区别](https://www.cnblogs.com/klb561/p/10513575.html)
