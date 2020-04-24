### 一级缓存

​		 MyBatis 会在表示会话的 `SqlSession` 对象中建立一个简单的缓存，将每次查询到的结果结果缓存起来，当下次查询的时候，如果判断先前有个完全一样的查询，会直接从缓存中直接将结果取出，返回给用户，不需要再进行一次数据库查询了。 

​		 一级缓存是 `SqlSession` 级别的缓存。在操作数据库时需要构造 sqlSession 对象，在对象中有一个（内存区域）数据结构（HashMap）用于存储缓存数据。不同的 sqlSession 之间的缓存数据区域（HashMap）是互相不影响的。其作用域是同一个 SqlSession，在同一个 sqlSession 中两次执行相同的 sql 语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。当一个 sqlSession 结束后该 sqlSession 中的一级缓存也就不存在了。Mybatis 默认开启一级缓存。 

**需要注意的是：**当 mybatis 整合 spring 后（包括 SpringBoot），如果不是在同一个事务中，每个 mapper 的每次查询操作都对应一个全新的 sqlSession 实例，这个时候就不会有一级缓存的命中，但是在同一个事务中时，是共用一个 sqlSession。

### 二级缓存

​		二级缓存是 mapper 级别的缓存，多个 `SqlSession` 去操作同一个 Mapper 的 sql 语句，多个 SqlSession 去操作数据库得到数据会存在二级缓存区域，多个 SqlSession 可以共用二级缓存，二级缓存是跨 SqlSession 的。其作用域是 mapper 的同一个 `namespace`，不同的 sqlSession 两次执行相同 namespace下的 sql 语句且向 sql 中传递参数也相同即最终执行相同的 sql 语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。Mybatis 默认没有开启二级缓存需要在 setting 全局参数中配置开启二级缓存。 

## 配置 MyBatis 二级缓存

### Spring Boot 中开启 MyBatis 二级缓存

