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

4.  `qiao` 和 `song` 共同关注：(集合取交集)

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

   

