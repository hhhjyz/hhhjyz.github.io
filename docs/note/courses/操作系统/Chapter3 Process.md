# Process

## 1 Process Concept

$job = process$

**process**: 一个正在顺序执行中的程序

一个进程包括    
- **代码段 (text section)** (代码)
- **程序计数器 (program counter)**
- **栈 (stack)** (函数参数, 局部变量, 返回地址), stack 指针是一个硬件寄存器
- **数据段 (data section)** (全局变量）        
- **堆 (heap)** (动态分配的内存): Heap is a complete binary tree
![](assets/Pasted%20image%2020250923144336.png)

### 1.1 Process State

- **new**: 进程被创建
- **running**, 指令正在被执行
- **waiting**: 进程在等待(系统调用)或者被锁了
- **ready**： 进程正在等待被分配给处理器
- **terminated**, 进程结束

![](assets/Pasted%20image%2020250923145136.png)

在任何时刻，任何处理器核心上只能有一个进程在运行

### 1.2 Process Control Block(PCB)

每个进程在操作系统中由一个进程控制块（PCB）表示——也称为任务控制块。

**与每个进程相关的信息**
- **进程状态** (新建, 就绪, ...)
- **程序计数器** (下一条指令的地址)
- **CPU 寄存器的内容**
- **CPU 调度信息** (优先级)
- **内存管理信息**
- **记账信息** (已使用的CPU时间, 时间限制, 进程号)
- **I/O 状态信息** (已分配的设备和打开的文件)
![](assets/Pasted%20image%2020250923150343.png)
寄存器信息用于应对进程切换
![](assets/Pasted%20image%2020250923150517.png)
### 1.3 Process Scheduling

- **Job Queue**: 系统中所有进程
- **Ready Queue**: 主存中就绪等待执行的进程
- **Device Queue**: 对每一个I/O设备，等待输入输出的进程

![](assets/Pasted%20image%2020250923151056.png)
![](assets/Pasted%20image%2020250923151107.png)

![](assets/Pasted%20image%2020250923151328.png)

 **调度程序 (Schedulers)**
 
- 什么是调度程序？一段程序。
    - **长期调度程序** (或称“作业调度程序”) – 选择哪些进程应该被调入内存（放入就绪队列）。
    - **短期调度程序** (或称“CPU调度程序”) – 选择下一个要执行的进程并为其分配CPU。
    - ![](assets/Pasted%20image%2020250923151822.png)
    - Medium Scheduler: ![](assets/Pasted%20image%2020250923152141.png)

- **短期调度程序**被非常频繁地调用（毫秒级）⇒（必须很快）
- **长期调度程序**被调用得非常不频繁（秒、分钟级）⇒（可以较慢）
- 长期调度程序控制着**多程序设计的程度 (degree of multiprogramming)**
- 进程可以被描述为以下两种之一：
    - **I/O密集型进程 (I/O-bound process)** – 花在I/O上的时间比计算多，表现为许多短暂的CPU计算周期（CPU bursts）。
    - **CPU密集型进程 (CPU-bound process)** – 花在计算上的时间更多；表现为少数非常长的CPU计算周期（CPU bursts）。

_Context Switch:_ 微秒级，切换进程时，操作系统要保存旧进程的状态并加载新的进程。上下文切换时间是一种开销 (overhead)；系统在切换时不做任何有用的工作——通常耗时**毫秒**级。时间长短取决于硬件支持。在SPARC架构中，提供了多组寄存器。

## 2 Operations on Process

### 2.1 Process Creation

父进程创建子进程，子进程又创建其他进程，从而形成一个进程树。

**资源共享**
- 父进程和子进程共享所有资源。
- 子进程共享父进程资源的一个子集。
- 父进程和子进程不共享任何资源。

**执行**
- 父进程和子进程并发执行。
- 父进程等待，直到子进程终止。

**地址空间 (Address space)**
- 子进程复制父进程的地址空间。
- 子进程将一个程序加载到其地址空间中。

**UNIX 示例**
- **`fork`** 系统调用创建新进程。
- **`exec`** 系统调用在 **`fork`** 之后使用，用一个新程序来替换进程的内存空间，`exec`后与原来的`main`进程没有关系了
![](assets/Pasted%20image%2020250923153350.png)
### 2.2 Process Termination


进程执行完最后一条语句后，请求操作系统将自己删除（**exit**，退出）。
- 子进程的输出数据传递给父进程（通过 **wait** 调用）。
- 进程的资源由操作系统释放。
        
父进程可以终止其子进程的执行（**abort**，中止）。    
- 子进程超出了其被分配的资源。     
- 分配给子进程的任务不再需要。
- 如果父进程正在退出：
	- 一些操作系统不允许父进程终止后子进程继续执行。
		- 所有子进程都被终止 - **级联终止 (cascading termination)**。
	- 在另外一些操作系统中，子进程会成为**孤儿进程 (orphaned)**。

### 2.3 Cooperating Processes

独立的进程之间不能相互影响

合作的进程之间可以相互影响

协作进程的范式：**生产者 (producer)** 进程生产信息，这些信息由 **消费者 (consumer)** 进程来消费
- **无界缓冲区 (unbounded-buffer)** 对缓冲区的大小没有实际限制。如果没有新的项目，消费者必须等待。
- **有界缓冲区 (bounded-buffer)** 假定缓冲区大小是固定的。如果缓冲区已满，生产者必须等待。


## 3 Interprocess Communication

进程发送消息来同步的机制

Models for IPC: **message passing** and **shared memory**

消息系统 – 进程之间相互通信，无需借助共享变量

PC 机制提供两种操作： 
- **send(message)** – 发送(消息) – 消息大小可以是固定的或可变的。 
-  **receive(message)** – 接收(消息)

![](assets/Pasted%20image%2020250925163051.png)

### 3.1 Direct Communication

进程必须显式地指定对方的名称： 
- `send(P, message)` – 发送一条消息给进程P。 
- `receive(Q, message)` – 从进程Q接收一条消息。

 通信链路的属性： 
 -  链路是**自动 (automatically)** 建立的。 
 - 一条链路只与**恰好一对 (exactly one pair)** 通信进程相关联。 
 - 在每对进程之间只存在**恰好一条链路 (exactly one link)**。 *
 - 链路可以是单向的，但通常是双向的。

### 3.2 Indirect Communication

消息被发送到**信箱 (mailboxes)** 并从中接收（也被称为**端口 (ports)**）。 
- 每个信箱都有一个唯一的ID。 
- 只有当进程共享一个信箱时，它们才能通信。

通信链路的属性： 
- 只有当进程共享一个公共信箱时，链路才会建立。 
- 一条链路可能与**多个进程 (many processes)** 相关联。 
- 每对进程之间可能共享**多条通信链路 (several communication links)**。 
- 链路可以是单向的或双向的


□ **操作 (Operations)** * 创建一个新信箱 (create a new mailbox) * 通过信箱发送和接收消息 (send and receive messages through mailbox) * 销毁一个信箱 (destroy a mailbox)

□ **原语 (Primitives)** 定义如下： * **`send(A, message)`** – 发送一条消息到信箱 A * **`receive(A, message)`** – 从信箱 A 接收一条消息


□ **信箱共享 (Mailbox sharing)** * P₁, P₂, 和 P₃ 共享信箱 A * P₁ 发送消息；P₂ 和 P₃ 尝试接收 * 谁会收到这条消息？ (Who gets the message?)

□ **解决方案 (Solutions)** * 允许一条链路一次最多只与两个进程关联。 * 一次只允许一个进程执行接收操作。 * 允许系统任意地选择接收者，然后通知发送方接收者是谁

### 3.3 Sychronization

□ 消息传递可以是**阻塞式 (blocking)** 或 **非阻塞式 (non-blocking)**。

□ **阻塞式 (Blocking)** 被认为是**同步的 (synchronous)**。 * **阻塞式发送 (Blocking send)** 指发送方被阻塞，直到消息被接收方接收。 * **阻塞式接收 (Blocking receive)** 指接收方被阻塞，直到有可用的消息。

□ **非阻塞式 (Non-blocking)** 被认为是**异步的 (asynchronous)**。 * **非阻塞式发送 (Non-blocking send)** 指发送方发送消息后立即继续执行。 * **非阻塞式接收 (Non-blocking receive)** 指接收方或者接收一个有效的消息，或者在没有可用消息时接收一个null值。

□ 附着在通信链路上的消息队列；通过以下三种方式之一实现：
1. **零容量 (Zero capacity)** – 0条消息
    
    - 发送方必须等待接收方 (称为**会合 (rendezvous)**)。
        
2. **有限容量 (Bounded capacity)** – 长度为 _n_ 的有限队列
    
    - 如果链路（队列）已满，发送方必须等待。
        
3. **无限容量 (Unbounded capacity)** – 无限长度
    
    - 发送方从不等待。
        

□ 缓冲控制 (Control of Buffering)
## 4 Communication in Client-Server System

### 4.1 RPC(Remote Procedure Calls)

**Stubs**: 可以被远程调用的协议

![](assets/Pasted%20image%2020250925164209.png)
