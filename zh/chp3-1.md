---
layout: post
title: GPIO 控制器 - GPIO 控制器内部结构 
---

# GPIO 控制器 #
## 控制器内部结构
	GPIO Controller
		how many pin
		pin number, pin name, pin mux functional
		device <-> GPIO (LED1 -> GPJ2_0) 原理图
	GPIO SFRs
		Set mux function
			控制寄存器 GPXCON
		Set pin value
			数据寄存器 GPXDAT
		Set interrupt function
			中断寄存器 ...
	

<br> <br> 
<div> <a href="chp2-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp3-2.html">下一节</a> </div> <br> <br>