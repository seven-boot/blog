# @tk.mybatis.mapper.MapperException: 当前实体类不包含名为user_id的属性

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