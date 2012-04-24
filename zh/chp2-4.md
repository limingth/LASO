---
layout: post
title: 时序图
---

## 时序图

### 基本概念
[时序图的基本概念](http://baike.baidu.com/view/916360.htm)

<img src="http://blog.chinaunix.net/attachment/201105/9/23860671_1304912021hzyD.jpg">

时序图是芯片与芯片之间进行数据通信所需要遵循的一种协议。通过时序图能够直观的看出，不同的芯片引脚在时间轴上的不同时刻所呈现出来的高低电平的变换，这些引脚中，有的代表地址信息，有的代表数据信息，有的代表控制信息，它们出现的先后顺序是有严格的时间参数规定的，这称为 Timing 。

芯片手册里的时序图一般都会列出其中关键的时间参数名称，在手册中都可以查到这些时间参数的具体要求。

	min	- 最小值
	max	- 最大值
	typical	- 典型值
	

[Timing Table] 

<img src="http://blog.chinaunix.net/attachment/201105/9/23860671_1304912130H6ww.jpg"> 

	
<br> <br> 
<div> <a href="chp2-3.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp3-1.html">下一节</a> </div> <br> <br>