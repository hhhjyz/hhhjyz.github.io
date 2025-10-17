
# The Microprocessor and its Architecture

寻址模式分为 real, protected and flat modes

## 1 Internal Microprocessor architecture

**IA-32 Basic Execution Environment**

![](assets/Pasted%20image%2020251001171228.png)

![](assets/Pasted%20image%2020251001171317.png)

![](assets/Pasted%20image%2020251001171326.png)


![](assets/Pasted%20image%2020251001171350.png)
### 1.1 General-Purpose Registers

![](assets/Pasted%20image%2020250925101614.png)部分寄存器: 只表示更大的寄存器的一部分，这一概念和x86汇编紧密相关（某些寄存器可以以不同的长度被加载）

_accumulator_ 主要用于乘法、除法和一些adjustment instructions![](assets/Pasted%20image%2020250925101417.png)
![](assets/Pasted%20image%2020251001172037.png)

前面的一两位是操作码，EAX系列的寄存器更短，对cache更友好

**RBP(EBP or BP)**: base pointer, 指向当前栈帧的基址 

**RSI**和**RDI**用于操作字符串，分别是源索引寄存器和目标索引寄存器，分到16bit之后不再划分，但是在64位架构下，开放低8位寄存器 **(BPL, SPL, SIL, DIL)**

 **R8 - R15**：在奔腾4 (Pentium 4) 和酷睿2 (Core2) 处理器上，如果启用了64位扩展，就可以找到这些寄存器。对于大多数指令而言，访问这些扩展的通用寄存器 (GPRs) 需要一个 **REX 前缀**。 
- 在64位模式下，通用寄存器包括：
    - 16个8位低字节寄存器：AL, BL, CL, DL, **SIL, DIL, BPL, SPL**, R8B-R15B
    - 4个8位高字节寄存器：AH, BH, CH, DH，**仅在不使用REX前缀时才可寻址**
    - 16个16位寄存器：AX, BX, CX, DX, DI, SI, BP, SP, **R8W-R15W**
    - 16个32位寄存器：EAX, EBX, ECX, EDX, EDI, ESI, EBP, ESP, **R8D-R15D**
    - 16个64位寄存器：RAX, RBX, RCX, RDX, RDI, RSI, RBP, RSP, **R8-R15**

大多数种类的 x86 寄存器都可以通过多种粒度 (granularity) 进行访问。
    
请注意，修改一个32位的部分寄存器 (partial register) 会将该寄存器的其余部分 (位 32-63) 设置为零，但是修改一个8位或16位的部分寄存器并**不会**影响寄存器的其余部分。例如：

- **`MOV RAX, 1111111111111111H`**
	- ; RAX 的值变为 `1111111111111111H`
	- (这里直接对完整的64位寄存器RAX进行操作)
- **`MOV EAX, 22222222H`**
	- ; RAX 的值变为 `0000000022222222H`
	- (因为修改了32位的EAX，所以RAX的高32位被**自动清零**)
- **`MOV AX, 3333H`**
	- ; RAX 的值变为 `0000000022223333H`
	- (在上一条指令的基础上，修改了16位的AX。这**只影响**RAX的低16位，其余位保持不变)
- **`MOV AL, 44H`**
	- ; RAX 的值变为 `0000000022223344H`
	- (在上一条指令的基础上，修改了8位的AL。这**只影响**RAX的最低8位，其余位保持不变)

因为最前面的32位是不可被单独操作的，所以可以直接被清零

Intel、AMD 和 VIA 的 CPU 无法对一个**部分寄存器 (partial register)** 进行重命名(乱序执行时)


![](assets/Pasted%20image%2020250925103649.png)
- `IMUL EAX, 6` 计算出一个32位的结果并写入 `EAX`。
- 接下来，`MOV AX, [mem3]` 指令虽然逻辑上只从内存加载新数据，但由于硬件限制，它需要读取 `IMUL` 指令写入的 `EAX` 的值，以便保留其高16位。
- 这就创建了一个**伪依赖 (False Dependence)**：`MOV AX` 指令被迫**等待** `IMUL` 指令执行完成，才能进行合并操作。这种等待就是**停顿 (Stall)**。


