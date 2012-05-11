---
layout: post
title: 源码开放学ARM - GPIO 控制器 - GPIO 输出引脚
---

## GPIO 输出引脚
General Purpose Input/Output (p92-p352)	

	1) 237 multi-functional input/output port pins  
		34 general port groups  
	2) GPIO <---> peripheral controller (signal mux)  
	3) GPIO Block Diagram  
		APB bus (addr + data) *(int *)0xE0000000 = 0x1234;  
		Register File (GPIO SFRs)  
		Mux Control (functional)  
		Interrupt Control (Interrupt cont.)  
	4) Pin Mux Description  
		pin name -> GPIO name  
		default function  




[上一节](chp3-1.html)  |  [目录索引](../index.html)  |  [下一节](chp3-3.html)
