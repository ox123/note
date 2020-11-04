### AdminClient

1. 主题管理：包括主题的创建、删除和查询。
2. 权限管理：包括具体权限的配置与删除。
3. 配置参数管理：包括 Kafka 各种资源的参数设置、详情查询。所谓的 Kafka 资源，主要有 Broker、主题、用户、Client-id 等。
4. 副本日志管理：包括副本底层日志路径的变更和详情查询。
5. 分区管理：即创建额外的主题分区。
6. 消息删除：即删除指定位移之前的分区消息。
7. Delegation Token 管理：包括 Delegation Token 的创建、更新、过期和详情查询。
8. 消费者组管理：包括消费者组的查询、位移查询和删除。
9. Preferred 领导者选举：推选指定主题分区的 Preferred Broker 为领导者。

**工作原理**

- **从设计上来看，AdminClient 是一个双线程的设计：前端主线程和后端 I/O 线程**。前端线程负责将用户要执行的操作转换成对应的请求，然后再将请求发送到后端 I/O 线程的队列中；而后端 I/O 线程从队列中读取相应的请求，然后发送到对应的 Broker 节点上，之后把执行结果保存起来，以便等待前端线程的获取。

<img src="assets/image-20201102212707561.png" alt="image-20201102212707561" style="zoom:150%;" />

> Note: 后端 I/O 线程其实是有名字的，名字的前缀是  kafka-admin-client-thread。有时候我们会发现，AdminClient 程序貌似在正常工作，但执行的操作没有返回结果，或者 hang 住了，现在你应该知道这可能是因为 I/O 线程出现问题导致的。如果你碰到了类似的问题，不妨使用**jstack 命令**去查看一下你的 AdminClient 程序，确认下 I/O 线程是否在正常工作。
>
> 这可不是我杜撰出来的好处，实际上，这是实实在在的社区 bug。出现这个问题的根本原因，就是 I/O 线程未捕获某些异常导致意外“挂”掉。由于 AdminClient  是双线程的设计，前端主线程不受任何影响，依然可以正常接收用户发送的命令请求，但此时程序已经不能正常工作了。

- 创建主题

  ```java
  String newTopicName = "test-topic";
  
  try (AdminClient client = AdminClient.create(props)) {
           NewTopic newTopic = new NewTopic(newTopicName, 10, (short) 3);
           CreateTopicsResult result = client.createTopics(Arrays.asList(newTopic));
           result.all().get(10, TimeUnit.SECONDS);
  }
  ```

- 查询消费者组位移

  ```java
  String groupID = "test-group";
  try (AdminClient client = AdminClient.create(props)) {
           ListConsumerGroupOffsetsResult result = client.listConsumerGroupOffsets(groupID);
           Map<TopicPartition, OffsetAndMetadata> offsets = 
                    result.partitionsToOffsetAndMetadata().get(10, TimeUnit.SECONDS);
           System.out.println(offsets);
  }
  ```

- 查看磁盘占用

  ```java
  try (AdminClient client = AdminClient.create(props)) {
  
           DescribeLogDirsResult ret = client.describeLogDirs(Collections.singletonList(targetBrokerId)); // 指定 Broker id
           long size = 0L;
           for (Map<String, DescribeLogDirsResponse.LogDirInfo> logDirInfoMap : ret.all().get().values()) {
                    size += logDirInfoMap.values().stream().map(logDirInfo -> logDirInfo.replicaInfos).flatMap(
                             topicPartitionReplicaInfoMap ->
                             topicPartitionReplicaInfoMap.values().stream().map(replicaInfo -> replicaInfo.size)).mapToLong(Long::longValue).sum();
           }
           System.out.println(size);
  
  }
  ```

### 认证

- 英文是 authentication，是指通过一定的手段，完成对用户身份的确认。认证的主要目的是确认当前声称为某种身份的用户确实是所声称的用户。

### 授权

- 英文是 authorization。授权一般是指对信息安全或计算机安全相关的资源定义与授予相应的访问权限。

### Kafka 认证机制

**Kafka 支持的 SASL 机制有 5 种**

1. GSSAPI：也就是 Kerberos 使用的安全接口，是在 0.9 版本中被引入的。
   - GSSAPI 适用于本身已经做了 Kerberos 认证的场景，这样的话，SASL/GSSAPI 可以实现无缝集成。
