> Java SE5 提供了一种新的类型-Java 的枚举类型，关键字 enum 可以将一组具名的值的有限集合创建为一种新的类型，而这个具名的值可以作为常规的程序组件使用，这是一种非常有用的功能。本文将深入分析枚举源码，看一看枚举是怎么实现的，他是如何保证线程安全的，以及为什么用枚举实现的单例是最好的方式。

## 枚举是如何保证线程安全的

enum 就和 class 一样，只是一个关键字，他并不是一个类，那么枚举是由什么类维护的呢，先来一个简单的枚举：

```java
public enum t{
    SPRING,SUMMER,AUTUMN,WINTER;
}
```

然后反编译这段代码：

```java
public final class T extends Enum
{
    private T(String s, int i)
    {
        super(s, i);
    }
    public static T[] values()
    {
        T at[];
        int i;
        T at1[];
        System.arraycopy(at = ENUM$VALUES, 0, at1 = new T[i = at.length], 0, i);
        return at1;
    }

    public static T valueOf(String s)
    {
        return (T)Enum.valueOf(demo/T, s);
    }

    public static final T SPRING;
    public static final T SUMMER;
    public static final T AUTUMN;
    public static final T WINTER;
    private static final T ENUM$VALUES[];
    static
    {
        SPRING = new T("SPRING", 0);
        SUMMER = new T("SUMMER", 1);
        AUTUMN = new T("AUTUMN", 2);
        WINTER = new T("WINTER", 3);
        ENUM$VALUES = (new T[] {
            SPRING, SUMMER, AUTUMN, WINTER
        });
    }
}
```

​	通过反编译后的代码，可以看到，该类继承 Enum，同时 final 关键字说明，该类是不能被继承的。当我们使用 `enum` 来定义一个枚举类型的时候，编译器会自动帮我们创建一个 final 类型的类继承 Enum 类，所以枚举类型不能被继承；该类中有几个属性和方法：

```java
public static final T SPRING;
public static final T SUMMER;
public static final T AUTUMN;
public static final T WINTER;
private static final T ENUM$VALUES[];
static
{
    SPRING = new T("SPRING", 0);
    SUMMER = new T("SUMMER", 1);
    AUTUMN = new T("AUTUMN", 2);
    WINTER = new T("WINTER", 3);
    ENUM$VALUES = (new T[] {
        SPRING, SUMMER, AUTUMN, WINTER
    });
}
```

都是 static 类型的，因为 static 类型的属性会在类被加载之后被初始化，当一个 Java 类第一次被真正使用到的时候静态资源被初始化、Java 类的加载和初始化过程都是线程安全的。所以，**创建一个 enum 类型是线程安全的**。

## 为什么用枚举实现的单例是最好的方式

在单例模式的七种写法中，Effective Java 作者 `Josh Bloch`  提倡使用枚举的方式。

1、枚举写法简单

```java
public enum EasySingleton{
    INSTANCE;
}
```

可以通过 `EasySingleton.INSTANCE` 直接访问。

2、枚举自己处理序列化

As we know，以前的所有单例模式都有一个比较大的问题，就是一旦实现了 Serializable 接口之后，就不再是单例的了，因为，每次调用 readObject() 方法返回的都是一个新创建的对象，有一种解决办法就是使用 readResolver() 方法来避免此事发生。但是，为了保证枚举类型像 Java 规范中所说的那样，每一个枚举类型及其定义的枚举变量在 JVM 中都是唯一的，在枚举类型的序列化和反序列化上，Java 做了特殊的规定。

在序列化的时候 Java 仅仅是将枚举对象的 name 属性输出到结果中，反序列化的时候则是通过 java.lang.Enum 的 valueOf() 方法来根据名字查找枚举对象。同时，编译器是不允许任何对这种序列化机制的定制的，因此禁用了 writeObject、readObject、readObjectNoData、writeReplace 和 readResolve 等方法。我们看一下这个 valueOf 方法：

```java
public static <T extends Enum<T>> T valueOf(Class<T> enumType,String name) {  
    T result = enumType.enumConstantDirectory().get(name);  
    if (result != null)  
        return result;  
    if (name == null)  
        throw new NullPointerException("Name is null");  
    throw new IllegalArgumentException(  
        "No enum const " + enumType +"." + name);  
}  
```

从代码中可以看到，代码会尝试从调用`enumType`这个`Class`对象的`enumConstantDirectory()`方法返回的`map`中获取名字为`name`的枚举对象，如果不存在就会抛出异常。再进一步跟到`enumConstantDirectory()`方法，就会发现到最后会以反射的方式调用`enumType`这个类型的`values()`静态方法，也就是上面我们看到的编译器为我们创建的那个方法，然后用返回结果填充`enumType`这个`Class`对象中的`enumConstantDirectory`属性。

所以，**JVM对序列化有保证。**

 **3.枚举实例创建是thread-safe(线程安全的)** 

 当一个Java类第一次被真正使用到的时候静态资源被初始化、Java类的加载和初始化过程都是线程安全的。所以，创建一个enum类型是线程安全的。 