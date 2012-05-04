---
layout: post
title: 源码开放学ARM - UART 控制器 - 串口硬件连接
---

# UART 控制器 
## 串口的硬件连接 (硬件原理图)
	COM0 接口 DB9 九针公头
	pin2: RSRXD0
	pin3: RSTXD0
	pin5: GND
	通过 TxD 发送字符（以字节为单位 5-8bits）
	通过 RxD 接收字符（以字节为单位 5-8bits）

	RS232 电平：-15v->+15v (+15v-逻辑0, -15v-逻辑1)
	TTL 电平： 0->+5v (0-逻辑0, 5v-逻辑1)
	逻辑电平的转换： MAX3232 (美信芯片)

	查看 MAX3232 芯片+核心板原理图可得
		RSTXD0 <- XuTxD0 -- TINY1B B7 -- XuTXD0/GPA0_1
		RSRXD0 -> XuRxD0 -- TINY1B B8 -- XuRXD0/GPA0_0

	结论： GPA0 管理了 UART 的 Txd/Rxd 两个引脚
	

<br> <br> 
<div> <a href="chp4-4.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp5-2.html">下一节</a> </div> <br> <br>
