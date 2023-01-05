---
title: IP数据包协议字段解释
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# IP数据包协议字段解释

参考资料:

[IP协议(Protocol)字段释义](https://blog.csdn.net/zhenyi2000/article/details/79795140)

[Protocol Numbers](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)

|数值|值描述|
|----|-----|
|0|保留字段，用于IPv6(跳跃点到跳跃点选项)|
|1|Internet控制消息 (ICMP)|
|2|Internet组管理 (IGMP)|
|3|网关到网关 (GGP)|
|4|1P中的IP(封装)  |
|5|流|
|6|传输控制 (TCP)|
|7|CBT|
|8|外部网关协议 (EGP)|
|9|任何私有内部网关(Cisco在它的IGRP实现中使用) (IGP)|
|10|BBNRCC监视|
|11|网络语音协议|
|12|PUP|
|13|ARGUS|
|14|EMCON|
|15|网络诊断工具|
|16|混乱(Chaos)|
|17|用户数据报文 (UDP)|
|18|复用|
|19|DCN测量子系统|
|20|主机监视|
|21|包无线测量|
|22|XEROXNSIDP|
|23|Trunk-1|
|24|Trunk-2|
|25|leaf-1|
|26|1eaf-2|
|27|可靠的数据协议|
|28|Internet可靠交易|
|29|1SO传输协议第四类 (TP4)|
|30|大块数据传输协议|
|31|MFE网络服务协议|
|32|MERIT节点之间协议|
|33|序列交换协议|
|34|第三方连接协议|
|35|域之间策略路由协议|
|36|XTP|
|37|数据报文传递协议|
|38|IDPR控制消息传输协议|
|39|TP+ +传输协议|
|40|IL传输协议|
|41|1Pv6|
|42|资源命令路由协议|
|43|1Pv6的路由报头|
|44|1Pv6的片报头|
|45|域之间路由协议|
|46|保留协议|
|47|通用路由封装|
|48|可移动主机路由协议|
|49|BNA|
|50|1Pv6封装安全有效负载|
|51|1Pv6验证报头|
|52|集成的网络层安全TUBA|
|53|带加密的IP|
|54|NBMA地址解析协议|
|55|IP可移动性|
|56|使用Kryptonet钥匙管理的传输层安全协议|
|57|SKIP|
|58|1Pv6的ICMP|
|59|1Pv6的无下一个报头|
|60|IPv6的信宿选项|
|61|任何主机内部协议|
|62|CFTP|
|63|任何本地网络|
|64|SATNET和BackroomEXPAK|
|65|Kryptolan|
|66|MIT远程虚拟磁盘协议|
|67|Internet Pluribus包核心|
|68|任何分布式文件系统|
|69|SATNET监视|
|70|VISA协议|
|71|Internet包核心工具|
|72|计算机协议Network Executive|
|73|计算机协议Heart Beat|
|74|Wang Span网络|
|75|包视频协议|
|76|Backroom SATNET监视|
|77|SUN ND PROTOCOL—临时|
|78|WIDEBAND监视|
|79|WIDEBAND EXPAK|
|80|ISO Internet协议|
|81|VMTP|
|82|SECURE—VMTP(安全的VMTP)|
|83|VINES|
|84|TTP|
|85|NSFNET—IGP|
|86|不同网关协议|
|87|TCF|
|88|EIGRP|
|89|OSPF IGP|
|90|Sprite RPC协议|
|9]|Locus地址解析协议|
|92|多播传输协议|
|93|AX.25帧|
|94|IP内部的IP封装协议|
|95|可移动网络互连控制协议|
|96|旗语通讯安全协议|
|97|IP中的以太封装|
|98|封装报头|
|99|任何私有加密方案|
|100|GMTP|
|101|Ipsilon流量管理协议|
|102|PNNI over IP|
|103|协议独立多播|
|104|ARIS|
|105|SCPS|
|106|QNX|
|107|活动网络|
|108|IP有效负载压缩协议|
|109|Sitara网络协议|
|110|Compaq对等协议|
|111|IP中的IPX|
|112|虚拟路由器冗余协议|
|113|PGM可靠传输协议|
|114|任何0跳跃协议|
|115|第二层隧道协议|
|116|D-II数据交换(DDX)|
|117|交互式代理传输协议|
|118|日程计划传输协议|
|119|SpectraLink无线协议|
|120|UTI|
|121|简单消息协议|
|122|SM|
|123|性能透明性协议|
|124|ISIS over IPv4|
|125|FIRE|
|126|Combat无线传输协议|
|127|Combat无线用户数据报文|
|128|SSCOPMCE|
|129|IPLT|
|130|安全包防护|
|131|IP中的私有IP封装|
|132|流控制传输协议|
|133～254|未分配|
|255|保留|
