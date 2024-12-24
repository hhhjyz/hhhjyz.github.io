# Large and Fast
## 1 key merits of a memory
- Speed
- Cost per bit (cost vs size)
- volatility 易失性
- Endurance cycles
>[!example] example
>![](assets/Chapter%205/file-20241115141801653.png)

# Memory Technologies
- SRAM: 静态随机访问存储器
	- 对任意位置的数据访问事件是固定的
	- 不需要定期刷新
	- value is stored  on a pair of inverting gates
	- 速度更快
- DRAM: 动态随机访问存储器
	- 密度更高，价格更低廉，需要不停地刷新
>[!example]
>![](assets/Chapter%205/file-20241115150257966.png)

## 1 Advanced DRAM Organization
地址分时赋用
- 每次访问一整行
- Burst mode: supply  successive words from  a row with reduced latency
- DDR DRAM 在上升下降沿都可以传输
- Quad data rate DRAM
	- 分离DDR 的inputs and outputs

## 2 Flash Storage
***非易失性存储***
### 2.1 Flash Types
- NOR flash: bit cell like a NOR gate
- NAND flash: bit cell like a NAND gate
- Flash bits 磨损after 1000's of access
## 3 Disk  Storage
### 3.1 Disk Sectors and Access
- 每个扇区存储了
	- 扇区ID
	- Data
	- ECC 错误纠正代码
- 访问扇区
	- 动探头
	- 转动磁盘
	- Data transfer
	- Controller overhead

>[!example] Disk Access Example
>![](assets/Chapter%205/file-20241115152100045.png)
### 3.2 Problems in memory designing
![](assets/Chapter%205/file-20241115152635391.png)

## 4 Memory Hieracrhy Introduction层次存储
每次访问的都是非常小的一部分
- 时间局部性
	- 最近被访问的对象被再次访问的可能性较高
- 空间局部性
	- 正在被访问的存储器单元附近的单元可能经常被访问

### 4.1 利用局部性
- Memory hierarchy
- 所有东西都放磁盘
- 把经常用的items  copy到更小的DRAM memory 里面
- 再把更近的items copy 到更小的SRAM memory 里面

### 4.2 Some important items
- Block: unit of copying, 可能是很多个words
- hit: CPU访问最顶层而且成功读取想要的数据
- miss: CPU 访问最顶层但是没能成功读取
- Hit time: 访问最顶层需要的时间
- miss penalty: 将最顶层中的block 替换为底层中对应block的时间，加上运输block到processor的时间。
搬运时要把整个block搬走
>[!note] Memory hierachy
>![](assets/Chapter%205/file-20241125133212039.png)

## 5 The basics of  Cache:  SRAM and DRAM
- cache 的出现解决了主存速度不够快的问题，主存速度跟不上CPU的速度
### 5.1 简单的实现
- 我们怎么知道要找的data item在cache里面
- 如果在，怎么找到data item

>[!info] 基本概念
>![](assets/Chapter%205/file-20241125134552548.png)

#### 5.1.1 Cache 工作原理
- 局部性原理
	- 时间局部性：最近被访问的存储器单元（指令或数据）很快会被访问
	- 空间局部性： 正在被访问的存储器单元附近的单元很快会被访问
- 对空间局部性的利用：从主存中取回待访问数据时，会同时取回与其相邻的主存单元的数据
- 对时间局部性的利用：保存近期被频繁访问的主存单元的数据
#### 5.1.2 Cache 地址映射机制
![](assets/Chapter%205/file-20241125134936715.png)
块偏移量：由一个block里面有多少个byte决定

***Cache 的结构：***
被分为若干行，每行的大小与主存块相同
每行包含四部分：
- 标签Tag是从CPU访问主存的地址中剥离得到
- Data是与主存交换的数据块
- Valid 表示Cache中的数据是否有效
- Dirty表示Cache中的数据是否为最新

##### 5.1.2.1 直接映射
每个存储地址都直接映射到cache中的确定位置
>[!note] 映射方法
>（块地址）mod（cache中的数据块数量）
>
>或者
>若cache有$2^n$个数据块，那么索引为主存块地址的最低n位

***如何在cache中快速查找数据？***
- 索引+标签+valid+dirty位共同锁定目标
	- 根据给出的主存块大小、Cache大小，从主存地址中剥离出字节偏移量字段、索引字段以及标签字段
	- 首先根据索引字段找到目标cache行
	- 目标cache行中的标签字段与剥离出的标签字段进行匹配
	- Valid位、dirty位是否有效
>[!example] 计算方法
>![](assets/Chapter%205/file-20241125140737799.png)
>因为内存中是字节对齐的，所以以byte为单位进行计算

Cache中的Data大小就是主存中的Block大小

***Tags and Valid Bits:***
Valid bit: 1=present, 0= Not Present




##### 5.1.2.2 组相联

