---
layout: post
title: 硬件原理图
---

##  硬件原理图

### 核心板
	SoC: 	S5PV210 -> 584pin	(S5PV210_UM_REV1.1.pdf)
	Mem:	K4T1G -> 1Gb=128MB (K4T1G164QE_rev11.pdf)
	Flash: 	K9F2G08 -> 256M*8bit=256MB (K9F2G08.pdf)
	Reset:	Max811
	Jtag:	TCK/TDI/TDO/TMS/TRST
	CLOCK:	24Mhz (X1) XXTI/XXTO
	LED:	LED1-4
	HDMI:	mini HDMI(CON8)

### 底板
	CONN-AB:	30*2 * 2个 = 120pin 引出 (例如串口TxD/RxD)
	CONN-C:		15*2 = 30pin 引出 (例如I2C, CAM)
	Power-On/Off: 	S1
	NET：		DM9000AEP (Ethernet) HR91105A
	Audio:		WM8960 (DA/AD convert)
	Buzzer:		PWM beep
	RTC:		Real-Time Clock
	I2C-Eeprom:	MAC address (6 bytes)
	Buttons:	K1-K8 (XEINT16-27)
	SD-CARD:	Data(4pin) + (4pin)
	UART:		串口(通用异步收发器)
	LCD：		40pin / 45pin (DATA-24pin)
	
		

<br> <br> 
<div> <a href="chp2-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp2-3.html">下一节</a> </div> <br> <br>