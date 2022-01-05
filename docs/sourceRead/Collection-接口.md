Collection 表示一组对象，它是集中、收集的意思。Collection 接口的两个子接口是 List、Set、Queue 接口。

![1571902582745](assets/1571902582745.png)

由于 List、Set 是 Collection 的子接口，意味着所有 List、Set、Queue 的实现类都有上面的方法

## 概念

- Collection 表示一组对象（其本身也是对象），它是集中、收集的意思，就是把一些数据收集起来。
- Collection 函数库是在 java.util 包下的一些接口和类
  - 类是用来产生对象存放数据用的
  - 接口是访问数据的方式
- 数组是一个典型的容器
- Collection 函数库与数组的不同
  - 数组的容量是有限制的，而 Collection 库没有这样的限制，它容量可以自动的调节
  - Collection 函数库只能用来存放对象，而数组没有这样的限制
- Collection 接口是 Collection 层次结构中的根接口，它定义了一些最基本的访问方法，让我们能用统一的方式通过它或它的子接口来访问数据。

- 区别：
  - Collection 代表一组对象
  - Collection 函数库就是 java 中的集合框架
  - Collection 接口，是这个集合框架中的根接口
- 存放在 Collection 库中的数据，被称为元素（element）

## Collection 接口

定义了存取一组对象的方法，其子接口 Set 和 List 分别定义了存储方式。

- Set 中的数据对象没有顺序且不可以重复。
- List 中的数据对象有顺序且可以重复。
- Map 接口定义了存储“键（key）--值（value）映射对” 的方法。