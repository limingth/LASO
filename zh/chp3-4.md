---
layout: post
title: 源码开放学ARM - GPIO 控制器 - GPIO 驱动代码实现
---

## GPIO 驱动代码实现

### 汇编程序
	TAB
	AREA led
	CODE, DATA
	READONLY
	label
	instruction...
	END

### ARM汇编的延时函数
		主程序中
		bl delay
		...

	delay
		ldr r0, =0x10000000
	go_on
		sub r0, r0, #1
		cmp r0, #0
		bne go_on

		mov pc, lr
	
### 立即数的表示
	mov 操作，#后面跟着的数字是立即数，会写入指令中
	有效位不超过8位，同时通过循环右移偶数位得到
	1 0000 0010 = 0x102
	10 0000 0100 = 0x204

### 连接开发板
	启动超级终端 hypertrm	(.exe)
	选择 COM1 (pc)
	修改波特率为 115200
	修改流控制为 无 (none) （影响输入）
	连接之后，输入 help
	如果发现有输出但无法输入，留意 Scroll Lock 是否被误按

	# loadb 输入命令
	超级终端菜单 -> 传送 -> 发送文件
	-> 选择文件 + Kermit协议 -> 点击发送
	# go 0x21000000
	之后观察 LED1 灯亮啦

### led.c 参考代码实现	

	// led.c
	#define GPJ2CON		(*(volatile unsigned int *)0xE0200280)
	#define GPJ2DAT		(*(volatile unsigned int *)0xE0200284)

	void led_init(void)
	{
		// LED1-4: GPJ2_0, ... GPJ2_3
		// 0001 0001 0001 0001 = output 
		GPJ2CON &= ~0xFFFF;	
		GPJ2CON |= 0x1111;	
		
		return;	
	}

	void led_on(void)
	{
		// set bit 0 -> led on
		GPJ2DAT &= ~0xF;
		
		return;	
	}

	void led_off(void)
	{
		// set bit 1 -> led off
		GPJ2DAT |= 0xF;
		
		return;	
	}

### button.c 参考代码实现

	// button.c	
	#define GPH2CON		(*(volatile unsigned int *)0xE0200C40)
	#define GPH2DAT		(*(volatile unsigned int *)0xE0200C44)

	void button_init(void)
	{
		// K1 - K4	(see mother board) 
		// GPH2_0 - GPH2_3
		// GPH2CON[0]   [3:0]   0000 = Input   
		// ...
		// GPH2CON[3]   [15:12]   0000 = Input     
		GPH2CON &= ~0xFFFF;
		
		return;	
	}

	int button_is_down(int which)
	{
		// button down -> DAT = 0
		int index = which - 1;
		
		if ((GPH2DAT & (1<<index)) == 0)
			return 1;
		
		return 0; 
	}

### buzzer.c 参考代码实现

	// buzzer.c
	#define GPD0CON		(*(volatile unsigned int *)0xE02000A0)
	#define GPD0DAT		(*(volatile unsigned int *)0xE02000A4)

	void buzzer_init(void)
	{
		// buzzer GPD0CON 
		// [0] SET 1	
		GPD0CON |= 1<<0;	
		
		return;	
	}

	void buzzer_on(void)
	{
		// set bit 1 -> buzzer on
		GPD0DAT |= 1<<0;
		
		return;	
	}

	void buzzer_off(void)
	{
		// set bit 0 -> buzzer off
		GPD0DAT &= ~(1<<0);
		
		return;	
	}




[上一节](chp3-3.html)  |  [目录索引](../index.html)  |  [下一节](chp4-1.html)
