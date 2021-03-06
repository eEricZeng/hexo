---
title: 设计模式-Builder Pattern
date: 2019-07-25 22:25:50
categories: 设计模式
tags:
  - 设计模式
  - builder
  - pattern
  - java
---

Builder Pattern：中文译名为“[生成器模式][1]”、“建造模式”。

## 示例

假设一个对象Student：

```java
public class Student {

    private int id;

    private String name;

    private int age;

    private String gender;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

}
```

### 初级编码

不使用builder模式，我们创建一个实例并给实例赋值是这样的：

```java
    Student std = new Student();
    std.setId(10001);
    std.setAge(18);
    std.setName("小明");
    std.setGender("male");
```

### 使用builder模式

重新改造一下Student类：

```java
public class Student {

    private int id;

    private String name;

    private int age;

    private String gender;

    public Student() {
    }

    public Student(int id, String name, int age, String gender) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.gender = gender;
    }

    public static Student.StudentBuilder builder(){
        return new StudentBuilder();
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public static class StudentBuilder {

        private int id;

        private String name;

        private int age;

        private String gender;

        public StudentBuilder id(int id) {
            this.id = id;
            return this;
        }

        public StudentBuilder name(String name) {
            this.name = name;
            return this;
        }

        public StudentBuilder age(int age) {
            this.age = age;
            return this;
        }

        public StudentBuilder gender(String gender) {
            this.gender = gender;
            return this;
        }

        public Student build() {
            return new Student(this.id, this.name, this.age, this.gender);
        }
    }
}
```

使用builder模式后的画风是这个样子的：

```java
  Student std = Student.builder().id(10001).age(18).name("小明").gender("male").build();
```

从此再也不用先new一个实例然后挨个赋值啦，尤其对那些属性超级多的对象，或者创建实例时有些属性是可选的，写起来更是简洁。

### 改造builder模式要点

-   静态内部类
-   静态内部类属性设置方法与属性名相同（建议相同）
-   静态内部类属性设置方法返回内部类实例本身
-   内部静态类需要一个build()方法且返回类型为外部类
-   外部类需要实现一个全参数和无参构造函数
-   外部类需要一个静态builder()方法且返回类型为静态内部类

### Lombok

Lombok提供了@Builder声明式注解，直接可以让POJO拥有builder模式。

## 参考资料：

[1]: https://zh.wikipedia.org/wiki/%E7%94%9F%E6%88%90%E5%99%A8%E6%A8%A1%E5%BC%8F "维基百科"

-   《Head First 设计模式》
-   org.elasticsearch.common.xcontent.XContentBuilder
-   J-IM消息类org.jim.common.packets.Message及子类设计(v2.6.0)
-   [Lombok使用与原理](https://juejin.im/post/5a6eceb8f265da3e467555fe "Lombok使用与原理")

## 更新(2019/8/3)

今天看到几篇Builder模式的博客，对之前的设计进行改进：

-   静态内部类
-   静态内部类属性设置方法与属性名相同（建议相同）
-   静态内部类属性设置方法返回内部类实例本身
-   内部静态类需要一个build()方法且返回类型为外部类
-   ~~外部类需要实现一个全参数和无参构造函数~~
-   外部类需要一个入参为内部类的构造函数
-   ~~外部类需要一个静态builder()方法且返回类型为静态内部类~~

### 改进

```java
public class Student {

    private int id;

    private String name;

    private int age;

    private String gender;

    public Student() {
    }

    public Student(Builder builder) {
        this.id = builder.id;
        this.name = builder.name;
        this.age = builder.age;
        this.gender = builder.gender;
    }

    @Override
    public String toString() {
        return "Student [id=" + id + ", name=" + name + ", age=" + age + ", gender=" + gender + "]";
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public static class Builder {

        private int id;

        private String name;

        private int age;

        private String gender;

        public Builder id(int id) {
            this.id = id;
            return this;
        }

        public Builder name(String name) {
            this.name = name;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public Builder gender(String gender) {
            this.gender = gender;
            return this;
        }

        public Student build() {
            return new Student(this);
        }
    }
}
```

### 使用对比

```java
// 改进前
Student std = Student.builder().id(10001).age(18).name("小明").gender("male").build();
// 改进后
Student std = new Student.Builder().id(10001).age(18).name("小明").gender("male").build();
```

### 讨论
当初想到用builder模式时，是在项目中有些javabean属性特别多，不想中规中矩的一行一行写setter方法。这里没有使用final修饰属性，说明这不是一个可变类，有一个public的无参构造函数，且所有的getter/setter方法都可访问，所以也可以看做是一个JavaBean。

### 参考资料

-   [优雅地创建复杂对象 —— Builder 模式](https://blog.csdn.net/justloveyou_/article/details/78298420 "CSDN")
-   [设计模式之Builder模式](https://www.jianshu.com/p/e2a2fe3555b9 "简书")
