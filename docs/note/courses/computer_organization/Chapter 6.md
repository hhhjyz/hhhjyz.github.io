# The  Hardware/Software Interface
## 1 Introduction
- I/O设计考虑多方面的因素
    - 可扩展性（Expandability）
    - 恢复力（Resilience）
    - 性能（Performance）

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
**寻道(Seek)**:将磁头单位到正确的轨道上，磁头找到正确轨道的时间被称为寻道时间（Seek Time）

**旋转延迟（Rotational latency)**:等待正确扇区转到读写头下的时间，取得所要信息的平均时延是磁盘旋转半周所需的时间,即$\frac{0.5转}{转速}$

**传输时间（transfer）**：传输一块（一般是一个扇区）需要的时间，传输时间是扇区大小、旋转速度、磁道记录密度的函数

**磁盘控制器（disk controller）**：通常用来精细地控制磁盘以及磁盘与内存之间的数据传输，其存在是的磁盘存取时间又多了一项**控制器时间(controller time)**

read time=seek time + rotational latency + transfer time + controller time

!!!  example "读磁盘时间"
    === "question"
    	![400](assets/Chapter%206/40a6db8bd0711d6b4d1dec4ac32ea60.jpg)
    === "answer" 
        $t=6.0ms+\frac{0.5r}/{10000r/min}+\frac{0.5KB}{50MB/s}+0.2ms=9.2ms$



### 2.3 flash storage

闪存具有如下特性：

- 是一种非易失性（即断电数据也不会丢失）、电子可擦除、可编程只读的半导体存储器（EEPROM） 
- 比磁盘快百倍到千倍
- 更小型、耗电更小、但是也更贵，介于磁盘和DRAM
- 写操作可能会磨损存储器内的树
    - 在1000次访问后数据位会有所磨损
    - 因此它不适合作为RAM/DISK  的替代
    - 为减缓磨损，闪存会用一个控制器来spread 写操作到被更少使用的区域，这被称为（wear leveling）

#### 2.3.1 flash types

- NOR flash :每一个cell像是一个或非门
    - 随机读写更快
    - 通常用于存储嵌入式系统的指令
-  NAND flash : 每一个cell像是一个与非门
    - 更为密集，但是以块为单位进行操作
    - 更便宜
    - 通常用于存储USB密钥、媒体数据等等



### 2.4 Disk performance Issues

- 局部性和OS规划，控制器，利用cache技术加速

**系统传递给用户的服务（Service）有两种状态：**
- 服务实现（Service Accomplishment）： 传递的服务与指定的服务相同
- 服务中断（Service interrupt）： 传递的服务与指定的服务不同

从服务实现到服务中断的过程称为**失败**（Failures），从服务中断到服务实现的过程称为**恢复**（Restorations）。失败可以分为永久性的（Permenant）和间歇性的（Intermittent），其中前者较好诊断出来，而后者由于会在两个状态间振荡，因此不太好诊断。由此，我们引出了以下相关术语：

***Dependability（可依赖性）:***提供服务的质量

每个模块都有一个理想的指定行为，其中服务说明应是对期望行为广泛认同的描述，当实际行为偏离指定行为时，系统发生错误


***Reliability（可靠性）:***

是连续服务时间的量度，或者从参照点至出现故障所需时间的量度，磁盘平均故障时间（MTTF）是一个可靠性的量度

***Availability（可用性）：***

如果说服务在是实现和中断两个状态之间变化的化，那么可用性为服务实现的量度。
$$可用性=\frac{MTTF}{MTTF=MTTR}$$

#### 2.4.1 Measure
- MTTF 平均无故障时间
- MTTR 平均修复时间
- MTBF=MTTF+MTTR平均故障间隔时间
- Availability=$\frac{MTTF}{MTTF+MTTR}$

- Fault(错误)：系统中某个部件(Component)的失败

***提升MTTF的三种方式：***

