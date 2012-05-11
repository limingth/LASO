---
layout: post
title: 源码开放学ARM - 异常处理 - 异常相关基本概念
---

# Exception 异常处理
## 异常相关基本概念 
### ARM 的工作模式有几种？各是哪些？
	7种
		USR: helloworld (非特权->MSR不允许执行)
		SYS: kernel        (2种非异常模式)
		--------------------
		SVC: reset加电     (5种异常模式)
		IRQ: EINT0 key
		FIQ: EINT0 key + MODE(REG)
		ABT: Memory Fault
		UND: execute undefine ins.
		
### ARM 的寄存器有多少？ 各是哪些？
	37个 = 31 通用 + 6 状态
		R0-R7: 未分组 (8个)
		R8-R12: 分组 (10个=5*2组 FIQ/~FIQ)
		R13-R14: 分组 (12个=2*6组 5异常+1非异常)
			R13: SP (stack pointer) 压栈/出栈
			R14: LR (Link Register) BL/异常发生后保存PC
		R15: 程序计数器 (1个)
			R15: PC (Program Counter) 流水线 +8
		-----------------------
		CPSR: 程序状态寄存器  (1个)
		SPSR: 备份的状态寄存器 (5个-5种异常)	
	
### ARM 的异常有几种？各是哪些？ 
	7种
		复位异常
		数据访问中止
		快速中断
		快速中断
		普通中断
		指令预取中止
		软件中断： SWI
		未定义指令异常： 什么样的指令是未定义？		
	



[上一节](chp7-6.html)  |  [目录索引](../index.html)  |  [下一节](chp8-2.html)
