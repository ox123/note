#### Message 存储

http://www.spring4all.com/article/3380

- RocketMQ 消息存储流程 https://www.kunzhao.org/blog/2018/03/12/rocketmq-message-store-flow/

- RocketMQ高性能之底层存储设计 https://www.jianshu.com/p/d06e9bc6c463

- dirty page,当程序顺序写文件时，首先写到Cache中，这部分被修改过，但却没有被刷进磁盘，产生了不一致，这些不一致的内存叫做脏页（Dirty Page）。