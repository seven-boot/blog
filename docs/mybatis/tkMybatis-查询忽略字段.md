在 tkMybatis 的模板方法中，实体类的字段需和数据库的字段一一对应，如果不对应会提示找不到该字段，如果需要忽略某字段，只需要在字段上面加 `@Transient` 注解即可。

关于 `@Transient` 的含义，可参考 [关键字 transient](/sourceRead/关键字-transient)

```java
@Transient
private String split;
```