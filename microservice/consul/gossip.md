### Consul架构-Gossip协议
Consul利用两个不同的gossip pool。我们分别把他们称为局域网池(LAN Pool)或广域网池(WAN Pool)。每个Consul数据中心都有一个包含所有成员（Server和Client）的LANgossip pool。LAN Pool有如下几个目的：首先，成员关系允许Client自动发现Server节点，减少所需的配置量。然后，分布式故障检测允许的故障检测的工作在某几个Server几点执行，而不是集中整个集群所有节点上。最后，gossip允许可靠和快速的事件广播，比如，Leader选举。

WAN Pool是全局唯一的，无论属于哪一个数据中心，所有Server应该加入到WAN Pool。由WAN Pool提供会员信息让Server可节电执行跨数据中心的请求。集成中故障检测允许Consul妥善处理整个数据中心失去连接，或在远程数据中心只是单个的Server节点。

所有这些功能都是通过利用Serf提供。从用户角度来看，它是作为一个嵌入式库提供这些功能。但其被Consul屏蔽，用户无需关心。作为开发人员可以去了解这个库是如何利用。

### serf 中去中心化系统的原理和实现
https://www.infoq.cn/article/principle-and-impleme-of-de-centering-system-in-serf