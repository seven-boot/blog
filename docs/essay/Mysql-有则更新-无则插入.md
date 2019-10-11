> 想插入数据库一条记录，如果这条记录的主键或者Unique键已存在，则更新这条记录，如果主键或Unique键不存在，则新增这条记录。

### 使用ON DUPLICATE KEY UPDATE

```sql
INSERT INTO user_allowance ( userId, allowance, usedAllowance ) VALUES ( '1231', 100, 0 ) 
ON DUPLICATE KEY UPDATE allowance = #{allowance}
```

