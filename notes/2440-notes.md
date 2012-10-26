	day1:

	ADS - IDE 集成开发环境
	AXD - Debugger
	H-Jtag Sever - Debug Agent 调试代理 (dll)
	ARMulator - 模拟器 （软件仿真cpu）

	0x20800000 - LED 地址
	0x20800080 - Seg7 地址

	0x1000 = 4K
	0x8000 = 32K 地址没有

	SRAM --> 0-4K


	ARM Linker : 
	RO Base - 0x8000 (.text)
	RW Base - none (含义就是跟在RO的后面) (.data)

	main 程序进入的真正入口是 __main
	__main -> main  中间出了问题，进不去 main 函数
	如果设置 entry 入口为 main ，则可以解决这个问题


	day2:
	s3c2440 芯片内部结构 SoC (System On Chip)
	1、ARM920T 内核
	2、BUS (AHB, APB) 总线
	3、Controllers 外设控制器

	System:
	cpu Core + Power + Clock + Jtag + Interrupt + Timer + DMA
	GPIO + UART + LCD + TS + Audio

	ARM920T 内核内部结构
	*1、ARM9TDMI Processor Core 处理器核
	2、MMU (Instruction & Data) 内存管理单元
	3、Cache (Instruction & Data) 高速缓存
	4、CP15 (Co-Processor) 协处理器 (配置MMU&Cache)
	5、WriteBuffer 写缓冲
	*6、AMBA Bus 总线
	*7、JTAG 调试接口

	CLOCK 时钟发生器 MPLL 锁相环
	DMA 直接存储访问 (介于AHB与APB总线之间，进行数据传输)

	BUS 总线
	AHB - High-performance 
		100M, 133M
		Memory (SDRAM)
		LCD Controller
		Interrupt Controller
		USB Host Controller

	APB - Peripheral 
		GPIO Controller
		UART Controller
		IIC/IIS Controller
		ADC Controller
		Timer Controller
		RTC Controller
		AC97 Controller

	S3C2440 Pin Assignment (289-pin FBGA)

	S3C2440 Special (Funtion) Register - SFR
	0x48000000 ----> 0x5B000000
	范围： 0x13000000 => 2^28 = 2^8M = 256M
	实际占用：200-300 regs * 4byte = 1000byte = 1K


	ARM9TDMI Processor Core 处理器内部结构
	1、寄存器名称 R0-R14, ...
	2、工作模式 (USR/SYS, ...)
	3、指令集 (mov, str, ldr, add/sub, ...)


	1) 寄存器
	37个
	37 = 17 + 0 + 3 + 3 + 3 + 3 + 8 
	     USR SYS SVC ABT UND IRQ FIQ 	
	37 = 8*1 + 5*2 + 2*6  + 1*1 + 1*1 + 1*5 
	    R0-R7 R8-R12 R13/14  PC   CPSR  SPSR

	2) 工作模式
	USR/SYS SVC
	IRQ/FIQ 
	UND, ABT

	3) 指令集
	指令分类
	问题1：1+2+3 ...+100 = ? 5050 = (0x13ba)
	a. 数据传输指令
	mov 指令（只能传输立即数）
	例子：mov r0, #0x1 或者 mov r1, #100
	立即数？ 规律： 有效位8bit + 移位数4bit
	举例： 
	是立即数 0xff, 0xff00, 0xff0000, 0x81, 0x8100, 0x204
	不是立即数 0xff1, 0xff8, 0x1001, 0x101, 0x102

	mov 指令 (通用寄存器 -> 通用寄存器)
	例子： mov r0, r1
	mov r0, cpsr （状态cpsr寄存器不能用 mov）
	重要结论：pc 寄存器的值：在执行时，等于当前指令地址+8

	b. 算术运算指令
	add/sub r0, r1, r2	(r1+r2->r0)
	++: add r0, r0, #1

	c. 循环指令 （跳转指令）
	b / bl
	举例：
	无条件跳转:  b loop 类似 goto
	有条件跳转： beq loop / bne loop
	带返回地址的跳转：  bl loop 用来实现函数的调用，
	函数内部的 return 是用 mov pc, lr 来实现的


	问题2：给定一个数组10个元素，进行累加，把结果填入最后
	d. 内存访问指令 
	ldr/str	(ldrb/strb, ldrh/strh)
	特殊用法：
	mov r0, #0x12345678
	ldr r0, =0x12345678	
	本质： 一条指令+一个数据


	Xmodem 协议
	1、一个包是定长，还是不定长？ 长度为多少字节？
	2、数据在包里的什么位置？
	3、谁启动 xmodem 的传输过程？ 如何启动的？
	4、传输过程中发生错误怎么办？ 正确的话怎么响应？
	5、最后是怎么结束的？


	发送方 			接收方	
				delay
			<--	NAK
	SOH + 131 bytes	-->	
			<--	ACK
	SOH + 131 bytes	-->
			<--	ACK
	...
	...
	EOT + 0	byte	-->
			<--	ACK
	loadx()
	{
		delay();

		putchar(NAK);
		while (getchar() != EOT)
		{
			// get 131 bytes
			// omit 2 bytes (serial num)
			getchar();
			getchar();
			
			// get 128 bytes (data)
			for (i = 0; i < 128; i++)
				*(char *)(addr++) = getchar();

			// omit 1 byte (parity)
			getchar();

			// send ACK
			putchar(ACK);
		}
		putchar(ACK);

		return 0;
	}


	CLOCK 时钟管理
	1、FCLK - ARM920T
	2、HCLK - AHB
	3、PCLK - APB (peripherals)

	2 PLLS
	1 -> FCLK, HCLK, PCLK
	2 -> USB

	CLOCK Generator BLOCK DIAGRAM
	时钟发生器
	1、OM 操作模式 Operation Mode
		OM[3:2] = 11 硬件连线配置，=12M hz
	2、MPLL 倍频
		Phase-Locked Loop 锁相环
		P[5:0] M[7:0] S[1:0]
		MPll <== (2*m*Fin)/(p*2^s)
			m=M+8	p=P+2	s=S
		MPLLin = 12M 
		MPLLout = Mpll = ?M
		PLL Value Selection Table
		12M -> 304M	0x44 0x1 0x1
		12M -> 405M	0x7f 0x2 0x1
		MPLLCON
	3、CLKCNTL (HDIVN, PDIVN)
		分频
		DIV 分频因子（用做除数）
		CLKDIVN 必须设 
		CAMDIVN 可以不设
	4、FCLK, HCLK, PCLK
		304M, 100M, 50M (6:2:1)

	结论：
	MPLLCON	0x4c000004	
	CLKDIVN 0x4c000014

	myboot> mw 0x4c000004 0x44011
	myboot> mw 0x4c000014 0x7


	50000000
	--------  -  1 = ？324.5 -> 325
	9600*16


	Memory Management 存储管理
	S3c2440 的存储结构
	Memory Controller
		SRAM, ROM (Nor Flash)
		SDRAM
	Nand Flash Controller
		Nand Flash

	Memory Map 内存映射
	S3C2440: 1GB(byte)
		128M * 8 banks
		8 banks -> nGCS0 - nGCS7 片选
		统一编址，
		SDRAM: 0x30000000 和2440芯片有关，和连接的片选nGCS6有关

	SRAM vs SDRAM
	数据线
	地址线
	以及cpu 地址线和 sram 地址线之间的连接

	SDRAM
	行地址：A0-A12	13根
	列地址：A0-A8	9根
	BA0/1: bank选择 2根
	-------------------------
	合计		24根
	所以： 4banks * 2^22(4M) * 16bit = 32M

	CPU 角度
	A0/A1: 	不连接
	A2-A14: 作为行地址，先发出
	A15-A23: 作为列地址，然后发出
	A24/A25: 作为 bank 选择

	举例： SDRAM 地址 0x31234560 的访问流程

	0x31234560
	0x 3    1    2    3    4    5    6    0
	0011 0001 0010 0011 0100 0101 0110 0000
	       bb +         +.              .--  

	SRAM vs SDRAM
	1、相同点 RAM，可读可写。LDR/STR r0, [r1]
	2、总体上，都有地址线Addr，数据线Data，控制线CS/WE/RE
	3、不同点 SRAM晶体管/无限读写 	SDRAM电容/充放电/动态刷新
	4、硬件接口： SRAM地址不复用  SDRAM地址是行列复用/内部分BANK
	5、软件编程： SRAM属于片内，无需驱动； SDRAM属于片外，需要控制器驱动，软件还需要配置控制器

	K4S561632C SDRAM
	27*2 = 54pin TSOP

	SRAM
	时序上
	1、地址
		控制信号
	2、数据


	SDRAM Timing Diagram
	时序上
	1、BANK 选通
	2、行地址
	3、列地址
	4、数据


	2^11 + 1 - r_c = 7.8 us * 100Mhz = 7.8 * 100

	r_c = 2048 + 1 - 780 = 1269

	2048 - 0 + 1/100Mhz = 2049

	以下3个地址各不相同
	0x30000000 (nGCS6)
	0x30800000 (Column address 9bit)
	0x34000000 (banksize 64MB)

	SDRAM 驱动的关键参数
	1、数据总线宽度 32bit (Bus Width) (每次出来4byte数据，A0,A1不管)
	2、列地址的宽度 9bit (Column ADDR Width)  (26-2-9 = 行地址15 BA0/BA1用了A25/A24，13根行A2-A14, 9根列A15-A23)
	3、芯片容量 64MB (BANK Size) - 2^26 (地址线 26根) A0-A25
	4、刷新频率 1269 = ()/() ---> 7.8us = 64ms / 8192(8K cycles)
	5、时间延迟参数 Trcd (RAS to CAS delay = 4clocks) > 20ns when HCLK=100Mhz

	SDRAM 重要的接口引脚
	A0-A12
	BA0/BA1
	--------
	D0-D15
	--------
	nCS
	nRAS
	nCAS
	nWE
	--------


	NandFlash 驱动编程

	#1 Nand Flash 重要的接口引脚
	IO0-IO7
	---------------
	nCE
	nWE
	nRE
	nWP
	-----
	ALE
	CLE
	nR/B
	-----

	#2 Nand Flash 的读/写/擦除时序
	page read:
		CMD-0x00/01
		ADDR-0 (A0-A7)
		ADDR-1 (A9-A16)
		ADDR-2 (A17-A24)
		ADDR-3 (A25)
		wait R/nB
		read DATA (256/512/528)

	page program:
		CMD-0x80
		ADDR-0 (A0-A7)
		ADDR-1 (A9-A16)
		ADDR-2 (A17-A24)
		ADDR-3 (A25)
		write DATA (512)
		CMD-0x10

	block erase:
		CMD-0x60
		ADDR-0 (A9-A16)
		ADDR-1 (A17-A24)
		ADDR-2 (A25)
		CMD-0xD0
		wait R/nB
		CMD-0x70
		read DATA(I/O-D0)

	#3 Nand Flash 的 read ID 怎么实现？
		CMD-0x90
		ADDR-0 (0x00)
		read DATA 4次(0xEC, 0x76, 0xA5, 0xC0)


	0x31000000 

	int nand_read(int flash_addr, char * sdram_buf, int size);

	int nand_read_page(int page_id, char * sdram_buf, int start_addr, int size);


	WavPlayer 项目
	1、音频驱动工作参数
	音频采样精度： 16位、8位 （AD转换的精度）
	频道数：2双声道, 1单声道
	音频采样级别： 22Khz，44Khz （1秒采样22000个)

	882000 字节 wav 在 16bit-2channel-22Khz 播放 10 秒

	1秒需要 22000*4= 88000 字节 = 704000 比特bit 704Khz < 1Mhz

	2、IIS音频总线接口
	I2SSDO  数据输出
	I2SSDI  数据输入
	I2SLRCK 帧时钟
	I2SSCLK 串行时钟 (位时钟)

	关系：	位时钟 = 帧时钟 * 32倍 (频道数 * 采样精度)
		帧时钟 = 采样级别（频率）
		
		帧时钟 = 22Khz
		位时钟 1秒 22Khz * 32 = 704Khz

		时钟输入 PCLK = 50Mhz

	3、L3控制总线
	L3MODE: GPB2	0: address  1: data
	L3DATA: GPB3	setup: 190ns
	L3CLOCK:GPB4	L: 250ns    H: 250ns

	4、UDA1341内部原理
	3个内部特殊功能寄存器
	DATA0:  00
	DATA1:  01
	STATUS: 10

	音量控制  Volumn / Mute

	device address 6bit : 000101
	SFR address 2bit: DATA0 - 00
	address: 00010100 -> 0x14
	data: 	0x0 (max)
		00111011 (min) -> 0x3b

	L3_write(0x14, 0x3b)	set volumn min
	L3_write(0x14, 0x00)	set volumn max

	L3_write(0x14, 0xA4)	set volumn mute
	L3_write(0x14, 0xA0)	set volumn no mute

	void delay(int t_ns)
	{
		for(i = 0; i < t_ns; i++)
			;
	}


	NandFlash 
	0x1M: 64block  1.wav try1-22K-16bit-Windows XP 登录音.wav
	0x2M: 128block 2.wav try2-44khz-16bit-Jingle Bells1_OUTPUT.wav
	0x3M: 192block 3.wav try3-22khz-8bit-1channel.wav
	0x4M: 256block 4.wav try4-44khz-8bit-1channel.wav


	WAV 文件格式
	16H 2byte channel 0001/0002 (1ch/2ch)
	18H 4byte fs	00005622/0000ac44 (22.05/44.10Khz)
	22H 2byte databit 0008/0010 (8bit/16bit)


	DMA 直接存储访问 Direct Memory Access
	工作参数：
	源数据地址
		读取源数据的方式 ++/fixed
	目标数据地址
		写目标数据的方式 ++/fixed
	数据长度
		atom size, burst 4, TC 次数
	启动
		SW/HW, trigger bit, source controller
	结束
		reload, INT REQ

	1、memcpy(const char * src, char * dst, int size)
	DMASRC = 
	DMADST = 
	DMASIZE =
	DMASTART = 1;

	while(size--)
	{
		*dst++ = *src++;
	}

	2、uart_puts
	SRC, DST, SIZE, START
	MODE (auto-inc, fixed)

	while(*buf)
		UTXH0 = *buf++;

	3、uart_gets
	MODE (auto-inc, fixed)
	while(size--)
		*buf++ = URXH0;

	4、uart_relay(uart0, uart1)  串口转发
	*buf = URXH0;
	UTXH1 = *buf;

	UTXH1 = URXH0;


	FSM (有限状态机)



	void IIS_trans_data_dma(short * pdata, int size)
	{
		//init IIS = DMA mode
		//Transmit FIFO access mode select [15] 0 = Normal 1 = DMA
		IISFCON |= 1<<15;
		//init dma mem->IISFIFO
		//Transmit DMA service request [5] 0 = Disable 1 = Enable
		IISCON |= 1<<5;
		
	#define DISRC2 		(*(volatile unsigned int *)0x4B000080)
	#define DISRCC2 	(*(volatile unsigned int *)0x4B000084)
	#define DIDST2 		(*(volatile unsigned int *)0x4B000088)
	#define DIDSTC2 	(*(volatile unsigned int *)0x4B00008C)
	#define DCON2 		(*(volatile unsigned int *)0x4B000090)
	#define DMASKTRIG2 	(*(volatile unsigned int *)0x4B0000A0)
		//start dma
		DISRC2 = (int)pdata;
		DISRCC2 = 0x0;
		DIDST2 = (int)0x55000010;
		DIDSTC2 = 1<<1 | 1<<0;
		DCON2 = (unsigned int)size/2;
		DCON2 |= 1<<23 | 1<<20;
		DMASKTRIG2 = 1<<1;
		
		return;
	}



	INT 中断
	1、中断的触发
	DMA -> INT Req
	INT controller -> INTMSK 屏蔽位打开
	ARM920T Core -> CPSR I-bit 打开

	pc <--- 0x18
	mode -> IRQ
	lr_irq <--- return address (IRQ)
	lr_svc











































