---
layout: post
title: 2410 dev notes
---

# 课程安排
	
	Day1: section1: 课堂调查
	bootloader 项目内容
	1-LED		*
	2-UART		*
	3-stdio		#
	4-printf	#
	5-shell		#
	6-command	#
	7-loader	#
	8-xmodem	*
	9-clock		*
	10-sdram	*
	11-flash	*
	
	C语言测试
	1、写出 main 函数原型
	int main(int argc, char * argv[]);
	
	2、写出 printf 函数原型
	int printf(const char *, ...);
	
	3、写出一个函数原型，完成数字到字符串的转换
	char * itoa(int num, char * buf);
	char * gets(char * buf);
	
	4、写出一个函数原型，完成字符串到数字的转换
	int atoi(const char *);
	
	5、写出2个函数原型，完成字符串的拷贝和比较
	char * strcpy(char * dst, const char * src);
	int strcmp(const char * s1, const char * s2);
	
	项目分组
	    组长	组员	 	
	1组 郭晓新	柯浩	梁鹏程
	2组 郭立龙	艾朝宇	虞福智	黄耀锋
	3组 张利	刘雪璐	贡兴	黄海鹏	马崇智
	4组 闫蓉蓉	刘玉
	
	1、组员的能力特长
	硬件知识，ARM处理器了解，英文能力，组织表达能力
	
	2、小组的学习目标
	希望在项目中学习到哪些知识，掌握哪些技术
	
	Day1: section2: Bootloader 项目开题
	一、项目背景
	bootloader 项目的技术需求 (www.zhaopin.com)
	问题：你未来想进入哪个行业工作？
	行业：消费类电子、汽车电子、医疗电子、电信
	安全安防、智能家居、电子商务、机器人、软件外包
	
	二、项目目标
	问题：如何完成实现一个bootloader，怎样启动Linux？
	思考：项目的开发流程
	1、准备工作
	环境的搭建：硬件 软件
	硬件：akae2440/2410
	软件：ads 1.2
	
	2、里程碑（关键路径）
	1) 硬件是好的
	2) 能点灯了（自己写的程序） +LED, +GPIO
	3) 能输出了 +UART
	4) 能交互了 +SHELL
	5) 能命令了 +COMMAND
	6) 能下载了 +LOADER
	------------------- 基本的bootloader
	7) 跑得更快了 +CLOCK
	8) 内存更大了 +SDRAM
	9) 能保存固化了 +FLASH
	10) 能自己启动 +AUTORUN
	------------------- 加电 bootloader -> app 自启动
	11) 能启动linux内核了，看到内核打印输出了 +KERNEL
	12) 能加载根文件系统，进入到 shell 提示符下了 +ROOTFS
	13) 能够通过网络交互了 +NET
	-------------------
	
	Day1: section3: 环境搭建
	网络畅通
	掌握命令 cmd, ping, ipconfig, path, 
	c:\windows;c:\windows\system32
	访问共享
	\\192.168.0.24
	
	tools 工具软件
	1) ADS1.2 开发套件 （armcc 命令行）
	2) FoxitReader 阅读器 （图形标注工具）
	3) Ultraedit 编辑器 （添加右键支持，CTRL+h）
	
	resource 相关资料
	芯片数据手册
	开发板的硬件原理图
	
	Day1: section4: 硬件检测
	电源，H-jtag dection cpu: ARM920T
	并口：2种 0x378(LPT), 0xDE00 (PCI-LPT)
	
	NandFlash 分区
	0x00000000-0x00020000 : "vivi"
	0x00020000-0x00030000 : "param"
	0x00030000-0x001f0000 : "kernel"
	0x00200000-0x04000000 : "root"
	
	vivi (bootloader): 0x20000 = 128K 
	1block = 32page = 512byte = 0.5K = 16K
	128K = 16K * 8block
	
	kernel (内核): 0x30000 = 192K = 12block
	size = 0x1c0000 = 128K*14 = 112blocks => 124block
	
	rootfs (文件系统): 0x200000 = 128bock
	
	MC2410E DEVELOP KIT
	
	VIVI version 0.1.4 (root@Rhvd) (gcc version 2.95.2 20000516 (release) [Rebel.com]) #0.1.4 Thu May 4 00:58:37 CST 2006
	MMU table base address = 0x33DFC000
	Succeed memory mapping.
	NAND device: Manufacture ID: 0xec, Chip ID: 0x76 (Samsung K9D1208V0M)
	Found saved vivi parameters.
	CS8900 - type: 630e, rev: a00
	CS8900 - status: ad6 (EEPROM present)
	Setting MAC address...
	Display format changed to VGA 640X480 mode
	Press Return to start the LINUX now, any other key for vivi
	type "help" for help.
	vivi> 
	vivi> help
	
	Day1: section5: 开发流程
	ADS 项目 工程文件 .mcp (.prj)
	AXD 调试 ARMulator.dll 软件仿真 RDI (Remote Debug Interface)
	ARM 可执行文件 .axf (.exe .elf)
	
	如何运行开发板输出程序
	串口发送寄存器地址  0x50000020 
	修改 link 参数 -ro-base 0x0
	修改 link 参数 -entry __main (main -> __main)s
	
	
	Day2: section1: 串口驱动原理
	硬件连接关系：串口 <--> S3c2410 cpu
	专业术语：硬件原理图 (pdf)
	串口硬件基础知识：
	1) DB9 9针
	2) 其中 pin2(TxD), pin3(RxD), pin5(GND) 有用
	3) Max3232 电平转换芯片 (TTL电平(0-5v)->RS-232电平(-15v->+15v
	从底板和核心板原理图查看可知 
	TxD -> K15/GPH2
	RxD -> K17/GPH3
	------------------------------------
	专业术语：芯片数据手册 (DataSheet)
	S3C2410.pdf  
	FBGA 封装 Pin Number/Pin Name
	UART 串口的引脚接口 - TxD/RxD nCTS/nRTS UEXTCLK
	专业术语：引脚功能复用 (Selectable Pin Functions)
	GPH2 (GPHCON) 0x56000070 
	GPH2 [5:4] 	00 = Input     01 = Output
			10 = TXD0   11 = Reserved
	------------------------------------
	
	Day2: section2: 串口驱动原理2
	SoC内部控制器 (里面有什么？ 最重要的10个)
	ARM920T 内核
		Processor Core, CP15, MMU, CACHE, AMBA BUS
	BUS 总线 
		AHB BUS, APB BUS
	Controllers 控制器
		CLOCK, Power, Mem controller, NandFlash, 
		Interrupt, Timer, DMA, UART, GPIO
		
	0x1000 = 4K
	0x100000 = 1M
	1G = 0x40000000
	
	UART 控制器驱动工作原理
	专业术语：特殊功能寄存器SFR (Special Function Reg)	
	地址范围  (0x48000000 - 0x5A000040)
			len = 0x12000000 = 256M
	占用存储： 4byte * 300 = 1K
	
	串口的特殊功能寄存器
	控制寄存器 W
	状态寄存器 R
	数据寄存器 R/W
	
	Day2: section3: 串口驱动原理3
	串口控制器 UART Controller 的编程
	专业术语：时序图 (Timing Diagram)
	串口的发送时序
	1) 空闲状态 '1'
	2) 起始位 1bit  1->0   (连续16clock)
	3) 数据位 8bits/7bits/6bits/5bits  (查看7,8,9clock)
	4) 校验位 none
	5) 停止位 1bit/1.5bit/2bits '1'	
	
	UART Block Diagram (内部结构框图)
	重要组成：Transmitter/Receiver (Buffer&Shifter), 
	Control Unit, Baud Generator, Clock Source
	DataBus, AddrBus, UEXTCLK 
	
	UART SFRs
	寄存器编程
	
	
	Day2: section4: 串口寄存器配置
	vivi> dump 0x50000000 0x40
	50000000: 03 00 00 00 45 02 00 00-00 00 00 00 00 00 00 00 | ....E...........
	50000010: 02 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 | ................
	50000020: 00 00 00 00 0D 00 00 00-1A 00 00 00 00 00 00 00 | ................
	50000030: 00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 | ................
	
	GPHCON: GPH4, GPH5 (Txd/Rxd)
	ULCON: 	8bit(3)
	UCON:	PCLK, polling mode 0101(5)
	UBRDIV:	50M/(115200*16)-1 = 26 (0x1A)
	UTXH:  	'h'
	
	总结：串口驱动关键参数 （必要条件）
	1、设置相关引脚的功能复用
	2、数据位 8bit	
	3、奇偶校验位 none 
	4、停止位 1bit
	5、流控制 none
	6、波特率 115200
	7、发送使能/接收使能
	8、时钟源 PCLK (vivi 50Mhz)
	9、工作方式 polling/interrupt  (DMA)
	
	Day2: section5: 串口驱动编程实现
	任务要求：实现UART1的输出 "hello, world" (无限次)
	
	课后作业：
	1、实现串口的相关接口,要求编码规范,学会用宏和位操作(~,|,&)
	void uart_init()
	void uart_putchar(char c);
	char uart_getchar(void);
	
	2、基于串口的 uart_put/getchar，完成以下 stdio 接口
	int puts(char *);
	char * gets(char *);
	
	3、完成 printf 格式化打印输出，要求支持%c,%s,%d,%x
	printf("%c, %s, %d, %x\n", 'a', "hello", 100, 100);
	
	day3: section 1: 标准库的实现
	#include <stdio.h>
	/usr/include/stdio.h 	-Isubdir
	ADS安装目录下找
	putchar
		int putchar(int c);
	getchar
		int getchar(void);
	puts
		int puts(const char * s);
	gets
		char *gets(char * s);
	printf
		int printf(const char * format, ...);
	
	代码的组织结构
	main (#include "stdio.h")
	-----
	stdio (5 api) (#include "uart.h")
	-----
	uart (uart_xxx) (uart.c, uart.h)
	
	字符 \r \n \b 的含义
	\r (Enter) 	回车 （回到行首）
	\n (Ctrl+Enter) 换行 （换下一行）
	\b (BackSpace)	退格 （回退一个字符，但不删除）
	putchar('a');
	putchar('\n');
	putchar('b');
	putchar('\n');
	a
	b
	
	a
	 b
	
	puts("hello\n");
	puts("world\n");
	hello
	world
	
	hello
	     world
	
	printf("hello\n");
	
	超级终端 (windows自带) hypertrm.exe hypertrm.dll
	
	ARM 体系结构下，函数栈帧的分配方法是：
	1、减栈：栈的空间是从高地址，向低地址方向增长的。
	2、函数的形式参数，都是分配在栈上的，连续分配的。
	3、C编译器在编译时，分配参数地址的顺序是最后的参数先入栈。
	4、第一个参数的地址，和后继参数的地址的关系是：p++
	
	#if 1
		#include <stdarg.h>
	#else
		typedef int *	va_list;
		#define va_start(p, fmt)	p = (va_list)&fmt + 1
		#define va_arg(p, type)		(type)(*p++);
		#define va_end(p)			p = (void *)0
	#endif
	
	包含3个宏和1个类型定义
	类型定义用 typedef
	va_start 宏：通过 format 的地址，使p指向第2个参数的地址
	va_arg 宏：通过p得到当前参数的值，并使p指向下一个参数 （通过调用多次）
	va_end 宏：使p指向空 (0)
	
	课后作业：
	完成 printf %d, %x 两个参数的打印输出
	printf("a = %d, b = 0x%x %8x (%p)", 100, 0x64);
	a = 100, b = 0x64 0x00000064
	
	day4: section1: Shell 命令解析器
	md
	md 0x0: SRAM 内存
	md 0x50000000: UART SFR
	md 0x30000000: SDRAM 内存
	
	mw 0x0 0x11223344
	
	help md
	
	格式 md addr size
	举例 "   md     0x0  64  "
	输出：
	0x00000000:  E52DE004 E24DD064 E28F2048 E3A01041 
	0x00000010:  E28F0048 EB0000B5 E28F2038 E3A01041 
	0x00000020:  E28F0038 EB0000B1 E3A03064 E3A02064 
	0x00000030:  E3A01064 E28F0038 EB0000AC E28F0048 
	任务要求：实现一个 shell_parse 函数
	int shell_parse(char * buf, char * argv[]);
	
	int argc;
	char * argv[5];
	char buf[100];
	
	argc = shell_parse(buf, argv);
	
	day4: section2: md/mw 命令实现
	bootloader $ md 0x11000300
	查看 0x11000300 内存地址
	11000300: 00000000 00000009 31120000 00D300D3
	11000310: 05480548 25580558 25580558 25580558
	11000320: 05480548 25580558 25580558 25580558
	11000330: 05480548 25580558 25580558 25580558
	
	bootloader $ mw 0x1100030a 0x0 2
	*(short *)0x1100030A = (short)0x0
	
	bootloader $ md 0x11000300
	查看 0x11000300 内存地址
	11000300: 00000000 00000009 30000000 630E630E
	11000310: 05480548 25580558 25580558 25580558
	11000320: 05480548 25580558 25580558 25580558
	11000330: 05480548 25580558 25580558 25580558
	
	bootloader $ mw 0x1100030a 0x20 2
	review mem 0x1100030A:  0x30200000
	
	bootloader $ md 0x11000300
	11000300: 00000000 00000009 30200000 03000300
	11000310: 05480548 25580558 25580558 25580558
	11000320: 05480548 25580558 25580558 25580558
	11000330: 05480548 25580558 25580558 25580558
	
	day5: section1: Loader 实现
	新的网络共享 \\172.16.0.21
	         UART
	pc bin --------> board (SRAM/SDRAM)
	        NET/USB
	
	vivi> load ram 0xc00 88 x
	Ready for downloading using xmodem...
	Waiting...
	(菜单：传送 -> 发送文件 -> 
	test_uart0.bin -> 
	xmodem 协议 -> 发送
	成功后显示
	Downloaded file at 0x00000c00, size = 128 bytes
	There are 0x0 bad blocks in this partition, please make it at least 0x28 bytes l
	arger
	vivi> go 0xc00
			
	day5: section2: Xmodem 协议实现
	背景
	1. 关于 size
	2. 数据包 packet (0x1A填充) 128 byte
	3. 包头 + （数据）+ 包尾
	    3       128      1 = 132
	简单协议： 包 4 + bin datas
	传送的过程中，可能会有意外
	什么意外？ 
	1）发送连接断开 （断点续传）
	2）发送数据的突变 （错误的数据）
	
	约定： 收发流程
	请求和应答 机制
	1、谁先请求？ 如何请求？
	2、谁来应答？ 如何应答？
	
	如何实现 my bootloader 的烧写启动
	1. 创建 start.s ，代码可参考 \\唐山-太原-ARM课程\codes\loader\start.s
	
	2. 修改 DebugRel Setting, -> ARM Linker -> Layout -> start.o
	
	3. main.c 最开始 添加一行 关闭看门狗 
		// disable watch dog
		*(int *)0x53000000 = 0;
		
	   uart.c 里面，PCLK 波特率计算改用 12M	
		// 12000000/19200/16 - 1 = 39.0 -1 = 38
		UBRDIV0 = 38;	
		
	4. reset 开发板，用 19200 波特率连接 uart0 进行通讯
	
	阶段总结：
	一定要能够自己独立完成并编码实现的：
	int add(int n, ...);
	int printf(const char * format, ...);
	串口驱动的几个必要条件
	stdio 里面 gets 的实现，需要处理 \r \n \b 
	printf 里面 va_list, va_start, va_arg, va_end
		实现以下函数 char * itoa(int a, char * buf)	
					void itoa_hex(int a, char * buf, int flag)
	shell 里面实现的函数
		int shell_parse(char * buf, char * argv[])
		int strcmp(const char * s1, const char * s2)
		int atoi(char * buf)  10/16进制
	xmodem 协议的原理和实现 SOH, ACK, NAK, EOT, CAN
			基本的代码框架 1 + 2 + 128 + 1		
	
	vivi> dump 0x48000000 64
	48000000: 10 1D 11 22 00 07 00 00-00 07 00 00 7C 1F 00 00 | ..."........|...
	48000010: 00 07 00 00 00 07 00 00-00 07 00 00 01 80 01 00 | ................
	48000020: 01 80 01 00 E9 01 8E 00-B1 00 00 00 20 00 00 00 | ............ ...
	48000030: 20 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 |  ...............
	vivi> dump 0x4c000000 64
	4C000000: FF FF FF 00 40 C0 05 00-32 80 04 00 F0 FF 0F 00 | ....@...2.......
	4C000010: 04 00 00 00 03 00 00 00-00 00 00 00 00 00 00 00 | ................
	4C000020: FF FF FF 00 40 C0 05 00-32 80 04 00 F0 FF 0F 00 | ....@...2.......
	4C000030: 04 00 00 00 03 00 00 00-00 00 00 00 00 00 00 00 | ................
	vivi> dump 0x4e000000 64
	4E000000: 30 E8 00 00 00 00 00 00-00 00 00 00 A5 00 00 00 | 0........... ...
	4E000010: 01 83 00 00 59 A9 95 00-00 00 00 00 00 00 00 00 | ....Y...........
	4E000020: 00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 | ................
	4E000030: 00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00 | 
			
	day6: section1: Clock 驱动
	MPLL 锁相环
		MPLLCON (PMS setting: PDIV/MDIV/SDIV)
		Fout = Fin * (MDIV+8) / (PDIV+2) / s^SDIV 
	DIVN
		HDIVN: FCLK/HCLK
		PDIVN: HCLK/PCLK
		
	day6: section2: Makefile 使用
	工具： armasm, armcc, armlink, fromelf
	linux: (arm-linux-)as, gcc, ld, objdump/objcopy	
	参数： -c, -first, -ro-base, -bin, -c -d -s
	
	day6: section3: SDRAM 驱动
	volatile 关键词 - 防止优化，强制访存
	JTAG：Joint Test Action Group 测试(调试)，烧写rom，下载ram
	SRAM/SDRAM/NandFlash/NorFlash 区别和联系
	
	SRAM
	6个晶体管组成触发器
		容量小，价格贵，速度快，掉电丢失，不用刷新
	SDRAM
	1个晶体管和1个电容，依靠电容的充放电来保存数据
		容量大，价格便宜，速度慢，掉电丢失，需要刷新
	
	从接口电路上和驱动程序上
	SRAM: 地址线，数据线，控制线，片选信号
	2*8bit SRAM: 数据线拼成16位，地址线丢掉A0
	
	SDRAM 驱动的要素
	工作特点：动态刷新，行列选通，数据拼凑，内部分BANK
	驱动代码实现：
	引脚的功能复用 （没有复用，无需设置）
	工作参数：
	1、数据总线的宽度 32bit -> A2 <-> A0
	2、类型是SDRAM，列地址宽度9bit
	3、RAS to CAS delay 行选通到列选通的延时参数
	4、SDRAM器件的容量64M -> 地址总线26根
	5、SDRAM器件的刷新周期 7.8us -> 8196 cycles/64 ms
	
	day7: section1: Flash 驱动
	NandFlash 工作特点
	无地址线，地址信号是通过数据线传递的，也就是地址和数据复用
	数据线也只有8根，每次只能传递1个byte
	64M 它需要26根地址信号，每次传8bit，得需要传4次
	每次出来的数据只有8bit
	
	块设备：每次读可以读1个pages
	读可以分为上半页(命令00) 下半页(命令01)
	
	命令也是通过 IO0-IO7 来传递给NandFlash
	三种信号的复用：命令，地址，数据
	
	SDRAM的容量
	4 banks * 2^13 * 2^9 * 4bytes = 2^26 = 64M
	
	NandFlash内部的组成结构
	4096 blocks * 32 pages * (512+16)bytes = 64M 
	
	NorFlash 工作特点
	有地址线，而且不用复用，发一次地址就能够访问数据
	和SRAM的读取方式一样，所以可以用来启动，代码可以执行
	结论：NorFlash可以直接启动bootloader，无需SRAM参与
	SROM = SRAM + ROM(NorFlash) bank0-bank5
	oneNand -> new NorFlash = [SRAM Interface + (NandFlash)]
	
	4pin usb interface + NandFlash
	
	课堂作业要求：
	1、通过 read ID 的时序图，配置相关寄存器，读出芯片ID
		0xEC 0x76 0x?? 0x??
	
	2、通过 read page 的时序图，读出从0地址开始的512字节
	按照 md 的输出格式打印 （简化办法：0字节和511字节）
	随机打印 buf[0] buf[511];
	
	3、实现 mybootloader 的升级：实现 autorun 功能
	条件：提前把 test-uart.bin 用 Hjtag 烧写到 Flash
	autorun 运行时，通过 nand_read 接口读取到 sdram
	go 到 sdram 去执行。 如果用户输入了，则进入到 shell
	
	1M = 16K * 64 blocks
	
	能够实现一条 nr (nandread) 命令，格式
	nr nand_addr sdram_addr size
	
	chapter 2, 3 (Arch, Ins)
	14 (Int)   8 (DMA)
	
	day8: section1: ARM 体系结构基础
	异常处理 系统调用 printf 真正的实现
	SoC 芯片内部的结构：内核+控制器(Watchdog,GPIO,UART,CLOCK,SDRAM,NandFlash) 
	ARM内核的结构：ARM920T (ProcessorCore,CP15,MMU,CACHE,AMBA bus)
	Processor Core: 
	推荐阅读资料 ARM 和 X86 的历史 (作者王齐，intel)
	
	重要概念
	1) Processor Operating State 处理器工作状态
	ARM 状态：32bit 指令
	THUMB 状态: 16bit 指令 -> BX ins
	
	2) processor mode 处理器模式
	非异常
	· User (usr): The normal ARM program execution state
	· System (sys): A privileged user mode for the operating system
	异常
	· IRQ (irq): Used for general-purpose interrupt handling
	· FIQ (fiq): Designed to support a data transfer or channel process
	· Supervisor (svc): Protected mode for the operating system
	· Abort mode (abt): Entered after a data or instruction prefetch abort
	· Undefined (und): Entered when an undefined instruction is executed
	
	3) Big/Little-Endian format 大尾端/小尾端
	(int *)0 => 0x12345678
	
	 78   56   34   12
	---- ---- ---- ----
	  0    1    2    3
	  
	*(short *)1 = ?	 0x3456
	
	4) Register 寄存器组织
	37 个 = 31 通用(r0-r15) + 6 状态(CPSR,SPSR)
	31 = 16 + 7 + 2 + 2 + 2 + 2
	31 = 8*1(r0-r7) + 5*2(r8-r12) + 2*6(r13,r14) + 1*1(r15-pc)
	
	6 = 1*1(CPSR) + 1*5(SPSR)
	
	5) Exception 异常
	Exception arise 异常事件发生 （异常向量表）
	0x0:  	1. 复位异常 -> SVC模式
	0x4:	2. 执行未定义指令 -> UND模式
	0x8:	3. 执行软件中断指令(SWI) -> SVC模式 （系统调用）
	0xc:	4. Prefetch预取指令终止 -> ABT模式
	0x10:	5. Prefetch预取数据终止 -> ABT模式
	0x18:	6. IRQ 普通中断 -> IRQ模式
	0x1c:	7. FIQ 快速中断 -> FIQ模式
	
	Exception Priority (Prio) 异常处理优先级
	1. 复位
	2. 数据终止
	3. 快速中断
	4. 普通中断
	5. 指令终止
	
	6. 未定义指令/软件中断指令异常
	
	异常服务/处理（进入/退出）
	* 异常进入/异常响应 （硬件完成）
	1）保存返回老地址 (PC+4) 到 LR_irq
	2）保存返回老模式 (CPSR) 到 SPSR_irq
	3）修改CPSR，进入新的模式 IRQ
	4）修改PC，跳转到新的地址-异常向量表 0x8/0x18
	软件来做：
	5）保存现场 r0-r12 -> stack
		stmfd r13!, {r0-r12}
		
	* 异常返回/异常恢复 （软件完成）
	0）软件：恢复现场 r0-r12 <- stack
		ldmfd r13!, {r0-r12}
	1）LR -> PC 恢复地址
	2）SPSR -> CPSR 恢复模式
		movs pc, lr
		
	3）清除中断相关位
	
	day8: section2: ARM 指令集
	0xE 3A 0DD40 
	立即数概念：immediate 
	D40 = 0x1000 : 0001 0000 0000 0000
	B40 = 0x10000: 0001 0000 0000 0000 0000
	940 = 0x100000
	740	= 0x1000000
	xYY: 
	x-类似幂，移位的个数
	YY-有效数字 0x40 : 0b0100 0000
	有效数字:没有移位之前的数
	x: 循环右移2*x位 26位： 左移6位
	
	
	SWI 软件中断指令会跳转到 0x8 地址
	
	mov pc, lr 地址返回
	0xe1a0f00e
	真正的返回： 模式也返回
	movs pc, lr
	0xe1b0f00e
	
	0xe0803001	 add r3, r0, r1
	0xe0823003	 add r3, r2, r3
	0xe1b0f00e	 movs pc, r14
	
	0x10 + X*4 = 0x802c (handler)	
	0xEA002007	b handler
	
	0x10 + ? * 4 = 0x8018
	
	0xEA002002
	
	day9: section1: system call 系统调用的实现
	boot> md 0x30008000
	boot> mw 0x30008000 0xEF000005
		swi 0x5 -> 0xef000005
	boot> md 0x30008000
	boot> go 0x30008000
	
	0x30008004
	b   .		[0xeafffffe]
	
	解决思路：
	1. 建立异常向量表，实现2个 b
	2. swi_handler: 保存现场 {r0-r12, lr}
	3. 维持保留 r0 不变或者mov r0, #0x68	bl uart_putchar
	4. 恢复现场
	5. 返回异常发生处
	
	day9: section2: Interrupt 中断处理
	中断的3层管理模型
	
	            nIRQ
	ARM920T <---------- Interrupt CONT <------ INT-SOURCES: GPIO
									   <------ UART, USB, SD 
	
	0xFE, 0xFD, 0xBF
	0xBC <----> 1011 1100
	
	Pending (suspend) 悬而未决的
	SRCPND
	
		配置 GPF0 为 EINT 方式（选择复用功能）
	bootloader $ mw 0x56000050 0x2
	
		配置 EINT0 为 Falling Edge Trigger （下降沿触发）
	bootloader $ mw 0x56000088 0x2
	
		查看 SRCPND 寄存器
	bootloader $ md 0x4a000000
	4A000000: 02000000 00000000 FFFFFFFF 0000007F
	4A000010: 00000000 00000000 00000003 0000FFFF
		此时按下 Key3 按键，触发一个中断，再次查看 SRCPND
	4A000000: 02000001 00000000 FFFFFFFF 0000007F
	4A000010: 00000000 00000000 00000003 0000FFFF
		打开屏蔽寄存器 0 位 INTMSK
	bootloader $ mw 0x4a000008 0xFFFFFFFE
		再次查看 SRCPND, INTPND
	buf = <md>
	4A000000: 02000001 00000000 00000000 000000FF
	4A000010: 00000001 00000000 00000003 0000FFFF
		如何给 Pending 位清除？ 写1清0
		如果先清除 SRCPND ，再清除 INTPND，则 OK
		如果先清除 INTPND ，再清除 SRCPND，则无用，还会再被自动设置1
		
	改为中断的关键代码： bootloader 中修改
	1、因为用户模式不能修改 CPSR，所以bootloader中直接打开中断 
	0xD0 -> 0x50
	
	2、irq_handler 中需要清除2个PND寄存器，调用 clear_int
	
	3、最后返回用 subs pc, lr, #4	而不是 movs pc, lr
	
	中断 Timer 定时器中断chp10， DMA 中断chp8
	
	总结：
	1、区分两个概念 异常和中断 vs 中断处理和异常处理
	                 ARM Core		Board (SoC)
	
	（中断IRQ）异常
					 
	2、结合中断处理的全过程，分析一下
	A. 中断的初始化，为中断的发生创造条件 -> 三层模型
	a1) 中断源的初始化（GPIO, UART, Timer, DMA）
		1. 中断源的使能：告诉中断源以中断的方式工作，通知上级(INT CONT.)
		2. 中断的触发方式：告诉中断源中断触发条件
	a2) 中断控制器的初始化，以便向上级汇报 (ARM920T)
		0. 中断 pending 位全部清除一遍
		1. 中断的屏蔽位打开
		2. 中断的优先级设置
	a3) ARM的内核中断异常位的使能
		1. CPSR I-bit/F-bit
	
	B. 中断的响应和处理以及返回
	中断条件具备，中断发生了，则第一步就是硬件进行（中断）异常响应
	b1) (中断)异常响应，硬件自动完成
		1. 保存地址： PC->LR_irq
		2. 保存模式： CPSR->SPSR_irq
		3. 修改模式:  IRQ_mode -> CPSR (0xD2)
		4. 修改地址:  0x18 -> PC 此时发生了跳转，同时模式完成切换
	b2) (中断)异常处理，软件手工实现
		0. 偷懒的做法：先设置一下 sp_irq: mov sp, #0x33000000
		1. 现场的保存：STMFD sp!, {r0-r12, lr}
		2. 异常处理程序: 注意如果调用C，则lr_irq需要保存
		3. 现场的恢复: LDMFD sp!, {r0-r12, lr}
		4. 异常的返回: movs pc, lr	(模式和地址同时返回)
	b3) 中断的特殊处理
		0. 中断模式下的 SP 栈指针的初始化
		1. 中断pending位的清除
			SRCPND -> INTPND 先后问题，要斩草先除根
		2. 中断异常返回的时候，需要 subs pc, lr, #4	
		 
	day10: section1: Timer 定时器中断处理	
	Timer ：
	1、Clock 概念
	输入时钟
	分频
	计数寄存器 counter--
	PWM 信号输出，占空比
	
	50M = 50000000  -->  <65535 = 50000
	50000000 -> 100000 -> 1/2 -> 50000
	-------- =  200000 -> 1/4 -> 50000
	250
	
	时钟和分频
	TCFG0：250 - 1 = 249 (8-bit prescaler)
	TCFG1：1/4 分频
	
	计数器 
	TCNTB0： = 50000
	TCON： set Manual update bit
	TCON： set Start bit
	TCON： clear Manual update bit
	
	day11：section1：嵌入式Linux开发
	1、硬件基础
	*Communication
	RS232(DB9), Ethernet (RJ45), 
	PS/2, USB(Host/Device),
	
	*Multimedia
	LCD+Ts(40pin), VGA, TV
	Audio output/in(MIC), 
	
	*Storage
	SD, IDE, 
	
	*Interface
	Key, Jtag, 
	
	2、重要芯片
	S3C2410(cpu), HY57V561620C(SDRAM), 
	k9f1208(NandFlash), sst39vf160(NorFlash)
	CS8900/DM9000(Net), UDA1341/WM9714(Audio)
	
	day11：section2：交叉开发环境搭建
	su 切换到管理员模式，密码为 123456
	rpm -ivh --force *.rpm
	vi /etc/profile
	添加系统可执行文件路径
	export PATH=$PATH:/opt/host/armv4l/bin:/sbin
	
	armv4l-unknown-linux-gcc hello.c -o hello
	
	4、搭建网络环境 - 试错
	startx 进入图形系统
	ifconfig 查自己ip
	ping 自己ip
	ping 192.168.0.1
	
	route add default gw 192.168.0.1
	ping 8.8.8.8  可以ping通外网
	ping www.baidu.com 看域名解析
	如果不通，则添加 /etc/resolv.conf  域名解析器
	nameserver 8.8.8.8
	nameserver 210.73.64.1
	nameserver 202.106.0.20
	
	能否 ping 得通的一个关键条件
	iptables -F 关闭Linux的防火墙
	windows XP 与之类似的是 控制面板->网络Internet连接->windows防火墙->关闭(不推荐)
	
	ftp server (ftpd:21)
	启动 server： /etc/init.d/vsftpd restart/stop/start
	ftp 172.16.0.101
	> username : akaedu
	passwd: akaedu
	> ls
	> get
	> put
	> bye/quit
	配置文件： /etc/vsftpd/vsftpd.conf
	主目录： /home/akaedu/
	
	webserver (httpd:80)
	启动 server: /etc/init.d/httpd restart/stop/start
	http://172.16.0.101
	可以看到测试页 test page
	配置文件： /etc/httpd/conf/httpd.conf
	主目录： /var/www/html/
	
	day11：section3：嵌入式Linux内核编译
	tar zxvf linux-br.tar.gz 解压
	du [-sh] 查看目录文件大小
	find . -name *.c | wc 查看所有 c 文件 (4000+ 个c程序)
	
	vmlinux: 其实就是 linux 的内核，带有 ELF 格式的内核
	zImage：就是我们最后烧写到板子上的，没有格式的bin文件内核
	       ld
	*.o ------> vmlinux
	
		objcopy
	vmlinux-----> zImage
	
	make menuconfig 
		General Setup
		Char Device - 串口
		Block Device
		MTD Device - NandFlash
		Network Device - Cs8900
	make dep
	make vmlinux
	make zImage
	
	zImage 在 arch/arm/boot 目录下, 拷贝到 /home/akaedu
	cp arch/arm/boot/zImage /home/akaedu
	
	切换到 D: 用 ftp 下载，修改binary传输模式，get zImage
	
	把 zImage 烧写到第12block处 即 0x30000 处
	1M: 12block 12*16K = 192K 处
	
	把 mycramfs.img 烧写到第128block处 即 0x200000 处
	1M: 128block 128*16K = 2M 处
	
	把 vivi 烧写到 0block
	
	C:\Documents and Settings\Administrator>type my.cmd
	akaedu
	akaedu
	binary
	get zImage
	bye
	
	C:\Documents and Settings\Administrator>ftp -s:my.cmd 172.16.0.101
	Connected to 172.16.0.101.
	220 (vsFTPd 1.1.3)
	User (172.16.0.101:(none)):
	331 Please specify the password.
	
	230 Login successful. Have fun.
	ftp> dir
	200 PORT command successful. Consider using PASV.
	150 Here comes the directory listing.
	drwxr-xr-x    2 0        0            4096 Jan 19 18:17 arm
	drwx------    4 500      500          4096 Apr 28 17:23 evolution
	drwxr-xr-x    2 500      500          4096 Apr 28 14:36 hello
	-rw-r--r--    1 0        0            5143 Apr 28 15:30 hello.1
	drwxr-xr-x    2 0        0            4096 Mar 23 15:53 image
	drwxr-xr-x    2 0        0            4096 Apr 28 15:41 led
	-rw-r--r--    1 500      500           755 Apr 28 15:25 led.c
	drwxrwxr-x    3 500      500          4096 Apr 28 15:41 lwj
	drwxrwxr-x    4 500      500          4096 Apr 28 16:58 mc2410
	drwxr-xr-x    2 0        0            4096 Jan 19 18:19 skyeye
	-rwxrwxr-x    1 500      500        989788 Apr 28 19:05 zImage
	226 Directory send OK.
	ftp: 收到 696 字节，用时 0.01Seconds 46.40Kbytes/sec.
	ftp> get zImage
	200 PORT command successful. Consider using PASV.
	150 Opening BINARY mode data connection for zImage (989788 bytes).
	226 File send OK.
	ftp: 收到 989788 字节，用时 0.03Seconds 31928.65Kbytes/sec.
	ftp> bye
	221 Goodbye.
	
	读取flash，把 zImage 放到 0x30008000 处，然后 go
	nr 0x30000 0x30008000 0x100000
	
	Source Insight 查源码
	
	得到一个 rootfs 的img文件。
	mkcramfs root_china my-cramfs.img
	ftp 下载下来，烧写到 128block
	
	./mnt/etc/init.d/rcS
	#exec /usr/etc/rc.local
	
	/var/www/html/hello
	wget http://172.16.0.101/hello

	
	




	
				 













								   



