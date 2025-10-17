# CPU Scheduling

## 1 Basic Concepts

burst 是一种中断的现象

<div align = center><img src="assets/Pasted%20image%2020251010101727.png" width = 50%></div>


**Dispatcher**: 将CPU控制权给short-term scheduler挑选出的进程，包括
- switching context
- switching to user mode
- jumping to the proper location in the user program to restart that program

**Dispatch Latency**: dispatcher 停止和启动一个线程的时间差，主要有conflicts和dispatch两个阶段

在冲突阶段中，主要包含两部分：
- 有些内核代码无法被抢占或者中断，调度器必须等待这个进程执行完这段不可抢占的内核代码，才能进行切换
- 当前正在运行的低优先级进程可能持有一些即将运行的高优先级进程也需要的资源，那么必须等待这个进程释放资源之后才可以继续执行

## 2 Scheduling Criteria

- CPU 利用率
- throughput
- turnaround time - 执行某个特定进程的时间
- Waiting time - 线程在ready队列中等待的时间
- Response time - 请求发起到第一个响应的时间

**FCFS Scheduling**: 到达ready的时间越早，先运行

**Shortest-Job-First(SJF) Scheduling**: 按照CPU运行时间排序，有两个版本（抢占式和等待式）


## 3 Scheduling Algorithms

## 4 Multiple-Processor Scheduling

## 5 Real-Time Scheduling

## 6 Thread Scheduling

## 7 Operating Systems Examples

## 8 Java Thread Scheduling

## 9 Algorithm Evaluation

