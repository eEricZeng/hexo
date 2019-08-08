---
title: fastjson自带JsonPath与json-path比较
categories: java
tags:
  - fastjson
  - jsonpath
  - java
date: 2019-08-07 20:57:07
---

## 场景

fastjson从1.2版本开始支持JsonPath语法，但是在部分情境下还是无法很好的支持。例如下面的json结构：

```json
{
	"store": {
		"book": [{
			"title": "Java虚拟机",
			"price": 20
		}, {
			"title": "Head First 设计模式",
			"price": 72
		}, {
			"title": "重构：改善既有代码的设计",
			"isbn": "123",
			"price": 38
		}, {
			"title": "Spring实践4",
			"isbn": "321",
			"price": 32
		}, {
			"title": "图解HTTP",
			"isbn": "543",
			"price": 25
		}],
		"bicycle": {
			"color": "red",
			"price": 219
		}
	}
}
```

需要取出book中的title和price字段，理想情况是这样的：

```json
[{
	"title": "Java虚拟机",
	"price": 20
}, {
	"title": "Head First 设计模式",
	"price": 72
}, {
	"title": "重构：改善既有代码的设计",
	"price": 38
}, {
	"title": "Spring实践4",
	"price": 32
}, {
	"title": "图解HTTP",
	"price": 25
}]
```

## 测试

引入fastjson/json-path版本如下：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.58</version>
</dependency>
<dependency>
    <groupId>com.jayway.jsonpath</groupId>
    <artifactId>json-path</artifactId>
    <version>2.4.0</version>
</dependency>
```

java代码：

```java
import com.alibaba.fastjson.JSONPath;
import com.jayway.jsonpath.JsonPath;

public class JsonPathTest {

    public static void main(String[] args) {
        String json = "{\"store\": {\"book\": [{\"title\": \"Java虚拟机\",\"price\": 20}, {\"title\": \"Head First 设计模式\",\"price\": 72}, {\"title\": \"重构：改善既有代码的设计\",\"isbn\": \"123\",\"price\": 38}, {\"title\": \"Spring实践4\",\"isbn\": \"321\",\"price\": 32}, {\"title\": \"图解HTTP\",\"isbn\": \"543\",\"price\": 25}],\"bicycle\": {\"color\": \"red\",\"price\": 219}}}";

        String jsonPath = "$['store']['book'][:-1]['price','title']";
        Object obj = JSONPath.read(json, jsonPath);
        System.out.println(obj);

        jsonPath = "$.store.book.[*].['title', 'price']";
        obj = JsonPath.parse(json).read(jsonPath);
        String str = JSONObject.toJSONString(obj);
        System.out.println(str);
    }
}
```

测试结果：

    [[20,72,38,32,25], ["Java虚拟机","Head First 设计模式","重构：改善既有代码的设计","Spring实践4","图解HTTP"]]
    [{"title":"Java虚拟机","price":20},{"title":"Head First 设计模式","price":72},{"title":"重构：改善既有代码的设计","price":38},{"title":"Spring实践4","price":32},{"title":"图解HTTP","price":25}]

从结果上看json-path可以实现json带list结构的结果筛选，而fastjson只提取了两个列表（也可能我的语法不对，但是以目前的版本，我查了很多资料都没有找到一个更好的语法实现，如果有请联系我）。

## 参考资料

1.  [fastjson对JSONPath的官方教程](https://github.com/alibaba/fastjson/wiki/JSONPath)
2.  [Jayway JsonPath](https://github.com/json-path/JsonPath)
3.  [GitRepo](https://github.com/eEricZeng/demo/tree/jsonpath "https&#x3A;//github.com/eEricZeng/demo/tree/jsonpath")
