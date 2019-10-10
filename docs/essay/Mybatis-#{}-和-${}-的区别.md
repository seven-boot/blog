动态SQL是mybatis的强大特性之一

mybatis在对SQL语句进行预编译之前，会对SQL进行动态解析，解析为一个BoundSql对象

在动态解析的过程中，#{}和${}的区别：

#{} 会被解析为一个占位符 ？

${} 会直接进行变量替换

综上所得：1、${} 的替换是在 动态SQL 解析阶段
		  2、#{} 的替换是在 DBMS 中
		  3、#{} 能够很大程度上防止SQL注入，${} 无法防止SQL注入

举例：

select * from ${tableName} where name = ${name}

如果传入的参数tableName 为：[user;delete user;--] ，那么 sql 动态解析之后，预编译之前的sql将变为：

select * from user;delete user;-- where name = ;

select * from user;delete user;

动态调用表名和字段名。示例：

```xml
<select id="getUser" resultType="java.util.Map" parameterType="java.lang.String" statementType="STATEMENT">
    select 
        ${columns}
    from ${tableName}
</select>
```

要动态调用表名和字段名，就不能使用预编译了，需添加 statementType="STATEMENT"，
注意：添加之后，#{} 就不能用了

statementType：STATEMENT（非预编译），PREPARED（预编译）或CALLABLE中的任意一个，这就告诉 MyBatis 分别使用Statement，PreparedStatement或者CallableStatement。默认：PREPARED。这里显然不能使用预编译，要改成非预编译。