**特殊用途寄存器**
- 包括 RIP、RSP 和 RFLAGS
    - 段寄存器包括 CS、DS、ES、SS、FS 和 GS
- **RIP** 指向内存中下一条要执行的指令。
    - 被定义为代码段中的 **(指令指针 instruction pointer)**。
- **RSP** 指向内存中一个被称为栈 (stack) 的区域。
    - **(栈指针 stack pointer)** 通过该指针来存储数据
- **RFLAGS** 指示了微处理器的**状态** (condition)，并**控制**其操作。
- 从 8086/8088 到酷睿2 (Core2)，这些标志位是**向上兼容**的 (upward-compatible)。
- **最右边的五个标志位**以及**溢出标志位**会被大多数算术和逻辑运算所改变。
    - —— 不过，数据传输指令不会影响它们。
![](assets/Pasted%20image%2020250925104121.png)


1. **C - Carry Flag (CF, 位 0): 进位标志**    
    - **用途**: 主要用于无符号数运算。当运算结果超出了目标操作数能容纳的最大值（产生进位）或最小值（产生借位）时，CF 会被置为 `1`。
    - **例子**: 在8位加法中，`200 + 100 = 300`。因为结果 `300` 超过了8位无符号数能表示的最大值 `255`，所以 CF 会被置为 `1`。在多精度运算（如64位加法用32位寄存器实现）中，这个标志位至关重要。
2. **P - Parity Flag (PF, 位 2): 奇偶标志**
    - **用途**: 用于检测数据传输中的错误。如果运算结果的最低有效字节 (least-significant byte) 中，值为 `1` 的位的个数是**偶数**，PF 就会被置为 `1`；如果是奇数，则置为 `0`。
    - **例子**: 如果结果是 `01100011B`，其中有4个 `1`（偶数），那么 PF = `1`。
3. **A - Auxiliary Flag (AF, 位 4): 辅助进位标志**
    - **用途**: 主要用于二进制编码的十进制数 (BCD) 运算。当一个算术运算在结果的第3位和第4位之间（即低4位到高4位）产生进位或借位时，AF 会被置为 `1`。
    - **说明**: 这个标志位比较特殊，主要由 `DAA` (十进制加法调整) 和 `DAS` (十进制减法调整) 等指令使用，普通程序员很少直接关心它。
4. **Z - Zero Flag (ZF, 位 6): 零标志**
    - **用途**: 这是最常用的标志位之一。如果一个运算的结果等于**零**，ZF 会被置为 `1`；如果不为零，则置为 `0`。
    - **例子**: `CMP EAX, EBX` (比较 EAX 和 EBX) 指令实际上是执行 `EAX - EBX` 并根据结果设置标志位。如果 EAX 和 EBX 的值相等，差为零，则 ZF = `1`。条件跳转指令 `JE` (Jump if Equal) 和 `JNE` (Jump if Not Equal) 就是通过检测 ZF 来工作的。
5. **S - Sign Flag (SF, 位 7): 符号标志**
    - **用途**: 反映有符号数运算结果的正负。它直接复制运算结果的最高有效位 (Most Significant Bit, MSB)。
    - **例子**: 在32位运算中，如果结果的第31位是 `1`，表示这是一个负数，SF 就会被置为 `1`；如果第31位是 `0`，表示正数，SF 就置为 `0`。
6. **O - Overflow Flag (OF, 位 11): 溢出标志**
    - **用途**: 用于**有符号数**运算。当运算结果超出了目标操作数能表示的范围（正数太大变成负数，或负数太小变成正数）时，OF 会被置为 `1`，表示发生了溢出。
    - **例子**: 在8位有符号数（范围 -128 到 127）加法中，`100 + 50 = 150`。结果 `150` 超出了 `127`，其二进制表示会被解释为一个负数，此时 OF = `1`。

