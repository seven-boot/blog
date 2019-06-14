> Joda Time ，一个面向 Java 平台的易于使用的开源时间\日期库
>
> 依赖引入（gradle）：
>
> ```xml
> // https://mvnrepository.com/artifact/joda-time/joda-time
> compile group: 'joda-time', name: 'joda-time', version: '2.10.2'
> ```
>
> Maven：
>
> ```xml
> <!-- https://mvnrepository.com/artifact/joda-time/joda-time -->
> <dependency>
>     <groupId>joda-time</groupId>
>     <artifactId>joda-time</artifactId>
>     <version>2.10.2</version>
> </dependency>
> ```

## Joda-Time 介绍

Joda-Time 令时间和日期值变得易于管理、操作和理解。事实上，易于使用是 Joda 的主要设计目标。其他目标包括可扩展性、完整的特性集以及对多种日历系统的支持。
并且 Joda 与 JDK 是百分之百可互操作的，因此您无需替换所有 Java 代码，只需要替换执行日期/时间计算的那部分代码。
Joda-Time提供了一组Java类包用于处理包括ISO8601标准在内的date和time。可以利用它把JDK Date和Calendar类完全替换掉，而且仍然能够提供很好的集成。

## 为什么要使用 Joda ？

考虑创建一个用时间表示的某个随意的时刻 — 比如，2000 年 1 月 1 日 0 时 0 分。
我如何创建一个用时间表示这个瞬间的 JDK 对象？使用 java.util.Date？
事实上这是行不通的，因为自 JDK 1.1 之后的每个 Java 版本的 Javadoc 都声明应当使用 java.util.Calendar。
Date 中不赞成使用的构造函数的数量严重限制了您创建此类对象的途径。

那么 Calendar 又如何呢？我将使用下面的方式创建必需的实例：

```java
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.JULY, 14, 0, 0, 0);
System.out.println(calendar.getTime());

Sun Jul 14 00:00:00 CST 2019
```

使用 joda：

```java
DateTime dateTime = new DateTime(2019, 6, 14, 0, 0, 0);
System.out.println(dateTime);

2019-06-14T00:00:00.000+08:00
```

在这个日期上加上 90 天并输出结果:

```java
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.JULY, 14, 0, 0, 0);
SimpleDateFormat sdf = new SimpleDateFormat("E yyyy-MM-dd HH:mm:ss.SSS");
calendar.add(Calendar.DAY_OF_MONTH, 90);
System.out.println(sdf.format(calendar.getTime()));

星期四 2019-09-12 00:00:00.000
```

使用 Joda 的方式：

```java
DateTime dateTime = new DateTime(2019, 6, 14, 0, 0, 0);
System.out.println(dateTime.plusDays(90).toString("E yyyy-MM-dd HH:mm:ss.SSS"));

星期四 2019-09-12 00:00:00.000
```

输出这样一个日期：某个日期5天之后的下一个月的当前周的最后一天的日期

使用 JDK 太麻烦，所以直接使用 joda：

```java
DateTime dateTime = new DateTime(2019, 6, 14, 0, 0, 0);     
System.out.println(dateTime.plusDays(5).plusMonths(1).dayOfWeek()
	.withMaximumValue().toString("E yyyy-MM-dd HH:mm:ss.SSS"));

星期日 2019-07-21 00:00:00.000
```

## 创建 Joda-Time 对象

## ReadableInstant

> Joda 通过 ReadableInstant 类实现了瞬时性这一概念。表示时间上的不可变瞬间的 Joda 类都属于这个类的子类（DateTime 和 DateMidnight）。

* **DateTime**

  这是最常用的一个类。它以毫秒级的精度封装时间上的某个瞬间。

  DateTime 始终与 DateTimeZone 相关，如果不指定，它将被默认设置为运行代码的机器所在的时区。

```java
DateTime dateTime = new DateTime();

DateTime dateTime = new DateTime(new Date());

DateTime dateTime = new DateTime(calendar);

// 字符串必须是一个经过精确格式化过的值
String timeStr = "2019-06-14";
dateTime = new DateTime(timeStr);
```

* **DateMidnight**

  

  这个类封装某个时区（通常为默认时区）在特定年/月/日的午夜时分的时刻。

  它基本上类似于 DateTime，不同之处在于时间部分总是与改对象关联的特定 DateTimeZone 时区的午夜时分。

### ReadablePartial

> 应用程序所需处理的日期问题并不全部都与时间上的某个完整时刻有关，因此您可以处理一个局部时刻。
>
> 例如：有时您比较关心的年/月/日，或者一天中的时间，甚至是一周中的某天。Joda 设计者使用 ReadablePartial 接口捕捉这种表示局部时间的概念。
>
> 这是一个不可变的局部时间片段。用于处理这种时间片段的有用类分别为 LocalDate 和 LocalTime.

* **LocalDate**

  该类封装了一个年/月/日的组合。当地理位置（即时区）变得不重要时，使用它存储日期将非常方便。
  例如，某个特定对象的出生日期 可能为 1999 年 4 月 16 日，但是从技术角度来看，
  在保存所有业务值的同时不会了解有关此日期的任何其他信息（比如这是一周中的星期几，或者这个人出生地所在的时区）。
  在这种情况下，应当使用 LocalDate。

