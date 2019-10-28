Java 8 在包 java.time 下包含了一组全新的时间日期 API。新的日期 API 和 开源的 Joda-Time 库差不多，但又不完全一样：

## Clock（时钟）

Clock 类提供了访问当前日期和时间的方法，Clock 是时区敏感的，可以用来取代 System.currentTimeMillis() 来获取当前的微秒数。某一个特定的时间点也可以使用 instant 类来表示，Instant 类也可以用来创建老的 java.util.Date 对象。

```java

```

