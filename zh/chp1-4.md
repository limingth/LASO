---
layout: post
title: 基本开发流程 
---

## 基本开发流程

### 编写源码
#### ARM 汇编语言格式要点
**TAB 开头**
ARM 汇编语言格式要求除了 label 标号之外，其他包含伪操作和指令的行都应该以一个 TAB 开头。

**AREA 名称**  
AREA 表示代码段区域的开始，后面跟的名称会进入符号表参与链接。

** ENTRY 入口**

** END 结束 **


** ; 注释 **

** label 定义符号**
跳转

** import symbol **

** export symbol **
(汇编文件内部的symbol对外不公开的，隐藏的，类似static)

#### C 程序注意要点
	main 函数带来的负面影响
	用 __main 或者 mymain 来替代 main
	用 while(1); 来替代 return 0
	voliatile 修饰符的作用

### 编写 Makefile
	all 目标
	clean 目标
	宏变量的引入 PRJ, SRCS, OBJS
	匹配替换技巧 $(SRC:.c=.o) 的用法
	依赖关系的传递
	隐含规则 %o:%c 的用法 

### make 编译项目
	make clean
	make

### 测试可执行文件
	# loadb 输入命令
		超级终端菜单 -> 传送 -> 发送文件 -> 选择文件 + Kermit协议 -> 点击发送
	# go 0x21000000
		之后观察 LED1 灯亮的现象
	

<br> <br> 
<div> <a href="chp1-3.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp2-1.html">下一节</a> </div> <br> <br>