---
layout: post
title: 源码开放学ARM - 异常处理 - 软中断异常代码实现
---

## 软中断异常代码实现
	
### New Project

	start.s -> add to project
	main.c  -> add to project
	Setting -> ARM Linker -> 1. robase 0x21000000
				 2. layout start.o 
				 3. PostLinker fromELF
	
	start.s
		1. 切换模式到 USR = 0xD0
		2. 跳转到 C
		
	main.c
		1. 在C程序中调用软中断的方法
			int __swi(0x1) sys_add(int a, int b, int c);
			ret = sys_add(1, 2, 3);
			上述代码会编译生成 4 条指令
			[0xe3a02003]   mov      r2,#3
			[0xe3a01002]   mov      r1,#2
			[0xe3a00001]   mov      r0,#1
			[0xef000001]   swi      0x1

		2. 在C程序中，注册 SWI 异常的处理函数
			因为要跳转的地址是在 0x21000000+ 区域，
			因此只能在 0x8 注册一条 LDR 跳转指令，而不是B指令
			实现办法是:	0x8:  写入一条 0xE59FFxxx (xxx=>020)
			           	0x30: 写入 handler 的地址 
	
		3. 实现一个真正的在汇编中能够返回模式和地址的 asm_swi_handler
			需要修改 0x30: (int)asm_swi_handler
			需要在 start.s 里面加入一个 asm_swi_handler 的函数
			它的实现应该为：
			asm_swi_handler	
				stmfd r13!, {r0-r12, r14}
	
				bl C_swi_handler
	
				ldmfd r13!, {r0-r12, r14}
				movs pc, lr	
	
			需要  	import C_swi_handler
				export asm_swi_handler
	
		4. 修改 C_swi_handler 使得它能够传递用户参数
		int C_swi_handler(int usera, int userb, int userc)
		{
			return add(usera, userb, userc);	
		}
	
		5. 修改 asm_swi_handler	使得它能够漏过返回值给用户
		asm_swi_handler
			stmfd r13!, {r1-r12, r14}
	
			bl C_swi_handler
	
			ldmfd r13!, {r1-r12, r14}
	
		6. 最后测试时，把 0x8 处的断点去掉，
		从用户看来，系统调用 sys_add 本质就是一条 swi 指令，而不是一个函数。
	
		


[上一节](chp8-3.html)  |  [目录索引](../index.html)  |  [下一节](chp9-1.html)
