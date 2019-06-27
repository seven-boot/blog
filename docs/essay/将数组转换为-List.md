假设有数组：

```java
Element[] array = {new Element(1),new Element(2),new Element(3)};
```

如何将其转换为 ArrayList<Element> arrayList = ???

### 回答1

```java
new ArrayList<String>(Arrays.asList(array))
```

### 回答2

```java
Arrays.asList(array);
或者
Arrays.asList(new Element(1),new Element(2),new Element(3))
```

不过，这样做有些坑要注意：

1. 这样做生成的 list ，是定长的。也就是说，如果你对它做add或者remove，都会抛UnsupportedOperationException。

2. 如果修改数组的值，list中的对应值也会改变！

   

* **Arrays.asList() 返回的是Arrays内部静态类，而不是Java.util.ArrayList的类。**
* **这个java.util.Arrays.ArrayList有set(),get(),contains()方法，但是没有任何add() 方法，所以它是固定大小的**

如果希望避免这两个坑，请改用这个方式：

```java
Collections.addAll(arraylist, array);
```

eg：

```java
String[] array = {"123", "456", "789"};
ArrayList<String> arrayList = new ArrayList<>();
Collections.addAll(arrayList, array);
arrayList.add("10");
System.out.println(arrayList);

[123, 456, 789, 10]
```

