### Stream 类的类结构图

![1571044966637](assets/1571044966637.png)

- 中间操作主要有以下方法（此类型方法返回的都是 Stream）：map（mapToInt、flatMap等），filter、distinct、sorted、peek、limit、skip、parallel、sequential、unordered
- 终止操作主要有以下方法：foreach、foreachOrdered、toArray、reduce、collect、min、max、count、anyMatch、allMatch、noneMatch、findFirst、findAny、iterator

### filter（筛选）

```java
// 筛选年龄大于15岁的学生
students.stream.filter(s -> s.getAge() > 15).collect(Collectors.toList());
// 筛选住在浙江省的学生
students.stream.filter(s -> "浙江".equals(s.getAddress)).collect(Collectors.toList());
```

### map（转换）

map 就是将对应的元素按照给定的方法进行转换。

```java
// 在地址前面加上部分信息，只获取地址输出
List<String> addresses = students.stream.map(s -> "地址:" + s.getAddress()).collect(Collectors.toList());
```

### distinct（去重）

```java
// 简单字符串去重
List<String> list = ...
list.stream().distinct();
```

### sorted（排序）

```java
// 简单集合
list.stream().sorted()
// 指定排序规则
students.stream().sorted((s1, s2) -> Long.compare(s1.getId(), s2.getId()))
    .sorted((s1, s2) -> Integer.compare(s1.getAge(), s2.getAge()))
```

### limit（限制返回个数）

```java
list.stream().limit(2)
```

### skip（删除元素）

```java
list.stream().skip(2)
```

### reduce（聚合）

reduce 接受两个参数，一个初始值，一个`BinaryOperator<T> accumulator`来将两个元素结合起来产生一个新值

```java
list.stream().reduce("北京", (a, b) -> a + b)
```

### min（求最小值）

min 获取流中最小值，max 获取流中最大值，方法参数为`Comparator<? super T> comparator`

```java
students.stream().min((s1, s2) -> Integer.compare(s1.getAge(), s2.getAge())).get();
// max （求最大值）同

Optional<BigDecimal> min = invoiceList.stream().map(Invoice::getAmount).min(BigDecimal::compareTo);
Optional<BigDecimal> max = invoiceList.stream().map(Invoice::getAmount).max(BigDecimal::compareTo);
// 也可以写成
OptionalInt min1 = invoiceList.stream().mapToInt(Invoice::getDetailSize).min();
OptionalInt max1 = invoiceList.stream().mapToInt(Invoice::getDetailSize).max();

// 通过minBy/maxBy获取最大最小值
invoiceList.stream().map(Invoice::getAmount).collect(Collectors.minBy(BigDecimal::compareTo)).get();
// 通过reduce 获取最大最小值
Optional<BigDecimal> max = invoiceList.stream().map(Invoice::getAmount).reduce(BigDecimal::max);
```

### anyMatch/allMatch/noneMatch（匹配）

```java
// 是否有湖北人
Boolean anyMatch = students.stream().anyMatch(s -> "湖北".equals(s.getAddress()));
// 是否所有学生都满15周岁
Boolean allMatch = students.stream().allMatch(s -> s.getAge() >= 15);
// 没有叫杨洋的人
Boolean noneMatch = students.stream().noneMatch(s -> "杨洋".equals(s.getName()));
```

- anyMatch：Stream 中任意一个元素符合传入的 predicate，返回 true
- allMatch：Stream 中全部元素符合传入的 predicate，返回 true
- noneMatch：Stream 中没有一个元素符合传入的 predicate，返回 true

### 综合实例：

#### 发票 Model：

```java
@Builder
@Data
public class Invoice implements Serializable {
    /**
     * 销方名称
     */
    private String saleName;
    /**
     * 是否作废
     */
    private Boolean cancelFlag;
    /**
     * 开票金额
     */
    private BigDecimal amount;
    /**
     * 发票类型
     */
    private Integer type;
    /**
     * 明细条数
     */
    private Integer detailSize;
}
```

#### Java 8 之前的实现方式

```java
@Test
public void testJava7() {
    ArrayList<Invoice> lowInvoiceList = new ArrayList<>();
    //筛选出 金额小于 10000 的发票
    for (Invoice invoice: invoiceList) {
        if (invoice.getAmount().compareTo(BigDecimal.valueOf(10000)) < 0) {
            lowInvoiceList.add(invoice);
        }
    }
    // 对筛选出的发票排序
    lowInvoiceList.sort(new Comparator<Invoice>() {
        @Override
        public int compare(Invoice o1, Invoice o2) {
            return o1.getAmount().compareTo(o2.getAmount());
        }
    });
    // 获取排序后的销方名字
    ArrayList<String> nameList = new ArrayList<>();
    for (Invoice invoice : lowInvoiceList) {
        nameList.add(invoice.getSaleName());
    }

}
```

#### Stream()

```java
// 筛选出金额小于1000的发票，根据金额排序，获取排序后的销方名称列表
List<String> nameList = invoiceList.stream()
    .filter(item -> item.getAmount().compareTo(BigDecimal.valueOf(1000)) < 0)// 过滤数据
    .sorted(Comparator.comparing(Invoice::getAmount))// 对金额升序排序
    .map(Invoice::getSaleName)// 提取名称
    .collect(Collectors.toList);// 转换成list
```

> 对查询出来的发票数据进行分类，返回一个 Map<Integer, List> 的数据

#### Java 7 的写法

