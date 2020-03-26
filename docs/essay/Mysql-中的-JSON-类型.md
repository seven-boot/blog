> 5.7 版本开始，引入 JSON 数据类型

- JSON 列存储的数据要么是 null，要么必须是 JSON 格式数据，否则会报错

具体细节可以查看官方文档

[MYSQL 5.7 官方文档](  https://dev.mysql.com/doc/refman/5.7/en/json-function-reference.html  )

 其实我们从JSON功能介绍的主页也可以看到，这些**内置函数支持我们创建、查找、替换和返回值**等相关的操作，像我们替换指定内容的操作就可以使用**JSON_REPLACE()**这个函数，不过最后实现通过纯SQL语句执行最终的内容替换，你**还需要通过执行UPDATE语句**，比如： 

```sql
update tab
set content = json_replace(content, '$.author', "tom")
where id = 1
```

 其中**“$.\**\*”**表示找到JSON内容中匹配的修改字段。 