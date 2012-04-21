---
layout: post
title: GPIO 代码实现
---

## GPIO 代码实现

### 汇编程序
	TAB
	AREA led
	CODE, DATA
	READONLY
	label
	instruction...
	END

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

	

<br> <br> 
<div> <a href="chp3-3.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp4-1.html">下一节</a> </div> <br> <br>