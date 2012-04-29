---
layout: post
title: 源码开放学ARM - 时钟管理 - 时钟发生器 
---

# CLOCK 时钟管理
## 时钟发生器 

### 时钟源的周期换算关系
		24Mhz 输入时钟
		10^(-3)	毫秒	10^(-6)	微秒	10^(-9)	纳秒
		1Khz 10^3		1Mhz 10^6		1Ghz 10^9
		1Ghz -> 1纳秒
		100Mhz -> 10纳秒

### Clock Controller 时钟控制器 (p353-p417)
	CMU: Clock Management Unit
		总线频率
		MSYS: Main  (200Mhz-100Mhz)
			Cortex A8 Core
			DRAM controller
		DSYS: Display (166Mhz-83Mhz)
			JPEG
		PSYS: Peripheral (133Mhz-66Mhz)
			UART
			AC97
			PWM Timer
			GPIO

	Clock Generator 时钟发生器
		S5PV210 Top-Level Clocks
			XXTI - 24Mhz --> (4 PLLS's input)
			XrtcXTI - 32.768Khz
		4 PLLs (APLL, MPLL, VPLL, EPLL)
			Phase Locked Loop 锁相环 (倍频)
	

<br> <br> 
<div> <a href="chp3-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp4-2.html">下一节</a> </div> <br> <br>