- Fault avoidance:通过结构来预防fault的发生
- Fault tolerance:使用冗余技术允许服务在错误发生时仍能照常工作，这里错误主要指硬件错误
- Fault forecasting:预测错误的存在和创造，允许部件在失败之气那被替换，应用于软硬件错误

#### 2.4.2 Redundant arrays of disks

**RAID**：独立/廉价硬盘冗余阵列 (Redundant Arrays of Inexpensive/Independent Disks)

- 用硬盘阵列取代一个容量更大的硬盘
- 文件被存储在多个硬盘中
- 增加校验盘/冗余盘，来提高存储的可依赖性
- 文件会分散存储在多个硬盘（形成一个阵列）内
- 通过冗余（Redundancy）得到更高的数据可用性（即使某个磁盘坏了，我们还可以从别的硬盘中获取文件）
- 冗余的代价：
    - 容量损失：存储冗余信息
    - 带宽损失：更新冗余信息


使用一系列小的磁盘来提高存储的可依赖性

$Reliability~of~N~disks = Reliability~of~1~Disk/N$

!!! example RAID
    ![](assets/Chapter%206/file-20241227204430205.png){width="400"}
     ![](assets/Chapter%206/file-20241227204654903.png){width="400"}

!!! warning  "注意"
    不能同时对一个磁盘进行多个操作，因为磁盘是通过旋转······来实现访问的，例如之后会提到的对一个校验磁盘不能同时进行写或者读操作，构成了瓶颈


!!! info Types of RAID
    === "RAID 0"
    
    	RAID 0 不使用冗余
    	
    	- 虽然数据分散在硬盘阵列里，但是未采用冗余来应对硬盘故障
    	- 但是该方法对于大量的访问而言提升了表现，因为只需对磁盘操作一次
    
    === "RAID 1"
    	
    	RAID 1 为硬盘镜像 / 投影（Disk Mirroring/Shadowing）
    	
    	- 每个磁盘都有一份对应的完整拷贝，称为**镜像**（Mirror)，因而该方法具备很高的可用性
    	- 该方法牺牲了写操作的带宽：一次逻辑写操作 = 两次物理写操作；但是读操作可能得到优化
    	- 由于是“完整拷贝”，所以它需要额外 100% 的容量开销，因而是最昂贵的方法
    
    === "RAID 3"

        ![](assets/Chapter%206/file-20241227222109874.png)
    	RAID 3 为位交错奇偶校验硬盘（Bit-Interleaved Parity Disk）
    	
    	- 我们让某几个硬盘共用一个冗余的磁盘 P，P 的数据的每一位等于“这些硬盘对应位之和 mod 2”（即奇偶位（Parity））
    	- 如果某个硬盘出现故障，可以用“磁盘 P - 其他正常的硬盘的数据之和”来还原因故障遗失的信息
    
    === "RAID 4"
    
    	RAID 4 为块交错奇偶校验（Block-Interleaved Parity）
    	![](assets/Chapter%206/file-20241228175918807.png)
    	我们希望自己的盘有自己的错误检测，不需要校验盘来检验自己对不对，盘与盘之间没有依赖关系。事实上，硬盘的每个扇区都有一个错误检测字段
    	
    	
    	- 把自己每个 Block 做了校验位，放到备份盘中
    	- 该方法允许同步、独立地读取不同硬盘的数据,同一行也可以同时写，small write 有问题（不在同一行上）
    	- 对于小范围读操作而言，不需要去读parity位
    	- 采用小范围写算法：1 次逻辑写 = 2 次物理读 + 2 次物理写（每次写操作都会涉及到奇偶校验硬盘 P），成本较高。具体过程如下所示：
        ![](assets/Chapter%206/file-20241227223356078.png)
    === "RAID 5"
    
    	RAID 5 为高 I/O 速率交错奇偶校验（High I/O Rate Interleaved Parity）
    	![](assets/Chapter%206/file-20241228175659694.png)
    	- 交错的奇偶校验块
    	- 独立的读写操作
        - 校验信息在不同的磁盘上,这种组织方式使得多个写操作可以同时进行
    === "RAID 6"
    
    	RAID 6 为 P+Q 冗余校验
    	
    	有 P, Q 两位，当单次错误纠正仍然无法满足要求时，可以从数据中再次计算得到奇偶校验位，以进行额外的信息检查，可以恢复出两个盘的内容。二次校验块机制可使系统从二次错误中恢复过来，因此它的开销是RAID的两倍，进行小数据量写的时候要访问6个盘