##### 5.1.2.3 全相连

#### 5.1.3 处理Cache 读 hit and misses
- Read misses: 指令缓存miss和数据缓存miss
##### 5.1.3.1 Instruction cache miss
- stall cpu，取出需要的memory到cache里面
- 把original PC value(现在的PC-4)存到内存
- 指示内存去读并等待直到这个过程结束
- 

##### 5.1.3.2 Data cache miss

#### 5.1.4 处理Cache 写 hit and misses
write through ，即写cache又写主存
write back, 只写cache
***write hits***
- write-back，只写到cache里面,之后再把数据从cache写回memory
- write-through， 对cache和memory同时更新。在写cache里面放到write buffer里
***write miss***:
整个block读入cache，然后再写
- 对于write through
	- allocate on miss: fetch the block
	- write around: don't fetch the block
- 对于 write back:
	- 总是fetch the block

>[!important] summary
>![](assets/Chapter%205/file-20241127103607824.png)

***直接映射机制的特点：***
- cache 利用率低
- 块冲突率高

>[!info] 块大小的权衡
>![](assets/Chapter%205/file-20241127110432420.png)

### 5.2 ***Deep concept in Cache:***
#### 5.2.1 Block Placement
-  直接映射：block只能到cache里面的一个位置
- 全相联：block可以到cache任意位置
- 组相联：block可以到cache中的其中一组位置，由求模计算在哪个set中，如果一个组中由n个block，就叫n-way set associative

>[!example] Figure 8-32 Block Placement
>![](assets/Chapter%205/file-20241127111224454.png)
>
>
#### 5.2.2 Block Identification


##### 5.2.2.1 The Format of the Physical Address
- Index field selects:
	- set,组相联
	- block，直接映射

#### 5.2.3 Block Replacement
- Random replacement
- Least-recently used(LRU):挑选出最早访问的
- First in, First out(FIFO)：

#### 5.2.4 Write Strategy
- Write through
- Write back
- Write stall, 在write through策略中药等待写入内存

### 5.3 Cache Performance

$$\begin{align}Average~Memory ~Assess ~Time=hit~time+~miss time\\ =hit time+miss~rate\times memory ~time
\end{align}$$

#### 5.3.1 Measuring cache performance
![](assets/Chapter%205/file-20241202134347794.png)

>[!example] Calculating cache performance
>![375](assets/Chapter%205/file-20241202134923562.png)
>![375](assets/Chapter%205/file-20241202134941962.png)

对于一个性能更高的处理器，memory stall 造成的性能代价更大

#### 5.3.2 Solution 1
更灵活的块替换来降低miss rate

组相连可以降低miss rate
- 将cache 分成很多组，每个组里面有很多个block
![](assets/Chapter%205/file-20241202140317650.png)

#### 5.3.3 Solution 2: Choosing which block to replace
最常见的策略是LRU

#### 5.3.4 Solution 3: Choosing different block size
取到缓存中时浪费时间，larger miss penalty
>[!example] Performance in different block size
>![400](assets/Chapter%205/file-20241202143209814.png)

#### 5.3.5 Solution4: Designing the memory system to reduce miss penalty

![](assets/Chapter%205/file-20241202143701963.png)
![](assets/Chapter%205/file-20241202143713984.png)![400](assets/Chapter%205/file-20241202143750165.png)

#### 5.3.6 Solution5: Decreasing miss penalty with multilevel caches

多级cache

#### 5.3.7 Solution 6 : Software optimization blocking


### 5.4 Virtual Memory 
一种将主存看作辅助存储的cache技术，扩大内存的寻址空间

- 使得多个程序可以共享主存，每个程序有自己私有的一个虚拟地址
- 消除由于主存本身的局限性导致的上限

基本单位： page（相当于cache中的block)
失效： page fault

>[!note]
>![400](assets/Chapter%205/file-20241202151954836.png)
保护每个程序独立进行而不受其他程序干扰

#### 5.4.1 pages
virtual page 比physical page 要多

- page fault: 数据不在主存中，要把它从disk中取出来
	- miss penalty 非常大，所以pages要尽可能大
	- 降低page fault非常重要，使用全相联
	- 在软件中处理faults
	- 使用write back策略


##### 5.4.1.1 page tables

![600](assets/Chapter%205/file-20241209135813651.png)
***Virtual to physical address***
- 得到virtual page number
- 用它作为索引在page table 中进行寻找，如果valid 位为一，就取出，如果为0，在disk中
- 全相联
- page table在主存中

##### 5.4.1.2 Making Address Translation Fast——TLB
TLB相当于page table 的缓存
放经常访问的pagetable中的内容
> [!note] FastMATH Memory Hierarchy
> ![](assets/Chapter%205/file-20241209141935925.png)

![](assets/Chapter%205/file-20241209142346220.png)

![](assets/Chapter%205/file-20241209142605992.png)




