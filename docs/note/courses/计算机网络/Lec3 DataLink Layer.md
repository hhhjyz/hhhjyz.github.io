# The Data Link Layer

## 1 Overview

三个主要功能：
- Framing: 把bits分割为frames
- Error Control: 通过**确认机制 (Acknowledgement)**、**计时器 (timer)** 和 **序列号 (sequence numbers)** 实现
- Flow control: 如何防止发送者向接送者发送过多数据包
	- Feedback-based flow control
	- Rated-based flow control

Data link layer的功能是为Network layer提供服务


在一个路由器Router中，数据链路层在line card 上实现。总的来说，链路层通常在网卡（也称为网络接口卡，NIC）的网络适配器中实现

## 2 Framing

数据链路层从网络层获取数据包，并将它们封装成帧以进行传输。

![](assets/Pasted%20image%2020251010133343.png)

为每个帧计算一个 checksum用于标记，在传输时把这个也包括进去。当到达目的地时会重新计算checksum来进行校验

### 2.1 Byte Count

在header字段中使用一部分字段来指定bytes in the frame

### 2.2 Flag Byte with Byte Stuffing

在每个frame的开头和结尾使用特殊字节标记，

![](assets/Pasted%20image%2020251010135724.png)

但是flag byte 可能出现在数据中，这个时候需要在数据中的flag byte前面加入一个ECS

#### 2.2.1 bit stuffing

HDLC protocol
![](assets/Pasted%20image%2020251010135924.png)

### 2.3 Coding Violations

采用位填充和字节填充时，一个副作用是帧的长度现在取决于其承载数据的內容。

在物理层，将比特编码为信号通常包含冗余以帮助接收器。

“violation” 不是指发生了错误，而是一种**巧妙的设计**。它通过发送一个不符合标准数据编码规则的“非法”信号，来充当数据帧的边界分隔符，从而帮助接收方准确地从连续的比特流中分离出独立的数据帧。

## 3 Error Control

Error-Correcting-Codes:一种策略是包含足够冗余信息，以便接收者能够推断出传输的数据本应是怎样的

Error-Detecting Codes:另一种方法是在其中包含足够的冗余信息，使接收者能够推断出发生了错误（但无法确定是哪种错误），并请求重新传输

但是这两种方法都无法处理所有可能的错误

分组码：一个帧由m个数据（或信息）位和r个冗余（或校验）位组成。r个校验位仅根据与其关联的m个数据位计算得出。