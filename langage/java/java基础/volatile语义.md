### synchronized

实现原理分析：https://www.itcodemonkey.com/article/6726.html

- https://blog.csdn.net/chenssy/article/details/54883355

- synchronized可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性
  
  > Java中每一个对象都可以作为锁，这是synchronized实现同步的基础： 
1. 普通同步方法，锁是当前实例对象 

2. 静态同步方法，锁是当前类的class对象 

3. 同步方法块，锁是括号里面的对象
- Java对象头和monitor是实现synchronized的基础

### volatile实现原理

- JMM内存模型
  JMM决定一个线程对共享变量的写入何时对另一个线程可见，JMM定义了线程和主内存之间的抽象关系：共享变量存储在主内存(Main Memory)中，每个线程都有一个私有的本地内存（Local Memory），本地内存保存了被该线程使用到的主内存的副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量

- 若用volatile修饰共享变量，在编译时，会在指令序列中插入内存屏障来禁止特定类型的处理器重排序

volatile禁止指令重排序也有一些规则，简单列举一下：

1. 当第二个操作是voaltile写时，无论第一个操作是什么，都不能进行重
2. 当地一个操作是volatile读时，不管第二个操作是什么，都不能进行重排序
3. 当第一个操作是volatile写时，第二个操作是volatile读时，不能进行重排序