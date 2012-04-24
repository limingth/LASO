---
layout: post
title: GPIO 控制器 - GPIO 特殊功能寄存器
---

## GPIO 特殊功能寄存器
	GPIO REGISTER DESCRIPTION
		addr range: 0xE020_0000 - 0xE020_0F80
		Reg1: GPA0CON: GPA0 + CON (control)
			bit-field name: GPA0CON[7] -> GPA0_7
			field width: [31:28]
			GPA0_7 pin setting:
				input: 0000
				output: 0001
				UART_1: 0010
				INT: 1111

		Reg2: GPA0DAT
			bit-field: [7:0]
			when input:	the pin state(high-level:1 low-level:0)
			when output: set bit 1, output high-level
			when functional: leave this pin to peripheral controller

		Reg3: GPA0PUD
			Pull-up
			Pull-down
				00 = Pull-up/down disabled
				01 = Pull-down enabled
				10 = Pull-up enabled
				11 = Reserved

		Reg4: GPA0_INT_CON
			Sets the signaling method
				000 = Low level
				001 = High level
				010 = Falling edge triggered
				011 = Rising edge triggered
				100 = Both edge triggered
				101 ~ 111 = Reserved

		Reg5: GPA0_INT_MASK
			Enables Interrupt / Masked
				0 = Enables Interrupt
				1 = Masked

		Reg6: GPA0_INT_PEND
			Interrupt occur status
				0 = Not occur
				1 = Occur interrupt

	

<br> <br> 
<div> <a href="chp3-2.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp3-4.html">下一节</a> </div> <br> <br>