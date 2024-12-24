# 流水线处理器

在单周期处理器中最长的延迟决定了时钟周期长度

不能使用缩短常用指令执行时间而不改变最坏情况的实现技术

## 1 Pipelining RISC-V instruction set
时钟周期降到了1，每个时钟周期多个指令在并行
![[file-20241030103513303.png|425]]

- 在流水线数据通路中，每两个阶段之间加入寄存器（堆），用以保存数据，是的部分数据通路可以在执行过程中被共享
- 用前后两个阶段的缩写命名
- IF: 取指
- ID: 译码，读寄存器
- EX: 执行，计算地址
- MEM: 内存访问操作
- WB: 结果写回寄存器
### 1.1 流水线的性能分析：计算时钟周期数
- 在理想条件下，给定N条指令，时钟周期为C,流水线级数M(阶段数目)，计算所需的时间T为:
$$T=N*C+(M-1)*C$$

对指令阶段适当的拆分可以提高流水线处理器的性能

### 1.2 流水线的级数
流水线的级数：执行指令所需要的阶段数目,***级数多则称流水线比较深***

若级数过多，流水线寄存器延迟在一个时钟周期中的占比大大增加，从而影响了流水线处理器性能的提升

流水线级数是一个tradeoff的设计，在级数合理时，具备最高的加速比

### 1.3 流水线加速
如果每一级需要的时间都是平衡的，加速比最快
如果所需时间不平衡，那么加速比就没那么快了

Latency(time for each instruction) 未降低

***RISC-V指令适合流水线加速***：
- 所有指令都是32bit的
- 指令格式更少且规整
- Load/store addressing 可以在第三阶段计算，第四阶段加载
### 1.4 pipeline  hazard
#### 1.4.1 Structural hazard
硬件不支持多条指令在同一时间周期执行

**解决方法**:指令和数据分开存储

#### 1.4.2 Data hazard
由于一个步骤必须等待另一步骤完成而导致的流水线停顿
 即`rd=rs`

**解决方案**：forwarding/bypassing,一旦ALU计算出加法的和，就可将其作为减法的输入。向内部资源添加额外的硬件以尽快找到缺少的运算项
>[!note] 非load-use data hazard
>不再等待计算的结果写回寄存器，直接放在内部缓存器里面，直接传递到下一条指令执行的输入
> ![|425](assets/Chapter%204/file-20241114135631149.png)

>[!nore] load-use data hazard
>即使使用了forwarding, 在mem阶段之后才能拿到结果，仍然需要浪费一个时钟周期，插入一个bubble
>![|350](assets/Chapter%204/file-20241104134900642.png)

***RISC-V中每一条指令只有一个结果，方便了进行这样的各种优化***

**如何优化load-store型数据冒险**：
改变指令的顺序，在原本应该插入bubble的时候插入一条与其他指令不冲突的指令

>[!warning] forwarding
> 时间不能倒流！


##### 1.4.2.1 如何检测data hazard
>[!info] Dependencies & Forwarding
>![|375](assets/Chapter%204/file-20241114153526335.png)

判断：
![|350](assets/Chapter%204/file-20241114154326987.png)
上一个指令的rd和下一个指令的rs的编号是一样的

第一种，ID从内存中拿数据的时候要用
第二种，ID从寄存器堆中拿数据的时候用
在***EX/MEM.RegWrite,MEM/WD.RegWrite为1***且***目标寄存器不为0***的时候才进行forwarding

>[!note] Forwarding 
>![|400](assets/Chapter%204/file-20241114155447715.png)
>![|400](assets/Chapter%204/file-20241114155455164.png)
>![|425](assets/Chapter%204/file-20241114155503038.png)

ALU 每次计算的时候输入有三个，一个是寄存器中读出的值或者立即数，上一个指令中EX阶段的计算结果，上一个指令中MEM中取出的数据/更早的ALU计算结果
>[!note] Double Data Hazard
>Consider the sequence:
>```
>add x1,x1,x2
> add x1,x1,x3
>add x1,x1,x4
>```
>在多个冒险同时发生的时候考虑最新的，EX冒险优先级更高
>![|375](assets/Chapter%204/file-20241114203918022.png)
>![|400](assets/Chapter%204/file-20241114204159635.png)


