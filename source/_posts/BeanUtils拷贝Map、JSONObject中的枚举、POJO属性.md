---
title: BeanUtils拷贝Map、JSONObject中的枚举、POJO属性
categories: java
tags:
  - beanutils
  - copyProperties
  - ClassCastException
  - java
  - 拷贝属性
date: 2019-08-26 22:49:14
---


## 版本说明

-   commons-beanutils：1.9.3
-   fastjson：1.2.58

## 场景

使用BeanUtils.copyProperties()拷贝Map/JSONObject时，无法正常拷贝属性类型为枚举或POJO。此时，需要使用ConvertUtils注册一个自定义的转换类。

## 实验

### 实验准备:

1.  Student类（拷贝对象），Student中有三个属性name/sex/grade分别为String/enum/POJO类型（包含属性比较方法propertiesEquals()）；
2.  Sex类，枚举类型，定义了FEMAL/MALE；
3.  Grade类，简单对象，包含三个属性。

定义如下：

```java
/**
 * 学生类，属性中有一个枚举类型、一个POJO。
 *
 * @author zengguang
 *
 */
public class Student {

    /**
     * 姓名
     */
    private String name;

    /**
     * 性别，enum
     */
    private Sex sex;

    /**
     * 成绩，POJO
     */
    private Grade grade;

    public Student() {
    }

    public Student(String name, Sex sex, Grade grade) {
        this.name = name;
        this.sex = sex;
        this.grade = grade;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Sex getSex() {
        return sex;
    }

    public void setSex(Sex sex) {
        this.sex = sex;
    }

    public Grade getGrade() {
        return grade;
    }

    public void setGrade(Grade grade) {
        this.grade = grade;
    }

}
/**
 * 成绩。
 *
 * @author zengguang
 *
 */
public class Grade {
    private Integer mathematics;
    private Integer chinese;
    private Integer english;

    public Grade() {
    }

    public Grade(Integer mathematics, Integer chinese, Integer english) {
        super();
        this.mathematics = mathematics;
        this.chinese = chinese;
        this.english = english;
    }

    public Integer getMathematics() {
        return mathematics;
    }

    public void setMathematics(Integer mathematics) {
        this.mathematics = mathematics;
    }

    public Integer getChinese() {
        return chinese;
    }

    public void setChinese(Integer chinese) {
        this.chinese = chinese;
    }

    public Integer getEnglish() {
        return english;
    }

    public void setEnglish(Integer english) {
        this.english = english;
    }

}

/**
 * 枚举类，性别。
 *
 * @author zengguang
 *
 */
public enum Sex {
    MALE, FEMAL;
}
```

### 实验对比

-   Java对象之间的拷贝
-   BeanMap拷贝到Java对象
-   JSONObject对象拷贝到Java对象
-   HashMap对象拷贝到Java对象

### 测试代码

```java
Grade g = new Grade(100, 99, 98);
Student origBean = new Student("小明", Sex.MALE, g);
Student destBean = new Student();
// JavaBean → JavaBean
try {
    BeanUtils.copyProperties(destBean, origBean);
} catch (IllegalAccessException | InvocationTargetException e) {
    e.printStackTrace();
}
validate(destBean, origBean, "JavaBean → JavaBean");
// BeanMap → JavaBean BeanMap/BeanUtils同为beanutils包中的类
BeanMap destBeanMap = new BeanMap(origBean);
try {
    BeanUtils.copyProperties(destBean, destBeanMap);
} catch (IllegalAccessException | InvocationTargetException e) {
    e.printStackTrace();
}
validate(destBeanMap, origBean, "BeanMap → JavaBean");
// Map → JavaBean
String json = JSONObject.toJSONString(origBean);
JSONObject obj = JSONObject.parseObject(json);
ConvertUtils.register(new Converter() {
    public <T> T convert(Class<T> clazz, Object value) {
        // 应对json中枚举类型被转为字符串的问题
        if (value instanceof String) {
            return clazz.cast(Sex.valueOf((String) value));
        }
        return clazz.cast(value);
    }
}, Sex.class);
ConvertUtils.register(new Converter() {
    public <T> T convert(Class<T> clazz, Object value) {
        Grade g = new Grade();
        try {
            BeanUtils.copyProperties(g, value);
        } catch (IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
        return clazz.cast(g);
    }
}, Grade.class);
try {
    BeanUtils.copyProperties(destBean, obj);
} catch (IllegalAccessException | InvocationTargetException e) {
    e.printStackTrace();
}
// 为方便比较，orig与dest反过来计算
validate(obj, destBean, "JSONOjbect → JavaBean");
Map<?, ?> m = JSONObject.toJavaObject(obj, Map.class);
Map<?, ?> map = new HashMap<>(m);
try {
    BeanUtils.copyProperties(destBean, map);
} catch (IllegalAccessException | InvocationTargetException e) {
    e.printStackTrace();
}
validate(map, destBean, "HashMap → JavaBean");
```

