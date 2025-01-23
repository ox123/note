- Buffers   是对原始磁盘块的临时存储，也就是用来**缓存磁盘的数据**，通常不会特别大（20MB 左右）。这样，内核就可以把分散的写集中起来，统一优化磁盘的写入，比如可以把多次小的写合并成单次大的写等等。

- Cached   是从磁盘读取文件的页缓存，也就是用来**缓存从文件读取的数据**。这样，下次访问这些文件数据时，就可以直接从内存中快速获取，而不需要再次访问缓慢的磁盘。

- SReclaimable 是 Slab 的一部分。Slab 包括两部分，其中的可回收部分，用 SReclaimable 记录；而不可回收部分，用 SUnreclaim 记录。

> 读文件时数据会缓存到 Cache 中，而读磁盘时数据会缓存到 Buffer 中。
> 
> Buffer 是对磁盘数据的缓存，而 Cache 是文件数据的缓存，它们既会用在读请求中，也会用在写请求中

### 模拟写入文件大小

```shell
dd if=/dev/urandom of=/tmp/file bs=1M count=500
```

#### 清理文件页、目录项、Inodes 等各种缓存

```shell
echo 3 > /proc/sys/vm/drop_caches
```


