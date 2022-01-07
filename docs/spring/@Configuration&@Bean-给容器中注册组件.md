首先，创建一个 Person 类作为测试的 JavaBean，代码如下：

```java
public class Person {

    private String name;

    private Integer age;

    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

接下来创建一个配置类，将 JavaBean 注入到 Spring 的 Ioc 容器中

```
// 配置类 == 配置文件
@Configuration
public class Config1 {

    // 给容器中注册一个Bean；类型为返回值的类型，id默认为方法名
    @Bean
    public Person person() {
        return new Person("lili", 12);
    }
}
```

然后写一个测试方法，输出容器中的Bean看是否已经注入：

```java
@Test
void getPersonBean() {
	AnnotationConfigApplicationContext applicationContext = new 		AnnotationConfigApplicationContext(Config1.class);
	Person person = applicationContext.getBean(Person.class);
	System.out.println(person);
}
```

首先指定配置类 Config1 初始化一个 Ioc 容器 applicationContext，获取 Person 类型的 Bean 并输出

输出结果为

```
Person{name='lili', age=12}
```

接着输出注入到容器的 Person 类型的 Bean 的名称

```java
String[] beanNames = applicationContext.getBeanNamesForType(Person.class);
for (String beanName : beanNames) {
	System.out.println(beanName);
}

# 输出
person
```

可见 Bean 名称为 person，修改方法为 person1 后再输出

```
@Bean
public Person person1() {
	return new Person("lili", 12);
}

# 输出
person1
```

可见在使用注解注入 JavaBean 时，Bean 在 Ioc 容器中的名称就是 @Bean 注解标注的方法名称。

如果想要在方法名不变的情况下，去修改 Bean 名称，需要在 @Bean 注解中指定 Bean 的名称，如下代码：

```
@Bean("person")
public Person person1() {
	return new Person("lili", 12);
}

# 输出
person
```

这时，注入的 Bean 的名称就是 @Bean 中指定的名称，不是方法名了