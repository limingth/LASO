---
layout: post
title: 源码开放学ARM - UART 控制器 - 串口控制器结构
---

## 串口控制器功能
UART Controller (p853-p882)
	类似是一个函数，需要了解它的->输入，输出，如何实现
		1. 输出：Timing (串行通信实现时序图)
			Serial I/O Frame Timing Diagram (Normal UART)  ->p860
		2. 输入：SFR (串口控制器的寄存器)
			REGISTER DESCRIPTION  ->p864
		3. 实现：Block Diagram (结构框图)
			Block Diagram of UART  ->p854
## 串口控制器结构			
	Block Diagram:
		Peripheral Bus 外设总线
			*(int *)SFR_ADDR = value;
		Controll Unit 控制单元
			Control Regs
		Baud-Rate Gengenrator 波特率发生器
			Clock Source 时钟源 (66M)
		Transmitter 发送器
			Transmit shift 发送移位器
			Transmit buffer 发送队列FIFO缓冲器
		Receiver 接收器
			Receiver shift 接收移位器

	

<br> <br> 
<div> <a href="chp5-3.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp5-5.html">下一节</a> </div> <br> <br>