## 3 Buses and Other Connections between Processors Memory, and I/O Devices

<div align=center><img src="../assets/Chapter%206/Pasted%20image%2020241211162651.png" alt="structure" width="50%" height="50%" ></div>


- Bus 总线： 共用的通信线路（包含一根或者多根线路）

**总线设计上的困难：**

- 总线长度和设备数量限制了最高总线速度
- 要支持一系列的延迟和数据传输率各不相同的设备
- 时钟偏差和信号反射的问题导致高速的并行线路很难设计

### 3.1 Bus Basics

- 总线包含两类线路：
    - 控制线路：信号的请求（Request）和确认（Acknowledgement），并表明数据线路上信息的类型
    - 数据线路：在源和目的之间传递信息（数据、地址、复杂命令等）

- 总线事务（Transaction）：发送地址，以及接收或发送数据
- 输出操作：
<div align=center><img src="../assets/Chapter%206/file-20241228190137615.png"></div>

- 输入操作：
<div align=center><img src="../assets/Chapter%206/file-20241228190847363.png" alt="Input operation" ></div>

- 总线类型：
    - 处理器 - 内存：较高的速度，个性化设计
    - 底板（Backplane）：高速，标准化设计（例如 PCI）
    - I/O：低速，不同设备，标准化（例如 SCSI）

<div align=center><img src="../assets/Chapter%206/file-20241228191133906.png" alt="Image 1" ></div>

<div align=center><img src="../assets/Chapter%206/file-20241228191146868.png" alt="Image 2"  ></div>

### 3.2 Synchronous vs Asynchronous

- 同步总线：使用**时钟**和固定的协议，快而小，但是所有设备需要保持相同的运作速率，且为避免时钟偏斜（Clock Skew）问题，总线需要短一些
    
- 异步总线：不用时钟，而采用**握手**协议（Handshaking Protocol）——一种用于协调异步总线传输的串行步骤，具体过程如下：
![](assets/Chapter%206/file-20241228191753259.png)

1. 当 `ReadReq` 处于高电平时，内存从数据总线中读取地址，执行读操作，然后抬高 `Ack` 的电平，告诉设备它看到了 `ReadReq` 信号
2. I/O 设备看到 `Ack` 处于高电平后，将 `ReadReq` 置于低电平状态
3. 内存看到 `ReadReq` 处于低电平后，将 `Ack` 置于低电平状态
4. 当内存完成数据读取后，它将数据放在数据线路上，并抬高 `DataRdy` 的电平
5. I/O 设备看到 `DataRdy` 处于高电平后，将从总线中读取数据，然后抬高 `Ack` 电平来表明 I/O 设备完成对数据的读取
6. 内存看到 `Ack` 处于高电平后，将 `DataRdy` 置于低电平状态
7. I/O 设备看到 `DataRdy` 处于低电平后，将 `Ack` 置于低电平状态，表明完成了整个传输过程

