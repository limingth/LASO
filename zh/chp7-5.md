---
layout: post
title: NandFlash 控制器 - NandFlash 寄存器配置
---

## Nand Flash Controller 控制器寄存器
	SFR 特殊功能寄存器 (部分)
	控制类
		NFCONF  0xB0E00000 
		NFCONT  0xB0E00004 	
		NFCMMD  0xB0E00008 	[7:0] 命令
		NFADDR  0xB0E0000C 	[7:0] 地址
	数据类	
		NFDATA  0xB0E00010 	[31:0] 数据
	状态类
		NFSTAT  0xB0E00028

## 初始化配置
		mw 0xe0200320 0x22222222
		mw 0xb0e00000 0x00006552
		mw 0xb0e00004 0x00c100c5
		mw 0xb0e00008 0x90
		mw 0xb0e0000c 0x00
		md 0xb0e00010
		
		GPIO 功能复用设置为 NF signal 						
			ALE: MP0_3[1]
			CLE: MP0_3[0]
			MP0_3CON 	Address = 0xE020_0320
			mw 0xe0200320 0x22222222
			
		NFCONF	0x00001000	-> 0x00006552
			NAND clock = 133M (p363-NFCON) -> 7.5ns (1 clock)
			Nand Timing (k9f2g08.pdf - p13)
			
			mw 0xb0e00000 0x00006552
			
		NFCONT 	0x00c100c6  -> 0x00c100c5
			mw 0xb0e00004 0x00c100c5
			
		NFCMMD:
			mw 0xb0e00008 0x90

		NFADDR:		
			mw 0xb0e0000c 0x00
		
		NFDATA:
			md 0xb0e00010
	

<br> <br> 
<div> <a href="chp7-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp7-6.html">下一节</a> </div> <br> <br>