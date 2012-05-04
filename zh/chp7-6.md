---
layout: post
title: 源码开放学ARM - NandFlash 控制器 - NandFlash 驱动代码实现
---

## NandFlash 驱动代码实现

### nand.c 参考代码实现			
	// nand.c
	#define	NFCONF  (*(volatile unsigned int *)0xB0E00000) 
	#define	NFCONT  (*(volatile unsigned int *)0xB0E00004) 	
	#define	NFCMMD  (*(volatile unsigned char *)0xB0E00008) 
	#define	NFADDR  (*(volatile unsigned char *)0xB0E0000C)
	#define	NFDATA  (*(volatile unsigned char *)0xB0E00010)
	#define	NFSTAT  (*(volatile unsigned int *)0xB0E00028)

	#define	MP0_3CON  (*(volatile unsigned int *)0xE0200320)
			
	#define PAGE_SIZE	2048

	void nand_init(void)
	{
		// [15:12] TACLS = 1 -> (1) 	1/133Mhz = 7.5ns
		// [11:8] TWRPH0 = 1 -> (1+1)	7.5ns * 2 = 15ns
		// [7:4] TWRPH1 = 1 -> (1+1)	7.5ns * 2 = 15ns
		NFCONF |= 1<<12 | 1<<8 | 1<<4;    

		// AddrCycle  [1] 	1 = 5 address cycle 
		NFCONF |= 1<<1; 
		
		// MODE  [0]  NAND Flash controller operating mode 
		//	0 = Disable NAND Flash Controller 
		//	*1 = Enable NAND Flash Controller
		NFCONT |= 1<<0; 
		
		// Reg_nCE0  [1]  NAND Flash Memory nRCS[0] signal control 
		//	*0 = Force nRCS[0] to low (Enable chip select) 
		//	1 = Force nRCS[0] to High (Disable chip select) 
		NFCONT &= ~(1<<1);
		
		// GPIO functional mux setting
		// 		0010 = NF_xxx 
		MP0_3CON = 0x22222222;
		
		return;
	}

	void nand_read_id(char id[])
	{
		int i;
		
		// write read_id cmd 90h
		NFCMMD = 0x90;
		
		// write address 00h
		NFADDR = 0x00;

		for (i = 0; i < 5; i++)
			id[i] = NFDATA;
		
		return;
	}

	void nand_read_page(int addr, char buf[])
	{
		int i;
		char tmp;
		
		// write read_page cmd 00h
		NFCMMD = 0x00;
		
		// write 5 address
		NFADDR = (addr>>0) & 0xFF;
		NFADDR = (addr>>8) & 0x7;
		NFADDR = (addr>>11) & 0xFF;
		NFADDR = (addr>>19) & 0xFF;
		NFADDR = (addr>>27) & 0x1;

		// write read_page cmd 30h
		NFCMMD = 0x30;
		
		// wait for R/nB -> Ready	
		while ((NFSTAT & (1<<0)) == 0)
			;
		
		// read data 2048 bytes
		for (i = 0; i < PAGE_SIZE; i++)
			buf[i] = NFDATA;
		
		for (i = 0; i < 64; i++)
			tmp = NFDATA;
			
		return;
	}

	void nand_read(int nand_addr, char * sdram_addr, int size)
	{
		int pages = (size - 1)/PAGE_SIZE + 1;
		int i;
		
		for (i = 0; i < pages; i++)
			nand_read_page(nand_addr + i*PAGE_SIZE, sdram_addr + i*PAGE_SIZE);
		
		return;
	}

### nand.h 参考代码实现
	// nand.h
	void nand_init(void);

	void nand_read_id(char id[]);

	void nand_read_page(int addr, char buf[]);

	void nand_read(int nand_addr, char * sdram_addr, int size);

	
### 思考问题：如何访问 Flash 0地址
	1. 软件上访问 应该是 计算出 0 地址所在的 page + block
		NFCMMD = 0x00;
		NFADDR = 0x0;	
		NFADDR = 0x0;
		NFADDR = 0x0;
		NFADDR = 0x0;
		NFADDR = 0x0;
		NFCMMD = 0x30;
		wait R/nB;
		read NFDATA 2048;
		
	2. 硬件上
		NFCMMD = 0x00;	-> CLE 使能/ALE 禁止, IO[7:0] = 0x00;
		NFADDR = 0x0;	-> ALE 使能/CLE 禁止, IO[7:0] = 0x00;
		NFADDR = 0x0;	-> ALE 使能/CLE 禁止, IO[7:0] = 0x00;
		NFADDR = 0x0;	-> ALE 使能/CLE 禁止, IO[7:0] = 0x00;
		NFADDR = 0x0;	-> ALE 使能/CLE 禁止, IO[7:0] = 0x00;
		NFCMMD = 0x00;	-> CLE 使能/ALE 禁止, IO[7:0] = 0x30;
		read NFSTAT;	-> R/nB 线会设置 STAT 寄存器 0 位
		read NFDATA;	-> nRE 使能, CLE/ALE 禁止, IO[7:0] => NFDATA 中

<br> <br> 
<div> <a href="chp7-5.html">上一节</a> &nbsp;&nbsp; | &nbsp;&nbsp; <a href="chp8-1.html">下一节</a> </div> <br> <br>