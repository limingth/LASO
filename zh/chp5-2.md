---
layout: post
title: 源码开放学ARM - UART 控制器 - 串口管脚功能复用
---

## 串口的管脚功能复用 
参考 S5PV210芯片手册

	GPA0 Mux Function
	查看 GPA0[0] GPA0[1]，确认了 UART 的复用功能
	查看 GPA0CON 寄存器，了解如何设置 (0010)
	GPA0CON : 地址 0xe0200000
	
	[FriendlyLEG-TINY210]# md 0xe0200000
	e0200000: 22222222 000000ab 00005555 00000000    """"....UU......
	e0200010: 00000000 00000000 00005555 00000000    ........UU......

	[FriendlyLEG-TINY210]# mw 0xe0200000 0x22222202

	结论：RXD 影响接收， TXD 影响发送
		RTS和CTS对发送和接收暂时无影响（无流控制）

	

<br> <br> 
<div> <a href="chp5-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp5-3.html">下一节</a> </div> <br> <br>