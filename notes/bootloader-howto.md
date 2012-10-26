	亚嵌40期ARM知识点总结

	第1大组：卢少昆，刘旺，刘瑞刚，赵雪峰，朱江，王珺
	正组长：朱江	副组长：卢少昆
	* Bootloader编程实现流程
	赵雪峰：
	三大部分
		1 硬件初始化
			- 关闭看门狗
				原因：作用-自动复位，会影响程序运行
				寄存器： WTDCON = 0		
			- 时钟频率配置
				S3C2440 的3个时钟频率，初始频率12M
				FCLK - 最快，直接用于CPU, 304M
				HCLK - 高速总线, 100M
				PCLK - 低速总线, 50M
				频率比例：6: 2: 1
				寄存器：MPLLCON = 0x44011
						MDIV: 0x44
						PDIV: 0x1
						SDIV: 0x1
					CLKDIV = 0x7
						FCLK:HCLK = 3:1
						HCLK:PCLK = 2:1
			- 启动提示
				闪灯3下
					直接控制 0x20800000 = 0x1 / 0x0 亮/灭		
			- 重要外设的初始化
				* GPIO 配置
					- 关闭蜂鸣器
						配置为输出状态
						设置值为 0x0	
				* 串口初始化
					- 帧的构成（串口帧结构）
						空闲态-高电平
						数据传输-低电平，起始位
						8位数据传输，从低位到高位
							中间采样
						校验位：不用，置0
						停止位：1位
					- 波特率设置
						115200
					- 引脚描述
						Txd 发送
						Rxd 接收
						GND 地				
				* SDRAM初始化
					- 总线设备
						总线控制寄存器
							挂在 Bank6 上，
							设置 BWSCON 寄存器						
				* Nand Flash初始化
					- 不是总线设备，IO设备
						数据，命令和地址都复用了 IO0-IO7 8根线
					- 引脚描述
						命令锁存，地址锁存
					- 寄存器
						设置 NFCONF: 
						     NFCONT:
		2 自拷贝
			- 超过4K，需要自拷贝
			- 硬件自动搬移 0-4K 到 SRAM
			- 超过部分需要自拷贝，从Nandflash 0 块拷贝到 0x31100000 （SDRAM中）
			- 实现跳转到 0x31100000 之后的某个偏移地址
			- SDRAM 的规划
				初始化地址 0x30000000 - 0x33FFFFFF (64M)
				第1部分：Kernel 参数 
				第2部分：Kernel 0x30080000 - 0x30800000 (2M)
				第3部分：文件系统 0x30800000 - 0x31000000 (8M)
					0x31100000 - bootloader 
				第4部分：栈 0x32000000 向下增长
				第5部分：用户空间 0x32000000 - 0x33FFFFFF 
			- NandFlash规划
				从第3块 block 2 开始，烧写Kernel，留出2-200块
				从200块开始到 1000块，烧写 FS ，共800块			
		3 Shell实现，加载操作系统
			- 命令实现
				help: 帮助命令
				id: 打印 nandflash chip id
				nandread: 读 nandflash
					格式 nandread nand_addr size  sdram_addr
				nanderase: 擦除 nandflash
					格式 nanderase block_id
				nandwrite: 写 nandflash
					格式 nandwrite nand_addr size  sdram_addr		
				nandpageread: 读页并显示
					格式 nandpageread block_id page_id
				xmodemdown: 用 xmodem 协议下载
					格式 x down_addr (0x31130000)
						1 接收方要发送一个 NAK (0x15)
						2 发送方收到 NAK 后，开始发送数据帧
							帧： 132 个字节
							前3个字节： 帧头
								byte 1：SOH (0x01) 合法的帧
								byte 2: 序号						
								byte 3：反码							
							中间128个字节： 数据
								byte 0-127 ： 数据
							最后1个字节：校验码
						3 接收方收到数据帧后，发送一个响应 ACK (0x06)
						4 发送方收到 ACK 后，接着发送下一个数据帧
						5 万一接收方没有收到，或者没有发送 ACK
						  发送方重新发送上一个数据帧
						6 发送方发完最后一帧，发送 EOT (0x04) 表示发送结束‘
						7 接收方回复一个 ACK (0x06) 表示知道了，停止接收					
				go: 跳转执行
					格式 go addr (0x31130000)
				memwrite: 
				memread:  读写内存
				kernel: 启动 kernel
					格式 kernel
						1 设置 kernel 启动参量
							r0: 0 - 
							r1: machine id - 0x7cf
							r2: cmdline addr - "init=/linuxrc console=ttySCA0,115200 root=/dev/ram rw ramdisksize=8192"
						2 从 nandflash 中读 2-200块 到 sdram 0x30008000
						3 加载 fs ，从 nandflash 中读 200-1000块 到 sdram 0x30800000
						4 go 到 0x30008000
			
	第2大组：王建生，李强，陶雨凡，刘福营，徐九龙，白广鹏
	正组长：刘福营	副组长：李强
	* ARM存储体系与软件编程
		SRAM
			掉电后丢失，价格比较昂贵，Mos管实现，size: 4K
			启动时自拷贝 nandflash 0-4K 到 SRAM (4K)
		
		SDRAM
			特点：掉电后丢失，比SRAM慢，比Flash快
			总线控制
				可以直接对地址进行读写
					*p 指针方式
					LDR r0, [r1]  读 r1 地址的内容到 r0
					STR r0, [r1]  写 r0 的内容到 r1 地址
				低16位在 sdram0
				高16位在 sdram1
				两片16位，拼接成 32位
			引脚描述
				16根数据线：
					data0-data15
				13根地址线：
					复用
						先是行地址 13根
						然后是列地址 9根
				2根bank线
					BA0, BA1
				SCLK: 时钟
				SCKE: 时钟使能
				nWE： 写
				nSCS: 片选
				nSRAS: 行地址锁存
				nSCAS: 列地址锁存
			初始化
				设置寄存器 
				BWSCON
					设置 总线宽度： 32bit
					BWSCON = 0x22000000			
				BANKCON6
					设置 使能
					BANKCON6 = 1	列9bit （默认是8bit）					
		NandFlash
			特点： 4096块block，1块32page，1page是512byte
			地址线，数据线，（和命令值）是复用的		
			掉电后仍在，IO设备
			无法直接用 *p 或者 ldr/str 的方式访问
			初始化
				设置寄存器
					NFCONF
						设置 CLE 和 ALE 的 duration = 0+1 HCLK (10ns)
						设置 TWRPH0 的 duration = 2+1 HCLK (30ns)
					NFCONT
						设置 NandFlash 的 controller enable = 1
						设置 NanfFlash 的 chip select = 0
						设置 NandFlash 的 ECC generation 给 unlock 掉，把ECC给 disable 掉了				
			访问方式：
				读时序：
					先发命令字 0x0
					再发地址 addr 4次
						addr0-addr7
						addr9-addr16
						addr17-addr24
						addr25	
					接着等待，看状态位是否为 busy?
					不busy，则可以开始读数据了，
						循环连续读 512 次，每次1个byte
				写时序：
					先发命令字 0x80
					再发地址 addr 4次
						addr0-addr7
						addr9-addr16
						addr17-addr24
						addr25	
					循环连续写 512 次，每次1个byte
					先发命令字 0x10
						接着等待，看状态位是否为 busy?
						不busy，则写入命令字 0x70
							可以读状态寄存器，检查是否有误？					
				擦除时序：
					先发命令字 0x60
					再发地址 block addr 3次
						（通过 >> 8 ，把页内地址排除掉）
						page id 的 低8位 （也包含了 block 地址）
						page id 的 中8位 （也包含了 block 地址）
						page id 的 高1位 （也包含了 block 地址）
					再发命令字 0xd0	
					接着等待，看状态位是否为 busy?
					不busy，则表示擦除完成
						先写入命令字 0x70
						可检测状态寄存器 最低位 是否为 1 ？
				
	第3大组：郭朋，邢芳琳，孟旭东，王周宇，吉早祥，叶向阳
	正组长：郭朋	副组长：吉早祥
	* 硬件驱动原理与编程实现
		串口的基本知识
			异步收发，RS232，适合 0-200000bps，（典型115200bps）
			按位bit收发，最长可达1200米
			4个工作参数
				波特率：bit per second
				数据位：data bit - 8bit
				停止位：stop bit - 1bit
				奇偶校验位： parity 无
			数据帧
				起始位 + 数据位 + 停止位 + 校验位（奇偶）
			串口的接口
				9针，DB9
				pin2：Rxd
				pin3：Txd
				pin5：GND
			串口寄存器设置：
				ULCON0: Line Control 设置串口工作参数
					数据位，停止位，校验位
						ULCON0 = 0x3;
				UCON0: 	使能接收和发送，并且采用 poll 方式/interrupt 方式
					UART的时钟源选择 PCLK 
						UCON0 = 0x5;
				UBRDIV0:  设置波特率
					BRDIV = (int) (PCLK / baudrate / 16) - 1;
				GPHCON: 设置 GPIO 的功能复用方式 pin function select
					130 个 gpios ，分组 GPA - GPJ
					GPH3: RXD0
					GPH2: TXD0
					GPHCON: [7:6]	10 = RXD[0]
						[5:4]	10 = TXD[0]
			串口数据读写
				UTRSTAT0 - 状态寄存器 	
					bit2: Transmitter empty (buffer&shifter empty)
					bit0: Receive buffer data ready
					轮询方式检查这2个位，”忙“等待
				
				UTXH0 数据发送寄存器 -> Buffer (FIFO) -> Shifter -> TxD (signal)
				ULCON0 -> Control Unit -> TxD (signal)	
				UBRDIV0 -> Buad-rate Generator -> TxD (signal)	
			串口内部结构图
				1 Shifter (Transmit/Receive)
				2 Buffer (Transmit/Receive)
					FIFO - 64byte
						UFCON0: UART 0 FIFO control
					non-FIFO - 1byte
						UTXH0: 数据发送寄存器
						URXH0: 数据接收寄存器
				3 Control Unit 
					data bit width
					stop bit width
					parity bit 			
				4 Buad-rate Generator
					buad rate
					
	第4大组：陈玉森，李振，梅文彬，王耀星，贾文涛，吴锦锦、
	正组长：陈玉森	副组长：贾文涛
	* ARM指令集与汇编
	陈玉森
		ARM指令格式
			0xE59FF018: 	ldr pc, [pc, 0x18]
			0xE5801000:     str r1, [r0]
			0xEAFFFFFE:     B .
			0xEB000028:     bl wait_ready
			0xE3A01000: 	mov r1,  #0
			0xE0455004:     sub r5, r5, r4 
			0xE2511001:     subs r1, r1, #1
			
			bit31-28:  条件码
				EQ：0000 
				NE：0001
				...
				AL: 1110 (E)
			bit24-21:  操作码
						
			bit20: S bit
				是否影响 cpsr
			bit19-16: 第一个操作寄存器编码，源操作数寄存器
			bit15-12: 目标寄存器编码
			bit11-0: 立即数 	
		ARM指令分类
			数据传输指令 (要有数)
				mov
			数据处理指令 (要能加)
				算术指令
					add
					sub
				逻辑指令
					and
					orr
				比较指令
					cmp
			跳转指令 (要能多加几次)
				b
				bl
				bx: 切换到 thumb
			访存指令 (要能把结果存起来)
				ldr: mem -> reg
				str: reg -> mem		
				栈操作指令（批量寄存器传输指令） (要能多存几次)
					ldm (fd, ed, fa, ea)
					stm (fd, ed, fa, ea) 
					FD: 满递减堆栈		
			---------------------------------------------------------		
			软件中断（异常产生） （要能区分用户和系统，从用户到系统）
				SWI: software interrupt
					USR ----swi----> SVC
			状态寄存器访问指令 （要能从系统回到用户）
				MSR: Reg -> Status(PSR)
				MRS: Status(PSR) -> Reg		
			信号量 （要能进行原子操作，实现多任务并发）
				SWP: r0, r1, mem
					r0 -> mem
					mem -> r1
			协处理器指令 （要能实现进程的独立地址空间，管理MMU）	
				MCR: Reg -> Co-Processs (CP15)
				MRC: CP15 -> Reg	
			----------------------------------------------------------	
		汇编编程
			汇编格式
				用TAB开头
				声明 AREA 
				声明 CODE, READONLY
				声明 CODE32
				声明 ENTRY
				正式的汇编语言			
				结尾用 END
				symbol 顶格写
				注释用 分号  ;
				b %1 就近跳转
			汇编和C的混合编程
				import	export
				C 内嵌汇编
					__asm
					{
					
					
					}
				汇编跳转C
					声明 import C的函数名
					直接跳到C的入口
					入口名字用 main ？
						一般不用 main 起名，避免会引入很多初始化代码
					汇编给C函数传递参数 ATPCS
						r0, r1, r2, r3 四个，
						超过4个，则压入栈中传递
					返回值一般放在 r0 中
					设置好SP，函数栈空间设置好
		
	第5大组：柴亚林，姚雪峰，王钺，张连帅，郭强，刘婷，魏彤波
	正组长：姚雪峰	副组长：魏彤波
	* ARM体系结构
	刘婷
		ARM处理器组成 (SoC: S3C2440 chip)
			1) ARM 处理器内核组成 (ARM920T Core)
				1 ARM运算核心（ARM9TDMI Processor Core)
					1 控制器
						取指令，译码，执行，读写内存
					2 运算器
						进行算术运算，位运算
					3 寄存器
						CPU内部临时存储空间					
				2 ARM MMU (Memory Management Unit)
					Instruction MMU
					Data MMU			
				3 ARM CACHE (16K)
					Write Buffer			
				4 CP15 (Co-Processor 15) 协处理器
					register 0 - register 15 (MCR/MRC)				
				5 AMBA Bus
					AHB - USB Host CONT, Camera Interface, Memory CONT.SRAM/NOR/SDRAM
					APB - GPIO, I2S, UART
			2) ARM Bus
				AHB
				APB
			3) Peripheral (device controller)
				GPIO
				CLOCK & Power
				UART 
					USB
					NET
					SPI
					IIS
					IIC
				LCD
				TS(touch screen)
				Interrupt Controller
				DMA controller	
			4) JTAG module
				Debug interface	
			
		ARM工作模式
			Soc -> ARM COre(ARM920T) -> Processor Core(ARM9TDMI) -> mode
			7种工作模式
				特权模式
					SYS: 系统
					SVC: 管理
					IRQ: 普通中断
					FIQ: 快速中断
					UND: 未定义
					ABT: 中止			
				非特权模式
					USR: 用户
				---------------------------------
				异常模式：				
					SVC: 管理
					IRQ: 普通中断
					FIQ: 快速中断
					UND: 未定义
					ABT: 中止			
				非异常模式：			
					USR: 用户
					SYS: 系统
				---------------------------------	
		ARM寄存器组织
			37个寄存器
			7种模式下
				通用寄存器
				r0-r7:  	8个 * 1组
				r8-r12: 	5个 * 2组 （FIQ一组，其他一组）
				r13-r14:	2个 * 6组 （USR/SYS共1组，其他异常模式下各1组）
				r15:		1个 * 1组 （所有模式下共1个）
				-------------------------------------------------
				状态寄存器
				CPSR：		1个 * 1组 （所有模式下共1个）
				SPSR：		1个 * 5组 （只有异常模式下各1个）
				-------------------------------------------------
				r13: SP 栈指针寄存器
					用法：
						汇编进入c时要设置，
						传递给函数的参数超过4个，要用到
						函数调用的时候，可能要压栈
						函数内部定义局部变量，特别是数组的时候，要用到
						异常产生的时候，要保持处理器现场寄存器，要用到压栈
				r14: LR 连接寄存器
					用法：
						BL 跳转时要用到，把返回地址存入LR
						返回时用 mov pc, lr
						BX LR 时，要用到，直接返回
						异常发生的时候，返回地址也会保存到LR
				r15: PC 程序计数器
					用法： PC的值永远是指向了预取指令的地址
						而不是正在执行指令的地址；
				CPSR: 当前程序状态寄存器
					bit0-bit4: mode bit
						USR: 10000 
						SVC: 10011
						IRQ: 10010
					bit5: T bit
						Thumb 状态/指令集
					bit6: F bit
						FIQ disable 禁止位
					bit7: I bit	
						IRQ disable 禁止位
					bit28-31: flag
						N: negtive
						Z: Zero
						C: Carry
						V: overflow
		ARM存储系统
			存储空间 S3C2440 为例
				BANK0： 0-4K  SRAM : Using NandFlash as bootROM
					Nor Flash: 128M
				BANK1 - BANK5:
					SROM: SRAM, ROM (Nor FLASH)
				BANK6:
					nGCS6: nSCS
						SDRAM0, SDRAM1				
				BANK7:	
					nGCS7: 
				BANK0-BANK7: 0x00000000 - 0x40000000 (0G-1G)
			
			Special (Function) Registers 特殊功能寄存器 SFR
				0x48000000: Memory Controllers
				0x49000000: USB Host Controller
				0X4A000000: Interrupt Controller
				0x4B000000: DMA
				0x50000000: UART
				...
				0x5B000000: AC97 Audio-CODEC
			
			S3C2440 芯片
				数据线： 多少根？
					32根
					data0 - data31
				地址线： 多少根？
					27根
					addr0 - addr26
				2^27 = 2^7 M = 128M = 1bank  nGCS0-nGCS7 (addr27,28,29) = 8 banks = 1G
				
				
				
	异常总结
		1 ARM 异常有哪些类型？			
		2 ARM 异常发生后，模式如何切换？	
		3 ARM 异常向量表是什么？ 有何特点？	
		4 软件中断异常如何触发？ 该指令有何特点？24-bit	
		5 什么是软中断号？ 有什么用？ 如何获得？	
		6 异常触发之后，硬件做了哪些工作？	
		7 异常发生硬件跳转后，软件如何实现跳转？注册	
		8 异常的处理器现场是指什么？ 17个寄存器 如何保存和恢复？	
		9 异常如何返回？ 返回时要注意什么？（同时）
		a 用C语言实现异常处理，哪些是用汇编语言完成的必要工作？	
		b 异常发生全过程，内存布局是怎样的？ 运行时的模式是怎样的？	
		c ARM 的异常和中断有何异同，如何区别？	

	中断总结
		a 中断的来源，中断源有哪些？ 以2440为例。
		b 中断是如何产生的？ 以IO按键为例。
		c 中断产生之后，硬件做了哪些处理？
		d 中断产生之后，软件上是如何进行处理的？
		e ARM 的软件中断处理和硬件中断处理有何异同？	
			
		
