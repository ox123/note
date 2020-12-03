- [hashmap 深入学习](https://zhuanlan.zhihu.com/p/21673805)
- [HashMap实现原理：容量、负载因子、hash与定位都搞定了吗？](https://www.toutiao.com/i6726809084521087496/)

```java
    /**
     * Returns a power of two size for the given target capacity.
     */
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

