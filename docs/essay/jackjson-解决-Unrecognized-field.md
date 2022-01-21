解析JSON 串转成Java实体对象时，出现了`Unrecognized field, not marked as ignorable` 错误。该错误的意思是说，不能够识别的字段没有标示为可忽略。出现该问题的原因就是JSON中包含了目标Java对象没有的属性。

解决方法有如下几种：

1. 格式化输入内容，保证传入的JSON串不包含目标对象的没有的属性。
2. `@JsonIgnoreProperties(ignoreUnknown = true)` 在目标对象的类级别上加上该注解，并配置`ignoreUnknown = true`，则Jackson在反序列化的时候，会忽略该目标对象不存在的属性。
3. 全局DeserializationFeature配置 
   `objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES,false);`配置该objectMapper在反序列化时，忽略目标对象没有的属性。凡是使用该`objectMapper`反序列化时，都会拥有该特性。

https://blog.csdn.net/duan196_118/article/details/117388877