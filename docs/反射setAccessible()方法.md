# 反射 setAccessible() 方法

java 代码中，常常将一个类得成员变量设置为 private

在类的外面获取此类得私有成员变量得 value 时，需要注意

```java
package youbang.sub.b2berpapi;

import lombok.Data;

import java.lang.reflect.Field;

/**
 * @author QH
 * @date 2019/5/21
 * @description
 */
public class Test {
    public static void main(String[] args) throws Exception {
        Class clazz = Class.forName("youbang.sub.b2berpapi.Dog");
        Dog dog = new Dog();
        dog.setId(1);
        dog.setName("青青");
        for (Field field:
             clazz.getDeclaredFields()) {
            field.setAccessible(true);  // Dog 类中的成员变量为private，所以必须进行此操作
            System.out.println(field.get(dog));  // 获取当前对象中当前Field 的Value
        }
    }
}

@Data
class Dog {
    private Integer id;
    private String name;
}
```

如果没有在获取 Field 之前调用 setAccessible(true) 方法，异常：

```xml
Exception in thread "main" java.lang.IllegalAccessException: Class youbang.sub.b2berpapi.Test can not access a member of class youbang.sub.b2berpapi.Dog with modifiers "private"
```



