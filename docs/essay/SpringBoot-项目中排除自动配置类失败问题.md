SpringBoot 项目排除自动配置的几种方法：

1、使用 `@SpringBootApplication` 注解中的 `exclude` 属性进行排除指定的类：

```java
@SpringBootApplication(exclude = {xxx.class})
public class Application {
	//...
}
```

2、 使用 `@EnableAutoConfiguration` 

> 注意：`@EnableAutoConfiguration` 注解除了主类上配置，其他地方不要添加

```java
@EnableAutoConfiguration(exclude = {xxx.class})
public class Application {
    // ...
}
```

3、当使用 `@SpringCloudApplication` 注解的时候

```java
@EnableAutoConfiguration(exclude = {xxx.class})
@SpringCloudApplication
public class Application {
    // ...
}
```

4、在配置文件中指定参数 `spring.autoconfiguration.exclude` 进行排除

```
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

或者还可以这样写：

```
spring.autoconfigure.exclude[0]=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

yml 配置文件：

```
spring:     
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

------

### 问题

正常情况下，对于多个自动配置类冲突的问题，以上几种方式即可解决，下面介绍遇到的问题：

有一个 `core` 项目，包命名为 `cn.dream.core`，主模块项目包命名为 `com.fly`，子模块为 `com.fly.user` `com.fly.order` 等具体业务命名。由于 `core` 项目配置了 `mica-auto` 自动配置（原理同 lombok），引用该 jar 包的项目会自动加入到 IOC 容器中，如果不需要该自动配置，只需按照上面几种方式即可排除。后来项目临近结束，想把 `core` 项目合并进主模块，修改包名为 `com.fly.core`，配置如下：

```java
@ComponentScan("com.fly")
@SpringBootApplication(exclude = {AaaConfiguration.class, BbbConfiguration.class})
public class Application {
}
```

修改后启动项目发现使用 `@SpringBootApplication` 中的 `exclude` 属性排除的自动配置类还是会被扫描进 IOC 容器，只是更换了包名，并没有修改别的代码，这是为什么呢？

仔细观察发现修改包名之后 `@ComponentScan` 注解扫描的包路径恰好包含了上面两个配置类。这是会有疑问？已经排除了为什么还会被扫描到呢？

### 结论

经排查后发现是 SpringBoot 和 Spring 的机制冲突了，SpringBoot 使用了约定大于配置的机制，是建立在 Spring 之上， 作为 Spring 的 “增强” 而存在的，也就是说它不会覆盖原来的 Spring Bean 扫描机制，所以还需要在 `@ComponentScan` 中排除掉需要排除的自动配置类：

```java
@ComponentScan(value = "com.fly", excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {AaaConfiguration.class, BbbConfiguration.class})
})
@SpringBootApplication(exclude = {AaaConfiguration.class, BbbConfiguration.class})
public class Application {
}
```

