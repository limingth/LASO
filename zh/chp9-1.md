---
layout: post
title: 源码开放学ARM - 中断处理 - 中断相关基本概念
---

# Interrupt 控制器
## 中断相关基本概念
### 异常和中断的概念区分
	异常指的都是内核里面(Cortex-A8)发生的事情
		例如：执行 swi 指令		
	中断指的都是板子上面(tiny210)发生的事情
		例如：用户按下 K1 按键，
				定时器Timer(不在板子上，但在芯片里)
	联系在于？都有模式的切换，中断处理需要包含异常处理
		异常模式包含(中断)异常模式

### 中断处理的相关概念
#### 中断源
	属于 Controller (Samsung) + Board (tiny210)

#### 中断控制器 Interrupt Controller
	属于 Controller (Samsung)

#### 中断模式的响应和恢复
	属于 Cortex-A Core (ARM)

	S3C4510 - ARM7TDMI
	S3C2440 - ARM920T
	S3C6410 - ARM1176
	S5PV210 - Cortex A8	
	

<br> <br> 
<div> <a href="chp8-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp9-2.html">下一节</a> </div> <br> <br>