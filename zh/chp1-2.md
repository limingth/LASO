---
layout: post
title: 硬件原理图
---

##  硬件原理图

通常提供硬件平台的厂家，都会随开发板光盘附带硬件原理图。硬件原理图一般都是以PDF格式提供，这和硬件设计人员通过使用 Protel/OrCAD Capture 这样的硬件原理图设计工具产生的文件不同，但PDF的文档方便查阅，易于打开，对于嵌入式软件开发工程师用于开发已经足够了。

### 原理图包含的信息
核心板的原理图和底板的原理图，通常分为两个文件存放。通过查看原理图，能够知道哪些信息？

*有哪些芯片  
*芯片有哪些管脚  
*芯片之间的连接关系  
*芯片与外设本身之间的连接关系  
*得到有些芯片管脚的默认连接（接地/接电源/NC (No Connection) 不连接的管脚）  
*电阻电容等分立元件（模拟电路）  

### 核心板
核心板一般包含了最小系统，也就是主芯片SoC，内存SDRAM，闪存FLASH，复位电路Reset，调试接口JTAG，时钟CLOCK和电源Power。请在原理图上分别找到这些器件，并初步熟悉了解它们的芯片型号。

	**主芯片	SoC: 	S5PV210 -> 584pin			(S5PV210_UM_REV1.1.pdf)  
	**内存		Mem:	K4T1G -> 1Gb=128MB 			(K4T1G164QE_rev11.pdf)  
	**闪存		Flash: 	K9F2G08 -> 2Gbx8bit=256Mx1Byte=256MB	(K9F2G08.pdf)  
	**复位电路	Reset:	Max811					(MAX811T.pdf)  
	**调试电路	Jtag:	TCK/TDI/TDO/TMS/TRST  
	**时钟		CLOCK:	24Mhz (X1) XXTI/XXTO  
	**发光二极管	LED:	LED1-4  
	**高清接口	HDMI:	mini HDMI(CON8)  

### 底板
底板一般包含了常见外设，例如通用GPIO，串口UART，网卡Net，液晶屏接口LCD，音频接口Audio等。请在原理图上找到这些器件，并初步熟悉了解它们的芯片型号。

	CONN-AB:	30*2 * 2个 = 120pin 引出 (例如串口TxD/RxD)
	CONN-C:		15*2 = 30pin 引出 (例如I2C, CAM)
	Power-On/Off: 	S1
	NET：		DM9000AEP (Ethernet) HR91105A		(DM9000.pdf)
	Audio:		WM8960 (DA/AD convert)			(WM8960_Rev40.pdf)
	Buzzer:		PWM beep
	RTC:		Real-Time Clock
	I2C-Eeprom:	MAC address (6 bytes)
	Buttons:	K1-K8 (XEINT16-27)
	SD-CARD:	Data(4pin) + (4pin)
	UART:		串口(通用异步收发器)			(MAX3232.pdf)
	LCD：		40pin / 45pin (DATA-24pin)		(H43-HSD043I9W1.pdf)
	
	
以上所有芯片的数据手册，都可以在开发板附带光盘中找到，也可以从 <https://github.com/limingth/ARM-Resources> 链接下载得到。


<br> <br> 
<div> <a href="chp1-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp1-3.html">下一节</a> </div> <br> <br>