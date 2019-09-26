## Mongo 格式：

```sql
{
_id:5d858c287e3b0a8518a2d712
question:"问题问题问题问题问题问题问题1"
answers:{
	0:
		_id:5d858c287e3b0a8518a2d582
		name:"答案答案答案1"
		right:false
	1:
		_id:5d858c287e3b0a8518a2d583
		name:"答案答案答案2"
		right:true
	2:
		_id:5d858c287e3b0a8518a2d584
		name:"答案答案答案3"
		right:false
	3:
		_id:5d858c287e3b0a8518a2d585
		name:"答案答案答案4"
		right:false
	}
}
	。。。
```

## 正常写法：

```java
Query query = new Query();
query.addCriteria(Criteria.where("_id").is(new ObjectId("5d858c287e3b0a8518a2d712")).and("answers._id").is("5d858c287e3b0a8518a2d582"));
mongoTemplate.find(query, AnswerModel.class);
```

但是这种写法不能满足，会将符合查询要求的整行数据全部返回，包含所有的子集。

## Aggregation 写法：

```java
@Test
public void aggregation() {
    AnswerModel answerModel = new AnswerModel();
    // 封装对象列表查询条件
    List<AggregationOperation> operations = Lists.newArrayList();
    // 指定查询主文档
    MatchOperation match = Aggregation.match(Criteria.where("_id").is(new ObjectId("5d858c287e3b0a8518a2d712")));
    operations.add(match);
    // 指定投影，返回哪些字段
    ProjectionOperation projection = Aggregation.project("answers");
    operations.add(projection);
    // 拆分内嵌文档
    UnwindOperation unwind = Aggregation.unwind("answers");
    operations.add(unwind);
    // 指定查询子文档
    MatchOperation match2 = Aggregation.match(Criteria.where("answers._id").is(new ObjectId("5d858c287e3b0a8518a2d583")));
    operations.add(match2);
    // 创建管道查询对象
    Aggregation aggregation = Aggregation.newAggregation(operations);
    AggregationResults<JSONObject> results = mongoTemplate.aggregate(aggregation, "question", JSONObject.class);
    List<JSONObject> mappedResults = results.getMappedResults();
    if (mappedResults != null && mappedResults.size() > 0) {
        answerModel = JSONObject.parseObject(mappedResults.get(0).getJSONObject("answers").toJSONString(), AnswerModel.class);
    }
    System.out.println(answerModel);
}
```

