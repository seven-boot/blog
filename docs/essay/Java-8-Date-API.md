Java 8 在包 java.time 下包含了一组全新的时间日期 API。新的日期 API 和 开源的 Joda-Time 库差不多，但又不完全一样：

## Clock（时钟）

Clock 类提供了访问当前日期和时间的方法，Clock 是时区敏感的，可以用来取代 System.currentTimeMillis() 来获取当前的微秒数。某一个特定的时间点也可以使用 instant 类来表示，Instant 类也可以用来创建老的 java.util.Date 对象。

```java

```

获取当天日期

```java
LocalDate date = LocalDate.now();

2019-10-30
```

构造指定日期

```java
LocalDate date = LocalDate.of(2019, 10, 30);

2019-10-30
```

获取年月日信息

```java
LocalDate date = LocalDate.now();	
System.out.println(date.getYear());			2019
System.out.println(date.getMonthValue());	10
System.out.println(date.getDayOfMonth());	30
```

比较两个日期是否相等

```java
LocalDate now = LocalDate.now();
LocalDate date = LocalDate.of(2019, 10, 30);
System.out.println(now.equals(date));

true
```

获取当天时间（只有时间，不包含日期）

```java
LocalTime now = LocalTime.now();

14:41:15.264
```

时间计算

```java
LocalTime now = LocalTime.now();	14:43:15.264
LocalTime time = now.plusHours(5);
19:43:05.328
    
LocalDate now = LocalDate.now();	2019-10-30
LocalDate date = now.plus(1, ChronoUnit.WEEKS);
2019-11-06
date.isAfter(now)	true
date.isBefore(now)  false
```

时区

```java
ZonedDateTime shanghaiTime = ZonedDateTime.now(ZoneId.of("Asia/Shanghai"));
java.time.format.DateTimeFormatter formatter = java.time.format.DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
System.out.println(shanghaiTime.format(formatter));
2019-10-30 14:51:54
    
ZonedDateTime tokyoTime = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
java.time.format.DateTimeFormatter formatter = java.time.format.DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
System.out.println(tokyoTime.format(formatter));
2019-10-30 15:53:06
```

格式化

```java
// 解析日期
String dateText = "20191030";
LocalDate date = LocalDate.parse(dateText, DateTimeFormatter.BASIC_ISO_DATE);
2019-10-30
// 格式化日期
LocalDate now = LocalDate.now();
String s = now.format(DateTimeFormatter.ISO_DATE);
2019-10-30
```

日期和字符串的相互转换

```java
// 日期时间转字符串
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime now = LocalDateTime.now();
String nowText = now.format(formatter);
2019-10-30 15:59:10
    
String dateText = "2019-10-30 15:39:19";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime dateTime = LocalDateTime.parse(dateText, formatter);
System.out.println(dateTime);
2019-10-30T15:39:19
```

相关类说明

```java
Instant         时间戳
Duration        持续时间、时间差
LocalDate       只包含日期，比如：2018-09-24
LocalTime       只包含时间，比如：10:32:10
LocalDateTime   包含日期和时间，比如：2018-09-24 10:32:10
Peroid          时间段
ZoneOffset      时区偏移量，比如：+8:00
ZonedDateTime   带时区的日期时间
Clock           时钟，可用于获取当前时间戳
java.time.format.DateTimeFormatter      时间格式化类
```

## LocalDate获取月初月末

```java
//获取月初
LocalDate monthOfFirstDate=LocalDate.parse(count_date,
                DateTimeFormatter.ofPattern("yyyy-MM-dd")).with(TemporalAdjusters.firstDayOfMonth());
//获取月末
LocalDate monthOfLastDate=LocalDate.parse(count_date,
                DateTimeFormatter.ofPattern("yyyy-MM-dd")).with(TemporalAdjusters.lastDayOfMonth());
```

