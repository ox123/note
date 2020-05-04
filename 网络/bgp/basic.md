### BGP
- 使用TCP保证可靠性传输，端口：179
- BGP路由器之间建立TCP连接，这些路由器成为BGP对等体，也叫BGP邻居，有两种关系： EBGP（External BGP），IBGP(internal BGP)
- 对等体关系建立后，BGP路由器只发送增量更新或触发更新