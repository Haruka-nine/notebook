# 概述

## 介绍

GSON是Google提供的用来在Java对象和JSON数据之间进行映射的Java类库。可以将一个Json字符转成一个Java对象，或者将一个Java转化为Json字符串

相对于FastJson,Gson面对复杂的Json更准确，但对性能要求更高

**fastJson是按照属性的名字来转换的**

## 引入

```XML
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.8.5</version>
    </dependency>
```



# 使用

## 对象

Gson进行反序列化操作时，是必须设置bean的，如果我们没有建立对应的实体类，我们就需要使用其提供的实体类

JsonObject.class或者JsonArray

这两个类都是JsonElement的子类，

JsonElement是Gson数据类型的基础类，用来表示Json中的数据类型。其子类有对象(JsonObject)，数组（JsonArray），空数据（JsonNull）， JsonPrimitive(基本数据类型 byte/short/char/boolean/int/long/double/float/number/string) 用于标识各种数据类型


具体的两个实现类的方法这里不进行描述，可以自己调试观看或者参考FastJson的两个类的方法，很相似



## 操作



### 转换普通对象

简单序列化

```Java
    Person p = new Person("艾伦·耶格尔", 16, true, Arrays.asList("自由", "迫害莱纳"));
    // 创建Gson对象
    Gson gson = new Gson();
    // 调用Gson的String toJson(Object)方法将Bean转换为json字符串
    String pJson = gson.toJson(p);
```



简单反序列化

```Java
Person person = gson.fromJson(pJson, Person.class);
System.out.println(person);
```



### 转换list对象



list序列化

```Java
    List<Person> list = new ArrayList<Person>();
    list.add(new Person("三笠·阿克曼", 16, false, Arrays.asList("砍巨人", "保护艾伦")));
    list.add(new Person("阿明·阿诺德", 16, true, Arrays.asList("看书", "玩海螺")));
    System.out.println(list);
    // 创建Gson实例
    Gson gson = new Gson();
    // 调用Gson的toJson方法
    String listJson = gson.toJson(list);
```



list反序列化

这里想要使用``List<person>``的泛型，但是直接写``List<person>.class``就会变成list的泛型

这种现象就是泛型擦除

所以需要使用TypeToken

```Java
List<Person> plist = gson.fromJson(listJson, new TypeToken<List<Person>>(){}.getType());
System.out.println(plist);
```



### 转换Map对象

```Java
    Map<String, Person> map = new HashMap<>();
    map.put("p1", new Person("利威尔·阿克曼", 35, true, Arrays.asList("砍猴儿", "打扫卫生")));
    map.put("p2", new Person("韩吉·佐耶", 33, false, Arrays.asList("研究巨人", "讲故事")));

    Gson gson = new Gson();
    String mapJson = gson.toJson(map);

    System.out.println(mapJson);
    // {"p1":{"name":"利威尔·阿克曼","age":35,"isMale":true,"hobbies":["砍猴儿","打扫卫生"]},"p2":{"name":"韩吉·佐耶","age":33,"isMale":false,"hobbies":["研究巨人","讲故事"]}}
    Map<String, Person> jsonMap = gson.fromJson(mapJson, new TypeToken<Map<String, Person>>() { }.getType());
    System.out.println(jsonMap);
```



