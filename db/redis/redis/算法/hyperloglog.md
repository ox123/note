- [探索HyperLogLog算法(含Java实现)](https://www.jianshu.com/p/55defda6dcd2)
- pf 的内存只有12k
    HyperLogLog 实现中用到的是 16384 个桶，也就是 2^14，每个桶的 maxbits 需要 6 个 bits 来存储，最大可以表示 maxbits=63，于是总共占用内存就是2^14 * 6 / 8 = 12k字节
- [HyperLogLog 算法的原理讲解以及 Redis 是如何应用它的](https://www.cnblogs.com/linguanh/p/10460421.html)