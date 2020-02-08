### netfilter
http://www.cnblogs.com/LittleHann/p/3708222.html
1. Netfilter是Linux操作系统核心层内部的一个数据包处理模块，它具有如下功能:
   - 网络地址转换(Network Address Translate)
   - 数据包内容修改
   - 以及数据包过滤的防火墙功能

> Netfilter平台中制定了五个数据包的挂载点(Hook Point，我们可以理解为回调函数点，数据包到达这些位置的时候会主动调用我们的函数，使我们有机会能在数据包路由的时候有机会改变它们
的方向、内容)，这5个挂载点分别是

    - PRE_ROUTING
    - INPUT
    - OUTPUT
    - FORWARD
    - POST_ROUTING

### iptables

- Netfilter所设置的规则是存放在内核内存中的，Iptables是一个应用层(Ring3)的应用程序，它通过Netfilter放出的接口来对存放在内核内存中的Xtables(Netfilter的配置表)进行修改
(这是一个典型的Ring3和Ring0配合的架构)
- Netfilter是负责实际的数据流改变工作的内核模块，而Xtables就是它的规则配置文件，Netfilter依照Xtables的规则来运行，Iptables在应用层负责修改这个规则文件