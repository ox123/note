### 读源代码好处

- 更深刻的了解内部设计原理，提升代码架构能力和代码功力
  - active Segment
- 快速定位问题并制定调优方案，减少问题定位的时间成本
- 努力成为社区contributter



### 源代码overeview

![image-20201106223825311](assets/image-20201106223825311.png)

### 服务器端源代码overview

![image-20201106224500412](assets/image-20201106224500412.png)

**kafka日志结构**

![image-20201106225206641](assets/image-20201106225206641.png)

### LogSegment

```scala
class LogSegment private[log] (val log: FileRecords,  // 实际保存的kafka消息的对象
                               val lazyOffsetIndex: LazyIndex[OffsetIndex], // 消息位移索引文件
                               val lazyTimeIndex: LazyIndex[TimeIndex], //时间戳索引文件
                               val txnIndex: TransactionIndex, // 终止事务索引文件
                               val baseOffset: Long, //每个日志段保存自己的起始位移，在磁盘中看到的日志文件名
                               val indexIntervalBytes: Int, //Broker 端参数 log.index.interval.bytes 值,控制日志段对象新增索引项的频率。默认情况下，日志段新写入4KB的消息数据才会新增一条索引项。
                               val rollJitterMs: Long, //rollJitterMs 是日志段对象新增倒计时的“扰动值”。因为目前 Broker 端日志段新增倒计时是全局设置，这就是说，在未来的某个时刻可能同时创建多个日志段对象，这将极大地增加物理磁盘 I/O 压力。有了 rollJitterMs 值的干扰，每个新增日志段在创建时会彼此岔开一小段时间，这样可以缓解物理磁盘的 I/O 负载瓶颈
                               val time: Time) extends Logging { // Timer用于计时的实现类
}
```



- append方法

  ```scala
  def append(largestOffset: Long,  // 最大位移
               largestTimestamp: Long, // 最大时间戳
               shallowOffsetOfMaxTimestamp: Long, //最大时间戳对应消息的位移
               records: MemoryRecords): Unit = { //真正要写入的消息集合
      if (records.sizeInBytes > 0) {
        trace(s"Inserting ${records.sizeInBytes} bytes at end offset $largestOffset at position ${log.sizeInBytes} " +
              s"with largest timestamp $largestTimestamp at shallow offset $shallowOffsetOfMaxTimestamp")
        val physicalPosition = log.sizeInBytes()
        if (physicalPosition == 0)
          rollingBasedTimestamp = Some(largestTimestamp)
  
        ensureOffsetInRange(largestOffset) // 确保消息位移值合法
  
        // append the messages
        val appendedBytes = log.append(records) // 真正写入消息
        trace(s"Appended $appendedBytes to ${log.file} at end offset $largestOffset")
        // Update the in memory max timestamp and corresponding offset.
        if (largestTimestamp > maxTimestampSoFar) {
          maxTimestampSoFar = largestTimestamp
          offsetOfMaxTimestampSoFar = shallowOffsetOfMaxTimestamp
        }
        // append an entry to the index (if needed)
        if (bytesSinceLastIndexEntry > indexIntervalBytes) {
          offsetIndex.append(largestOffset, physicalPosition)
          timeIndex.maybeAppend(maxTimestampSoFar, offsetOfMaxTimestampSoFar)
          bytesSinceLastIndexEntry = 0
        }
        bytesSinceLastIndexEntry += records.sizeInBytes // 更新已写入字节数
      }
    }
  ```

  ![image-20201119231009748](assets/image-20201119231009748.png)

