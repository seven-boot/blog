## 单值缓存：

```sql
// 字符串常用操作
SET key value				// 存入字符串键值对
SETNX key value				// 存入一个不存在的字符串键值对(只有在key不存在时设置key的值)

// 原子加减
INCR key		// 将key中存储的数字值加1
DECR key		// 将key中存储的数字值减1
INCRBY key increment	// 将key中存储的值加上increment
DECRBY key decrement	// 将key中存储的值减去decrement
```

## 对象缓存：

```sql
SET user:1 value(json格式数据)
MSET user:1:name zhuge user:1:balance 1888
MGET user:1:name user:1:balance

> MSET user:1:name zhuge user:1:balance 1888
OK
> MGET user:1:name user:1:balance
1) "zhuge"
2) "1888"
> MGET user:1:name
) "zhuge"
```

## setnx 举例：

```cmd
> set hello true
OK
> get hello
"true"
> setnx hello false
(integer) 0
> get hello
"true"
```

## setnx 实现简单分布式锁：

```cmd
SETNX product:10001 true		// 返回1代表获取锁成功
SERNX product:10001 true		// 返回0代表获取锁失败
。。。执行业务操作	
DEL product:10001				// 执行完业务释放锁

SET product:10001 true ex 10 nx // 设置失效时间防止程序意外终止导致死锁
```

（缺陷：某进程想要获得锁，需要一直去查询redis是否能获得锁，即：锁失效不能通知到下一个进程，参考zookeeper）

## INCR 实现计数器：

```sql
INCR article:readcount:{文章id}		// 文章阅读数+1
GET article:readcount:{文章id}
```

