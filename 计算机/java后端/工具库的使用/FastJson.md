# 概念

## 简介

阿里官方给的定义是， fastjson 是阿里巴巴的开源JSON解析库，它可以解析 JSON 格式的字符串，支持将 Java Bean 序列化为 JSON 字符串，也可以从 JSON 字符串反序列化到 JavaBean。

## 优点

- **速度快**
fastjson相对其他JSON库的特点是快，从2011年fastjson发布1.1.x版本之后，其性能从未被其他Java实现的JSON库超越。

- **使用广泛**
fastjson在阿里巴巴大规模使用，在数万台服务器上部署，fastjson在业界被广泛接受。在2012年被开源中国评选为最受欢迎的国产开源软件之一。

- **测试完备**
fastjson有非常多的testcase，在1.2.11版本中，testcase超过3321个。每次发布都会进行回归测试，保证质量稳定。

- **使用简单**
fastjson的 API 十分简洁。

- **功能完备**
支持泛型，支持流处理超大文本，支持枚举，支持序列化和反序列化扩展

**但在进行复杂的反序列化时，不如Gson精确**

## 使用

你可以通过如下地方下载fastjson:

- maven中央仓库: [http://central.maven.org/maven2/com/alibaba/fastjson/](http://central.maven.org/maven2/com/alibaba/fastjson/)

- Sourceforge.net : [https://sourceforge.net/projects/fastjson/files/](https://sourceforge.net/projects/fastjson/files/)

- 在maven项目的pom文件中直接配置fastjson依赖

fastjson最新版本都会发布到maven中央仓库，你可以直接依赖。



```
<dependency>
     <groupId>com.alibaba</groupId>
     <artifactId>fastjson</artifactId>
     <version>1.2.62</version>
</dependency>
```



# 操作

## 原理

fastjson的使用主要是三个对象:

- JSON

- JSONObject

- JSONArray



JSONArray和JSONObject继承JSON



## 主要API

### JSON对象

JSON这个类主要用于转换：

- 将java对象转换为JSON字符串

- 将JSON字符串反序列化成java对象

- `JSON.parseObject(String text, Class<T> clazz)`

- `JSON.parseArray(String text, Class<T> clazz)`

- `JSON.toJSONString(Object object)`

---

**parseObject和parseArray如果不填入class就会转成默认的JSONObject和JSONArray对象，填入就会转成对应的javabean**

---



```Java
package com.alibaba.fastjson;
public abstract class JSON {
      // Java对象转换为JSON字符串
      public static final String toJSONString(Object object);
      //JSON字符串转换为Java对象
      public static final <T> T parseObject(String text, Class<T> clazz, Feature... features);
}

```



```Java
//序列化
//将对象转化为JSON字符串
String jsonString = JSON.toJSONString(obj);

//反序列化
//将JSON字符串转化为相应的对象
VO vo = JSON.parseObject("...", VO.class);
```



JSON还有一个JSON.parse方法，如果填入class，就和JSON.parseObject一样，转为javabean。

但如果不传入对应的class，就会生成Object,不能使用JSONObject的方法，使用时可能需要强转为对应的Bean。



### JSONObject

JSON对象(JSONObject)中的数据都是以`key-value`形式出现，所以它实现了`Map`接口：

使用起来也很简单，跟使用`Map`就没多大的区别（因为它底层实际上就是操作`Map`)，常用的方法：

- `getString(String key)`

- `remove(Object key)`

如果内部嵌套一个列表json,可以在上一步后使用`JSON.parseArray(String text, Class<T> clazz)`

也可以在这里直接使用方法

- `getJSONArray(String key)`



### JSONArray

JSONArray则是JSON数组，JSON数组对象中存储的是一个个JSON对象，所以类中的方法主要用于**直接操作JSON对象**

最常用的方法：

- `getJSONObject(int index)` 返回的是JSONObject对象