2. PLAIN：是使用简单的用户名 / 密码认证的机制，在 0.10 版本中被引入。
   - SASL/PLAIN 的配置和运维成本相对较小，适合于小型公司中的 Kafka 集群
3. SCRAM：主要用于解决 PLAIN 机制安全问题的新机制，是在 0.10.2 版本中被引入的。
4. OAUTHBEARER：是基于 OAuth 2 认证框架的新机制，在 2.0 版本中被引进。
5. Delegation Token：补充现有 SASL 机制的轻量级认证机制，是在 1.1.0 版本被引入的。



### 监控

**要做到 JVM 进程监控，有 3 个指标需要你时刻关注**

1. Full GC 发生频率和时长。这个指标帮助你评估 Full GC 对 Broker 进程的影响。长时间的停顿会令 Broker 端抛出各种超时异常。
2. 活跃对象大小。这个指标是你设定堆大小的重要依据，同时它还能帮助你细粒度地调优 JVM 各个代的堆大小。
3. 应用线程总数。这个指标帮助你了解 Broker 进程对 CPU 的使用情况。

**集群监控**

1. 查看 Broker 进程是否启动，端口是否建立。
2. 查看 Broker 端关键日志
3. 查看 Broker 端关键线程的运行状态
   - Log Compaction 线程，这类线程是以 ** kafka-log-cleaner-thread** 开头的。就像前面提到的，此线程是做日志 Compaction 的。一旦它挂掉了，所有 Compaction 操作都会中断，但用户对此通常是无感知的。
   - 副本拉取消息的线程，通常以 **ReplicaFetcherThread 开头**。这类线程执行 Follower 副本向 Leader  副本拉取消息的逻辑。如果它们挂掉了，系统会表现为对应的 Follower 副本不再从 Leader 副本拉取消息，因而 Follower 副本的 Lag 会越来越大。
4. 查看 Broker 端的关键 JMX 指标
   - BytesIn/BytesOut：即 Broker 端每秒入站和出站字节数。你要确保这组值不要接近你的网络带宽，否则这通常都表示网卡已被“打满”，很容易出现网络丢包的情形。
   - NetworkProcessorAvgIdlePercent：即网络线程池线程平均的空闲比例。通常来说，你应该确保这个 JMX  值长期大于 30%。如果小于这个值，就表明你的网络线程池非常繁忙，你需要通过增加网络线程数或将负载转移给其他服务器的方式，来给该 Broker  减负。
   - RequestHandlerAvgIdlePercent：即 I/O 线程池线程平均的空闲比例。同样地，如果该值长期小于 30%，你需要调整 I/O 线程池的数量，或者减少 Broker 端的负载。
   - UnderReplicatedPartitions：即未充分备份的分区数。所谓未充分备份，是指并非所有的 Follower 副本都和  Leader 副本保持同步。一旦出现了这种情况，通常都表明该分区有可能会出现数据丢失。因此，这是一个非常重要的 JMX 指标。
   - ISRShrink/ISRExpand：即 ISR 收缩和扩容的频次指标。如果你的环境中出现 ISR 中副本频繁进出的情形，那么这组值一定是很高的。这时，你要诊断下副本频繁进出 ISR 的原因，并采取适当的措施。
   - ActiveControllerCount：即当前处于激活状态的控制器的数量。正常情况下，Controller 所在 Broker  上的这个 JMX 指标值应该是 1，其他 Broker 上的这个值是 0。如果你发现存在多台 Broker 上该值都是 1  的情况，一定要赶快处理，处理方式主要是查看网络连通性。这种情况通常表明集群出现了脑裂。脑裂问题是非常严重的分布式故障，Kafka 目前依托  ZooKeeper 来防止脑裂。但一旦出现脑裂，Kafka 是无法保证正常工作的。
5. 监控 Kafka 客户端
   - 客户端与broker端时延
   - 发送消息线程： kafka-producer-network-thread
   - 心跳线程： kafka-coordinator-heartbeat-thread
   -  Producer 角度，需要关注的 JMX 指标是 request-latency，即消息生产请求的延时
   -  Consumer 角度来说，records-lag 和 records-lead 是两个重要的 JMX 指标
6. 

