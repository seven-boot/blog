在 Java 中，针对 static 静态成员，我们有一些最基本的常识：静态变量（成员）它是属于 `类` 的，而非属于 `实例对象` 的属性；同样的静态方法也是属于类的，普通方法（实例方法）才属于对象。而 Spring 容器管理的都是 `实例对象`，包括它的 `@Autowired` 依赖注入的均是容器内的对象实例，所以对于 static 成员是不能直接使用 `@Autowired` 注入的。

> 这很容易理解：类成员的初始化较早，并不需要依赖实例的创建，所以这个时候 Spring 容器可能还没有 “出生”，谈何依赖注入呢？

代码示例：

```java
@Component
public class Son {
}
```

```java
@Component
public class SonHolder {

    @Autowired
    private static Son son;

    public static Son getSon() {
        return son;
    }
}
```

使用上面定义的组件 SonHolder

```
@Test
void autoWiredStatic() {
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Config1.class);
    SonHolder sonHolder = applicationContext.getBean(SonHolder.class);
    sonHolder.getSon().toString();
}
```

会抛出一个 NPE 异常

### 问题分析

场景描述：有一个公共的方法，获取公司下的用户信息，然后有不同规则过滤，每个规则是一个方法，会有多个模块通用这个类下的过滤方法

```
public class User {

    private Long id;

    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

模拟 feign 远程调用

```
@Component
public class UserFeign {

    /**
     * 模拟feign远程调用
     * @param userIds
     * @return
     */
    public List<User> getByIds(List<Long> userIds) {
        return userIds.stream().map(u -> {
            User user = new User();
            user.setId(u);
            user.setName("YourBatman");
            if (u % 2 == 0) {
                user.setName(user.getName() + "_test");
            }
            return user;
        }).collect(Collectors.toList());
    }
}
```

通用的过滤方法

```java
@Component
public class UserHelper {

    @Autowired
    private UserFeign userFeign;

    public List<User> getAndFilter(List<Long> userIds) {
        List<User> users = userFeign.getByIds(userIds);
        return users.stream().filter(u -> {
            Long id = u.getId();
            String name = u.getName();
            if (name.contains("test")) {
                System.out.printf("id=%s name=%s是测试用户，已过滤\n", id, name);
                return false;
            }
            return true;
        }).collect(Collectors.toList());
    }
}
```

以上是正常情况，注入 UserHelper 之后调用过滤方法即可。但是如果这些过滤方法为一个工具类中的静态方法，那如何为静态字段注入 Bean 呢？

```java
@Component
public class UserHelper {

    @Autowired
    private static UserFeign userFeign;

    public static List<User> getAndFilter(List<Long> userIds) {
        List<User> users = userFeign.getByIds(userIds);
        return users.stream().filter(u -> {
            Long id = u.getId();
            String name = u.getName();
            if (name.contains("test")) {
                System.out.printf("id=%s name=%s是测试用户，已过滤\n", id, name);
                return false;
            }
            return true;
        }).collect(Collectors.toList());
    }
}
```

运行程序

```
UserHelper.getAndFilter(Arrays.asList(1L, 2L, 3L, 4L, 5L))
```

结果输出

```
一月 07, 2022 7:46:41 下午 org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor lambda$buildAutowiringMetadata$1
信息: Autowired annotation is not supported on static fields: private static com.keke.bean1.UserFeign com.keke.bean1.UserHelper.userFeign

java.lang.NullPointerException
	at com.keke.bean1.UserHelper.getAndFilter(UserHelper.java:19)
