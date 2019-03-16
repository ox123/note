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

