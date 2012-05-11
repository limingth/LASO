---
layout: post
title: 源码开放学ARM - 芯片手册导读 - 存储管理和地址映射
---

## 存储管理和地址映射

### 存储器件
对于一个SoC芯片，最重要的认识莫过于了解它所能支持的存储器件和地址空间。这对于任何一个SoC芯片而言，都应该放在精读内容的首位。

存储器件主要包括以下这些，了解和掌握它们的接口和存储特性，是做嵌入式底层开发人员所必须掌握的理论知识。

	1) 片内的 RAM 
		iRAM - SRAM
	2) 片外的 RAM
		SRAM
		SDRAM
		DDR SDRAM
	3) 片内的 ROM
		iROM
	4) 片外的 ROM
		Nor Flash
		Nand Flash
		OneNand Flash

### 地址空间
地址空间主要是指32位地址线 0-4G 所对应（也可称为映射）的存储器件和访问方法（如何读写）。
	
	0 地址 	- 加电后运行的第一条指令
	片内RAM	- 可以无需初始化直接使用的存储	
	SFR	- 特殊功能寄存器的地址范围
	SDRAM 	- 程序通过bootloader命令可以下载到的地址
	虚地址 	- 使能MMU之后的虚拟地址
	
### Memory Map 存储映射
存储映射这个概念是本节最重要的知识，以 S5PV210 芯片为例，了解并掌握它的映射情况。

	Boot Area: 0x0 - 0x20000000 =512M
		Mirrored region depending on boot mode
	
	DRAM0: 	0x20000000 - 0x3FFFFFFF: 2^29 = 512M
	DRAM1:  0x40000000 - 0x7FFFFFFF: =1G
	SFR:	0xE0000000 - 0xFFFFFFFF: =512M
	iROM:	0xD0000000 - 0xD0010000: =64K
	iRAM: 	0xD0020000 - 0xD0038000: =96K (0x18=24*0x1000) 
	
	思考问题： 根据上述地址映射，对不同地址进行的访问，会引发底层硬件产生何种响应？
		*(int *)0x00000000 : it depends
		*(int *)0x21000000 : DRAM
		*(int *)0xE0200280 : GPIO SFR
		*(int *)0xE2900000 : UART SFR




[上一节](chp2-1.html)  |  [目录索引](../index.html)  |  [下一节](chp2-3.html)
