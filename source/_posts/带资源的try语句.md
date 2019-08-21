---
title: 带资源的try语句
categories: java
tags:
  - java
  - try
date: 2019-08-21 21:02:59
---


## 语法

带资源的try语句，英文try-with-resources，JDK1.7及之后有效：

```java
    try(/**
         * 实现了java.io.Closeable接口的资源定义。
         */) {
      // 业务代码
    }
```

示例：

```java
    try (BufferedReader br = new BufferedReader(new FileReader("text.txt"))){
      // 业务代码
    } catch (IOException e) {
        e.printStackTrace();
    }
```

## 与不带资源try的对比

如果只有一个资源使用try-with-resources并无太大优势，try/catch/finally则更简洁一点。但是当多个资源同时打开时，就方便太多了，对比如下：

**try-with-resources**

```java
    try (BufferedReader br1 = new BufferedReader(new FileReader("text1.txt"));
        BufferedReader br2 = new BufferedReader(new FileReader("text2.txt"))) {
        // 业务代码
    } catch (IOException e) {
        e.printStackTrace();
    }
```

**JDK1.7之前**

```java
    BufferedReader br1 = null;
    BufferedReader br2 = null;
    try {
        br1 = new BufferedReader(new FileReader("text1.txt"));
        br2 = new BufferedReader(new FileReader("text2.txt"));
        // 业务代码
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (null != br1) {
                br1.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (null != br2) {
                    br2.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

**注意：**JDK1.7之前正确关闭的资源是finally和try互相嵌套才能正确关闭，下面这种是错误的：

```java
    BufferedReader br1 = null;
    BufferedReader br2 = null;
    try {
        br1 = new BufferedReader(new FileReader("text1.txt"));
        br2 = new BufferedReader(new FileReader("text2.txt"));
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (null != br1) {
                br1.close();  // br1执行close()出现异常，br2就无法关闭了
            }
            if (null != br2) {
                br2.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

## 常见异常

之前开发时经常出现一个异常提示如下：

```log
    java.io.IOException: 您的主机中的软件中止了一个已建立的连接
```

后来发现调用的框架工具中未正确关闭资源，导致连续请求时触发该异常。

## 参考资料

1.  [The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html "https&#x3A;//docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html")
