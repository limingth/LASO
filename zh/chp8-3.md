---
layout: post
title: 异常处理 - 异常处理流程
---

## 异常处理流程
### ARM 的软中断异常发生后，硬件做何响应？
	硬件要做6件事情，以发生SWI异常为例：
		1. 保存 PC -> LR_svc
		2. 保存 CPSR -> SPSR_svc
		3. 修改 CPSR -> SVC mode
		4. 修改 CPSR I-bit -> disable IRQ
		5. 映射相应IRQ模式的寄存器
		6. 修改 PC -> 0x8
		
### cpu 内核跳转到 0x8 之后，软件需要做哪些工作？
	软件要做以下工作：
		1. 保存现场： r0-r12, r14 压栈
			STMFD r13!, {r0-r12, r14}
		2. 进入异常处理:
			BL	swi_handler
		3. 恢复现场： r0-r12, pc 出栈
			A) LDMFD r13!, {r0-r12, pc}^
			B) LDMFD r13!, {r0-r12, r14}
				movs pc, lr	
			将原来保存的 spsr 恢复给 CPSR

<br> <br> 
<div> <a href="chp8-2.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp8-4.html">下一节</a> </div> <br> <br>