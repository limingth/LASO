---
layout: post
title: SDRAM 控制器 - SDRAM 驱动代码实现
---

## SDRAM 驱动代码实现



## 课后作业：
	1) 修改 uart_init, 把波特率改为 19200
	2) 修改 时钟发生器，把 PCLK 输出改为 33M, 波特率重新计算 115200
	3) 实现 puts("hello, world");  输出 "hello, world" 
		实现 实现 putchar_hex('a') 十六进制, 输出 0x61
	目的：了解 \n换行  \r回车  之间的差别
		
	4) 修改 DRAM Controller，
		把 col address 的宽度改为 9bit
		把 data bit 的32bit 改为 8bit
		用上述 putchar_hex 输出接口，观察读取DDR内存单元的数值变化

<br> <br> 
<div> <a href="chp6-5.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp7-1.html">下一节</a> </div> <br> <br>