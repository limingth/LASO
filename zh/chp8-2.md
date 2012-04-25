---
layout: post
title: 异常处理 - 异常向量表的实现
---

## 异常向量表的实现
### ARM 的异常向量表是指什么? 有什么特点？
	异常发生后的入口地址
		从0开始的32字节，7种异常都有入口，0x14保留
		向量一般情况下是指地址，但是异常向量表里面存放是指令
		所有ARM内核的处理器都按上述方式工作
		0x0: reset
		0x8: swi
		0x18: IRQ
		0x1C: FIQ (放在最后，那么好处是可以省一条跳转)
	里面存放的是跳转指令
		跳转指令可以是 B (相对，有限) / BL(不能)
		还可以是 LDR (任意跳转)
			B: 		0xEA000000 + offset
			LDR: 	0xE59ff000 + offset

<br> <br> 
<div> <a href="chp8-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp8-3.html">下一节</a> </div> <br> <br>