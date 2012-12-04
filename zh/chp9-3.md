---
layout: post
title: 源码开放学ARM - 中断处理 - 中断寄存器配置
---

## 中断寄存器配置
### 中断相关寄存器的设计演变

			IC			IC			Vectored IC ->
			
			ARM7(4510)		ARM9(2440)		ARM11(6410) & A8(210)		
	------------------------------------------------------------------------------------------
			CPSR I-bit		CPSR I-bit		CPSR I-bit
	内核    								VIC Port(Enable)
	(Core)  								VIC interface(PC<->A0-A31)
	------------------------------------------------------------------------------------------
						INTOFFSET      		VectADDRESS(32bit->A0-A31)
	        							Vectors(handlers)
	        				INTPRI			Priority
	        	INTMOD			INTPND			(IRQ/FIQ)STATUS(PND) 
	中断    	INTPND			INTMOD			SELECT(MOD) IRQ/FIQ
	控制器  	INTMSK			INTMSK			ENABLE(MSK)
	(IC)    				SRCPND			RAWINTR(SRC)									
	------------------------------------------------------------------------------------------
									INTMSK
									INTPND(clear)
			EINTCON			EINTCON			EINTCON
	中断源		(F/R/L)			(F/R/L)			(F/R/L)		
	控制器		GPXCON			GPXCON			GPXCON
	(GPIO)		(EINT)			(EINT)			(EINT)		
	------------------------------------------------------------------------------------------
	硬件层 		Key/UART/USB/Timer 

### S5PV210 中断相关寄存器
	中断源 GPIO Controller
		GPH2CON[0]   [3:0]   Set the pin mux function as EXT_INT
			0000 = Input     
			0001 = Output 
			0010 = Reserved 
			0011 = KP_COL[0] 
			0011 ~ 1110 = Reserved 
		*	1111 = EXT_INT[16] 
		
		EXT_INT_2_CON[0]  [2:0]  Sets the signaling method of EXT_INT[16] 
			000 = Low level     
			001 = High level 
		*	010 = Falling edge triggered 
			011 = Rising edge triggered 
			100 = Both edge triggered 
			101 ~ 111 = Reserved 
				
		EXT_INT_2_MASK[0]  [0]  
		*	0 = Enables Interrupt     
			1 = Masked 
	
		EXT_INT_2_PEND[0]  [0]  (R)
			0 = Not occur     
			1 = Occur interrupt 

	向量中断控制器 Vectored Interrupt Controller
		VIC0RAWINTR  0xF200_0008 R  
			Specifies the Raw Interrupt Status Register 
			RawInterrupt  [31:0]  Shows the status of the FIQ interrupts before masking by the 
				VICINTENABLE and VICINTSELECT Registers: 
				0 = Interrupt is inactive before masking 
				1 = Interrupt is active before masking
				
		VIC0INTENABLE  0xF200_0010 R/W 
			Specifies the Interrupt Enable Register
			IntEnable  [31:0]  Enables the interrupt request lines
				Write: 
				0 = No effect 
		*		1 = Enables Interrupt. 
		
		VICINTENCLEAR 
			IntEnable Clear 	
				Write: 	
				0 = No effect 
		*		1 = Disables Interrupt in VICINTENABLE Register. 				
		
		VIC0IRQSTATUS  0xF200_0000 R  
			Specifies the IRQ Status Register 
			IRQStatus  [31:0]  Shows the status of the interrupts after masking by the 
				VICINTENABLE and VICINTSELECT Registers: 
				0 = Interrupt is inactive 
				1 = Interrupt is active. 
		
		VIC0INTSELECT  0xF200_000C R/W 
			Specifies the Interrupt Select Register 
			IntSelect  [31:0]  Selects interrupt type for interrupt request: 
		*		0 = IRQ interrupt 
				1 = FIQ interrupt 

		VIC0VECTADDR0 0xF200_0100 R/W 
			Specifies the Vector Address 0 Register		
			VICVECTADDR[0-31]  Bit  Description 
				VectorAddr 0-31  [31:0]  Contains ISR vector addresses. 
			
		VIC0VECTPRIORITY0  0xF200_0204 R/W 
			Specifies the Vector Priority 1 Register 
			VectPriority  [3:0]  Selects vectored interrupt priority level. You can select 
				any of the 16 vectored interrupt priority levels by 
				programming the register with the hexadecimal value of 
				the priority level required, from 0-15. 

		VIC0ADDRESS  0xF200_0F00 R/W 
			Specifies the Vector Address Register 
			VectAddr  [31:0]  
			Contains the address of the currently active ISR

	内核 
		CPSR I-bit
			__asm
			{
				mov r0, #0x53
				msr CPSR_cxsf, r0
			}
				
		VIC Enable (p15)		
			__asm
			{
				mrc p15, 0, r0, c1, c0, 0
				orr r0, r0, #(1<<24)
				mcr p15, 0, r0, c1, c0, 0	
			}	





[上一节](chp9-2.html)  |  [目录索引](../index.html)  |  [下一节](chp9-4.html)
