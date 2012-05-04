---
layout: post
title: 源码开放学ARM - SDRAM 控制器 - SDRAM 硬件连接
---

# SDRAM 控制器 
## SDRAM 硬件连接

### 芯片引脚描述
硬件原理图

	1) 从芯片角度  
		地址线	A0-A13 	14根
			BA0, BA1, BA2
		数据线	DQ0-DQ7 * 4 = 32bit data bus (8bit * 4chips)
		控制线	nCS, nRAS, nCAS
		
	2) 从处理器角度  
		地址线	Xm1ADDR0-Xm1ADDR13
			Xm1BA0, Xm1BA1, Xm1CSn1/BA2
		数据线	Xm1DATA0-Xm1DATA31
		控制线	Xm1CSn0, Xm1RASn, Xm1CASn	


### SDRAM 内部结构 
芯片手册

	1Gbit = 128MByte
	
	内部分 8 bank，每个bank = 128M/8 = 16M (24根地址线)
	
	24根地址线的信号分为行地址和列地址  
		行地址：14根
		列地址：10根
						
### 从SoC芯片到SDRAM芯片
	128M * 4 chips = 512M 存储容量 (512M = 2^29)
				
	29根地址线 (A0-A28)
		A28,A27,A26 : BA2,BA1,BA0
		A25-A12: 行地址 14根
		A11-A2: 列地址 10根
		A1, A0, - GND

	如何访问 0x0 地址
		A31,A30,A29 : 000	-> nCS 片选无效
		A28,A27,A26 : 0 00
		A25-A12: 00 0000 0000 0000
		A11-A2: 0000 0000 00
		A1, A0: 00	
	
	如何访问 0x20000000 地址
		A31,A30,A29 : 001	-> nCS 片选有效
		A28,A27,A26 : 0 00
		A25-A12: 00 0000 0000 0000
		A11-A2: 0000 0000 00
		A1, A0: 00	
	
	如何访问 0x21000000 地址
		A31,A30,A29 : 001	-> nCS 片选有效
		A28,A27,A26 : 0 00
		A25-A12: 01 0000 0000 0000
		A11-A2: 0000 0000 00
		A1, A0: 00
		
	如何访问 0x3FFFFFFF 地址
		A31,A30,A29 : 001	-> nCS 片选有效
		A28,A27,A26 : 1 11
		A25-A12: 11 1111 1111 1111
		A11-A2: 1111 1111 11
		A1, A0: 11	
		
	如何访问 0x40000000 地址
		A31,A30,A29 : 010	-> nCS 片选无效
		A28,A27,A26 : 0 00
		A25-A12: 00 0000 0000 0000
		A11-A2: 0000 0000 00
		A1, A0: 00	
		
	

<br> <br> 
<div> <a href="chp5-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp6-2.html">下一节</a> </div> <br> <br>