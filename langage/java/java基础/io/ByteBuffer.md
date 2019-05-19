### java.io.ByteBuffer
### BufferPool
频繁的创建与释放ByteBuffer对于性能不好，因此需要使用池化管理，在kafka中具体的实现类为：BufferPool

![BufferPool](../images/BufferPool.png)
```java
ByteBuffer byteBuffer = this.mappedByteBuffer.slice();
```