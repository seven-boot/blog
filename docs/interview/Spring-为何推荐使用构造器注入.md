> 参考： https://www.cnblogs.com/joemsu/p/7688307.html 

 在idea中使用field注入时，提示 

>  Field injection is not recommended. 
>
> Spring Team recommends: "Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies".

面试中也会问到：如何解决循环依赖？（使用构造器注入，会在启动的时候报错）

 spring团队为何推荐使用构造器注入，而不推荐方法注入呢？
下面将对常见的三种构造器注入方式进行介绍。 

## filed注入

### 使用方法

```java
@Controller
public classFooController{
    @Autowired  // @Inject  
    private FooService fooService;
    //简单的使用例子，下同  
    publicListlistFoo(){
      return fooService.list();
    }
}
```

该种注入方式是使用开发中最常见的注入方式。

### 优点

1. 注入方式简单：加入要注入的字段，附上注解`@AutoWired` 
2. 整体代码简洁明了

### 缺点

1.  **对于IOC容器以外的环境，除了使用反射来提供它需要的依赖之外，无法复用该实现类**。而且将一直是个潜在的隐患，因为你不调用将一直无法发现NullPointException的存在
2. 使用field注入可能会导致循环依赖

```java
public class A {
    @Autowired
    private B b;
}

public class B {
    @Autowired
    private A a;
}
```

## constructor注入

### 使用方法

```java
@Controller
public class FooController {  
    private final FooService fooService;
  
    @Autowired
    public FooController(FooService fooService) {
        this.fooService = fooService;
    }
}
```

### 优点

- 依赖不可变 components as *immutable objects* ，即注入对象为final
- 依赖不可为空required dependencies are not *null*，省去对注入参数的检查。当要实例化FooController的时候，由于只有带参数的构造函数，spring注入时需要传入所需的参数，所以有两种情况：1) 有该类型的参数传入 => ok; 2) 无该类型参数传入，报错
- 提升了代码的可复用性：非IOC容器环境可使用new实例化该类的对象。
- 避免循环依赖：如果使用构造器注入，在spring项目启动的时候，就会抛出：BeanCurrentlyInCreationException：Requested bean is currently in creation: Is there an unresolvable circular reference？从而提醒你避免循环依赖，如果是field注入的话，启动的时候不会报错，在使用那个bean的时候才会报错。

### 缺点

- 当注入参数较多时，代码臃肿。

## setter注入

### 使用方法

```java
@Controller
public class FooController {
    private FooService fooService;

    @Autowired
    public void setFooService(FooService fooService) {
        this.fooService = fooService;
    }
}
```

### 优点

1. 相比构造器注入，当注入参数太多或存在非必须注入的参数时，不会显得太笨重
2. 允许在类构造完成后重新注入

### 缺点

## 答疑

**Q1：如果有大量的依赖要注入，构造方法注入不会显得很臃肿吗？**

对于这个问题，说明你的类当中有太多的责任，那么你要好好想一想是不是自己违反了类的**单一性职责原则**，从而导致有这么多的依赖要注入。

**Q2：是不是其他的注入方式都不适合用了呢？**

当然不是，存在即是合理！setter的方式既然一开始被Spring推荐肯定是有它的道理，像之前提到的setter的方式能用让类在之后重新配置或者重新注入，就是其优点之一。除此之外，如果一个依赖有多种实现方式，我们可以使用`@Qualifier`，在构造方法里选择对应的名字注入，也可以使用field或者setter的方式来手动配置要注入的实现。