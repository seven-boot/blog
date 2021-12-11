## @tk.mybatis.mapper.MapperException: 当前实体类不包含名为user_id的属性

错误写法：

```java
Example example = new Example(ClinicEmployeeConfig.class);
Example.Criteria criteria = example.createCriteria();
criteria.andEqualTo("clinic_id", clinicId);
criteria.andEqualTo("employee_id", employeId);
```

正确写法：

```java
Example example = new Example(ClinicEmployeeConfig.class);
Example.Criteria criteria = example.createCriteria();
criteria.andEqualTo("clinicId", clinicId);
criteria.andEqualTo("employeeId", employeId);
```

`andQuealTo` 方法中第一个参数 `property` 应为实体类的属性名，而不是数据库字段名

# spring-boot启动警告:No MyBatis mapper was found…

spring-boot 集成 tk.mybatis 启动时，警告如下：

```
tk.mybatis.spring.mapper.ClassPathMapperScanner: No MyBatis mapper was found in '[com.mmm.kangaroo.erp]' package. Please check your configuration
```

### 原因：

`doScan()` 会扫描启动类同级目录下的 `mapper` 接口，但是合理的目录绝对不允许所有的 `mapper` 都在启动类目录下

### 解决：

在启动类目录下加一个伪 `mapper`：

```java
package com.mmm.kangaroo.erp;

import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface NoWarnMapper {
    
}
```

