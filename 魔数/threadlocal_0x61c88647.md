### [ThreadLocal 原理](https://juejin.im/post/5e26e2636fb9a030051f02dd) 

### 魔数0x61c88647
https://juejin.im/post/5b5ecf9de51d45190a434308

生成hash code间隙为这个魔数，可以让生成出来的值或者说ThreadLocal的ID较为均匀地分布在2的幂大小的数组中。
``` java
private static final int HASH_INCREMENT = 0x61c88647;

private static int nextHashCode() {
   return nextHashCode.getAndAdd(HASH_INCREMENT);
}
```
可以看出，它是在上一个被构造出的ThreadLocal的ID/threadLocalHashCode的基础上加上一个魔数0x61c88647的。

这个魔数的选取与斐波那契散列有关，0x61c88647对应的十进制为1640531527。
斐波那契散列的乘数可以用(long) ((1L << 31) * (Math.sqrt(5) - 1))可以得到2654435769，如果把这个值给转为带符号的int，则会得到-1640531527。换句话说
(1L << 32) - (long) ((1L << 31) * (Math.sqrt(5) - 1))得到的结果就是1640531527也就是0x61c88647
。

通过理论与实践，当我们用0x61c88647作为魔数累加为每个ThreadLocal分配各自的ID也就是threadLocalHashCode再与2的幂取模，得到的结果分布很均匀。

ThreadLocalMap使用的是线性探测法，均匀分布的好处在于很快就能探测到下一个临近的可用slot，从而保证效率。。为了优化效率。
