## Set 运算操作

```
SINTER key [key ...]	交集运算
SINTERSTORE destination key [key ...]	// 将交集结果存入新集合destination中
SUNION key [key ...]	// 并集运算
SUNIONSTORE destination key [key ...]	// 将并集结果存入新集合destination中
SDIFF key [key ...]	// 差集运算
SDIFFSTORE destination key [key ...]	// 将差集结果存入新集合destination中
```

1. `qiao` 关注的人：

   ```sql
   > sadd qiaoset song li xu
   (integer) 3
   ```

2. `song` 关注的人：

   ```sql
   > sadd songset li sun wu xu
   (integer) 4
   > sadd songset lin
   (integer) 1
   ```

3. `li` 关注的人：

   ```sql
   > sadd liset wu sun chen
   (integer) 3
   ```

4. `qiao` 和 `song` 共同关注：(集合取交集)

   ```sql
   > sinter songset qiaoset
   1) "xu"
   2) "li"
   ```

5. 我`qiao` 关注的人也关注他`li`

   ```sql
   > sismember songset li
   (integer) 1
   > sismember xuset li
   (integer) 0
   ```

6. 我`qiao ` 可能认识的人(找出我关注的人的关注列表里我没有关注的人，其实就是求差集)

   ```sql
   > sdiff songset qiaoset
   1) "wu"
   2) "sun"
   3) "lin"
   ```

   