>[!Note] Load-Use Hazard Detection
>在ID阶段进行检测
>***Load Use Hazard when***:
>
>ID/EX.MemRead and
>
>(ID/EX.RegisterRd = IF/ID.RegisterRs1)or
>
>(ID/EX.RegisterRd = IF/ID.RegisterRs2)
>
>如果检测到就插入一个bubble


*如何进行stall？*
ID/EX寄存器堆中的所有信号都置为0
阻止PC和IF/ID寄存器的更新
![|400](assets/Chapter%204/file-20241114210618569.png)
***Datapath with Hazard Detection:***
![|400](assets/Chapter%204/file-20241114211004212.png)

#### 1.4.3 Control hazard

需要根据一条指令的结果做出决定，而其他指令正在执行
>[!note] **解决方案**：
>将分支判断提前到ID阶段，这样只要浪费一个时钟周期
>![|450](assets/Chapter%204/file-20241114144207419.png)

Branch Prediction
- 较长的流水线无法提前进行分支的比较，
- 进行分支预测，当预测错误时阻塞发生，
- In RISC-V pipeline
	- Can predict branches not taken
	- Fetch instruction after branch, with no delay
**更现实的分支预测**：
- 静态
	- 总是预测跳转或者不跳转
- 动态
	- 借助硬件支持，记录跳转的历史
- 预测错误，状态单元全部更新(flush)，从正确的指令地址重新开始流水线

## 2 Pipeline Summary
- 流水线通过增加instruction throughout 来提升性能,单位时间内的吞吐率得到了提升
- 指令集的设计会影响流水线的表现


## 3 流水线数据通路及其控制
WB阶段和PC更新阶段出现从右到左的通路，容易产生hazard

在每两个阶段之间插入一个寄存器堆用来保存数据

## 4 Branch Hazards
跳转信号和ALU输出的zero在MEM级进行与操作，决定是否跳转

***Reducing Branch Delay***
- 在ID阶段决定比较的结果（硬件代价：ALU计算目标地址，寄存器）
>[!example] Branch Taken
>![|400](assets/Chapter%204/file-20241115001851640.png)
>错了就加一个bubble
### 4.1 Dynamic Branch Prediction
在一个非常深的流水线中，branch penalty 非常关键
- 根据历史跳转结果进行跳转
- 用跳转指令的地址作索引，记录是否跳转了
- 为了执行branch
	- 查表，预测相同的结果
	- 取指
	- 如果预测错了，fulsh并翻转预测

### 4.2 1-bit predictor
for 循环里面预测最多错两次，开始和结束的时候各错一次
### 4.3 2-bit predictor
只有连续预测错两次才去改预测的方针
![|475](assets/Chapter%204/file-20241115003042310.png)
在这种情况下，预测错了依然要延迟一个时钟周期

**解决方法**： 加一个缓存来存储目标地址

## 5 Exceptions and Interrupts异常和中断
- "Unexpected" 事件改变了我们的控制流
- "Exception", Arises within CPU，不区分什么引起的
- "Interrupt", 来自外部I/O控制
- 不去牺牲性能来处理是困难的

### 5.1 处理中断
- 保存原来的执行的地址：Supervisor Exception Program Counter
- SCAUSE: 用来存储问题发生的原因
- Jump to handler

>[!example] 一种可选择的方式
>向量式中断，由原因决定
>
>基址寄存器加上vector address
>
处理中断或者跳到handler
> 
> ***Handler的操作***
> 读出cause并跳到相关的handler，确定什么样的action，
> ![|400](assets/Chapter%204/file-20241115004856497.png)
#### 5.1.1 流水线中的异常
- 另一种形式的control hazard
- ![|450](assets/Chapter%204/file-20241115005131257.png)
![|425](assets/Chapter%204/file-20241115005240752.png)
 
#### 5.1.2 Exception的性质
-  Restartable
	- 流水线可以flush
	- handler 执行并返回指令
- PC存到SEPC寄存器中

>[!example] Exception 
>![|400](assets/Chapter%204/file-20241115005704591.png)
>![|425](assets/Chapter%204/file-20241115005815706.png)
#### 5.1.3 Multiple Exceptions
- 流水线中同时有多个异常
- Simple approach:从最早出现异常的指令开始处理，后面的全部flush

>[!example] Imprecise Exceptions
>- 停下流水线并保存状态
>- handler工作
>- 简化硬件但是更多handler 软件
>- complex multiple-issue out-of -order pipelines 中不可行

