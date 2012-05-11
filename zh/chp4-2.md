---
layout: post
title: 源码开放学ARM - 时钟管理 - 时钟输出频率
---

##  时钟输出频率

### 时钟输出
	MSYS clock domain (H->HighPerformance P->Peripheral)
		AHB / APB
		freq(ARMCLK)  = 1000Mhz		1000M/1
		freq(HCLK_MSYS) = 200Mhz	ARMCLK/5
		freq(PCLK_MSYS) = 100Mhz	HCLK_M/2
		freq(HCLK_IMEM) = 100Mhz	HCLK_M/2

	DSYS clock domain
		freq(HCLK_DSYS) = 166Mhz
		freq(PCLK_DSYS) = 83Mhz		HCLK_D/2

	PSYS clock domain
		freq(HCLK_PSYS) = 133Mhz
		freq(PCLK_PSYS) = 66Mhz		HCKL_P/2
		freq(SCLK_ONENAND) = 133M/166Mhz

	PLL PMS setting
		



[上一节](chp4-1.html)  |  [目录索引](../index.html)  |  [下一节](chp4-3.html)