!!! example "二者性能比较"
    === "Question"

    	假设同步总线时钟周期为 50ns，每条总线的传输需要一个时钟周期；异步总线每次握手需要 40ns。两种总线的数据宽度都为 32 位
    	
    	求出每种总线从一个 200ns 的内存当中读取数据的带宽
    
    === "Answer"
    
    	=== "Synchronous"
    	
    		对于同步总线，它执行以下步骤：
    		
    		- 将地址送给内存：50ns
    		- 读取内存：200ns
    		- 将数据送回设备：50ns
    		
    		所以需要的总时间为 300ns，带宽 $\text{Bandwidth}=\frac{4\text{ Bytes}}{300ns}=13.3\text{ MB/s}$
    
    	=== "Asynchronous"
    	
    		对于异步总线来说，七个步骤有些可以同时进行，具体来说，Step1 结束时内存已经拿到地址了，这个过程中 Step234 可以同时做：
    		
    		- Step 1: 40ns
    		- Step 2,3,4: $\max(2\times 40ns+40ns, 200ns)=200ns$
    		- Step 5,6,7: $3\times 40ns=120ns$
    		
    		所以需要的总时间为 360ns，带宽 $\text{Bandwidth}=\frac{4\text{ Bytes}}{360ns}=11.1\text{ MB/s}$
    	
    	综上所述同步总线大约快 20%

### 3.3 Bus Arbitration

- 如果不加任何控制，许多设备都期望获得对控制 / 数据总线的控制权，以便实现自己这部分的数据传递，这样显然会出乱子
- 因此需要用一个**总线控制器**（Bus master）来发起和控制所有的总线请求

!!! example "单个控制器在总线事务中的初始化"
    ![](assets/Chapter%206/file-20241228193341428.png)
    ![](assets/Chapter%206/file-20241228193346993.png)

我们有以下方法进行总线仲裁：

- 菊式链（Daisy Chain）仲裁（并不是很公平）
- 集中（Centralized）并行仲裁（需要一个仲裁者，例如 PCI）
- 自选（Self Selection）（例如 Macintosh 用的 NuBus）
- 冲突检测（Collision Detection）（例如以太网）

为设备分配总线需要考虑的因素：

- 总线优先级
- 公平性（Fairness）

### 3.4 Increasing the Bancwidth

假设我们有一个具有如下特征的系统：

- 一个内存和总线系统，支持 4 到 16 字大小的块访问
- 一条 64 位同步总线，时钟频率为 200MHz，每次传输 64 位数据需要一个时钟周期，每次把地址传给内存需要一个时钟周期
- 每次总线操作之间需要两个时钟周期
- 从内存读取前 4 个字需要的时间为 200ns，每多读取 4 字需要 20ns，并且假设传输最近读取的数据和读取接下来的四个字操作可以同时进行

!!! question
    === "Question"

    	分别求出访问 4 字大小块用于传输和访问 16 字大小块用于传输时，读取 256 字数据的带宽和延迟。同时分别计算出每秒的有效总数事务数
    
    === "Answer"
    
    	=== "4 字大小块"
    	
    		每个块需要：
    		
    		- 1 个时钟周期把地址送给内存
    		- $\frac{200ns}{5ns/cycle}=40$ 个时钟周期来读取内存
    		- 2 个时钟周期把数据从内存送回来
    		- 在每次总线操作之间需要 2 个时钟周期
    		
    		总共 45 个时钟周期，而总共有 $\frac{256}{4}=64$ 个块
    		
    		所以传输 256 个字需要 $45\times 64=2880$ 个时钟周期
    		
    		传输 256 个字的延迟为 $2880Cycles\times(5ns/cycle)=14400ns$
    		
    		所以总线带宽为 $(256\times 4)Bytes\times\frac{1\text{ second}}{14400ns}=71.11MB/s$
    		
    		每秒的总线事务数为 $64\text{transactions}\times\frac{1\text{ second}}{14400ns}=4.44M\text{ transactions/s}$
    	
    	=== "16 字大小块"
    	![](assets/Chapter%206/file-20241228215241116.png)
    		第一个块需要：
    		
    		- 1 个时钟周期把地址送给内存
    		- $\frac{200ns}{5ns/cycle}=40$ 个时钟周期来读取内存
    		- 2 个时钟周期将数据传送回来，同时接下来 4 个字被读取
    		- 在每次总线操作之间需要 2 个时钟周期
    		
    		
    		根据上面的示意图，总共需要 $1+40+4\times 3+2+2=57$ 个时钟周期，而总共有 $\frac{256}{16}=16$ 个块
    		
    		所以传输 256 个字需要 $57\times 16=912$ 个时钟周期
    		
    		传输 256 个字的延迟为 $912Cycles\times(5ns/cycle)=4560ns$
    		
    		所以总线带宽为 $(256\times 4)Bytes\times\frac{1\text{ second}}{45600ns}=224.56MB/s$
    		
    		每秒的总线事务数为 $16\text{transactions}\times\frac{1\text{ second}}{4560ns}=3.51M\text{ transactions/s}$
    	
    	所以 16 字大小的块带宽比 4 字大小的块带宽高 3.16 倍

