### CAC 三个问题
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