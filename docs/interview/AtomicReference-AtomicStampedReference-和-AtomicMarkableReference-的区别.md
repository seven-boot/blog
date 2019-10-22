## AtomicReference

通过 colatile 和 Unsafe 提供的 CAS 函数实现原子操作。**自旋 + CAS** 的无锁操作保证共享变量的线程安全

1. value 是 volatile 类型，这保证了：当某线程修改 value 值时，其他线程看到的 value 的值都是最新的值，即修改之后的 volatile 的值
2. 通过 CAS 设置 value。这保证了：某线程池通过 CAS 函数（如 compareAndSet 函数）设置 value 时，它的操作是原子性的，即线程在操作 value 时不会被中断。

但是 CAS 操作可能存在 ABA 问题。AtomicStampedReference 的出现就是为了解决这问题

## AtomicStampedReference

构造方法中 initialStamp 用来唯一标识引用变量，在构造器内部，实例化了一个 Pair 对象，Pair 对象记录了对象引用和时间戳信息，采用 int 作为时间戳，实际使用的时候，要保证时间戳唯一（一般做成自增的），如果时间戳重复，还会出现 **ABA** 问题。

AtomicStampedReference 中的每一个引用变量都带上 pair.stamp 这个时间戳，这样就可以解决 CAS 中的 ABA 问题。

## AtomicMarkableReference

AtomicStampedReference 可以知道，引用变量中途被更改了几次。有时候，我们并不关心引用变量更改了几次，只是单纯的关心是否更改过，所以就有了 AtomicMarkableReference。

AtomicMarkableReference 的唯一区别就是不再使用 int 标识引用，而是使用 Boolean 变量表示引用变量是否被更改过。