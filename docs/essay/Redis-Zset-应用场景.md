## Zset 常用操作

```sql
ZADD key score member [[score member] ...]		// 往有序集合key中加入带分值元素
ZREM key member [member ...]	// 从有序集合key中删除元素
ZSCORE key member		// 返回有序集合key中元素member的分值
ZINCRBY key increment member	// 为有序集合key中元素member的分值加上increment
ZCARD key	// 返回有序集合key中元素个数
ZRANGE key start stop [WITHSCORES] 		// 正序获取有序集合key从start下标到stop下标的元素
ZREVRANGE key start stop [WITHSCORES]		// 倒序获取有序集合key从start下标到stop下标的元素
```

## Zset 集合操作

```sql
ZUNIONSTORE destkey numkeys key [key ...] 	// 并集计算
--Redis Zunionstore 命令计算给定的一个或多个有序集的并集，其中给定 key 的数量必须以 numkeys 参数指定，并将该并集(结果集)储存到 destination 。
--默认情况下，结果集中某个成员的分数值是所有给定集下该成员分数值之和 。
--返回值
--保存到 destination 的结果集的成员数量。

ZINTERSTORE destkey numkeys key [key ...] 	// 交集计算
```

### Zset 集合实现排行榜

```sql
1.点击新闻
ZINCRBY hotNews:20190918 1 今天918
    > ZINCRBY hotNews:20190918 1 今天918
    1.0
    > ZINCRBY hotNews:20190918 1 今天918
    2.0
    > ZINCRBY hotNews:20190918 1 今天918
    3.0
    > ZINCRBY hotNews:20190918 1 今天918
    4.0
    > ZINCRBY hotNews:20190918 1 今天918
    5.0
2.展示当日排行前十
ZREVRANGE hotNews:20190918 0 10 WITHSCORES	// 倒叙获得有序集合hotNews:20190918中的前十条数据
	> ZREVRANGE hotNews:20190918 0 10 WITHSCORES
    1) "今天918"
    2) 5.0
    3) "2022冬奥会吉祥物"
    4) 5.0
    5) "杨丞琳李荣浩领证"
    6) 2.0
    7) "周杰伦新歌评分"
    8) 2.0
3.七日搜索榜单计算
ZUNIONSTORE hotNews:20190912-20190918 7 hotNews:20190912 hotNews:20190913 hotNews:20190914 hotNews:20190915 hotNews:20190916 hotNews:20190917 hotNews:20190918
    >ZUNIONSTORE hotNews:20190917-20190918 2 hotNews:20190917 hotNews:20190918
    "2"
4.展示七日排行前十
ZREVRANGE hotNews:20190912-20190918 0 9 WITHSCORES
    >ZREVRANGE hotNews:20190917-20190918 0 9 WITHSCORES
     1)  "今天918"
     2)  "3"
     3)  "周杰伦新歌评分"
     4)  "1"
```

