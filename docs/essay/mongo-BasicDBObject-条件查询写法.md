```java
//链接数据库
MongoClient mongoClient = new MongoClient( "172.26.xxx.xxx" , 27017 );

MongoDatabase mongoDatabase =mongoClient.getDatabase("xxxx");

MongoCollection<Document> collection = mongoDatabase.getCollection("test_logs");

//加入查询条件
BasicDBObject query = new BasicDBObject();
//时间区间查询 记住如果想根据这种形式进行时间的区间查询 ，存储的时候 记得把字段存成字符串，就按yyyy-MM-dd HH:mm:ss 格式来
query.put("times", new BasicDBObject("$gte", "2018-06-02 12:20:00").append("$lte","2018-07-04 10:02:46"));
//模糊查询
Pattern pattern = Pattern.compile("^.*王.*$", Pattern.CASE_INSENSITIVE);
query.put("userName", pattern);
//精确查询
query.put("id", "11");
//skip 是分页查询，从第0条开始查10条数据。 Sorts是排序用的。有descending 和ascending
MongoCursor<Document> cursor = collection.find(query).sort(Sorts.orderBy(Sorts.descending("times"))).skip(0).limit(10).iterator();//
int unm=0;
try {
    while (cursor.hasNext()) {
        UserBehaviorLogs userBehaviorLogs = new UserBehaviorLogs();
        //查询出的结果转换成jsonObject,然后进行封装或者直接返回给前端处理。我这是封装成对象了
        JSONObject jsonObject = JSONObject.parseObject( cursor.next().toJson().toString());
        userBehaviorLogs.setId(jsonObject.getString("id"));//id
        userBehaviorLogs.setUserId(jsonObject.getString("userId"));//用户id
        userBehaviorLogs.setUserName(jsonObject.getString("userName"));//用户名称
        userBehaviorLogs.setParams(jsonObject.getString("params"));//参数
        userBehaviorLogs.setException(jsonObject.getString("Exception"));//异常信息
        userBehaviorLogs.setTimes(jsonObject.getString("times")+"");//创建时间
        unm++;
        System.out.println(unm+"="+userBehaviorLogs.getTimes()+"==="+userBehaviorLogs.getId());
    }
} catch (Exception e) {
    e.printStackTrace();
} finally {
    cursor.close();
}
```

