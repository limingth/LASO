---
layout: post
title: 开发工具链 
---

##  开发工具链

### 工具位置
	C:\Program Files\ARM\ADSv1_2\Bin
	IDE.exe 	ADS IDE
	axd.exe		AXD debugger

### 命令行开发工具链 (GNU tools-chain)
	armcc.exe	C compiler	(armcpp.exe) /gcc
	armasm.exe	ASM Assembler /as
	armlink.exe	Linker	/ld (collect2)
	fromelf.exe	Bin-Utils (objdump/objcopy)

### C 编译器
	armcc
		-c 只编译，不连接
		-D (定义)条件编译 (-DDEBUG)
		-U (不定义)条件编译 (-DDEBUG)
		-g 增加调试信息
		-I 指定 include 路径(自己的)
		-On 编译优化级别
		-S 生成汇编
		-o 指定生成文件名
	arm-linux-gcc 交叉编译器的库 和 armcc 链接的库是否一样？

	armcc hello.c
		默认会生成  __image.axf (a.out)

	如果C程序，没有 main 函数，有警告，无error，能生成可执行文件

### C 链接器
	armcc -c hello.c
	armlink hello.o -o hello.axf
	fromelf -bin hello.axf -o hello2.bin

	armlink -entry test hello.o -o hello3.axf
	fromelf -c hello3.axf -o hello3.txt

	armlink -ro-base 0x0 -entry test hello.o -o hello4.axf
	fromelf -c hello4.axf -o hello4.txt

	armasm start.s -o start.o
	armlink start.o main.o -o demo.axf


### 二进制转换工具
	fromelf -bin hello.axf -o hello.bin
	fromelf -c hello.axf -o hello.txt
	

<br> <br> 
<div> <a href="chp0-1.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp1-2.html">下一节</a> </div> <br> <br>