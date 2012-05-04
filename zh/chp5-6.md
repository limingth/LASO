---
layout: post
title: 源码开放学ARM - UART 控制器 - 串口驱动代码实现
---

## 串口驱动代码实现
### uart.c 参考代码实现
	// uart.c
	#define ULCON0  	(*(volatile unsigned int *)0xE2900000) 
	#define UCON0  		(*(volatile unsigned int *)0xE2900004) 
	#define UTRSTAT0  	(*(volatile unsigned int *)0xE2900010)
	#define UTXH0  		(*(volatile unsigned char *)0xE2900020) 
	#define URXH0  		(*(volatile unsigned char *)0xE2900024) 
	#define UBRDIV0 	(*(volatile unsigned int *)0xE2900028) 
	#define UDIVSLOT0  	(*(volatile unsigned int *)0xE290002C) 

	void uart_init(void)
	{
		// 66Mhz / (115200*16) - 1 = 0x23
		// 66Mhz / (19200*16) - 1 = 0xD5
		//UBRDIV0 = 0xD5;
		return;
	}

	char uart_getchar(void)
	{
		char c;
		// polling receive status: if buffer is full
		//while ((UTRSTAT0 & (1<<0)) == 0)
		while (!(UTRSTAT0 & (1<<0)))
			;
		
		c = URXH0;
			
		return c;
	}

	void uart_putchar(char c)
	{
		// polling transmit status: if buffer is empty
		//while ((UTRSTAT0 & (1<<2)) == 0)
		while (!(UTRSTAT0 & (1<<2)))
			;
		
		UTXH0 = c;
		
		return;
	}

### uart.h 参考代码实现	
	
	// uart.h
	void uart_init(void);

	char uart_getchar(void);

	void uart_putchar(char c);

<br> <br> 
<div> <a href="chp5-5.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp6-1.html">下一节</a> </div> <br> <br>