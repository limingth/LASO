---
layout: post
title: 源码开放学ARM - 时钟管理 - 时钟驱动代码实现
---

## 时钟驱动代码实现

### Clock 时钟管理知识点总结
	
	时钟管理单元 CMU 
		MSYS Domain
			MSYS (Cortex A8, DRAM)					
		DSYS Domain
			DSYS (JPEG, IIC_HDMI)
		PSYS Domain
			PSYS (JTAG, NandFlash, USB, IIS, AC97, IIC, PWM Timer, RTC)	
		
		几点结论：
			ARMCLK 进行分频可以得到 HCLK_MSYS, PCLK_MSYS
			不同 domain 域之间，输出频率的关系 PCLK = HCLK / 2
		
	时钟发生器 Clock Generator
		锁相环 PLL (Phase-Locked Loop)
			APLL, MPLL, EPLL, VPLL
			倍频公式 Fout = Fin * M/(P * 2^S)
			Fout_mpll / Fin_mpll
			
		分频器 Divider
			1-2-4-8-16 分频 divider
			Div_out = Div_in / (DIVN + 1)
			DIV_PCLKP	DIV_HCLKP
			
		二路选通器 Mux				
			OM 时钟源选择
	
	输入时钟	
		X1: XXTI/XXTO 24Mhz
		X2: XusbXTI 24Mhz
		X3: XhdmiXTI 24Mhz
		X4: XrtcXTI 32.768khz
		24Mhz	12Mhz	常用输入频率
		
	输出时钟	
		ARMCLK 			(1Ghz)
		HCLKM / PCLKM	(200Mhz / 100Mhz)
		HCLKD / PCLKD	(166Mhz / 83Mhz)
		HCLKP / PCLKP	(133Mhz / 66Mhz)
		记住 100Mhz = 10ns
		
	Clock 时钟特殊功能寄存器
		PLL_CON - APLL_CON			
		CLK_SRC
		CLK_DIVn
	
	经验总结
		PLL Sel 决定用或者不用 PLL 锁相环的输出时钟
		串口的时钟输入，采用 MPLL 的输出，但是也可以用 APLL 的输出
		时钟输出的图表，可以从后往前分析
		24Mhz 时钟的选择 是因为 iROM 是采用 24Mhz 时钟 (P354)
	
		搜索芯片手册的时候，通过在图标的名字，DIV_HCKLP  DIVHCLKP , HCLKP
		时钟的输出都可以通过软件来控制，输出主频越高，耗电量越高
		
		
### 代码举例: 

	1) 设置 ARMCLK 分频因子 从 1 到 8，把 1Ghz 调整为 128Mhz

	2) 设置 DIV_PCLKP 分频因子 从 2 到 4，把 PCLK 从 66M 调整为 33M  
		修改超级终端把 波特率 改为 57600 来测试 PCLK 的设置是否成功
	
	

<br> <br> 
<div> <a href="chp4-2.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp5-1.html">下一节</a> </div> <br> <br>