## 6 Instruction Level Parallelism
指令集并行
**如何提升并行？**：
- 更深的流水线
- Multiple issue 多发
	- 多条pipeline
	- 每个clock cycle 执行多条指令
![|425](assets/Chapter%204/file-20241115010916966.png)
![|425](assets/Chapter%204/file-20241115010940139.png)
预测之后提早执行
***编译器或硬件投机***：
编译器可以重新排序指令
硬件可以提前执行指令
![|400](assets/Chapter%204/file-20241115011327727.png)
***Static Multiple Issue***
- 编译器将指令打包到"issue packets"中，根据流水线资源的需要
- 把一个issue packet 看作一个非常长的指令
***Scheduling Static Multiple Issue***
- reorder instructions issue packets
- 打包到一个包内的instructions 没有dependency
- 不同的包之间可能存在以来
- pad with nop if necessary，不能成对发射的时候空着
>[!example] RISC-V with Static Dual Issue
>![|400](assets/Chapter%204/file-20241115103601143.png)
>不存在任何资源上的竞争
>![|375](assets/Chapter%204/file-20241115103807009.png)

#### 6.1.1 Hazard in the Dual-Issue RISC-V

>[!example] RISC-V 成对发射
>![|450](assets/Chapter%204/file-20241115104026527.png)
IPC instruction per cycle

#### 6.1.2 循环展开
>[!example] 
>![|450](assets/Chapter%204/file-20241115104318701.png)
>![|375](assets/Chapter%204/file-20241115104957211.png)

- 复制循环体来进行更多的并行
- 在每次复制过程中使用不同的寄存器

#### 6.1.3 动态多发射
- CPU来决定一个时钟周期发射几条
- 避免compiler来进行schedule
- 要保证结果式计算正确的

>[!example] Dynamically Scheduled CPU
>![|350](assets/Chapter%204/file-20241115134710782.png)
由硬件对指令进行重排

>[!note] Register Renaming
>- 当一条指令发射时，该指令会被复制到某个**功能单元**(functional unit) 的**保留区**(reservation station)（一块保留操作数或操作的缓存）中。在寄存器堆或**重排缓存**(reorder buffer)（一块保留动态调度处理器结果的缓存，直到能够安全地将结果存储到寄存器或内存为止）内的任何空闲操作数也都会被复制到保留区中。指令将会一直存在保留区中，直到所有的操作数和功能单元处于空闲状态。对于正在发射的指令，操作数的寄存器拷贝不再需要，其值可被覆写。
> - 如果操作数不在寄存器堆或重排缓存中，那一定是在等待功能单元生成这个操作数，该功能单元的名字将会被追踪。当该单元生成结果后，该结果的拷贝会绕过寄存器，被直接放入保留区中。

***Why Do Dynamic Scheduling:***
- 不是所有阻塞都可以被预测
- 不可能一直围绕branch进行预测
- 由于流水线的时延和发射宽度根据具体实现的不同而有所变化，因此编译代码序列的最佳方式也会随之改变

>[!question] 多发射总是有效嘛
> 不一定，发射速率不是越快越好，因为很少有应用能够保持一个时钟周期内发射多于两个指令，原因有：
> - 在流水线内，最主要的性能瓶颈来自无法消除的依赖关系，因而降低了指令间的并行和发射速率 
> - 比如使用指针创造别名，这会带来更多的依赖关系；但如果用数组的话就没有这种依赖关系    
> - 又比如我们很难在编译时或运行时精确预测分支结果，这也带来了限制
>- 内存层级的缺失也会限制流水线运行的能力，比如内存的实验和有限带宽等

### 6.2 Power Efficiency
复杂的动态分支预测require power

Multiple simpler cores may be better

### 6.3 Fallacies and Pitfalls

>[!warning] 谬误
>- ~~流水线很简单~~（~~简单 nm~~）
>- 大致思路很简单（洗衣服的那个类比），但细节上的理解就困难了（比如处理各类冒险问题等）
>- 流水线思想的实现与工艺无关
>- 实际上，更多的晶体管将会带来更高级的工艺
>- 流水线相关的 ISA 设计需要考虑工艺的发展趋势

>[!warning] pitfalls
>不良的 ISA 设计将会对流水线运行产生不利影响
>
>复杂的指令集、复杂的寻址模式、延迟分支都会影响流水线的运行效率

