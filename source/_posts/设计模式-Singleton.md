---
title: 设计模式-Singleton Pattern
date: 2019-07-27 20:10:45
categories: 设计模式
tags:
  - 设计模式
  - singleton
  - pattern
---

## 线程安全

四种线程安全的单例实现方式

### 饿汉式

```java
/**
 * 饿汉式单例，在第一次加载类到内存时进行初始化（线程安全）。
 * <p>
 * <strong>缺点：</strong>非懒加载模式，在实例依赖参数或配置文件时无法使用。
 *
 * @author zengguang
 *
 */
public class StarveSingleton {

    /**
     * 第一次类加载时创建实例。
     */
    private static final StarveSingleton INSTANCE = new StarveSingleton();

    /**
     * 构造函数私有，不允许外部创建实例。
     */
    private StarveSingleton() {
    }

    /**
     * 直接返回类加载时创建的实例。
     *
     * @return {@link StarveSingleton}的实例。
     */
    public static StarveSingleton getSingleton() {
        return INSTANCE;
    }
}
```

### 双检锁

```java
/**
 * 双检锁单例模式（懒汉式，线程安全）。
 * <p>
 * <strong>注意:</strong>JDK1.5之前的版本volatile声明不一定起作用。
 *
 * @author zengguang
 * @since JDK1.5
 */
public class DoubleCheckSingleton {

    /**
     * 一定要有<strong>volatile</strong>修饰，否则instance受指令重排的影响会返回一个空对象。
     */
    private volatile static DoubleCheckSingleton instance;

    /**
     * 构造函数私有，不允许外部创建实例。
     */
    private DoubleCheckSingleton() {
    }

    /**
     * 返回一个单例，当第一次调用时才创建一个实例。
     * <p>
     * <code>instance = new DoubleCheckSingleton();</code>并非源自操作，JVM中该行代码做了如下操作：
     * <ol>
     * <li>给instance分配内存</li>
     * <li>调用{@link DoubleCheckSingleton}的构造函数来初始化成员变量</li>
     * <li>将instance对象指向分配的内存空间</li>
     * </ol>
     * JVM在做指令重排时，并不能保证2/3步的顺序，顺序可能是1-2-3或者1-3-2，所以其他线程调用时可能{@link DoubleCheckSingleton}还没有初始化，进而出现NPE的问题。所以在instance变量声明需要加{@code volatile}修饰。
     *
     * @return {@link DoubleCheckSingleton}的单实例。
     */
    public static DoubleCheckSingleton getSingleton() {
        if (null == instance) {
            synchronized (DoubleCheckSingleton.class) {
                if (null == instance) {
                    instance = new DoubleCheckSingleton();
                }
            }
        }
        return instance;
    }
}
```

### 静态内部类

```java
/**
 * 静态内部类单例（懒汉式，线程安全）。
 *
 * @author zengguang
 *
 */
public class NestedClassSingleton {

    /**
     * 构造函数私有，不允许外部创建实例。
     */
    private NestedClassSingleton() {
    }

    /**
     * 静态内部类只有在第一次引用的时候才会被加载。
     *
     * @return {@link NestedClassSingleton}的单实例。
     */
    public static NestedClassSingleton getSingleton() {
        return SingletonHolder.INSTANCE;
    }

    /**
     * 内部类，且为私有的，只有{@link NestedClassSingleton}本身才能访问。
     */
    private static class SingletonHolder {
        private static final NestedClassSingleton INSTANCE = new NestedClassSingleton();
    }
}
```

### 枚举类型

```java
/**
 * 枚举型单例模式（线程安全）。
 *
 * @author zengguang
 * @since JDK1.5
 */
public class EnumSingleton {

    /**
     * 构造函数私有，不允许外部创建实例。
     */
    private EnumSingleton() {
    }

    /**
     *
     * @return {@link EnumSingleton}的单实例
     */
    public static EnumSingleton getSingleton() {
        return Singleton.INSTANCE.getSingleton();
    }

    /**
     * 这种实现方式有点类似静态内部类，而且枚举类型本质还是一个类。
     */
    private enum Singleton {

        INSTANCE(new EnumSingleton());

        private EnumSingleton isntance;

        /**
         * 外部类作为构造参数
         *
         * @param singleton
         *            外部类
         */
        private Singleton(EnumSingleton singleton) {
            this.isntance = singleton;
        }

        /**
         * 枚举类型持有的单实例
         *
         * @return {@link EnumSingleton}的单实例
         */
        private EnumSingleton getSingleton() {
            return isntance;
        }
    }
}
```

## 参考资料

1.  [Java 利用枚举实现单例模式](https://blog.csdn.net/yy254117440/article/details/52305175)
2.  [Java实现单例模式（懒汉式、饿汉式、双重检验锁、静态内部类方式、枚举方式）](https://blog.csdn.net/u011595939/article/details/79972371)
3.  [单例模式有五种写法：懒汉、饿汉、双重检验锁、静态内部类、枚举](https://blog.csdn.net/nsw911439370/article/details/50456231)

## GitHub仓库

[SINGLETON](https://github.com/eEricZeng/demo/tree/pattern-singleton)
