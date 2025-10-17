# Lec1
mac 地址48位， ipv4地址32位， ipv6地址128bits

子网掩码可以判断哪些机器在同一子网内

默认网关，子网内的最小地址，

全0地址是机器进入新网络环境中未分配ip地址的初始化地址

全1地址是广播地址
## 1 Network hardware

两个重要的维度： transmission technology and scale

**transmission technology**:
- Broadcast links(Multicasting 多播),通信通道由网络上的所有机器共享，每个数据包内的地址字段指定了预期的接收者，通过在地址字段中使用特殊代码，可以将数据包发送到所有目的地。
- Point to point links（Unicasting 单播）， 链接了一对机器，在由点对点链路组成的网络上，短消息（在某​​些情况下称为数据包）要从源到达目的地，可能必须首先访问一个或多个中间机器。通常存在多条不同长度的路由，因此在点对点网络中找到合适的路由非常重要

![](assets/Pasted%20image%2020250916104653.png)

不同物理类型的连接方式：
- twisted pair
- coaxial cable(TV)
- Fiber optics
- radio spectrum(cellphone)

**transmission rate:** in bits/second

### 1.1 LAN

根据信道分配方式，无线和有线广播网络均可分为静态和动态设计

static: time slot(TDM) and FDM

公共渠道的动态分配方法有两种：
集中式或分散式。在集中式信道分配方法中，只有一个实体（例如蜂窝网络中的基站）来决定下一个信道分配者。在去中心化信道分配方法中，没有中央实体；每台机器必须自行决定是否传输。

### 1.2 MAN(Metropolitan Area Network)

最典型的例子是有线电视

无线例子： IEEE 802.16 WiMax 不成功案例

### 1.3 WAN（wide area network）

覆盖很大的区域

通过 transmission lines and switching elements 组成

由很多小的网络组成

两种类型：
- VPN(Virtual Private network)
- ISP(Internet Service Provider)
	- The routing algorithm, 选择通道
	- The forwarding algorithm， 


![](assets/Pasted%20image%2020250916122233.png)![](assets/Pasted%20image%2020250916122251.png)



**分组交换**：
- 电路交换， 例如传统电话网
- 报文交换，用户数据加上源与目的地址之后封装成报文。采用存储转发技术