## 常见用法

```
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
	private String username;
    private String password;
    
    // getter setter...
}
```

还可以把 `@ConfigurationProperties` 直接加在定义了 `@bean` 的注解上，这时 bean 实体类就不用`@Component` 和 `@ConfigurationProperties` 了 

```java
@Bean
@ConfigurationProperties(prefix = "connection")
public ConnectionSettings connectionSettings(){
	return new ConnectionSettings();
}
```

使用的时候直接注入就可以了

```java
@Autowired ConnectionSettings conn;
```

如果发现 ` @ConfigurationPropertie ` 不生效，有可能是因为项目目录结构的问题，可以通过使用 ` @EnableConfigurationProperties(ConnectionSettings.class) ` 来明确指定需要用哪个实体类来装载配置信息

```
@Configuration
@EnableConfigurationProperties(ConnectionSettings.class)
 public class MailConfiguration { 
    @Autowired private MailProperties mailProperties; 

    @Bean public JavaMailSender javaMailSender() {
      // omitted for readability
    }
 }
```

