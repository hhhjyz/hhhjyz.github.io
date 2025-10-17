# Physical Layer

协议模型中的最低层，是网络建立的基础，真正的信号传输都是在物理层完成的

最小单位：bit

**涉及的计算**：
- throughput
- latency
- error rate

## 1 The theoretical Basis 

### 1.1 Fourier Series

在信道中传输的信息，通过电压和电流的变化来表示，即随时间变换的函数

**傅立叶级数**：针对周期信号

![](assets/Pasted%20image%2020250923100946.png)

- **带宽 (Bandwidth)**: 未受强烈衰减的频率范围的宽度。 它是传输介质的物理属性。
    
- **基带信号 (Baseband signals)**: 频率从0开始到某个最大值的信号。
    
- **通带信号 (Passband signals)**: 信号被移动到更高频率范围进行传输，例如所有无线传输。

带宽(Hz)和最大速率(bits/sec)直接相关

**channel capacity**: 在给定的条件下（发射功率，出错率）能达到的最大速率
- data rate(bps),对二进制输入输出，没有噪声，data rate = 1bps
- - **带宽**：由以下因素决定
    - 发射端为防止来自其他信源的干扰而进行的有意限制。
    - 传输介质的物理特性 (Hz)。（带宽越大，通信系统的成本越高）。
- **噪声**
- **错误率**：发生错误的速率，其中错误指的是发送“0”时接收到“1”，或发送“1”时接收到“0”。

- **奈奎斯特带宽 (Nyquist Bandwidth)**: 早在1924年，AT&T的工程师亨利·奈奎斯特就指出，即使是完美的信道，其传输容量也是有限的。 对于一个带宽为 B 的无噪声信道，如果每个信号符号有 V 个离散电平，最大数据速率 C 为：
    $$C=2B \log_2​V$$
    这个公式关注的是如何在没有符号间干扰（ISI, inter-symbol interference）的情况下最快地发送符号。
    矩形脉冲的频谱无限延伸， 虽然主瓣能量集中在低频， 但旁瓣一直延伸到无穷远。 注意这里横轴表示频率f， 不是时间轴！如果是一个时限信号，那它一定不会是一个带限信号
- **香农容量 (Shannon Capacity)**: 对于一个带宽为 B 且存在高斯白噪声的信道，其理论最大可靠数据速率 C（即信道容量）由香农公式给出：$$C=B\log_2​(1+SNR)bits/second$$$$SNR_{dB} = 10\log_{10}(signal~power/ noise~power) = 10\log_{10}SNR$$
    其中，SNR 是信噪比（Signal-to-Noise Ratio）。 这个公式考虑了噪声的影响，并指出了在给定信噪比下，每个符号能可靠承载多少比特信息。 
    ![](assets/Pasted%20image%2020250923112632.png)

data rate 越高，噪声影响的bits更多

## 2 Transmission Media

### 2.1 Persistent Storage


## 3 Digital modulation and multiplexing

**digital modulation**: 将bit转化为模拟信号的过程

### 3.1 Baseband Transmission

![](assets/Pasted%20image%2020250928104332.png)

- NRZ: 用正电压 / 有光表示 1，负电压 / 没有光表示 0,对于NRZ，信号可以在正负电平之间循环，每个电平最多持续2个比特。这意味着当比特率为 B 比特/秒时，我们至少需要 B/2 Hz 的带宽