---
layout: post
title: 开发工具链 
---

##  开发工具链

在ARM开发领域，有两大类开发工具可以选择，一类是基于 Windows 平台的 SDT, ADS, RealView MDK 系列，一类是基于 Linux 平台的 GNU Cross-Toolchain 。

考虑到大部分学习者对 Linux 平台比较陌生，我们先介绍 Windows 平台上的工具链。一旦我们对工具链背后的开发思路比较了解之后，再来学习 Linux 上的工具。

有关 ADS 工具和 MDK 工具的介绍，可以参考阅读百度百科的介绍。

[ADS] (http://baike.baidu.com/view/171249.htm#sub6295819)
[MDK] (http://baike.baidu.com/view/1745465.htm)

总体说来，MDK 是 ADS 的升级版本，界面上做了很大改动，但后台使用的命令行工具链基本一样。以下就是以 ADS 安装为例，对命令行工具链做一个简单说明。

### 安装说明
	工具下载： http://limingth.github.com/ARM-Tools
	安装目录： C:\Program Files\ARM\ADSv1_2\Bin
	图形开发环境：
		IDE.exe - 	ADS IDE
		axd.exe	-	AXD debugger

### 命令行开发工具链 /(GNU tools-chain)
	armcc.exe -	C compiler (armcpp.exe) /(gcc)
	armasm.exe -	ASM Assembler /(as)
	armlink.exe -	Linker	/(ld)
	fromelf.exe -	Bin-Utils /(objdump/objcopy)

### C 编译器 
	armcc 常用编译参数
		-c 只编译，不连接
		-D (定义)条件编译 (-DDEBUG)
		-U (不定义)条件编译 (-DDEBUG)
		-g 增加调试信息
		-I 指定 include 路径(自己的)
		-On 编译优化级别
		-S 生成汇编
		-o 指定生成文件名
		
	思考问题： arm-linux-gcc 交叉编译器的库 和 armcc 链接的库是否一样？

	用法举例：
		armcc hello.c
			默认会生成  __image.axf (a.out) ，其中 axf 文件是 ELF 格式的可执行文件
		
		armcc -c hello.c
			默认会生成  hello.o ，此时还需要 link 之后才能生成 axf 可执行文件

	特殊用法： 
		如果一个C程序，没有 main 函数，编译会怎么样？ （有警告warning，无错误error，能生成可执行文件axf）
	

### asm 汇编器
	armasm 通常只生成 .o 的目标文件
	
	用法举例：
		armasm start.s
			默认会生成  start.o，此时还需要 link 之后才能生成 axf 可执行文件
	

### obj 链接器
	armlink 常用链接参数	
		-ro-base 指定可执行代码的位置（代码段执行地址）
		-rw-base 数据段执行地址
		-first  指定 .o 文件放在链接的最开始处
		-entry  指定 axd 调试工具加载 axf 文件的入口地址（转成bin之后就丢失了）
		-scatter file 指定链接脚本（.scf文件/在linux下.lds文件）

	用法举例：
		armlink hello.o -o hello.axf
		armlink -first start.o -ro-base 0x0 -entry begin start.o main.o -o hello4.axf

### 二进制转换工具
	fromelf 常用转换参数
		-bin 生成bin文件，最终烧写到开发板上
		-c 生成txt文本文件，反汇编文件
		-d 打印数据段内容
		-s 打印符号表
		-t 打印字符串表

	用法举例：
		fromelf -bin hello.axf -o hello.bin
		fromelf -c -s -d hello.axf -o hello.txt
	

### 综合应用
	单独汇编程序的编译链接
		armasm start.s
		armlink start.o -o demo.axf

	单独C程序的编译链接
		armcc -c hello.c
		armlink hello.o -o hello.axf
	
	汇编和C程序的混合链接
		armasm start.s
		armcc -c main.c
		armlink -first start.o -ro-base 0x0 -entry begin start.o main.o -o demo.axf
	

### ARM Docs 开发文档
	DDI0100E_ARM_ARM.pdf  		- ARM体系结构知识，侧重于内核
	ADS_CompilerGuide_D.pdf  	- 编译器使用	
	ADS_AssemblerGuide_B.pdf 	- 汇编器使用
	ADS_LinkerGuide_A.pdf 		- 链接器使用	
	ADS_DebugTargetGuide_D.pdf 	- 调试器使用


<br> <br> 
<div> <a href="chp2-2.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp2-4.html">下一节</a> </div> <br> <br>