### 完整代码

> github: https://github.com/eEricZeng/demo/tree/beanutils

```java
package io.github.eericzeng;

import java.lang.reflect.InvocationTargetException;
import java.util.HashMap;
import java.util.Map;

import org.apache.commons.beanutils.BeanMap;
import org.apache.commons.beanutils.BeanUtils;
import org.apache.commons.beanutils.ConvertUtils;
import org.apache.commons.beanutils.Converter;

import com.alibaba.fastjson.JSONObject;

/**
 * 使用{@link BeanUtils#copyProperties}拷贝Map/Json中的Enum和POJO。
 *
 * @author zengguang 2019年8月24日
 *
 */
public class BeanUtilsTest {

    public static void main(String[] args) {
        BeanUtilsTest beanUtilsTest = new BeanUtilsTest();
        beanUtilsTest.copyProperties();
    }

    /**
     * 校验属性值是否相等。
     *
     * @param dest
     *            拷贝目标对象
     * @param orig
     *            拷贝源对象
     * @param label
     *            标签
     */
    private void validate(Object dest, Student orig, String label) {
        if (orig.propertiesEquals(dest)) {
            System.out.println(label + ": properties copy success !");
        } else {
            System.out.println(label + ": properties copy fail !");
        }
    }

    /**
     * 正确的拷贝方法。
     */
    private void copyProperties() {
        Grade g = new Grade(100, 99, 98);
        Student origBean = new Student("小明", Sex.MALE, g);
        Student destBean = new Student();
        // JavaBean → JavaBean
        try {
            BeanUtils.copyProperties(destBean, origBean);
        } catch (IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
        validate(destBean, origBean, "JavaBean → JavaBean");
        // BeanMap → JavaBean BeanMap/BeanUtils同为beanutils包中的类
        BeanMap destBeanMap = new BeanMap(origBean);
        try {
            BeanUtils.copyProperties(destBean, destBeanMap);
        } catch (IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
        validate(destBeanMap, origBean, "BeanMap → JavaBean");
        // Map → JavaBean
        String json = JSONObject.toJSONString(origBean);
        JSONObject obj = JSONObject.parseObject(json);
        ConvertUtils.register(new Converter() {
            public <T> T convert(Class<T> clazz, Object value) {
                // 应对json中枚举类型被转为字符串的问题
                if (value instanceof String) {
                    return clazz.cast(Sex.valueOf((String) value));
                }
                return clazz.cast(value);
            }
        }, Sex.class);
        ConvertUtils.register(new Converter() {
            public <T> T convert(Class<T> clazz, Object value) {
                Grade g = new Grade();
                try {
                    BeanUtils.copyProperties(g, value);
                } catch (IllegalAccessException | InvocationTargetException e) {
                    e.printStackTrace();
                }
                return clazz.cast(g);
            }
        }, Grade.class);
        try {
            BeanUtils.copyProperties(destBean, obj);
        } catch (IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
        // 为方便比较，orig与dest反过来计算
        validate(obj, destBean, "JSONOjbect → JavaBean");
        Map<?, ?> m = JSONObject.toJavaObject(obj, Map.class);
        Map<?, ?> map = new HashMap<>(m);
        try {
            BeanUtils.copyProperties(destBean, map);
        } catch (IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
        validate(map, destBean, "HashMap → JavaBean");
    }

    /**
     * 学生类，属性中有一个枚举类型、一个POJO。
     *
     * @author zengguang
     *
     */
    public class Student {

        /**
         * 姓名
         */
        private String name;

        /**
         * 性别，enum
         */
        private Sex sex;

        /**
         * 成绩，POJO
         */
        private Grade grade;

        public Student() {
        }

        public Student(String name, Sex sex, Grade grade) {
            this.name = name;
            this.sex = sex;
            this.grade = grade;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Sex getSex() {
            return sex;
        }

        public void setSex(Sex sex) {
            this.sex = sex;
        }

        public Grade getGrade() {
            return grade;
        }

        public void setGrade(Grade grade) {
            this.grade = grade;
        }

        public boolean propertiesEquals(Object o) {
            if (this == o)
                return true;
            if (null == o)
                return false;
            if (o instanceof Student) {
                Student s = (Student) o;
                if (!this.name.equals(s.name))
                    return false;
                if (!this.sex.equals(s.sex))
                    return false;
                if (!this.grade.propertiesEquals(s.grade))
                    return false;
                return true;
            } else if (o instanceof Map) {
                Map<?, ?> m = (Map<?, ?>) o;
                if (!this.name.equals(m.get("name")))
                    return false;
                Object obj = m.get("sex");
                Sex s = m.get("sex") instanceof String ? Sex.valueOf((String) obj) : (Sex) obj;
                if (!this.sex.equals(s))
                    return false;
                if (!this.grade.propertiesEquals(m.get("grade")))
                    return false;
                return true;
            }
            return false;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", sex=" + sex + ", grade=" + grade + "]";
        }

    }

    /**
     * 成绩。
     *
     * @author zengguang
     *
     */
    public class Grade {
        private Integer mathematics;
        private Integer chinese;
        private Integer english;

        public Grade() {
        }

        public Grade(Integer mathematics, Integer chinese, Integer english) {
            super();
            this.mathematics = mathematics;
            this.chinese = chinese;
            this.english = english;
        }

        public Integer getMathematics() {
            return mathematics;
        }

        public void setMathematics(Integer mathematics) {
            this.mathematics = mathematics;
        }

        public Integer getChinese() {
            return chinese;
        }

        public void setChinese(Integer chinese) {
            this.chinese = chinese;
        }

        public Integer getEnglish() {
            return english;
        }

        public void setEnglish(Integer english) {
            this.english = english;
        }

        public boolean propertiesEquals(Object o) {
            if (this == o)
                return true;
            if (null == o)
                return false;
            if (o instanceof Grade) {
                Grade g = (Grade) o;
                if (!this.mathematics.equals(g.mathematics))
                    return false;
                if (!this.chinese.equals(g.chinese))
                    return false;
                if (!this.english.equals(g.english))
                    return false;
                return true;
            } else if (o instanceof Map) {
                Map<?, ?> m = (Map<?, ?>) o;
                if (null == m.get("mathematics"))
                    return false;
                if (null == m.get("chinese"))
                    return false;
                if (null == m.get("english"))
                    return false;
                Object om = m.get("mathematics") instanceof String ? Integer.valueOf((String) m.get("mathematics"))
                        : m.get("mathematics");
                Object oc = m.get("chinese") instanceof String ? Integer.valueOf((String) m.get("chinese"))
                        : m.get("chinese");
                Object oe = m.get("english") instanceof String ? Integer.valueOf((String) m.get("english"))
                        : m.get("english");
                if (!this.mathematics.equals(om))
                    return false;
                if (!this.chinese.equals(oc))
                    return false;
                if (!this.english.equals(oe))
                    return false;
                return true;
            }
            return false;
        }

        @Override
        public String toString() {
            return "Grade [mathematics=" + mathematics + ", chinese=" + chinese + ", english=" + english + "]";
        }

    }

    /**
     * 枚举类，性别。
     *
     * @author zengguang
     *
     */
    public enum Sex {
        MALE, FEMAL;
    }
}
```

## 题外话

BeanUtils性能堪忧，可参考某大佬写的对比博客，见参考资料。

## 参考资料

1.  [BeanCopy类库](https://www.jianshu.com/p/9a136ecd3838 "BeanCopy类库")
