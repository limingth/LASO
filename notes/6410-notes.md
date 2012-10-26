	6410 开发 课堂笔记
	
	课题提问：
	1、bootloader 如何实现
	2、作用，功能，干什么，启动了哪些不可见的，有必要吗
	3、给一个板子，上面能够跑什么应用
	4、shell如何实现
	5、引脚，424pin如何连接，功能如何实现
	6、6410内部的组成结构，包含哪些模块
	7、ARM core 是什么，有什么，做什么用？(what is, what is in it, What can it do?)
	8、ARM 指令集
	9、ARM 汇编程序
	10、ARM 寄存器
	11、ARM 工作模式和分类
	12、MMU和Cache
	13、Bus in 6410 (data, addr, ctrl, chip select, enable, clock, ready/busy, RE, WE)
	14、外设 (寄存器，时钟，引脚，时序)
	15、存储体系 (SRAM, SDRAM, NorFlash, NandFlash)
	16、PLL和时钟，如何编程
	17、watchdog，DMA 如何实现
	18、Timer，Interrupt 如何实现

	课表安排:
	-----------------------------------------------
	ARM 基础理论和编程
	day 1: 上午	ARM 嵌入式硬件概述
	day 1: 下午	ARM ADS 开发工具
	day 2: 上午	ARM 体系结构概述
	day 2: 下午	ARM 指令集
	day 3: 上午	ARM 异常处理
	day 3: 下午	ARM 芯片手册导读
	day 4: 上午	ARM 编程实践(GPIO)
	day 4: 下午	ARM 编程实践(GPIO)
	-----------------------------------------------
	ARM Bootloader 项目实践
	day 5: 上午	UART工作原理和实现
	day 5: 下午	stdio 标准输入输出实现
	day 6: 上午	shell 用户命令行解析
	day 6: 下午	command 用户命令实现
	day 7: 上午	xmodem 协议分析
	day 7: 下午	bootloader 项目总结
	-----------------------------------------------
	ARM 中断理论和实践
	day 8: 上午	ARM 中断理论
	day 8: 下午	ARM 中断编程(按键中断实现)
	day 9: 上午	Timer 定时器中断实现
	day 9: 下午	UART 中断实现
	day 10: 上午	DMA 理论
	day 10: 下午	DMA 编程实践
	-----------------------------------------------

	day 1: 上午	ARM 嵌入式硬件概述
	板子-->
	芯片(引脚)-->
	6410 block diagram
		Processor Core
			MMU, CACHE, CP15,AMBA BUS
		BUS
			AXH  APB
		Peripheral
			MEMC, Clock&Power,GPIO, UART, Timer, DMA, NANDFLASH, SPI, IIS, IIC, USB, LCD, AC97
					

	day 1: 下午	ARM ADS 开发工具
	ADS、AXD 安装
	PDF 文档目录
	Bin 工具目录
		armasm 	-> asm
		arrmcc 	-> gcc (-c, -o)
		armlink	-> ld
				-ro-base
				-rw-base
				-entry
				-first
				__main vs main
		fromelf	-> objcopy, objdump
				-bin
				-c ,-d ,-a
		make.exe -> make

	汇编 		as	armasm
	编译		gcc 	armcc
			g++	armcpp
	链接		ld 	armlink
			*.o	*.o
	可执行文件	*.elf	*.axf
	做库文件	ar	armar
	二进制工具	objcopy	fromelf -bin
			objdump fromelf -c

	day 2: 上午	ARM 体系结构概述
	1、ARM 体系结构的特点(说出至少3个)
	2、什么是RISC？
	3、load/store体系指的是什么？
	4、什么叫conditional execution？
	5、ARM 体系结构最突出的优势是什么？

	ARM 体系结构 (ARM Architectur Refence Manual.pdf)
	1、工作模式 (7种)
	       usr, sys, svc, irq, fiq, und, abt

	2、寄存器组织 (37个)
	r0 - r7 : 8个*1组 - 8个
	r8 - r12: 5个*2组 - 10个
	r13- r14: 3个*6组 - 12个
	PC:	1个*1组 - 1个
	CPSR:	1个*1组 - 1个
	SPSR:	1个*5组 - 5个
	比较重要的：R13(sp), R14(lr), R15(pc), cpsr

	day 2: 下午	ARM 指令集
	3、指令集 (Assembly Guide.pdf)
	汇编语言格式 (TAB, AREA, END)
	汇编指令格式
		条件执行，操作数，寄存器编号，S位等
		常用指令：
			MOV, ADD, SUB, B, BL
		mov r0, pc(三级流水线)
			用途 [B指令构造0xEA000000, LDR指令构造0xe59ff000([pc, #000])]
		STR (字节B, 半字H, 多寄存器STM)
		LDR (字节B, 半字H, 多寄存器STM)

	汇编指令分类：
	数据处理 mov, add, sub
	跳转 b, bl
	访存 ldr, str, 
	栈指令 ldm, stm

	特权指令： 对状态寄存器的访问指令 msr, mrs
	异常产生指令:	软件中断指令 SWI
	特权指令：对协处理器操作的指令 mcr, mrc
	信号量指令：SWP

	day 3: 上午	ARM 异常处理
	异常处理 
	异常分类，异常模式，swi, 未定义指令 0xE7FF0010
	异常触发，异常硬件响应，
	软件异常向量表，异常处理程序handler，
	现场保存和恢复，异常返回（模式和地址同时返回，movs pc, lr)

	异常模式切换
		USR  ---SWI---> SVC
		
	MSR(读)  MRS(写)  为特权指令
	除了USR模式以外均可以改变自己的工作状态
				    |SWI 0x01
	(SUB, LDR, BIC)LR_SVC <-----|SWI 0x02

	异常分类（7种)，异常向量表(0, 0x08, 0x18...)
	异常触发，异常发生后的硬件响应(保存pc->lr, cpsr->spsr)
	向量表中的跳转指令 handler
	Hanlder
		1.压栈，保存现场
		2.异常处理程序
		3.出栈，恢复现场
		4.异常返回(同时)  movs pc, lr

	day 3: 下午	ARM 芯片手册导读

	6410芯片手册阅读

	一、OverView 
	1、Table of Contents - ***	
		芯片手册目录结构
	2、Product Overview - ***	
		芯片的概述
	3、Features - ***	
		芯片的功能特点
	4、Block Diagram - ******
		芯片的内部组织结构框图
	4.1 ARM core
		芯片所选用的ARM内核版本(ARM7TDMI, ARM920T, ARM1176JZF-S)
	4.2 Peripheral
		芯片内部所支持的外设控制器
	4.3 Bus(AHB, APB, AXI bus)
		芯片内部连接ARM内核与外设控制器的总线
	5、(Peripheral/Device) Controllers - ****
		外设控制器的详细描述章节
	6、Pin Assignments - *
		引脚的赋值（定义)- 289-FBGA, 424-Pin FBGA
	7、Pin Number - Pin Name - *
		引脚的序号（名称)
	8、Default Function - ***
		引脚默认功能 （引脚功能是可以多种选择的，其中有一个是默认的，其他的功能是复用这个引脚的)
	9、Signal Descriptions - *****
		（引脚的)信号描述
		分类描述 （每一个 controllers 都负责管理其中一部分自己的引脚，有多少，方向，功能作用，复用种类和排序)
	10、Special (Function) Registers - *******
		SFR 特殊功能寄存器 （每一个 controllers 都有一批自己的寄存器，读写操作就可以用来进行软件编程和控制)
		多则20多个，少则5, 6个
	10.1 Register Name
		全大写，未来用来宏定义的，前面部分是这个Controller的缩写，后面部分就是它的功能)
		CON - control 控制
		STAT - status 状态
		DAT - data 数据
		MOD - mode 模式
		FIFO - fifo 缓冲寄存器
		CFG - config 配置
		CNT - counter 计数
		TXH - transmit Holder 发送缓冲
		RXH - receive Holder 接收缓冲
		BRDIV - baud divisor 波特率分频因子
	10.2 Register Address
		这个地址，是在写代码的时候，所对应操作的寄存器的唯一标识。
		名字只是用来帮助记忆的，不是内部标识，也不是用来给编译器的。

	二、Memory Controller
	1、Memory - 存储布局  *******
		(System) Memory Map 
		(系统)内存映射图表 （芯片所支持的内存空间的起始地址和结束地址，内存空间大小)

		寻址空间： Bank （一段连续的内存地址，通常会外接到一个存储器件芯片上，通常有一个Size)
		这些存储器件可以包括 ROM 和 RAM (SRAM，NorFlash， SDRAM，NAND Flash)
	2、boot ROM - *****
		启动0地址所在的器件（内存芯片)，决定启动代码的执行。
		Operation Mode: (OM[xx]) 启动时操作模式

		6410： XOM[4:0]
		2440： OM[1:0]
	3、Memory Interface - *****
		存储器件的接口（SOC芯片和外部存储器件的连接关系)
	4、Memory SFR - ****
		与Memory有关的寄存器配置

	三、Clock Management
	时钟管理
	clock generator block diagram: 时钟发生器内部结构框图 - ****
	1、供给CPU外部晶振的时钟频率
		external crystal: 外部晶振 XXTIpll
		12MHz 12M赫兹
	2、锁相环进行倍频
		PLL: MIDV，PDIV，SDIV
		PLLCON: 寄存器
		APLL_CON: 0xC10A0301
		MPLL_CON: 0xC10A0301
		CLK_DIV0: 0x01043310
		CLK_DIV2: 0x00030000
		CLK_SRC:  0x00002007	bit[2:0]
		计算公式和查询表
	3、分频之后给各个不同的部件
		Divider: 
		DIVN: 分频参数
	4、输出
		ARMCLK/FCLK: ARM core 内核
		HCLK: AXI/AHB 高速设备
		PCLK: APB 外设
		SCLK: 特殊

	四、Power Management
	电源管理

	day 4: 上午	ARM 编程实践(GPIO)
	五、Device 外设
	1、I/O  （led, beep, button, pwm, seg7, motor, switch)
	OverView - ***
	how many, multi-functional, input/output
	Ports: Number of Pins : Muxed Pins : *****
	EINT : External Interrupt : ***
	Features: **
	Block diagram: *****
	Register Description: *******
	Memory Map: 0x7F008000 - 0x7F008288 (0x288 / 4 = 10 1000 1000 = 0xA2 = 162)
	Reset Value: ***** 芯片加电时候的默认值
	SFR
	GPXCON: 初始化时配置input/output方向，同时也可以
	GPXDAT: 运行时设置它的高低电平(1/0), 或者读取它的状态(1/0)
	GPXUP: 引脚的上拉/下拉状态

	用.s实现GPIO编程led, beep, button
	用.c实现GPIO编程led, beep, button
		1.main去掉   mymain
		2.delay() 引发链接参数 -first
		3.start.s   调整到C之前初始化sp

	day 4: 下午	ARM 编程实践(GPIO)
	用.s + .c实现较为复杂的应用程序

	ARM Bootloader 项目实践
	day 5: 上午	UART工作原理和实现
	编程实现函数接口（getchar, putchar)
	2、UART 
	OverView: ****
	Features: **
	Block Diagram: ******
	Description: ***
	1) Data Transmission: *****
	2) Data Reception: *****
	3) Auto Flow Control: **
	4) RS-232C INTERFACE: ****
	5) INTERRUPT/DMA REQUEST GENERATION: ***
	6) UART ERROR STATUS FIFO: **
	7) INFRA-RED (IR) MODE: *
	8) Serial I/O Frame Timing Diagram: *****
	9) Functional Pins Description(External Interface): *****
	Register Description: *******
	SFR: Memory Map: 0x7F005000 - 0x7F005C38
	[control register]
	ULCON0
	UCON0
	UFCON0
	UMCON0
	[status register]
	UTRSTAT0
	UERSTAT0
	UFSTAT0
	UMSTAT0
	[data register] - data, buffer, fifo, data-holding, 
	UTXH0
	URXH0
	[baud rate related]
	UBRDIV0
	UDIVSLOT0
	[interrupt related]
	UINTP0
	UINTSP0
	UINTM0

	day 5: 下午	stdio 标准输入输出实现
	编程实现函数接口(gets, puts, putcharx, putx)

	day 6: 上午	shell 用户命令行解析
	编程实现函数接口(shell_parse, shell_cmd)

	day 6: 下午	command 用户命令实现
	编程实现函数接口(cmd_help, cmd_md, cmd_set, cmd_loadb, cmd_go)

	day 7: 上午	xmodem 协议分析
	编程实现函数接口(cmd_xmodem)

	day 7: 下午	bootloader 项目总结


	day 8: 上午	ARM 中断理论
	数据手册导读 (S3C4510, S3C2440, S3C6410)
	和中断有关的重要概念
	中断源外设：Interrupt Source
	enable: 使能中断
	trigger: 设置中断方式
	中断控制器: Interrupt Controller
	Mode: 设置模式 (IRQ/FIQ)
	Mask: 设置屏蔽 (masked/unmask)
	Pend: 悬挂标志 (clear/before mask/after mask)
	Prio: 优先级 
	Offset: 最高优先级中断偏移

	day 8: 下午	ARM 中断编程(按键中断实现)
	中断源寄存器: GPIO Controller Regs
	1) GPNCON : 	[1:0] 	10 = Ext. Interrupt[0]	设定引脚的功能复用方式，改input为interrupt
	2) EINT0CON0 :	[2:0]	01x = Falling edge triggered   设置引脚触发中断的方式(下降沿)
	3) EINT0MASK :	[0] 	0 = Enable Interrupt 	设置引脚的中断屏蔽位(打开不屏蔽)
	4) EINT0PEND :  [0] 	1= Occur interrupt 	 1. Each bit is cleared by writing "1"      
	中断控制器寄存器: Interrupt Controller Regs
	5) VIC0INTSELECT: 	0 = IRQ interrupt (reset)	确定(设置)中断发生后触发的异常模式为 IRQ
	6) VIC0INTENABLE:	1 = interrupt enabled.		使能这个中断源能够产生中断向上传递
	7) VIC0VECTADDR[0]:	Contains ISR vector addresses. 
	8) VIC0VECTPRIORITY[0]:	
	9) VIC0ADDRESS:		Contains the address of the currently active ISR	
	10) VICxIRQSTATUS:
	中断处理程序 irq_handler 实现技巧
	0x18:    e59ff000            ldr  r15, [r15,#0]
	0x1c:
	0x20:    0x50000128 (beep函数地址)
	中断模型层次结构图
	ARM Core  I-bit 	ARM内核
	--------------------------------------------
	VIC0IRQSTATUS (INTPND)
	VIC0INTENABLE (INTMSK)
	VIC0RAWINTR (SRCPND)	中断控制器
	--------------------------------------------
	EINT0MASK
	EINT0PEND
	EINT0CON0		中断源
	--------------------------------------------

	day 9: 上午	Timer 定时器中断实现
	4、Timer (gettime irq)
	外设寄存器设置
	TCFG0 : 分频
	TCNTB0 : 计数-Interrupt
	TCMPB0 : 比较-Tout0
	TCON : 使能定时器
	TINT_CSTAT : 使能中断
	中断控制器相关
	VIC0INTENABLE = 1<<23;	: 中断控制器层次使能该中断源
	代码示例:
		TCFG0 = 199;	
		TCNTB0 = 65535;
		TCMPB0 = 60000;
		TINT_CSTAT = 1<<0;
		TCON   = (1<<1);   // 手动更新
		TCON   = 0x0d;     // 自动加载，清“手动更新”位，启动定时器0

	day 9: 下午	UART 中断实现
	待总结

	day 10: 上午	DMA 理论
	5、DMA (解放cpu)
	1) secure DMAC(SDMAC) or general DMAC(DMAC).
	SDMA_SEL
	2) Select a free DMA channel 
	3) Clear any pending interrupts 
	4) DMACCxSrcAddr : Write the source address
	DMACCxSrcAddr: SrcAddr [31:0] R/W DMA Source address 
	5) DMACCxDestAddr : Write the destination address
	DMACCxDestAddr: DestAddr [31:0] R/W  DMA destination address 
	6) DMACCxLLI : Write the address of the next LLI 
	DMACCxLLI: LLI [31:2] R/W Linked list item. Bits [31:2] of the address for the next LLI.
	7) DMACCxControl0:  Write the control information 
	DMACCxControl: 
	DI [27] Destination increment. 
	SI [26] Source increment. 
	DMACCxControl1: TransferSize [24:0]

	8) DMACCxConfiguration : Enable bit : Write the channel configuration information
	DMACConfiguration: 1 =enabled. 
	This bit is reset to 0. Disabling the DMA controller reduces power consumption. 

	day 10: 下午	DMA 编程实践
	编程参考
	// Enable DMA Controller
	DMACConfiguration[0] = 1
	// Clear Interrupt Reuest - do NOT write these 2 regs
	DMACIntTCClear[x] = 1
	DMACIntErrClr[x] = 1

	// Setup DMA Interrupt Handler
	ICVECTADDRx = address of Handler
	// Enable DMA Interrupt
	VICxINTENABLE[y] = 1
	// Setup DMA LLI Features to DRAM
	If transferring as LLI mode
	Set Source Address,
	Set Destination Address,
	Set Next address of LLI Item,
	Set Control0 register,
	And Set Control1(Transfer Size) register

	// Setup DMA Channel Control Reg
	Set Src/Dst increment,
	Set LLI register,
	Select Src/Dst Master as SPINE/P
	Set Src/Dst Transfer Width,
	Set Src/Dst Burst Size, 
	And set Transfer size

	// Setup DMA Channel Src/Dst Address
	DMACC0SrcAddr = SrcAddr
	DMACC0DstAddr = DstAddr

	// Setup DMA Channel Configuration Regist
	Set Flow control,
	And Set Src/Dst Request number

	// Start DMA Channel
	DMACC0Configuration[0] = 1

	后继学习指南
	6、NandFlash (实现固化)
	7、AC97 (声卡播放音乐)
	8、LCD (驱动液晶显示图片)
	9、TS (触摸屏用户输入)
	10、DM9000 (网卡网络通信)


	课程主页：
	http://2372614758.qzone.qq.com


	Lecture 1: 硬件环境
	底板
		外设
	核心板
	（最小系统）
	CPU - S3c6410 - ARM11
	MEM - DDR SDRAM - K4X - （2片）
	FLASH - NandFLash (NorFlash)
	电源
	时钟
	JTAG - 调试

	超级终端：hypertrm
	波特率 115200
	硬件流控制 无


	Lecture 2: 开发工具

	SRAM: S3c6410 芯片内部 0-16K
	0x0: 	ea000013
	0x4000: ea000013
	0x8000:
	16K == 0K


	SDRAM DDR: S3c6410 芯片外部 2片一样的 256M
	0x50000000: 起始
	0x60000000: 0x50000000+256M: 结束

	1M = 20个0
	256 = 2^8 = 8个0
	20+8 = 28 个 0 
	28 / 4 = 7
	0x100000000 = 2^28 = 256M


	SFR：
	S3c6410 芯片内部外设控制器的特殊功能寄存器：
	Special Function Register: 内存地址
	芯片手册 SFR
	LED实验
	MINI6410 # mw 0x7f008800 0x11111111
	MINI6410 # mw 0x7f008808 0x0		亮
	MINI6410 # mw 0x7f008808 0xffffffff	灭
	MINI6410 # mw 0x7f008808 0x0
	MINI6410 # mw 0x7f008808 0xffffffff
	MINI6410 # mw 0x7f008808 0x0

	蜂鸣器实验
	MINI6410 # mw 0x7f0080a0 0x55555555
	MINI6410 # mw 0x7f0080a4 0x0
	MINI6410 # mw 0x7f0080a4 0xffffffff
	MINI6410 # mw 0x7f0080a4 0x0
	MINI6410 # mw 0x7f0080a4 0xffffffff
	MINI6410 # mw 0x7f0080a4 0x0

	按键输入设备


	Lecture 3：软件工具
	ADS 1.2
	IDE.exe

	命令行
	-----------------
	armcc: 
		-c 只生成 .o 目标，不链接生成 axf
	armasm:
	armlink: 
		*.o(start.o, main.o) -> __image.axf
		-o demo.axf (output)
	fromelf:
		-bin 
		axf->bin
		7,252 demo.axf
		  996 demo.bin
	make: 

	.o 目标
	.axf 可执行




	Section 2: GPIO 编程实现
	=========================
	Lecture 2.1: 理论基础
	LED, Beep, Key
	md, mw 
	SFR: 0x7f008800, 0x7f008808

	硬件开发的专业术语：
	芯片手册：
		S3C6410X.pdf
	硬件原理图： 底板+核心板 之间，所有引脚连接情况
		底板：Tiny6410SDK-1111-底板原理图.pdf
		核心板：Tiny6410-1107.pdf
	硬件手册：
		01- Tiny6410硬件手册-20110805.pdf

	硬件原理-连接关系：
	1）s3c6410 的管脚
	424-pin FBGA 
	Pin Assignment 管脚物理位置-逻辑名称

	Pin Description 管脚逻辑名称-功能描述

	2）硬件原理图
	如何查看
	LED_1: GPK4 (0: on, 1: off)


	嵌入式开发的一般原理
	解决 SoC 芯片(s3c6410) --> 外围设备 之间编程控制的关系

	解决的途径：通过编程去控制 s3c6410 芯片外围424管脚
		时序：Timing 串口

	编程的方法：
	需要我们了解这些管脚GPK4和哪些SFR(GPKCON)相关联
	对SFR的所代表的“内存”地址，进行读写操作.

	通过C语言：
	指针： int * p = 0x7f008800;
		a = *p;
		*p = b;

		device(peripheral) controller
	SFR  ------------------------------------->  Pin -> device



	Lecture 2.2: 编程实现

	led 的控制实现

		GPIO controller(GPK port) 第10章
	SFR  ------------------------------------->  Pin -> device
	GPKCON0					GPK0-GPK15 
	GPKCON1					 (16 pin)
	GPKDAT	----->				GPK4 ------> LED_1
	GPKPUD


		蜂鸣器
	device： buzzer (底板的硬件原理图)
	-> PWM0 pin -> Connector (底板) -> (核心板) -> H16(GPF14)

	bit14:


	举例说明：
	GPKCON0: bit16 = 1
		bit17, 18, 19 = 0
	控制字：(setting value)
	0x00010000

	0001 0000 0000 0000 0000
	    15                0


	mw 0x7f008800 0x11111111
	mw 0x7f008808 0x0	

	GPK4 : output

	mw 0x7f008800 0x00010000
	mw 0x7f008808 0x0 (0x10)

	GPKDAT: bit4 = 0/1
	0x0;
	控制字：(setting value)
	0x10 (led off)
	0x0 (led on)

	0x00000010
	0001 0000
	   4 3210
	loadb 命令
	go 命令

	MINI6410 # help loadb
	loadb [ off ] [ baud ]
	    - load binary file over serial line with offset 'off' and baudrate 'baud'

	MINI6410 # loadb 0x50000000
	## Ready for binary (kermit) download to 0x50000000 at 115200 bps...
	## Total Size      = 0x0000001c = 28 Bytes
	## Start Addr      = 0x50000000
	MINI6410 #
	MINI6410 # go 0x50000000
	## Starting application at 0x50000000 ...


	课后作业：
	1）请分析蜂鸣器设备的硬件原理图，写出可以控制蜂鸣器发声的精确代码（具体到bit）。

	2）请分析按键的工作原理，写出用按键K5控制蜂鸣器发声，K6控制所有LED灯亮灭的代码
	。


	Lecture 2.3: 嵌入式开发的代码规范化

	如何规范？
	问题1： 能否用 delay() 来代替代码中的 for();

	解决的办法： 
	引入一个启动的汇编文件，从汇编首先进入，然后调用c程序。

	volatile 关键词
	http://learn.akae.cn/media/index.html
	http://learn.akae.cn/media/ch19s06.html


	1) 引入宏定义，定义所有需要用到的 SFR （名字需要从芯片手册中得到）
	2) (*(int *)0xADDR)
	3) 指针类型加入修饰符 volatile unsigned  
	4) 所有对于之前地址操作，都修改为对于 SRF 宏定义的操作

	更好的程序要求：
	a) 注释
	b) 自注释

	位操作
	|= (1<<N);	 set 1
	&= ~(1<<N);	 set 0
	http://learn.akae.cn/media/ch16s01.html



	Section 3: UART 编程实现
	Lecture 1: UART 工作原理
	外围设备（外设）驱动开发的一般流程

	1）开发板硬件连接情况
	查看开发板的硬件原理图
	1.1 接插件：外观，直观
	DB9 接口，编号 1-5（5根），6-9（4根）

	2号：RxD 接收
	3号：TXD 发送
	5号：GND 接地

	1.2 查看连接关系，接插件引脚 -- SoC 芯片引脚之间的关系
	MAX3232 芯片 T1in(TXD0) -> T1out (RSTXD0)
	电平转换，TTL 电平1(3.3v) ->  RS232 电平 (+15v  -15v)

	1.3 SoC s3c6410 芯片
	A23： XuTXD0/GPA1
	D20:  XuRXD0/GPA0

	查看 6410 芯片手册
	P76 页： 结论：硬件原理图和芯片手册 吻合
	GPA1：属于 GPIO。功能复用。属于UART controller管理。
	GPA0：

	属于 GPACON 寄存器，功能方式设为非IO
	GPXDAT

	结论是： Pin 功能复用，一般情况下，都是需要设置的。
	默认情况一般是输入。（也有例外：NandFlash controller pin ALE/CLE ）
	设置为 0010， 0010 是完成串口发送和接收的工作方式的一个必要条件



	2）Pin -> Regs

		     UART controller 第31章                        硬件原理图
	SoC SFR  -------------------------------------> SoC Pin --------------------> device


	Chapter ->  Overview  ->  Block Diagram (框图)  -> SFR (15个*4)  -> ( ??/15 )

	Read-Only:  状态寄存器
	Write-Only: 控制寄存器
	Read/Write: 数据寄存器


	初始化：写控制寄存器
	读写数据： 读写数据寄存器 （通常需要去查看状态寄存器）


	Lecture 2: 串口驱动的具体分析

	UART

	(SFR) - Setting Value 控制参数 （15 reg *32bit = 400-500）
	------------

	(controller) block diagram - Logic 工作逻辑

	------------
	(pin) - Timing 时序 （结果）


	从结果分析：
	问题1： TxD 引脚上，需要产生什么样的时序（高低电平的变化）才能正确工作？
	解答：需要去分析发送时序，一个串口数据的帧(Frame)结构。
	帧： 
	空闲状态
	起始位
	数据位
		（奇偶校验位）
	停止位
	恢复到空闲状态



	问题2： 要得到这样的时序输出，UART Controller 控制器是如何工作的？
	解答：串口控制器工作逻辑

	Transmitter 发送器 黑匣子
	1） 数据总线 （'a'）
	2） 控制单元 control unit （数据位8位，停止位，有无奇偶校验位）
	3） 波特率发生器 （每秒的数据量/每1个bit位数据所占用的时序上时间宽度）

	黑匣子的内部：
	4）Transmit Buffer FIFO 队列（缓冲器 64byte）
	5）Transmit Shifter 移位器

	115200 bps 波特率 ->  1秒传送 115200 bit -> 1帧10bit  
	115200/10 = 11520 byte 数据 = 11k/s

	1bit 需要的时间 1/115200 = 8.68 * 10 ^ (-6) = 8 微秒
	1byte 'a' -> 10bit * 8微妙 = 80微秒

	80/1000 = 0.08 微秒 / 3 = 0.02 微秒 = 20 纳秒 1条 

	相对人的感觉 秒（毫秒）很短的
	但是微秒相对于 cpu 执行速度 其实是很长的


	问题3： 要让控制器这样去工作，那么应该怎样去设置SFR的值？
	串口有哪些 SFR ，哪些需要设置，应该设置成什么？

	一般常识：
	很多SFR的Reset Value，通常都是能够工作，也就是写驱动时，直接使用默认值。

	经验之谈：
	能不设的就先不设，让它工作起来再说。

	如何让串口工作？
	1） 串口能够输出，有东西。
	对串口的SFR进行分析，可以得到（猜出）哪个是和据有关。
	得到结论： 
	UTXH0 0x7F005020 W 数据发送寄存器
	URXH0 0x7F005024 R 数据接收寄存器


	Lecture 3: 串口驱动代码实现
	要点：轮询状态寄存器的标志位，确认缓冲区满/空之后，才能进行读写
	void uart_putchar(char c);
	char uart_getchar(void);


	作业1： 在字符收发的基础之上，实现关于 gets, puts 字符串的收发，以用户输入Enter('\r')作为字符串的结束。
	然后回显这个字符串，并且自动实现换行输出。
	puts 相对容易
	gets 比较困难 
	1. 对于结束标志的判断。\r
	2. 用户可能输入 Backspace 退格键的处理。 \b  （3个键 \b ' ' \b）
	3. 用户可能退格退过头了，造成把前面提示符删除。



	作业2： 完整串口驱动，还需要有 uart_init();
	初始化完成的工作，本质上就是设置SFR的值。

	UART0 的初始化值（通过uboot bootloader 来设置）
	MINI6410 # md 0x7f005000
	7f005000: 00000003 00000e45 00000000 00000000    ....E...........
	7f005010: 00000000 00000000 00000100 00000011    ................
	7f005020: 00000000 0000000d 00000022 00001fff    ........".......
	7f005030: 00000005 00000005 00000000 00000000    

	通过分析，可以尝试去驱动 UART1 ，使得可以从 UART1 来进行输出。



	Section 4: Clock 时钟驱动

	System clock control P120 



	AC97 Driver

	整体架构

	LCD controller
	----------------- 6 signals
	LCD Module
	-LCD driver
	-----------------
	-TFT LCD 

	S3C6410 SoC
	AC97 Controller 控制器
	---------- 5 signals
	AC97 CODEC
	WM9714 chip 芯片
	---------- 2 signal
	Headphone 耳机


	IIS
	音频总线
	1，帧时钟： LRCK (Left/Right)
	代表1秒钟时间内有多少个帧数据
	典型：wav文件中22Khz，44Khz
	22000个音频帧数据，播放1个帧，需要 1/20000 秒 = 50 微秒

	2，位(串行)时钟： SCLK (serial)
	代表在1个帧里面，数据所需要的位数
	典型：左右声道各8bit，16bit

	结论：SCLK/LRCK = 16 or 32

	3, SD (serial data) 数据线
	串行数据


	问题？ 不足？

	AC97

	AC97 Controller
	------------------
	AC-Link
	------------------
	AC97 CODEC


			AC-Link
	Controller  ---------------->
		       PCM data

	BMP = BMP Header + DIB
	WAV = WAV Header + PCM data

	PCM -- DIB 
	(Pulse-Code Modulation)
	(device independant bitmap)


	AC97 Signals
	------------
	1. X97SYNC 帧同步信号
	fixed at 48Khz

	2. X97BITCLK 位时钟
	fixed at 12.288Mhz

	3. X97SDO/X97SDI 数据输出/输入

	4. Reset


	AC97 Timing
	------------
	1 Frame = 256 bits = 32 bytes
	1 Frame = 13 time slots
	slot 0 = Tag Phase = 16bit
	slot 1-12 = Data Phase = 20bit * 12
	16bit + 20bit * 12 = 256bits

	48Khz * 256 = 12.288 Mhz

	Sample BIT CLK = BITCLK * 2 = 24.576Mhz

	MCLKA = 24.576Mhz => 由外部晶振制造的


	AC97 SFR 
	------------

	AC97 Controller : Control & Status Reg
	AC97 CODEC: Command & Status Reg
		refer to WM9714.pdf Page80 (Register Map)

	(FIFO ADDRESS Reg)
	AC97 PCM OUT/IN DATA: FIFO DATA Reg


	AC97 State Machine (FSM)
	---------------------------
	0: IDLE
	1: INIT (reset value)
	2: READY (aclink-on)
	3: ACTIVE
	4: LP
	5: WARM


	AC97 Coding
	-------------
	1. AC97 Controller Init -> State 3 Ready
	2. AC97 CODEC INIT -> Enable, Volume, 44Khz
	3. AC97 PCM OUT -> WAV PCM Data -> FIFO Reg
	4. AC97 DMA PCM OUT -> DMA 




































