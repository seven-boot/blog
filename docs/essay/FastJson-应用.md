## 字符串转对象

```java
JSON.parseObject(String text, Class<? extends Object> clazz);
```

## 对象转 Json 字符串

```java
JSON.toJSONString(Object obj);

map 转 json字符串:
String str = JSON.toJSONString(map.get("key"));
```

## JSONObject转对象

```java
JSONObject jsonObject = new JSONObject();
jsonObject.getObject(String key, Class<? extends Object > clazz);

Answers{
    ObjectId id;
    String name;
}
Answers answer = jsonObject.getObject("answers", Answers.class);
```