- **T - Trap Flag (TF, 位 8)**: 陷阱标志。当 TF = `1` 时，CPU进入单步执行模式，每执行一条指令后都会产生一个调试中断。这是调试器 (debugger) 实现单步调试功能的关键。
- **I - Interrupt Enable Flag (IF, 位 9)**: 中断允许标志。当 IF = `1` 时，CPU可以响应外部设备的可屏蔽中断请求。操作系统可以通过设置该位为 `0` 来临时禁止中断。
- **D - Direction Flag (DF, 位 10)**: 方向标志。用于控制字符串操作指令（如 `MOVS`, `CMPS`）的处理方向。当 DF = `0` 时，地址会自动增加（从低到高处理）；当 DF = `1` 时，地址会自动减少（从高到低处理）。
- **ID - ID Flag (位 21)**: 标识标志。如果程序可以设置和清除这个标志，就说明CPU支持 `CPUID` 指令。`CPUID` 指令用于获取处理器的详细信息（如制造商、型号、支持的功能等）。
- **VM (virtual mode - 虚拟模式) 标志位** 在保护模式系统中选择虚拟模式操作。
- **ID (identification - 标识) 标志** 表明奔腾 (Pentium) 微处理器支持 `CPUID` 指令。
    - `CPUID` 指令为系统提供有关奔腾微处理器的信息。
- **VIF** 是中断标志位的一个副本，可用于奔腾4处理器 — (**virtual interrupt - 虚拟中断**)。
- **VIP (virtual - 虚拟)** 为奔腾处理器提供关于虚拟模式中断的信息 — (**interrupt pending - 中断挂起**)。
    - 用于多任务环境中以提供虚拟中断标志。
- **IOPL** 在保护模式操作中使用，用于为 I/O 设备选择特权级别。
- **NT (nested task - 嵌套任务) 标志** 表明在保护模式操作中，当前任务嵌套在另一个任务之内。
- **AC (alignment check - 对齐检查) 标志位** 如果一个字 (word) 或双字 (doubleword) 在非字或非双字边界上被寻址，该标志位就会被激活。
- **RF (resume - 恢复) 标志** 与调试一同使用，用于控制下一条指令执行后的恢复流程。
        
## 2 Segment Registers


- **CS (code ) segment** 存放由微处理器使用的代码（程序和过程）。
- **DS (data) segment** 包含程序所使用的大部分数据。
    - 数据通过一个偏移地址，或通过持有该偏移地址的其他寄存器的内容来访问。
- **ES (extra - 附加) 段** 一个额外的数据段，被某些指令用来存放目标数据。
- **SS (stack - 栈) 段** 定义了用于栈的内存区域。
    - 栈的入口点由栈段寄存器和栈指针寄存器共同决定。
    - BP 寄存器也用于寻址栈段内的数据。
- **FS 和 GS 段** 是补充的段寄存器，在 80386 到 酷睿2 (Core2) 微处理器中可用。
    - 允许程序访问两个额外的内存段。
    - 这些段被用于操作系统相关的功能。
    - Windows 使用这些段来执行内部操作。
#### 2.1.1 64位模式下的段寄存器 (Segment Registers In 64-bit mode)

- 在64位模式下，处理器只识别 **CS**、**FS** 和 **GS** 段。
- 软件可以使用 **FS** 和 **GS** 段基址寄存器作为地址计算的**基址寄存器**。
- 64位模式假定所有其他数据段寄存器 (**DS**、**ES** 和 **SS**) 的基地址都为 **0**
![](assets/Pasted%20image%2020250925110524.png)

### 2.2 System Registers

![](assets/Pasted%20image%2020250925110547.png)

## 3 Modes of Operation

![](assets/Pasted%20image%2020250925111058.png)

### 3.1 长模式与64位模式 (Long mode and 64-bit mode)

- **长模式 (Long mode)**，英特尔称之为 IA-32e（"e" 代表 "extensions" - 扩展），是传统保护模式的一个扩展。
- 长模式包含两种子模式：**64位模式 (64-bit mode)** 和 **兼容模式 (compatibility mode)**。
    - **64位模式** 支持64位架构的所有特性和寄存器扩展。    
    - **兼容模式** 支持与现有的16位和32位应用程序的二进制兼容性。
