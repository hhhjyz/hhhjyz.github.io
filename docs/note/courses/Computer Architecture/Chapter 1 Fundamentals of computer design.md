# Chapter 1 Fundamentals of computer design

## 1 Introduction

Von Neumann Structure
![](assets/Chapter%201%20Fundamentals%20of%20computer%20design/file-20250218150858260.png)

**Classes of Computers**

- Desktop computers  
    PC: Personal Computers
- Servers computers  
    更强大的处理速度，容量（用于冗余备份）；强调同一时间多人访问更好的性能
- Embedded computers  
    不能随意安装第三方应用的，与系统一体，称为嵌入式
    最大的种类并最多元化
- Personal Mobile Devices  
    如手机，iPad
- Supercomputer 计算机集群

**Classed by Flynn**
按照指令流和数据流进行分类

![](assets/Chapter%201%20Fundamentals%20of%20computer%20design/file-20250218154355024.png)


**Performance**

- Algorithm,决定操作的数量
- Programming language, compiler, architecture,决定一个操作的机器指令的数量
- Processor and memory system， 决定指令执行的速度
- I/O system(including OS),决定I/O操作执行的速度

这里有很多因素会影响性能：体系结构，硬件实现，编译器，OS...

We need to be able to define a measure of performance. 

* Single users on a PC -> a minimization of response time
* Large data -> a maximization of throughput

为了衡量性能，我们有响应时间和吞吐量两个指标：

* Latency (Response time 响应时间)  
一个事件开始到结束的时间  
* Throughput (bandwidth 带宽)  
给定时间范围内完成了多少的工作量

定义performance和执行时间成反比，性能的比较可以理解为执行时间的比较

**Measuring Execution Time**
- Elapse time

> 整体运行的时间

- CPU Time

> 在CPU上花费的时间

<u>***The main goal of architecture improvement is to improve the performance of the system.***</u>

## 2 Technology Trend

The improvement of computer architecture

* Improvement of input / output 
* The development of memory organization structure
* Two directions of instruction set development
    * CISC / RISC 
* Parallel processing technology  
不同层次、粒度的并行

## 3 Quantitative approaches

### 3.1 CPU Performance

* CPU 执行时间 = CPU 时钟周期数 * CPU 时钟周期时间 = CPU 时钟周期数 / CPU 时钟频率
* IC：Instruction Count，指令数
* CPI：Cycle Per Instruction，每条指令的平均时钟周期数
    * 由 CPU 硬件决定
    * 不同的指令也会有不同的 CPI，平均 CPI 取决于指令的组合方式
    * CPI = CPU 时钟周期数 / IC
    * CPU 执行时间 = IC * CPI / CPU 时钟频率

### 3.2 Amdahl's Law

Amdahl's Law: the performance improvement to be gained from using some faster mode of execution is limited by the fraction of the time the faster mode can be used.  
当提升系统性能时，有多大的收益受限于被提升的部分所占的运行时间比例

$T_{improved}=\dfrac{T_{affected}}{\text{improvement factor}}+T_{unaffected}$

Make the common case fast!

也被用来分析可行性

* 加速比  

    $$
    \begin{align*}
    \text{Speedup} & =\dfrac{\text{Performance for entire task}_\text{using Enhancement}}{\text{Performance for entire task}_\text{without Enhancement}}\\
    & = \dfrac{\text{Total Execution Time}_\text{without Enhancement}}{\text{Total Execution Time}_\text{using Enhancement}}
    \end{align*}
    $$  

    加速比 Sp = 改进后的性能 / 改进前的性能 = 改进前的时间 / 改进后的时间

* 执行时间  
$T_{new} = T_{old}\times \left((1-f)+\dfrac{f}{Sp}\right)$  
$f$ 指改进的部分所占的比例
* $Sp_{overall} = \dfrac{T_{old}}{T_{new}} = \dfrac{1}{(1-f)+\dfrac{f}{Sp}}$
    * 其中 $Sp$ 为被优化部分的加速比，$Sp_\text{overall}$ 为整体加速比，$f$ 为被优化部分所占的运行时间比例

## 4 Great Architecture Ideas

* 摩尔定律
    * 每过 18-24 个月，集成电路的晶体管数量将增加一倍
* 使用抽象来简化设计
* 让最常见的情况更快
* 通过并行来提高性能
* 由很多级别的并行，比如指令集并行、进程并行等
* 通过流水线来提高性能
    * 将任务分为多段，让多个任务的不同阶段同时进行
    * 通常用来提高指令吞吐量
* 通过预测来提高性能
* 使用层次化的内存
    * 让最常访问的数据在更高层级，访问更快
* 使用冗余提高可靠性

## 5 ISA

* Instruction Set Architecture  
<div align = center><img src="https://cdn.hobbitqia.cc/20230928223516.png" width=60%></div>

Instruction Set Design Issues

* Where are operands stored?  
registers, memory, stack, accumulator
* How many explicit operands are there? (Classification of ISAs)   
0, 1, 2, or 3 
* How is the operand location specified? (Addressing Modes)  
register, immediate, indirect, ...
* What type & size of operands are supported? (Data Representation)  
byte, int, float, double, string, vector, ...
* What operations are supported? (Types of Instructions)  
add, sub, mul, move, compare, ...

Basic Principles

* Compatibility
* Versatility
* High efficiency
* Security

### 5.1 ISA Classification Basis

这里主要指的是从哪里取数，存到哪里以及计算的规则。

* stack
    First operand removed from second op replaced by the result.  
!!! example Stack Architecture
    <div align=center><ima src ="assets/Chapter%201%20Fundamentals%20of%20computer%20design/file-20250225145931305.png" width=40%></div>
* accumulator 累加器
    * One implicit operand: the accumulator; one explicit operand: mem location
    * Accumulator is both an implicit input operand and a result  
    * <div align=center><img src="assets/Chapter%201%20Fundamentals%20of%20computer%20design/file-20250311140737558.png"></div>
* register
    * Register-memory architecture  
    任何指令都可以访存
    * Load-store architecture  
    只有 load/store 的时候才能访存，其他时候都是基于寄存器操作 

### 5.2 GPR Classification

<div align = center><img src="https://cdn.hobbitqia.cc/20230928225257.png" width=60%></div>
<div align = center><img src="https://cdn.hobbitqia.cc/20230928225337.png" width=60%></div>

!!! Example "A+B"
    <div align = center><img src="https://cdn.hobbitqia.cc/20230928225409.png" width=60%></div>

    More: try to do with $D=A*B-(A+C*B)$

GPR 速度快，但是 GPR 太多也会有资源的浪费和性能下降（如寻找对应的寄存器）

- Registers are much faster than memory , even cache
- Register are convenient for variable storage