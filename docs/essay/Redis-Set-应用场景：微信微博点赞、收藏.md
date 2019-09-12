## 1、点赞

```sql
SADD like:{消息ID} {用户ID}

> sadd like:100 1000
(integer) 1
> sadd like:100 1001
(integer) 1
> sadd like:100 1002
(integer) 1
```

## 2、取消点赞

```sql
SREM like:{消息ID} {用户ID}

> srem like:100 1001
1
```

## 3、检查用户是否点过赞

```sql
SISMEMBER like:{消息ID} {用户ID}

> sismember like:100 1001
(integer) 0
> sismember like:100 1000
(integer) 1
```

## 4、获取点赞的用户列表

```sql
SMEMBERS like:{消息ID}

> smembers like:100
1) "1000"
2) "1002"
```

## 5、获取点赞用户数

```sql
SCARD like:{消息ID}

> scard like:100
2
```



