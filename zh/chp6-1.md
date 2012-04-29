---
layout: post
title: SDRAM 控制器 - SDRAM 硬件连接
---

# SDRAM 控制器 
## DDR SDRAM 硬件连接情况 (原理图)
	Memory Summary (p4)
	DDR SDRAM (p8)
		行地址：14
		列地址: ?
	
	DDR DRAM 的内部结构（容量）
		数据线 8bit * 4chip = 32bit data bus
		地址线 14根
			内部分 8 bank，每个bank=64M
			0-64M:		0x4000000
					100 000...000 (24-0)
			64M-128M:
					200 000...000 (24-0)
			128-192M:
					300 000...000 (24-0)
					
				  000
				  111
				  bank0,bank1,bank2
			
			26根-64M/bank
			行地址：14根
			列地址：10根
			--------------
					24根 + (A0,A1 -- GND)
					
		CPU:
			512M = 2^29
			29根地址线 (A0-A28)
				A28,A27,A26 : BA2,BA1,BA0
				A25-A12: 行地址 14根
				A11-A2: 列地址 10根
				A1, A0, - GND
	

<br> <br> 
<div> <a href="chp5-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp6-2.html">下一节</a> </div> <br> <br>