从上面例子我们可以得知，如果想要提高带宽，可以：

- 增大数据总线的宽度
- 使用分开的地址和数据块
- 传输多个字

## 4 Interfacing I/O Devices to the Memory, Processor, and Operating System

I/O 系统的三大特征：

- 多个程序共用（Share）I/O 系统
- 通常使用**中断**（Interrupt）来实现 I/O 操作的信息传递（Communicate）
    - 必需的传递类型有：
        - OS 必须能够为 I/O 设备提供**命令**（Commands）
            - 内存映射 I/O：部分的内存地址空间会被分配给 I/O 设备，且 `lw` 和 `sw` 指令能够访问 I/O 端口
            - 特殊 I/O 指令
                - 输入：`in al, port`
                - 输出：`out port, al`
            - 命令端口、数据端口
                - 状态寄存器（记录完成位（Done Bit）、错误位（Error Bit）等）
                - 数据寄存器、命令寄存器
        - 当 I/O 设备完成操作或遇到错误时，必须**通知**（Notify）OS
        - 数据必须在内存和 I/O 设备之间传输
- 对 I/O 设备的底层控制较为复杂

处理器与 I/O 设备之间的通信：

- **轮询**（Polling）：处理器周期性地检查状态位，观察当前是否需要进行下一步 I/O 操作
    - 缺点：浪费大量的处理器时间
- **中断**（Interrupt）
    
    - 中断驱动的 I/O 模式，中断用来通知处理器I/O设备需要处理器 的注意优点是并发操作（Concurrent Operation）
    - 一个I/O中断就像异常一样，但是有两个重要区别：从指令执行的角度说，I/O中断是异步的，中断与任何指令不想管且不影响任何指令的完成；中断代表的设备有不同的优先级
![](assets/Chapter%206/file-20241228201059813.png)

**直接内存访问**（Direct Memory Access, DMA）：设备控制器直接向/从内存传输数据，不需要处理器的介入

- DMA 传输模式
    
    - 首先处理器通过提供一些信息来设置 DMA 模式，具体包括：设备 ID、操作、待传输数据的内存源地址和目标地址、需要传输的字节数等
    - DMA 开始执行操作，并对总线进行仲裁。若某个请求需要在总线上多次传输数据，那么 DMA 单元生成下一个内存的地址，并发起下一次传输
    - 一旦 DMA 传输完成，控制器向处理器发起中断，随后检查是否发生错误
![](assets/Chapter%206/file-20241228202228540.png)

