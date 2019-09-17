## Set 常用操作：

```sql
SADD key member[member ...]		// 往集合key中存入元素，元素存在则忽略，若key不存在则新建
SREM key member[member ...]		// 从集合key中删除元素
SMEMBERS key	// 获取集合key中的所有元素
SCARD key	// 获取集合key的元素个数
SISMEMBER key member	// 判断member元素是否存在于集合key中
SRANDMEMBER key [count]		// 从集合key中选出count元素，元素不从key中删除
SPOP key [count]	// 从集合key中选出count元素，元素从key中删除
```

## 应用场景：

### 微信抽奖小程序

```sql
1.点击参与抽奖加入集合
SADD key {userId}
SADD act:1001 10001		// 用户10001参与活动1001
2.查看参与抽奖所有用户
SMEMBERS key
3.抽取count名中奖者
SRANDMEMBER key [count]		// 从集合key中选出count元素，元素不从key中删除
SPOP key [count]	// 从集合key中选出count元素，元素从key中删除
```

