---
layout: post
title: 源码开放学ARM - 中断处理 - 硬件中断异常代码实现
---

## 硬件中断异常代码实现
### 实验验证结论：		
	第一阶段，观察 GPIO 控制器里面的 pending bit 设置情况
	
		VIC0(IRQ/FIQ)STATUS	标识中断是否通过(IRQ/FIQ)
		VIC0INTSELECT		选择IRQ还是FIQ
		VIC0INTENABLE		使能中断通过
		VIC0RAWINTR			通过 EXT_INT_2_MASK 之后的情况
		---------------------
		EXT_INT_2_MASK		中断 Mask bit	
		EXT_INT_2_PEND		中断 Pending bit
		EXT_INT_2_CON		下升沿触发 Falling Edge
		GPH2CON				管脚复用 EXT_INT[16]	
			
	第二阶段，如何清除 pending bit ？
		写1清除 pending bit
		写0无效
	
	第三阶段，观察中断控制器中的使能Enable和状态Status标识寄存器
		使能 Enable 寄存器在 RAW 和 STATUS 之间
		STATUS 寄存器的标识位无需软件清除，只要清除 PENDing 位，该状态位自动清 0
		SELECT 寄存器决定该中断是 IRQ 还是 FIQ，从而硬件会设置不同的 STATUS 寄存器
	
	第四阶段，如何打开 CPSR I-bit ?
		通过 汇编 MSR 	(SVC: 0xD3	1101->0101 0x53->CPSR)
		__asm
		{
			mov r0, #0x53
			msr	cpsr, r0
		}
	
	第五阶段，中断发生了之后怎么办？
		接下来有2种处理办法：
		A) 简单的办法就是使用 VIC 向量中断控制器的功能
			1. 跳转的地址向量要提前设置好
			2. 通知 ARM11 内核，启用 VIC Port 功能
			紧接着的问题是，如何在执行完 beep 之后返回主程序？
				原因： beep 程序不能作为 IRQ_handler
				真正的 IRQ_handler 应该要完成
				1) 保存cpu 现场 STMFD
				2) 清除掉 Pending bit，调用 beep					
				3) 恢复cpu 现场 LDMFD (lr-4)->PC 	SPSR->CPSR
			修改 start.s ，实现 IRQ_handler
				1) IRQ 模式下的 sp 指针需要初始化
				2) 除了清除pending bit 之外，还需要清除 VIC0ADDRESS = 0;
			
		B) 复杂的办法就是不用 VIC ，自己实现全过程
			1. 当 IRQ 异常发生的时候，cpu 跳转到 0x18
			2. 背景知识：reset 0 地址被映射 map 到 iROM
				0 地址 在 iROM 中 (0xD0000000)
					iRAM (0xD0020000) -> 0x20000
				0x18: 0xEA000018
				最终在 iROM 中的程序(不可修改)会加载
				0xD0037400 地址开始的值，作为异常发生后要跳转的地址+offset	
			3. (int)IRQ_handler -> 0xD0037400 + 0x18
				如果是 SWI 软件中断，则在 0xD0037408 处填写swi_handler的地址	

	

<br> <br> 
<div> <a href="chp9-3.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp10-1.html">下一节</a> </div> <br> <br>