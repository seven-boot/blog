## @ActiveProfiles

一般声明在测试类上，用于指定这个测试类里的测试方法运行时的 profile

```mysql
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

```java
@SpringBootTest
@ActiveProfiles("dev")
public class Tests {
    @Test
	public void contextLoads() {
	
	}
}
```

## @Profile

Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能；指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

1. 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
2. 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效

```java
public class TestBean {
    private String content;

    public TestBean(String content) {
        super();
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}
```

```java
@Configuration
public class TestConfig {
    @Bean
    @Profile("dev")
    public TestBean devTestBean() {
        return new TestBean("this is dev profile bean");
    }

    @Bean
    @Profile("pro")
    public TestBean proTestBean() {
        return new TestBean("this is pro profile bean");
    }
}
```

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ActiveProfiles("dev")
public class DemoApplicationTests {

	@Autowired
	private TestBean testBean;

	@Test
	public void contextLoads() {
		String content=testBean.getContent();
		System.out.println(content);
	}
}
```

运行结果：

```cmd
this is dev profile bean
```