- read方法

  ```scala
   def read(startOffset: Long, // 要读取的第一条消息的位移
             maxSize: Int, // 能读取的最大位移数
             maxPosition: Long = size, // 能读到的最大文件位置
             minOneMessage: Boolean = false): FetchDataInfo = { 是否允许在消息过大时至少返回第一条信息
      if (maxSize < 0)
        throw new IllegalArgumentException(s"Invalid max size $maxSize for log read from segment $log")
  
      val startOffsetAndSize = translateOffset(startOffset)
  
      // if the start position is already off the end of the log, return null
      if (startOffsetAndSize == null)
        return null
  
      val startPosition = startOffsetAndSize.position
      val offsetMetadata = LogOffsetMetadata(startOffset, this.baseOffset, startPosition)
  
      val adjustedMaxSize =
        if (minOneMessage) math.max(maxSize, startOffsetAndSize.size)
        else maxSize
  
      // return a log segment but with zero size in the case below
      if (adjustedMaxSize == 0)
        return FetchDataInfo(offsetMetadata, MemoryRecords.EMPTY)
  
      // calculate the length of the message set to read based on whether or not they gave us a maxOffset
      val fetchSize: Int = min((maxPosition - startPosition).toInt, adjustedMaxSize)
  
      FetchDataInfo(offsetMetadata, log.slice(startPosition, fetchSize),
        firstEntryIncomplete = adjustedMaxSize < startOffsetAndSize.size)
    }
  ```

  ![image-20201119230951435](assets/image-20201119230951435.png)

- Recover方法

  - kafka在broker启动时会从磁盘上加载所有的日志段信息到内存中，并创建相应的LogSegment对象实例

  ```scala
   def recover(producerStateManager: ProducerStateManager, leaderEpochCache: Option[LeaderEpochFileCache] = None): Int = {
      offsetIndex.reset()
      timeIndex.reset()
      txnIndex.reset()
      var validBytes = 0
      var lastIndexEntry = 0
      maxTimestampSoFar = RecordBatch.NO_TIMESTAMP
      try {
        for (batch <- log.batches.asScala) {
          batch.ensureValid()
          ensureOffsetInRange(batch.lastOffset)
  
          // The max timestamp is exposed at the batch level, so no need to iterate the records
          if (batch.maxTimestamp > maxTimestampSoFar) {
            maxTimestampSoFar = batch.maxTimestamp
            offsetOfMaxTimestampSoFar = batch.lastOffset
          }
  
          // Build offset index
          if (validBytes - lastIndexEntry > indexIntervalBytes) {
            offsetIndex.append(batch.lastOffset, validBytes)
            timeIndex.maybeAppend(maxTimestampSoFar, offsetOfMaxTimestampSoFar)
            lastIndexEntry = validBytes
          }
          validBytes += batch.sizeInBytes()
  
          if (batch.magic >= RecordBatch.MAGIC_VALUE_V2) {
            leaderEpochCache.foreach { cache =>
              if (batch.partitionLeaderEpoch >= 0 && cache.latestEpoch.forall(batch.partitionLeaderEpoch > _))
                cache.assign(batch.partitionLeaderEpoch, batch.baseOffset)
            }
            updateProducerState(producerStateManager, batch)
          }
        }
      } catch {
        case e@ (_: CorruptRecordException | _: InvalidRecordException) =>
          warn("Found invalid messages in log segment %s at byte offset %d: %s. %s"
            .format(log.file.getAbsolutePath, validBytes, e.getMessage, e.getCause))
      }
      val truncated = log.sizeInBytes - validBytes
      if (truncated > 0)
        debug(s"Truncated $truncated invalid bytes at the end of segment ${log.file.getAbsoluteFile} during recovery")
  
      log.truncateTo(validBytes)
      offsetIndex.trimToValidSize()
      // A normally closed segment always appends the biggest timestamp ever seen into log segment, we do this as well.
      timeIndex.maybeAppend(maxTimestampSoFar, offsetOfMaxTimestampSoFar, skipFullCheck = true)
      timeIndex.trimToValidSize()
      truncated
    }
  ```

  ![image-20201119231648756](assets/image-20201119231648756.png)

  ![image-20201119231921067](assets/image-20201119231921067.png)

- 