## JSR-303 简介

JSR-303 是 JavaEE 6 中的一项子规范，叫做 Bean Validation，官方参考实现是 Hibernate Validator。

此实现与 Hibernate ORM 没有任何关系。JSR-303 用于对 Java Bean 中的字段的值进行验证。 Spring MVC 3.x 之中也大力支持 JSR-303，可以在控制器中使用注解的方式对表单提交的数据方便地验证。

Spring 4.0 开始支持 Bean Validation 功能。

## JSR-303 基本的校验规则

### 空检查

- `@Null` 验证对象是否为 `null`
- `@NotNull` 验证对象是否不为 `null`, 无法查检长度为 0 的字符串
- `@NotBlank` 检查约束字符串是不是 `Null` 还有被 `Trim` 的长度是否大于 0,只对字符串,且会去掉前后空格
- `@NotEmpty` 检查约束元素是否为 `NULL` 或者是 `EMPTY`

### 布尔检查

- `@AssertTrue` 验证 `Boolean` 对象是否为 `true`
- `@AssertFalse` 验证 `Boolean` 对象是否为 `false`

### 长度检查

- `@Size(min=, max=)` 验证对象（`Array`, `Collection` , `Map`, `String`）长度是否在给定的范围之内
- `@Length(min=, max=)` 验证字符串长度介于 `min` 和 `max` 之间

### 日期检查

- `@Past` 验证 `Date` 和 `Calendar` 对象是否在当前时间之前，验证成立的话被注释的元素一定是一个过去的日期
- `@Future` 验证 `Date` 和 `Calendar` 对象是否在当前时间之后 ，验证成立的话被注释的元素一定是一个将来的日期

### 正则检查

- `@Pattern` 验证 `String` 对象是否符合正则表达式的规则，被注释的元素符合制定的正则表达式
- `regexp`：正则表达式
  - `flags`：指定 `Pattern.Flag` 的数组，表示正则表达式的相关选项

### 数值检查

**注意：** 建议使用在 `String` ,`Integer` 类型，不建议使用在 `int` 类型上，因为表单值为 `“”` 时无法转换为 `int`，但可以转换为 `String` 为 `“”`，`Integer` 为 `null`

- `@Min` 验证 Number 和 String 对象是否大等于指定的值
- `@Max` 验证 Number 和 String 对象是否小等于指定的值
- `@DecimalMax` 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过 `BigDecimal` 定义的最大值的字符串表示 `.小数` 存在精度
- `@DecimalMin` 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过 `BigDecimal` 定义的最小值的字符串表示 `.小数` 存在精度
- `@Digits` 验证 Number 和 String 的构成是否合法
- `@Digits(integer=,fraction=)` 验证字符串是否是符合指定格式的数字，`integer` 指定整数精度，`fraction` 指定小数精度
- `@Range(min=, max=)` 被指定的元素必须在合适的范围内
- `@Range(min=10000,max=50000,message=”range.bean.wage”)`
- `@Valid` 递归的对关联对象进行校验, 如果关联对象是个集合或者数组，那么对其中的元素进行递归校验，如果是一个 `map`，则对其中的值部分进行校验.(是否进行递归验证)
- `@CreditCardNumber` 信用卡验证
- `@Email` 验证是否是邮件地址，**如果为 `null`，不进行验证，算通过验证**
- `@ScriptAssert(lang= ,script=, alias=)`
- `@URL(protocol=,host=, port=,regexp=, flags=)`

## JSR 标准和 Spring 校验框架

`Java` 的 `JSR-303` 标准的数据校验的核心接口是 ` javax.validation.Validator `， 该接口根据目标对象中标注的校验注解进行数据校验，并得到校验结果。 

 `Spring`也有自己的校验框架，同时支持`JSR-303`标准的校验框架。`Spring`的`DataBinder`在进行数据绑定时，同时调用校验框架完成数据校验工作。 

 `Spring`的校验框架包是`org.springframework.validation`，其中`LocalValidatorFactoryBean`同时实现了`Spring`的`Validator`和`JSR-303`的`Validator`接口，但是`Spring`本身没有提供`JSR-303`的实现，因此必须将实现了`JSR-303`的`jar`放在类路径下。 

