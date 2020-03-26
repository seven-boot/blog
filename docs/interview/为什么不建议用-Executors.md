​		Java 为什么要创建 Executors 类？考虑到ThreadPoolExecutor的构造函数实在是有些复杂，所以Java并发包里提供了一个线程池的静态工厂类Executors，利用Executors你可以快速创建线程池。由下图可见，使用Executors类中提供的几个静态方法来创建线程池，内部实现均使用了 ThreadPoolExecutor 实现，其实都只是ThreadPoolExecutor 类的封装。

​		不建议使用Executors的最重要的原因是：Executors提供的很多方法默认使用的都是无界的LinkedBlockingQueue（如下图），高负载情境下，无界队列很容易导致OOM，而OOM会导致所有请求都无法处理，这是致命问题。所以强烈建议使用有界队列。  注：LinkedBlockingQueue是有界队列，但是不设置大小的话，就默认为Integer.MAX_VALUE，相当于无界队列了。

### Executors 返回的线程池对象的弊端：

1. FixedThreadPool 和 SingleThreadPool

   允许的请求队列长度为 Integer.MAX_VALUE ，可能会堆积大量的请求，从而导致 OOM

2. CachedThreadPool 和 ScheduledThreadPool

   允许的创建线程数量为 Integer.MAX_VALUE ，可能会创建大量的线程，从而导致 OOM