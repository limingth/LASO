---
layout: post
title: 源码开放学ARM - GPIO 控制器 - GPIO 控制器内部结构 
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
	



[上一节](chp2-4.html)  |  [目录索引](../index.html)  |  [下一节](chp3-2.html)
