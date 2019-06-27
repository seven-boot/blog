在比较两个**数值相等**的Integer类型的整数时，我们可能会发现，用 “==” 比较，有的时候返回 true，有的时候返回 false。比如：

```java
Integer a = 127;
Integer b = 127;
Integer c = new Integer(127);

System.out.println(a == b);			true
System.out.println(a.equals(b));  	true
System.out.println(a == c);			false
System.out.println(a.equals(c));	false
```

然而：

```java
Integer a = 128;
Integer b = 128;

System.out.println(a == b); 		false
System.out.println(a.equals(b));	true
```

为什么会出现这种情况呢？ 因为 Integer a = 128；这种方式赋值，会调用 `valueof()` 方法。查看 `valueOf()` 的源码，发现这里做了一些关于 `IntegerCache` 的操作：

```java
static final int low = -128;		已固定
static final int high;				可调

public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

`IntegerCache` 源码：

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        // 这里是把 -128 ~ 127（可调） 的整数都提前实例化了
        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```

可以看出 Integer 把 -128 ~ 127 的整数都提前实例化了，所以不管创建多少这个范围内的 Integer ，都是同一个对象。

那么，如何比较两个 Integer 类型是否相等呢，就是使用 equals() 方法，看下 equals() 源码：

```java
private final int value;
	
...
	
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}
```

通过源码可知，是获取 Integer 的基本类型值，然后使用 “==” 来比较。所以，我们也可以通过 `Integer.intValue()` 获取 int 值来直接比较。

一个 int 类型和 Integer 类型 直接 “==” 比较也是没有问题的，Integer 会拆箱成 int 类型。

```java
Integer i = 128;
int j = 128;
System.out.println(i == j);//返回true
```

## 总结

如果要比较两个 Integer 类型的整数：

1. 如果 Integer 类型的两个数相等，如果范围在 -128 ~ 127 ，那么用 “==” 返回 true，其余的返回 false。
2. 两个基本类型 int 进行相等比较，直接用 “==” 即可。
3. 一个基本类型 int 和一个包装类型 Integer 比较，用“==” 也可，这个时候，Integer 类型做了拆箱操作。
4. Integer 类型比较大小，要么调用 `Integer.intValue()` 转为基本类型用 “==” 比较，要么直接用 equals 比较。

## 拓展

Long 和 Short 类型也做了相同的处理，只不过最大值是不可调的。

参考 Long 源码：

```java
public static Long valueOf(long l) {
    final int offset = 128;
    if (l >= -128 && l <= 127) { // will cache
        return LongCache.cache[(int)l + offset];
    }
    return new Long(l);
}
```

参考 Short 源码：

```java
public static Short valueOf(short s) {
    final int offset = 128;
    int sAsInt = s;
    if (sAsInt >= -128 && sAsInt <= 127) { // must cache
        return ShortCache.cache[sAsInt + offset];
    }
    return new Short(s);
}
```

