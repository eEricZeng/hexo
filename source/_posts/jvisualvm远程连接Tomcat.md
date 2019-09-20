---
title: jvisualvm远程连接Tomcat
categories: java
tags:
  - java
  - jvm
  - jvisualvm
  - jmx
  - jstatd
date: 2019-09-20 19:03:45
---


jvisualvm远程连接有两种方式：jmx和jstatd。

## JMX

需要在服务器上修改Tomcat的启动参数，打开$TOMCAT_HOME/bin/catalina.sh，在文件中添加如下参数：

```bash
JAVA_OPTS="$JAVA_OPTS
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=8777
-Dcom.sun.management.jmxremote.rmi.port=8777
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
-Djava.rmi.server.hostname=192.168.1.110"
```

其中，192.168.1.110是部署Tomcat的服务器地址。然后我们在本地启动jvisualvm，右击导航栏的“远程”->"添加远程主机"，输入远程主机IP——以“192.168.1.110”为例，右击远程主机“192.168.1.110”->“添加JMX连接”，输入端口号8777，勾选不要求SSL连接，这里没有使用安全凭证，因此也不需要勾选安全凭证，点击“确定”进行连接。

**然而**，如果JMX没有缺点也就不需要使用第二种方法了。JMX无法使用VisualGC插件，这个真的无法忍受，只能用下面这种方法进行连接。

## jstatd

jstatd需要JDK1.8，低版本的可能没有这个工具。运行jstatd命令需要开放权限，创建安全策略文件如下：

```bash
cd $TOMCAT_HOME/
touch jstatd.all.policy
vim jstatd.all.policy
```

添加如下内容：

    grant codebase "file:${java.home}/../lib/tools.jar" {
        permission java.security.AllPermission;
    };

运行jstatd工具：

    jstatd -J-Djava.security.policy=jstatd.all.policy -J-Djava.rmi.server.hostanme=192.168.1.110 &

以上默认端口是1099，若需要修改可以添加-p参数进行制定。

好了，可以本地打开jvisualvm远程连接一下192.168.1.110，VisualGC可以正常使用，神奇的是CPU监控显示“**不受此JVM支持**”（手动愣住(°ー°〃)），这就是为什么会有两种方法同时存在？！！！
