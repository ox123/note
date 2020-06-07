### CAS 三个问题
- ABA问题：使用版本号解决，每次变量更新，版本号+1，```AtomicStampedReference```用于解决ABA问题
```java
    public boolean compareAndSet(
    V expectedReference,    // 期望reference
    V newReference,         // 修改后的reference
    int expectedStamp,      // 期望的值
    int newStamp) {        // 修改后的值
       ...
    }
```
- 循环时间长开销大
- 只能保证一个共享变量的原子操作，使用```AtomicReference```来保证引用对象之间的原子性，把多个变量放在同一个对象中进行操作。

### 重排序

为了提高性能，编译器和处理器常常会对指令进行重排序，分为如下三种。
- ```编译器优化重排序```
- ```指令级并行重排序```
- ```内存系统的重排序```，由于处理器使用缓存和读/写缓冲区，使得加载和存储操作看上去是在乱序执行。