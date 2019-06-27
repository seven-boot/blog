官方解释：

```text
InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference.
```

按照效率来比较：

count(*) = count(1) > count(primary key) > count(column)