!!! example "比较三种通信方式的开销"

    假设：
    
    - 处理器的时钟频率为 500 MHz
    - 软盘以 16 位为单元传输数据给处理器，并且传输速度为 50KB/s
    - 硬盘以 4 字的块为单元传输数据，传输速度为 4MB/s
    - 没有错漏任何数据传输
    
    === "轮询"
    
    	=== "Question"
    	
    		假设：
    		
    		- 轮询操作需要的时钟周期数为 400
    		- 轮询操作足够频繁，所有的数据都没有缺失
    		- 所有设备大概率一直都非常忙碌（应该是被占用的意思）
    		- 鼠标必须每秒钟轮询 30 次，确保我们没有错漏用户的任何动作
    		
    		求出 CPU 浪费在鼠标、软盘和硬盘的时间比例
    	
    	=== "Answer"
    	
    		对于鼠标来说：每秒为了轮询需要的时钟周期为 $30\times 400=12000\text{ Cycles}$，则浪费比例为 $\frac{12\times 10^3}{500\times 10^6}=0.002\%$
    		
    		对于软盘来说，每秒需要的轮询次数为 $\frac{50KB}{2B}=25K$，需要的时钟周期为 $25K\times 400=10\times 10^6$，则浪费比例为 $\frac{10\times 10^6}{500\times 10^6}=2\%$
    		
    		对于硬盘来说，每秒需要的轮询次数为 $\frac{4MB}{16B}=0.25M$，需要的时钟周期为 $0.25M\times 400=10\times 10^7$，则浪费比例为 $\frac{10\times 10^7}{500\times 10^6}=20\%$
    		
    		可以看出，对于鼠标来说，轮询是不会太大影响到处理器的性能的，但是对于硬盘来说就是不可接受的了
    
    === "中断"
    
    	=== "Question"
    	
    		假设每次数据传输，包括中断时间，加起来一共需要 500 个时钟周期
    		
    		如果硬盘只在 5% 的时间中传输数据，求出处理器浪费的时间比例
    	
    	=== "Answer"
    	
    		我们首先假设硬盘在 100% 的时间中传输数据，此时中断率和轮询率是一样的，对于硬盘来说每秒的时钟周期为 $0.25M\times 500=125\times 10^6\text{ Cycles Per Second}$，此时的浪费比例为 $\frac{125\times 10^6}{500\times 10^6}=25\%$
    		
    		实际上硬盘只在 5% 的时间中传输数据，那么浪费比例即为 $25%\times 5\%=1.25\%$
    		
    		我们可以看到，一个中断驱动的 I/O 设备如果并没有在传输数据时，是不需要 CPU 时间的。这便是终端驱动相对于轮询的优点。
    
    === "DMA"
    
    	=== "Question"
    	
    		假设：
    		
    		- DMA 传输的初始设置需要 1000 个时钟周期
    		- DMA 结束后的中断处理需要 500 个时钟周期
    		- 从磁盘中传输数据的平均大小为 8KB
    		- 磁盘在 100% 的时间都在传输数据
    		
    		求出处理器浪费的时间比例
    	
    	=== "Answer"
    	
    		每次传输 8KB 的时间为 $\frac{8KB}{4MB/\text{second}}=2\times 10^{-3}\text{seconds}$
    		
    		那么每秒需要的时钟周期数为 $\frac{(1000+500)\frac{\text{Cycles}}{\text{Transfer}}}{2\times 10^{-3}\frac{\text{Seconds}}{\text{Transfer}}}=750\times 10^3\frac{\text{Clock Cycles}}{\text{Second}}$
    		
    		则浪费时间比例为 $\frac{750\times 10^3}{500\times 10^6}=0.2%$
    		
    		与轮询和中断驱动的 I/O 都不相同，DMA 可以被用于连接硬盘，而不会消耗单个 I/O 的所有处理器周期

***
## 5 I/O Performance Measures

几类关于 I/O 的基准测试（Benchmark）：

- 超级计算机 I/O 基准测试
- 事务处理 I/O 基准测试
    - I/O 速率：每秒访问硬盘的数量（数据速率的倒数）
- 文件系统 I/O 基准测试：包括 MakeDir、Copy、ScanDir、ReadAll、Make 操作等
***
## 6 Designing an I/O System

设计 I/O 系统的通用方法：

- 找到 I/O 系统最弱的连接，它是 I/O 通路上限制设计的一个组件。工作负载和配置限制都可以决定最弱链接的位置
- 通过配置该部件来维持所需带宽。确定系统其余部分的要求，并配置它们以支持此带宽

!!! example "Example"

	=== "Question"
	
		![](assets/Chapter%206/file-20241228202417643.png)
	
	=== "Answer"
	
		![](assets/Chapter%206/file-20241228202423801.png)
