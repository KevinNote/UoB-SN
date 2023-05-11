# 0x01 VPN

## Virtual Private Networks

匿名性Anonymity

为了获得一些匿名性，您可以通过 VPN 路由所有流量。
- 服务器认为您是 VPN 提供商
- ISP 只看到与 VPN 的连接
- 全球观察员（global observer）可能会链接您的连接。
- 对 VPN 提供商：没有匿名性

![[img/wifi-q.png]]

![[img/vpn-1.png]]

![[img/website-q.png]]


## 洋葱路由 Onion Routing

通过多个代理路由您的流量，您可以获得最好的匿名性。
- Onion Routing 确保您的消息确实通过您想要的代理进行路由。
- Tor 网络正在使用此协议


每个proxy只知道它前面的proxy和后面的proxy的IP。
• 每个代理的公钥是已知的。
• SrcIP 对第一个节点可见，DestIP 对最后一个节点可见。
• 用户选择 3 个代理（入口、中间和出口节点）并且只要它们不是全部损坏（corrupt）就是匿名的。

![[img/tor.png]]

### Hidden Service

![[img/Pasted image 20230511004312.png]]

Bob 选择 Introduction Pts 并构建回环

![[img/Pasted image 20230511004342.png]]

Bob 发布服务（包含 Intro PK）

![[img/Pasted image 20230511004420.png]]

Alice 查询服务，并构建 Rendezvous Pt

![[img/Pasted image 20230511004451.png]]

发送讯息与OTS 给Intro Pt

![[img/Pasted image 20230511004536.png]]
传递 OTS

![[img/Pasted image 20230511004555.png]]

构建回环