```

抛出 NPE 异常并且打印 **@Autowired不支持标注在static字段/属性上**。 

![image-20220107194841014](assets/image-20220107194841014.png)

### 为什么 @Autowired 不能注入 static 成员属性

静态变量是属于**类本身**的信息，当类加载器加载静态变量时，Spring的上下文环境**还没有**被加载，所以不可能为静态变量绑定值（这只是最表象原因，并不准确）。同时，Spring也不鼓励为静态变量注入值（言外之意：并不是不能注入），因为它认为这会增加了耦合度，对测试不友好。 

 这些都是表象，那么实际上Spring是如何“操作”的呢？我们沿着AutowiredAnnotationBeanPostProcessor输出的这句info日志，倒着找原因，这句日志的输出在这： 

```
private InjectionMetadata buildAutowiringMetadata(Class<?> clazz) {
        ...

            do {
                List<InjectedElement> currElements = new ArrayList();
                // 遍历所有的字段（包括静态字段）
                ReflectionUtils.doWithLocalFields(targetClass, (field) -> {
                    MergedAnnotation<?> ann = this.findAutowiredAnnotation(field);
                    if (ann != null) {
                        if (Modifier.isStatic(field.getModifiers())) {
                            if (this.logger.isInfoEnabled()) {
                                this.logger.info("Autowired annotation is not supported on static fields: " + field);
                            }

                            return;
                        }
						...
                    }

                });
                // 遍历所有的方法（包括静态方法）
                ReflectionUtils.doWithLocalMethods(targetClass, (method) -> {
                    Method bridgedMethod = BridgeMethodResolver.findBridgedMethod(method);
                    if (BridgeMethodResolver.isVisibilityBridgeMethodPair(method, bridgedMethod)) {
                        MergedAnnotation<?> ann = this.findAutowiredAnnotation(bridgedMethod);
                        if (ann != null && method.equals(ClassUtils.getMostSpecificMethod(method, clazz))) {
                            if (Modifier.isStatic(method.getModifiers())) {
                                if (this.logger.isInfoEnabled()) {
                                    this.logger.info("Autowired annotation is not supported on static methods: " + method);
                                }

                                return;
                            }

                            if (method.getParameterCount() == 0 && this.logger.isInfoEnabled()) {
                                this.logger.info("Autowired annotation should only be used on methods with parameters: " + method);
                            }

                            ...
                        }

                    }
                });
                ....
        }
    }
```

从代码中可以看出来，Modifier.isStatic 不给静态的字段、方法执行 @Autowired 注入。

### 间接实现 static 成员注入

方式一：以 set 方法作为跳板，在里面实现对 static 静态成员的赋值

```java
@Component
public class UserHelper {

    static UserFeign userFeign;

    @Autowired
    public void setUserFeign(UserFeign userFeign) {
        UserHelper.userFeign = userFeign;
    }

    public static List<User> getAndFilter(List<Long> userIds) {
    	...
    }
}
```

 方式二：使用 @PostConstruct 注解，在里面为static静态成员赋值 

```java
@Component
public class UserHelper {

    static UserFeign userFeign;

    @Autowired
    ApplicationContext applicationContext;

    @PostConstruct
    public void init() {
        UserHelper.userFeign = applicationContext.getBean(UserFeign.class);
    }
    
    public static List<User> getAndFilter(List<Long> userIds) {
    	...
    }
}
```

以上两种方式，思想只有一个：**延迟为 static 成员属性赋值**，因此可以延申出其他方案

#### 高级实现方式

```java
@Component
public class AutowireStaticSmartInitializingSingleton implements SmartInitializingSingleton {

    @Autowired
    private AutowireCapableBeanFactory beanFactory;

    /**
     * 当所有的单例Bena初始化完成后，对static静态成员进行赋值
     */
    @Override
    public void afterSingletonsInstantiated() {
        // 因为是给static静态属性赋值，因此这里new一个实例做注入是可行的
        beanFactory.autowireBean(new UserHelper());
    }
}
```

UserHelper类不再需要标注@Component注解，也就是说它不再需要被Spirng容器管理（static工具类确实不需要交给容器管理嘛，毕竟我们不需要用到它的实例），这从某种程度上也是节约开销的表现。

```
public class UserHelper {

    @Autowired
    static UCClient ucClient;
    ...
}
```

运行程序，结果输出：

```
08:50:15.765 [main] INFO org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor - Autowired annotation is not supported on static fields: static cn.yourbatman.temp.component.UCClient cn.yourbatman.temp.component.UserHelper.ucClient
Exception in thread "main" java.lang.NullPointerException
	at cn.yourbatman.temp.component.UserHelper.getAndFilterTest(UserHelper.java:26)
	at cn.yourbatman.temp.component.RoomService.create(RoomService.java:26)
	at cn.yourbatman.temp.DemoTest.main(DemoTest.java:19)
```

报错。当然喽，这是我故意的，虽然抛异常了，但是看到我们的进步了没：info日志只打印一句了（自行想想啥原因哈）。不卖关子了，正确的姿势还得这么写：

```
public class UserHelper {

    static UCClient ucClient;
    @Autowired
    public void setUcClient(UCClient ucClient) {
        UserHelper.ucClient = ucClient;
    }
}
```

或者

```
@Component
public class AutowireStaticSmartInitializingSingleton implements SmartInitializingSingleton {

    @Autowired
    private AutowiredAnnotationBeanPostProcessor autowiredAnnotationBeanPostProcessor;
    @Override
    public void afterSingletonsInstantiated() {
        autowiredAnnotationBeanPostProcessor.processInjection(new UserHelper());
    }
}
```

