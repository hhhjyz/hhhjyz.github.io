# The  Hardware/Software Interface
## 1 Introduction
- I/O设计考虑多方面的因素
- 度量I/O系统的表现是非常困难的
- I/O系统的性能依赖于设备和系统之间的链接，内存层次，操作系统

> [!note] Typical connection of I/O device
> ![600](assets/Chapter%206/file-20241210210521927.png)

***IO的三个特性:***
- Behavior: Input, Output ,or storage
- Partner: 读取数据或者输入数据的人/设备
- Data rate: 数据在I/O设备和主存或者处理器之间的最高传输速率

IO 性能依赖于应用：
- Throughput:  I/O bandwidth
	- 一定时间内系统可以运输多少数据
	- 单位时间内多少I/O操作可以进行
- Response time:当I/O请求非常大时，响应时间严重依赖于带宽，但是许多情况下大多数访问的数据量很小，所以单个访问操作延迟时间很短的I/O系统将获得最快的相应时间
- both throughput and response time ：比如ATM

### 1.1 Amdahl's law
Sequential part can limit speedup 
$$T_{new}=T_{paralized}/{n_{processors}+T_{sequential}}$$
$$Speed~up=\frac{1}{(1-F_{parallelizable}+F)}$$
## 2 Disk Storage and Dependability
磁盘的两种主要类型：
- floppy disks: 
- hard disks:larger, higher density , higher data rate , more than one platter

	磁盘存储是非易失性的
### 2.1 The organization of hard disk

一个磁盘包括一组盘片（platters），每张盘片有课记录的两个盘面。每个盘表面被分为许多被称为轨道(tracks)的同心圆,每条轨道又被分成一些记录信息的扇区（sectors），在磁介质中一次存放着扇区号、间隔、带有纠错码的扇区信息、间隔、下一个扇区的 扇区号······

### 2.2 加载disk中的数据
寻道(Seek):将磁头单位到正确的轨道上，磁头找到正确轨道的时间被称为寻道时间（Seek Time）

旋转延迟（Rotational latency):等待正确扇区转到读写头下的时间，取得所要信息的平均时延是磁盘旋转半周所需的时间

传输时间（transfer）：传输一块（一般是一个扇区）需要的时间，传输时间是扇区大小、旋转速度、磁道记录密度的函数

磁盘控制器（disk controller）：通常用来精细地控制磁盘以及磁盘与内存之间的数据传输，其存在是的磁盘存取时间又多了一项**控制器时间(controller time)**

!!! example 读磁盘时间
	![400](assets/Chapter%206/40a6db8bd0711d6b4d1dec4ac32ea60.jpg)
	



### 2.3 flash storage

#### 2.3.1 flash types

### 2.4 Disk performance Issues

***Dependability:***

***Reliability:***

***Availability：***

#### 2.4.1 Measure
- MTTF 平均无故障时间
- MTTR 平均修复时间
- MTBF=MTTF+MTTR平均故障间隔时间
- Availability=$\frac{MTTF}{MTTF+MTTR}$

***提升MTTF的三种方式：***
- Fault avoidance:通过结构来预防fault的发生
- Fault tolerance:使用冗余来允许服务规范，尽管发生故障，应用于硬件错误
- Fault forecasting:预测错误，应用于软硬件错误

#### 2.4.2 Redundant arrays of disks
- 文件被存储在多个硬盘中
- 增加校验盘/冗余盘，来提高存储的可依赖性
- 

使用一系列小的磁盘来提高存储的可依赖性

>[!note] Array Reliability
>![600](assets/Chapter%206/file-20241211102036027.png)

>[!example] a disk arrays repalce lager disk
>![](assets/Chapter%206/file-20241211102551066.png)

#### 2.4.3 RAID: Redundant Arrays of Inexpensive Disks
##### 2.4.3.1 RAID 0: No Redundancy

##### 2.4.3.2 RAID 3: 位交叉校验盘
问题在于只读一个数据时无法判断数据正确与否，所以要把一行的数据都读取出来
写入之后要重新计算校验盘的数据

##### 2.4.3.3 RAID 4: Block-Interleaved Parity
把自己每个block做了校验位，放到备份盘中

## 3 Buses and Other Connections between Processors Memory, and I/O Devices
**bus:** 共享通信的链接

**Difficult Design:**

- may be bottleneck  
    很容易成为性能的 bottleneck(瓶颈).
- length of the bus
- number of devices
- tradeoffs (fast bus accesses and high bandwidth)
- support for many different devices
- cost

### 3.1 Bus Basics
 总线包含两种线： 
 - Control lines:  信号的请求和回应，指示data lines中有什么类型的数据
 - Data lines: 携带源与目标之间的数据，地址，复杂指令

**Bus transaction**  
sending the address and receiving or sending the data  
总线传输的两部分，送地址，送数据

> [!example] Output and Input operation
> ![600](assets/Chapter%206/file-20241211110255245.png)
> **input operation:从设备输入数据到内存中**
> ![600](assets/Chapter%206/file-20241211110305523.png)
> **output operation: 把处理器写入内存的数据输出到设备上**

### 3.2 Types of Buses
- Processor-memory bus 处理器-内存总线: 长度短，高速，定制
- backplane bus: 高速，标准化，使处理器、内存和I/O设备能够同时接在单条总线上的总线
- I/O总线: 长的，链接不同设备， 标准化

### 3.3 Synchronous vs. Asynchronous

- **Synchronous bus**: use a clock and a fixed _protocol_, fast and small but every device must operate at same rate and clock skew requires the bus to be short  
    所有设备需要以同样的频率。  
    clock skew, 即上升沿无法对齐
- **Asynchronous bus**: don’t use a clock and instead use _handshaking_  
    A serial of steps used to coordinate asynchronous bus transfers

>[!example] 异步总线是如何工作的
>![](http://cdn.hobbitqia.cc/202306231653192.png)
>1. When memory sees the ReadReq line, it reads the address from the data bus, begin the memory read operation，then raises Ack to tell the device that the ReadReq signal has been seen.
>2. I/O device sees the Ack line high and releases the ReadReq data lines.
>3. Memory sees that ReadReq is low and drops the Ack line.
>1. When the memory has the data ready, it places the data on the data lines and raises DataRdy.
>2. The I/O device sees DataRdy, reads the data from the bus , and signals that it has the data by raising ACK.
>3. The memory sees Ack signals, drops DataRdy, and releases the data lines.
>4. Finally, the I/O device, seeing DataRdy go low, drops the ACK line, which indicates that the transmission is completed.
>
>读数据时, CPU 把 read request 拉起来。内存看到后，会把 Data 总线上的读走（即地址），随后进行内存读取，同时把 Ack 信号拉起来，告诉 IO 设备我们已经接收到 read request 了. IO 设备看到 Ack 后把自己的 read request 放下，内存看到 read request 放下后，把 Ack 也放下。
>  
>内存读出数据后，会把 data ready 拉起来，把数据放在 data line 上. IO 设备看到 data ready 后会把数据取走，并把 Ack 信号拉起。内存看到 Ack 信号后会放下 data ready 信号，随后 IO 设备放下 ack 信号。