- 长模式不支持传统的实模式 (real mode) 或传统的虚拟8086模式 (virtual-8086 mode)。
---
### 3.2 兼容模式 (Compatibility mode）
- **兼容模式 (Compatibility mode)** 是长模式的第二种子模式，它允许64位的操作系统运行现有的16位和32位x86应用程序。
- 这些传统的应用程序在兼容模式下无需重新编译即可运行。
- 兼容模式下的应用程序使用32位或16位寻址，并且可以访问虚拟地址空间的前4GB。
- 传统的x86指令前缀用于在16位和32位的地址及操作数大小之间进行切换。

---
### 3.3 传统模式 (Legacy mode)
- **传统模式 (Legacy mode)** 包含三种子模式：
    - **保护模式 (Protected mode)** 支持带有内存分段、可选分页和特权级检查的16位和32位程序。保护模式下的程序可以访问高达**4GB**的内存空间。
    - **虚拟8086模式 (Virtual-8086 mode)** 支持在保护模式下作为任务运行的16位实模式程序。它使用一种简单形式的内存分段、可选分页和有限的保护检查。此模式下的程序可以访问高达**1MB**的内存空间。
    - **实模式 (Real mode)** 支持使用基于寄存器的内存分段的16位程序。它不支持分页或保护检查。实模式下的程序可以访问高达**1MB**的内存空间。
---
### 3.4 系统管理模式 (System management mode）
- **系统管理模式 (System management mode, SMM)** 是一种为系统控制活动而设计的操作模式，它对常规的系统软件是透明的。
- 电源管理是系统管理模式的一个常用用途。
- SMM 主要的目标用户是平台**固件 (firmware)** 和专门的底层**设备驱动程序 (device drivers)**
![](assets/Pasted%20image%2020250925111445.png)

## 4 Memory Management Requirements

### 4.1 Relocation

### 4.2 Segmentation
![](assets/Pasted%20image%2020250925112507.png)
段寄存器地址从高位与编程时的地址拼接，那么就在不同的段中寻址了

### 4.3 Paging

![](assets/Pasted%20image%2020250925112940.png)
![](assets/Pasted%20image%2020250925113124.png)

线性地址在保护模式下又被称为虚拟地址，![](assets/Pasted%20image%2020250925114326.png)

![](assets/Pasted%20image%2020250925114344.png)

#### 4.3.1 Real Mode Memory Addressing

- 80286及以上的处理器可以在实模式 (real mode) 或保护模式 (protected mode) 下运行。
    
- **实模式操作**只允许对内存空间的前1M字节进行寻址——即使是在奔腾4 (Pentium 4) 或酷睿2 (Core2) 微处理器中也是如此。
    
    - 内存的前1M字节被称为**实内存 (real memory)**、**常规内存 (conventional memory)** 或 **DOS内存系统**。

- 所有的**实模式**内存地址都必须由一个**段地址 (segment address)** 加上一个**偏移地址 (offset address)** 组成。
    
    - **段地址 (segment address)** 定义了任意一个64K字节内存段的起始地址。
        
    - **偏移地址 (offset address)** 选定64K字节内存段内的任意位置。
        
- **线性地址 (Linear address)** 是通过将以下两者相加生成的：
    
    - 一个16位的段寄存器（段地址），将其**左移四位 (shifted left four bits)**
        
    - 以及一个16位的偏移量（段内偏移或有效地址）

## 5 Protected Mode Memory Addressing


**G(granularity bit):** 
- _G = 1_: $ending = starting + (limit)FFF~H$
![](assets/Pasted%20image%2020251009101138.png)

![](assets/Pasted%20image%2020251009103127.png)
栈是私有数据，只有三个权限都一致才能访问

### 5.1 Program invisivble registers

三类描述符表： GDT, LDT, IDT

四种寄存器
- **GDTR and IDTR**: 存储GDT和IDT的地址，在进入保护模式之前加载
- LDTR and TR: GDT中的特殊描述符

ldtr存放的事局部描述符表在全局描述符表中的位置，然后找到ldt的基地址

局部描述符与应用上下文切换密切相关

## 6 Memory Paging

- effective addresses/segment offsets
- logical addresses
- linear addresses
- Physical addresses

$Effective~Address = Base + (Scale\times Index) + Displacement$

![](assets/Pasted%20image%2020251009105849.png)![](assets/Pasted%20image%2020251009105903.png)