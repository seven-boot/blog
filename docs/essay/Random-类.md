## 1、Java 随机数的产生方式

在Java中，随机数的概念从广义上将，有三种。

​    1、通过System.currentTimeMillis（）来获取一个当前时间毫秒数的long型数字。

​    2、通过Math.random（）返回一个0到1之间的double值。

​    3、通过Random类来产生一个随机数，这个是专业的Random工具类，功能强大。

## 2、Random 类 API 说明

Random类的实例用于生成伪随机数流。此类使用 48 位的种子，使用线性同余公式对其进行修改（请参阅 Donald Knuth 的《The Art of Computer Programming， Volume 2》，第 3.2.1 节）。

如果用相同的种子创建两个 Random 实例，则对每个实例进行相同的方法调用序列，它们将生成并返回相同的数字序列。为了保证属性的实现，为类 Random 指定了特定的算法。

很多应用程序会发现 Math 类中的 random 方法更易于使用。

```text
Random()   
          创建一个新的随机数生成器。   
Random(long seed)   
          使用单个 long 种子创建一个新随机数生成器： public Random(long seed) { setSeed(seed); } next 方法使用它来保存随机数生成器的状态。  
   
protected  int next(int bits)   
          生成下一个伪随机数。   
boolean nextBoolean()   
          返回下一个伪随机数，它是从此随机数生成器的序列中取出的、均匀分布的 boolean 值。   
void nextBytes(byte[] bytes)   
          生成随机字节并将其置于用户提供的字节数组中。   
double nextDouble()   
          返回下一个伪随机数，它是从此随机数生成器的序列中取出的、在 0.0 和 1.0之间均匀分布的 double 值。   
float nextFloat()   
          返回下一个伪随机数，它是从此随机数生成器的序列中取出的、在 0.0 和 1.0 之间均匀分布的 float 值。   
double nextGaussian()   
          返回下一个伪随机数，它是从此随机数生成器的序列中取出的、呈高斯（“正常地”）分布的 double 值，其平均值是 0.0，标准偏差是 1.0。   
int nextInt()   
          返回下一个伪随机数，它是此随机数生成器的序列中均匀分布的 int 值。   
int nextInt(int n)   
          返回一个伪随机数，它是从此随机数生成器的序列中取出的、在 0（包括）和指定值（不包括）之间均匀分布的 int值。   
long nextLong()   
          返回下一个伪随机数，它是从此随机数生成器的序列中取出的、均匀分布的 long 值。   
void setSeed(long seed)   
          使用单个 long 种子设置此随机数生成器的种子。  
```

## 3、Random 类使用说明

1、带种子与不带种子的区别Random类使用的根本是策略分带种子和不带种子的Random的实例。

​    通俗说，两者的区别是：带种子的，每次运行生成的结果都是一样的。

​    不带种子的，每次运行生成的都是随机的，没有规律可言。

2、创建不带种子的Random对象

​    Random random = new Random（）；

3、创建不带种子的Random对象有两种方法：

​    1） Random random = new Random（555L）；

​    2） Random random = new Random（）；random.setSeed（555L）；
