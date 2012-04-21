---
layout: post
title: 存储管理和地址映射
---

## 存储管理和地址映射

### Memory Map 存储映射
	地址的划分
		*(int *)0x21000000 : DRAM
		*(int *)0xE0200280 : GPIO SFR
		*(int *)0xE2900000 : UART SFR
	DRAM0: 	0x20000000 - 0x3FFFFFFF: 2^29 = 512M
	DRAM1:  0x40000000 - 0x7FFFFFFF: =1G
	SFR:	0xE0000000 - 0xFFFFFFFF: =512M
	iROM:	0xD0000000 - 0xD0010000: =64K
	iRAM: 	0xD0020000 - 0xD0038000: =96K (0x18=24*0x1000) 
	Boot Area: 0x0 - 0x20000000 =512M
		Mirrored region depending on boot mode
	
	

<br> <br> 
<div> <a href="chp2-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp2-3.html">下一节</a> </div> <br> <br>