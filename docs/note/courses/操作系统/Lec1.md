
# Overview

一个计算机系统分为硬件、操作系统、应用程序和一个用户

造作系统控制硬件，并协调其在不同应用程序和不同用户之间的使用

![](assets/Pasted%20image%2020250917211928.png)

操作系统包括kernel,简化应用开发并提供功能的中间件框架，以及运行时协助管理系统运行的系统程序

## 1 Interrupt

device controller 通过中断通知 device driver 已经完成操作

硬件可以通过向CPU发送信号来触发中断

CPU有一个interrupt-request line

