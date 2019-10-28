## 错误：The 'cursor' option is required

Mongo 聚合 Java 的实现，Mongo API 提供了两种实现方式：

1. 基于 MongoTemplate 提供的聚合接口：aggregate(arg1, arg2.....) 等不同参数的方法，按需调用；特点是该 API 封装了 mongo 底层的聚合操作符，比如 $match,$project 等。暴露对应的接口为 project(), match() 等；使用这种方式不会出现标题中的错误。
2. 基于比较底层提供的 DBCollection 提供的 aggregate(arg1, arg2.....) ;该方法要求对 mongo 的 nosql 比较熟悉，因为该接口入参是 DBObject，所以需要我们自己写 mongo 的聚合操作符：$match 等。该接口内部调用 mongo 底层的数据默认的输出格式为 INLINE，所以会出现标题中的错误。

## 解决方法

经过排查，是因为 mongo 服务升级到了 4.2.1，新版本支持事务，需要学习新特性。如果 mongo serve 服务还是 3.5 以下，就不会出现这个问题了。最终解决方法是调用 DBCollection 的聚合接口中有包含聚合配置的参数：AggregateOptions 的接口，然后配置 AggregationOptions 的 outputModel 为 CURSOR 了，代码如下：

```java
List<DBObject> list = Lists.newArrayList();
            DBObject size = new BasicDBObject("size", 10);
            DBObject sample = new BasicDBObject("$sample", size);
            list.add(sample);
            AggregationOptions build = AggregationOptions.builder().outputMode(AggregationOptions.OutputMode.CURSOR).build();
Cursor order = mongoTemplate.getCollection("question").aggregate(list, build);
```

## 源码分析

1、我最开始调用的接口为

```java
/**
 * Method implements aggregation framework.
 *
 * @param pipeline operations to be performed in the aggregation pipeline
 * @return the aggregation's result set
 * @mongodb.driver.manual core/aggregation-pipeline/ Aggregation
 * @mongodb.server.release 2.2
 */
public AggregationOutput aggregate(final List<? extends DBObject> pipeline) {
    return aggregate(pipeline, getReadPreference());
}
```

2、进入 aggregate 方法

```java
/**
 * Method implements aggregation framework.
 *
 * @param pipeline       operations to be performed in the aggregation pipeline
 * @param readPreference the read preference specifying where to run the query
 * @return the aggregation's result set
 * @mongodb.driver.manual core/aggregation-pipeline/ Aggregation
 * @mongodb.server.release 2.2
 */
@SuppressWarnings("unchecked")
public AggregationOutput aggregate(final List<? extends DBObject> pipeline, final ReadPreference readPreference) {
    Cursor cursor = aggregate(pipeline, AggregationOptions.builder().outputMode(INLINE).build(), readPreference, false);

    if (cursor == null) {
        return new AggregationOutput(Collections.<DBObject>emptyList());
    } else {
        List<DBObject> results = new ArrayList<DBObject>();
        while (cursor.hasNext()) {
            results.add(cursor.next());
        }
        return new AggregationOutput(results);
    }
}
```

可以看到第一行，指定 outputModel 类型为 INLINE

3、有提供可以配置 AggregationOptions 的接口，如下：

```java
/**
 * Method implements aggregation framework.
 *
 * @param pipeline operations to be performed in the aggregation pipeline
 * @param options  options to apply to the aggregation
 * @return the aggregation operation's result set
 * @mongodb.driver.manual core/aggregation-pipeline/ Aggregation
 * @mongodb.server.release 2.2
 */
public Cursor aggregate(final List<? extends DBObject> pipeline, final AggregationOptions options) {
    return aggregate(pipeline, options, getReadPreference());
}
```

配置 options 的 outputModel 为 CURSOR 即可解决问题；返回的是 Cursot 对象，该对象是 Iterator 的子类，所以获取数据的方法是通过迭代器获取。