```java
@Test
public void testGroupByTypeJava7() {
  HashMap<Integer, List<Invoice>> groupMap = new HashMap<>();
  for (Invoice invoice : invoiceList) {
    //存在则追加
    if (groupMap.containsKey(invoice.getType())) {
      groupMap.get(invoice.getType()).add(invoice);
    } else {
      // 不存在则初始化添加
      ArrayList<Invoice> invoices = new ArrayList<>();
      invoices.add(invoice);
      groupMap.put(invoice.getType(), invoices);
    }
  }
  System.out.println(groupMap.toString());
}
```

#### Stream() groupingBy 分组

```java
@Test
public void testGroupByTypeJava8() {
  Map<Integer, List<Invoice>> groupByTypeMap = invoiceList.stream().collect(Collectors.groupingBy(Invoice::getType));
}

// 还可以通过嵌套使用 groupingBy 进行多级分类
// 首先根据发票类型分组，再根据开票金额大小分组，返回的数据类型是Map<String, Map<String, List>>
Map<String, Map<String, List<RzInvoice>>> = invoiceList.stream().collect(Collectors.groupingBy(Invoice::getType, Collectors.groupingBy(invoice -> {
    if (invoice.getAmount().compareTo(BigDecimal.valueOf(1000)) <= 0) {
        return "low";
    } else if (invoice.getAmount().compareTo(BigDecimal.valueOf(8000)) <= 0) {
        return "mi";
    } else {
        return "high";
    }
})));
```

#### 进阶通过 partitioningBy 进行分区

特殊的分组，它分类依据是 true 和 false，所以返回的结果最多可以分为两组

```java
Map<Boolean, List<Dish>> = invoiceList.stream().collect(Collectors.partitioningBy(RzInvoice::getCancelFlag));
// 等同于
Map<Boolean, List<Dish>> = invoiceList.stream().collect(Collectors.groupingBy(RzInvoice::getCancelFlag));

// 上面的例子可能并不能看出分区和分类的区别，换个例子
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
Map<Boolean, List<Integer>> result = list.stream().collect(partitioningBy(i -> i < 3));
// 返回值得键依然是布尔类型，但是它得分类是根据范围进行分类的，分区比较适合处理根据范围进行分类
```

#### 求和

```java
// reduce 求和
int sum = integerList.stream.reduce(0, Integer::sum);
// 通过 summingInt 求和
Integer sum = invoiceList.stream().collect(Collectors.summingInt(Invoice::getDetailSize));
// 如果数据类型为double、long，则通过 summingDouble、summingLong 方法进行求和
Integer sum = invoiceList.stream().map(Invoice::getDetailSize).reduce(0, Integer::sum);
// 通过sum，最佳写法,推荐：mapToInt将对象转换成数值流，避免了装箱和拆箱操作
Integer sum = invoiceList.stream().mapToInt(Invoice::getDetailSize).sum();

// 统计发票金额
BigDecimal reduce = invoiceList.stream().map(Invoice::getAmount).reduce(BigDecimal.ZERO, BigDecimal::add);
// 注意：以下这种方式会造成精度丢失
BigDecimal.valueOf(invoiceList.stream().mapToDouble(Invoice::getAmount).sum())
```

#### 通过 averagingInt 求平均值

```java
Double avg = invoiceList.stream().collect(Collectors.averagingInt(Invoice::getDetailSize));
// 如果数据类型为double、long，则通过avaragingDouble、averagingLong 方法进行求平均

// 对于 BigDecimal 则需要先求和再除以总条数
List<BigDecimal> sumList = invoiceList.stream().map(Invoice::getAmount).collect(Collectiors.toList());
BigDecimal average = average(sumList, RoudingMode.HALF_UP);
// 求平均值
public BigDecimal average(List<BigDecimal> bigDecimals, RoudingMode roudingMode) {
	BigDecimal sum = bigDecimals.stream().map(Objects::requireNonNull)
					.reduce(BigDecimal.ZERO, BigDecimal::add);
	return sum.divide(new BigDecimal(bigDecimals.size()), roudingMode);
}
```

#### 通过 summarizingInt 同时求总和、平均值、最大值、最小值

```java
IntSummaryStatistics statistics = invoiceList.stream().collect(Collectors.summarizingInt(Invoice::getDetailSize));
double average = statistics.getAverage();
int max = statistics.getMax();
int min = statistics.getMin();
long sum = statistics.getSum();
```

#### 通过 joining 拼接流中的元素

```java
String result = invoiceList.stream().map(Invoice::getSaleName).collect(Collectors.joining(", "));
```

### 复杂示例：

```java
// 过滤T-1至T-12 进12个月数据，根据省份分组求和开票金额，使用金额进行倒序，产生LinkedHashMap
LinkedHashMap<String, BigDecimal> areaSortByAmountMaps = invoiceStatisticsList.stream()
    // 根据时间过滤数据
    .filter(FilterSaleInvoiceUtil.filterSaleInvoiceWithRange(1, 12, analysisDate))
	// 根据开票地区分组，并同时将每个分组数据的开票金额求和   .collect(Collectors.groupingBy(FkSalesInvoiceStatisticsDO::getBuyerAdministrativeAreaCode, Collectors.reduce(BigDecimal.ZERO, FkSaleInvoiceStatisticsDO::getInvoiceAmount, BigDecimal::add)))
    // 根据金额大小倒序
    .entrySet().stream().sorted(Map.Entry<String, BigDecimal>.comparingByValue().reversed())
    // 收集数据生成LinkedHashMap
    .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
```

