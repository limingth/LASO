---
layout: post
title: 源码开放学ARM - 芯片手册导读 - 特殊功能寄存器
---

## 特殊功能寄存器

特殊功能寄存器(Special Function Register) 是在 SoC 芯片内部的一个重要组成部分，区别于 ARM 内核的 R0-R15 这样的寄存器，这些寄存器本质上和片内的 SRAM 一样，按地址访问，掉电以后值会丢失，无需初始化直接可以访问这样一些特性。 

从逻辑结构上看，所有对特殊功能寄存器(以下简称 SFRs) 的访问操作的实现，都对应到不同的外设控制器上。访问指令本质上和对外部存储器的读写一样，都是通过LDR和STR汇编指令来实现的。

### 关于内核，控制器，总线和外设之间的关系
	内核：Cortex-A8 (CORE)
		寄存器 Regs (R0-R15, CPSR)
		指令集 Ins (add/sub, ldr/str)
		总线 (访存指令)
	地址概念：
		cpu：32bit	ldr r0, [r1] 	(r1:addr)
			1)SRAM:
			2)SFR:
			--------------------------
			3)DDR:
			4)Device internal memory:
	总线概念：
		片内总线
			地址线-32bit	由 地址寄存器的字节数 决定
		片外总线
			地址线-29bit	由 设计cpu时，外接的单个存储器件的最大容量 决定
		内存控制器
			通过把 32bit 的最大访存范围，分割为若干个 bank 来进行统一访问
	外设控制器概念：
		外设 = 可见(接插件/关联芯片/连接线) + 不可见(外设控制器)
		外设控制器 = 寄存器接口 <---- 黑匣子(类似函数库.o) ---> 外设工作的时序图
	总结关系：
		都是涉及到裸板驱动 (无操作系统/无MMU参与)
		本质上除了运算之外，都是变成为对地址的读写
		C语言的指针，在本阶段，占一个很重要的角色。
	
### 特殊功能寄存器的设置
所有我们设置的SFR，都应该和外设控制器内部的工作逻辑有关。因此了解Controller的工作方式和内在逻辑，是我们能够得以正确配置这些寄存器的正确路径。

每一个 controllers 都有一批自己的寄存器，通过读写操作就可以用来进行软件编程和控制。

#### Register Name
	全大写，未来用来宏定义的，前面部分是这个Controller的缩写，后面部分就是它的功能)
	CON - control 控制
	STAT - status 状态
	DAT - data 数据
	MOD - mode 模式
	FIFO - fifo 缓冲寄存器
	CFG - config 配置
	CNT - counter 计数
	TXH - transmit Holder 发送缓冲
	RXH - receive Holder 接收缓冲
	BRDIV - baud divisor 波特率分频因子
	
#### Register Address
	这个地址，是在写代码的时候，所对应操作的寄存器的唯一标识。
	名字只是用来帮助记忆的，不是内部标识，也不是用来给编译器的。
	
	
#### 定义举例
	#define PRO_ID  	(*(volatile unsigned int *)0xE0000000)
	
	volatile 关键词的作用
		http://learn.akae.cn/media/index.html
		http://learn.akae.cn/media/ch19s06.html
		
	写法要点：
		强制类型转换，1个括号
		防止优先级结合问题，1个括号
		unsigned 无符号类型，防止右移问题
		int 类型对应4个字节，依据手册决定是否换为 char 类型


<br> <br> 
<div> <a href="chp2-2.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp2-4.html">下一节</a> </div> <br> <br>