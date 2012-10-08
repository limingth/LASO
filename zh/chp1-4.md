---
layout: post
title: 源码开放学ARM - 开发环境搭建 - 基本开发流程 
---

## 基本开发流程

### Windows 平台开发
#### ADS 汇编语言格式要点
**TAB 开头**  
    ARM 汇编语言格式要求除了 label 标号之外，其他包含伪操作和指令的行都应该以一个 TAB 开头。

**AREA 名称**  
    AREA 表示定义代码段/数据段区域的开始，后面跟的名称会进入符号表参与链接。

**ENTRY 入口**  
    表示指定汇编程序的入口，最多只能写一个，也可以不写。

**END 结束**  
    表示汇编程序的结束，必须要写。

**; 注释**  
    分号表示注释，用 // 或者 /*  */ 都不是注释。

**label 定义符号**  
    用于程序跳转时的标号，必须顶格写。

**import symbol**  
    用于链接外部的符号名，例如跳转到C语言的主函数。

**export symbol**  
    用于对外输出汇编程序内部的符号名，汇编文件内部的symbol默认对外是不公开的，隐藏的，类似C程序的static修饰。

#### C 程序注意要点
**main函数的负面影响**  
    链接器会引入很多外部代码，带来跟踪调试的很多问题，同时造成可执行bin文件的体积明显增大。

**"__main的用法"**  
    默认main函数真正的入口是 "__main"，因此直接用 "__main" 来定义C语言的主函数是比较便利的做法。

**替换return 0**  
    如果主程序没有无限循环，那么最后执行 return 0 的结果是不可预期的，应该用 while(1); 无限循环来替代。

**volatile 修饰符**  
    编译器对 volatile 修饰的变量或者指针，都会避免优化，强制访存。这对于嵌入式寄存器的读写操作是非常必要的。

**unsigned 修饰符**  
    对于特殊功能寄存器的访问，通常不需要进行算术运算，将它们声明为 unsigned 无符号整型是通常的做法。

#### 编写 Makefile
**all 目标**  
    第一个默认的目标，一般都起名为 all，make执行时不需要跟目标名，会自动执行默认目标。 
        
**clean 目标**  
    清除中间产生的不必要的文件，例如 .o .bak 等。

**宏变量**  
    引入 PRJ, SRCS, OBJS ，增强项目的可移植性。

**匹配替换技巧**  
    $(SRC:.c=.o) 的用法，灵活替换源文件到目标文件。

**依赖关系的传递**  
    用依赖关系，对于文件数量较多，编译时间较长的项目非常有好处，可以做到增量编译。

**隐含规则**  
    %o:%c 的用法，如果需要修改编译参数，则需要重新实现该条隐含规则。

#### make 编译项目

	make clean	清除原有临时文件
	make      	重新生成所有文件

#### 测试可执行文件	

	# loadb 输入命令
		超级终端菜单 -> 传送 -> 发送文件 -> 选择文件 + Kermit协议 -> 点击发送
		
	# go 0x21000000
		之后观察 LED1 灯亮的现象

### Linux 平台开发
#### led.s 汇编程序

	.global _start
	_start:
		ldr r0, =0x1
		ldr r1, =0xe0200280
		str r0, [r1]    

	loop:
		ldr r0, =0x0
		ldr r1, =0xe0200284
		str r0, [r1]    
		bl delay

		ldr r0, =0x1
		ldr r1, =0xe0200284
		str r0, [r1]    
		bl delay

		b loop

	delay:
		ldr r0, =0x100000
	go_on:  
		sub r0, r0, #1
		cmp r0, #0
		bne go_on
		mov pc, lr      
		
#### Makefile
			
	all:
		arm-linux-as led.s -o led.o
		arm-linux-ld led.o -o led.elf
		arm-linux-objcopy -O binary led.elf led.bin
		arm-linux-objdump -d led.elf > led.lst
		./mktiny210spl.exe led.bin sd-led.bin
				
#### make

	make

	-> ls -l *.bin
	   -rwxr-xr-x 1 limingth limingth   76 2012-05-11 18:21 led.bin
	   -rw-r--r-- 1 limingth limingth 8192 2012-05-11 18:21 sd-led.bin	

#### 烧写 sd-led.bin 到 SD-Card
	   
	insert sd card 
	dmesg | grep sdb
		-> /dev/sdb should be found
	sudo dd iflag=dsync oflag=dsync if=sd-led.bin of=/dev/sdb seek=1
		-> 记录了16+0 的读入
		   记录了16+0 的写出
		   8192字节(8.2 kB)已复制，0.209805 秒，39.0 kB/秒
	   
#### 补充：如何格式化 SD-Card

	1. fdisk SD card to a Win95-FAT32 partition

	if you just buy a new SD card, here we do it from the very beginning
	sudo fdisk /dev/sdb
	Command (m for help): m (help)
	Command (m for help): p (print the partition table)
	Command (m for help): d (delete the old partition)
	Command (m for help): n (add a new partition)
	Command action
	   e   extended
	   p   primary partition (1-4)
	p (for primary) -> 
	Partition number (1-4): 1
	First cylinder (1-121, default 1): 1
	Last cylinder, +cylinders or +size{K,M,G} (1-121, default 121):  (Enter)
	Using default value 121

	Command (m for help): t
	Selected partition 1
	Hex code (type L to list codes): L

	Hex code (type L to list codes): b 
	Changed system type of partition 1 to b (W95 FAT32)

	Command (m for help): p

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdb1               1         121      971901    b  W95 FAT32

	Command (m for help): w
	The partition table has been altered!

	Syncing disks.
		
	2. format FAT32 
	(This is VERY important for later use! However, it is not a must for led blink)
	sudo mkfs.vfat /dev/sdb1
	now you can test if SD-boot can blink led1

	3. insert SD card to slot (CON10 of tiny210)
	>> switch to SDBOOT of S2
	reset and see if it works!
	 
	 
[上一节](chp1-3.html)  |  [目录索引](../index.html)  |  [下一节](chp2-1.html)