* **LocalTime**

  这个类封装一天中的某个时间，当地理位置不重要的情况下，可以使用这个类来只存储一天当中的某个时间。
  例如，晚上 11:52 可能是一天当中的一个重要时刻（比如，一个 cron 任务将启动，它将备份文件系统的某个部分），
  但是这个时间并没有特定于某一天，因此我不需要了解有关这一时刻的其他信息。

```java
DateTime dt1 = new DateTime();
System.out.println(dt1);

DateTime dt2 = new DateTime(new Date());
System.out.println(dt2);

// 当天日期 2019-06-14
LocalDate localDate = LocalDate.now();

LocalDate ld1 = new LocalDate(2019, 6, 14);
System.out.println(ld1);

LocalTime lt1 = new LocalTime(14, 42, 40, 0);
System.out.println(lt1);

2019-06-14T14:43:14.927+08:00
2019-06-14T14:43:14.966+08:00
2019-06-14
14:42:40.000
```

## 与 JDK 日期对象转换

许多代码都使用了 JDK Date 和 Calendar 类。但是幸亏有 Joda，可以执行任何必要的日期算法，然后再转换回 JDK 类。
这将两者的优点集中到一起。您在本文中看到的所有 Joda 类都可以从 JDK Calendar 或 Date 创建，正如您在 创建 JodaTime对象 中看到的那样。

```java
DateTime dt1 = new DateTime();
// 转换成 java.util.Date 对象
Date d1 = dt1.toDate();
Date d2 = new Date(dt1.getMillis());
System.out.println(d1);
System.out.println(d2);
// 转换成 java.util.Calendat 对象
Calendar c1 = Calendar.getInstance();
c1.setTimeInMillis(dt1.getMillis());
Calendar c2 = dt1.toCalendar(Locale.getDefault());
System.out.println(c1.getTime());
System.out.println(c2.getTime());

Fri Jun 14 14:57:17 CST 2019
Fri Jun 14 14:57:17 CST 2019
Fri Jun 14 14:57:17 CST 2019
Fri Jun 14 14:57:17 CST 2019
```

对于 ReadablePartial 子类：

```java
LocalDate ld1 = new LocalDate(2019, 6, 14);
Date d3 = ld1.toDateTimeAtStartOfDay().toDate();
System.out.println(d3);

Fri Jun 14 00:00:00 CST 2019
```

要创建 Date 对象，您必须首先将它转换为一个 DateMidnight 对象，然后只需要将 DateMidnight 对象作为 Date。
（当然，产生的 Date 对象将把它自己的时间部分设置为午夜时刻）。
JDK 互操作性被内置到 Joda API 中，因此您无需全部替换自己的接口，如果它们被绑定到 JDK 的话。比
如，您可以使用 Joda 完成复杂的部分，然后使用 JDK 处理接口。

## 日期计算

假设在当前的系统日期下，我希望计算上一个月的最后一天：

```java
LocalDate localDate = new LocalDate();
LocalDate lastDayOfPreviousMonth = localDate.minusMonths(1).dayOfMonth().withMaximumValue();
System.out.println(lastDayOfPreviousMonth);

2019-05-31
```

首先，我从当前月份减去一个月，得到 “上一个月”。
接着，我要求获得 dayOfMonth 的最大值，它使我得到这个月的最后一天。
注意，这些调用被连接到一起（注意 Joda ReadableInstant 子类是不可变的），这样您只需要捕捉调用链中最后一个方法的结果，从而获得整个计算的结果。

您可能对dayOfMonth() 调用感兴趣。这在 Joda 中被称为属性（property）。它相当于 Java对象的属性。
属性是根据所表示的常见结构命名的，并且它被用于访问这个结构，用于完成计算目的。
属性是实现 Joda 计算威力的关键。您目前所见到的所有 4 个 Joda 类都具有这样的属性。一些例子包括：
**yearOfCentury**
**dayOfYear**
**monthOfYear**
**dayOfMonth**
**dayOfWeek**

获得任何一年中的第 11 月的第一个星期二的日期，而这天必须是在这个月的第一个星期一之后：

```java
LocalDate localDate = LocalDate.now(); // 2019-06-14
LocalDate electionDate = localDate.monthOfYear()
    .setCopy(11)
    .dayOfMonth()
    .withMinimumValue()
    .plusDays(6)
    .dayOfWeek()
    .setCopy(DayOfWeek.MONDAY.getValue())
    .plusDays(1);
System.out.println(electionDate);

2019-11-05
在每月的开始再加上 6 天就能够让您得到第一个星期一。再加上一天就得到第一个星期二
```

```java
DateTime dateTime = new DateTime();
// 昨天
DateTime yesterday = dateTime.minusDays(1);
// 明天
DateTime tomorrow = dateTime.plusDays(1);
// 上一个月
DateTime before1Month = dateTime.minusMonths(1);
// 三个月之后
DateTime after3Month = dateTime.plusMonths(3);
// 两年前
DateTime before2Year = dateTime.minusYears(2);
// 五年后
DateTime after5Year = dateTime.plusYears(5);
```

## 格式化时间

```java
DateTime dateTime = DateTime.now();
System.out.println(dateTime.toString("yyyy-MM-dd HH:mm:ss"));

2019-06-14 16:25:55
```

