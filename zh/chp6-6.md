---
layout: post
title: 源码开放学ARM - SDRAM 控制器 - SDRAM 驱动代码实现
---

## SDRAM 驱动代码实现

	/*
	 *  armboot - Memory Initialize Code for S5PV210/ARM-Cortex CPU-core
	 *
	 *  Copyright (c) 2009  Samsung Electronics
	 *
	 *
	 * See file CREDITS for list of people who contributed to this
	 * project.
	 *
	 * This program is free software; you can redistribute it and/or
	 * modify it under the terms of the GNU General Public License as
	 * published by the Free Software Foundation; either version 2 of
	 * the License, or (at your option) any later version.
	 *
	 * This program is distributed in the hope that it will be useful,
	 * but WITHOUT ANY WARRANTY; without even the implied warranty of
	 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
	 * GNU General Public License for more details.
	 *
	 * You should have received a copy of the GNU General Public License
	 * along with this program; if not, write to the Free Software
	 * Foundation, Inc., 59 Temple Place, Suite 330, Boston,
	 * MA 02111-1307 USA
	 *
	 * Base codes by scsuh  (sc.suh)
	 * Modified By JhoonKim (jhoon_kim@nate.com), aESOP Embedded Forum(http://www.aesop.or.kr)
	 * 10.08.15 - To Supported for SEC K4T1G164QX DDR2 Memory for aESOP S5PV210
	 */

	/* #include <config.h>
	*/
	/*#include "s5pc110.h"
	#include "tiny210.h"
	*/


	/*
	 * SDRAM Controller
	 */
	#define APB_DMC_0_BASE			0xF0000000
	#define APB_DMC_1_BASE			0xF1400000
	#define ASYNC_MSYS_DMC0_BASE		0xF1E00000

	#define ELFIN_GPIO_BASE			0xE0200000


	#define DMC0_MEMCONTROL		0x00202400	// MemControl	BL=4, 1Chip, DDR2 Type, dynamic self refresh, force precharge, dynamic power down off
	#define DMC0_MEMCONFIG_0	0x20E00323	// MemConfig0	256MB config, 8 banks,Mapping Method[12:15]0:linear, 1:linterleaved, 2:Mixed
	#define DMC0_MEMCONFIG_1	0x00E00323	// MemConfig1

	#define	DMC1_MEMCONTROL		0x00202400	// MemControl	BL=4, 2 chip, DDR2 type, dynamic self refresh, force precharge, dynamic power down off
	#define DMC1_MEMCONFIG_0	0x40F00313	// MemConfig0	512MB config, 8 banks,Mapping Method[12:15]0:linear, 1:linterleaved, 2:Mixed
	#define DMC1_MEMCONFIG_1	0x00F00313	// MemConfig1

	#define DMC0_TIMINGA_REF        0x00000618      // TimingAref   7.8us*133MHz=1038(0x40E), 100MHz=780(0x30C), 20MHz=156(0x9C), 10MHz=78(0x4E)
	#define DMC0_TIMING_ROW         0x2B34438A      // TimingRow    for @200MHz
	#define DMC0_TIMING_DATA        0x24240000      // TimingData   CL=3
	#define DMC0_TIMING_PWR         0x0BDC0343      // TimingPower

	#define DMC1_TIMINGA_REF        0x00000618      // TimingAref   7.8us*133MHz=1038(0x40E), 100MHz=780(0x30C), 20MHz=156(0x9C), 10MHz=78(0x4E)
	#define DMC1_TIMING_ROW         0x2B34438A      // TimingRow    for @200MHz
	#define DMC1_TIMING_DATA        0x24240000      // TimingData   CL=3
	#define DMC1_TIMING_PWR         0x0BDC0343      // TimingPower

		.globl mem_ctrl_asm_init
	mem_ctrl_asm_init:

	#ifndef CONFIG_EVT1

		ldr     r0, =ASYNC_MSYS_DMC0_BASE

		ldr     r1, =0x0
		str     r1, [r0, #0x0]

		/* This register is removed at EVT1 of C110. */
		ldr     r1, =0x0
		str     r1, [r0, #0xC]

	#endif

	/* #ifdef CONFIG_MCP_SINGLE */
	#if 1

	#define MP1_0DRV_SR_OFFSET 		0x3CC
	#define MP1_1DRV_SR_OFFSET 		0x3EC
	#define MP1_2DRV_SR_OFFSET 		0x40C
	#define MP1_3DRV_SR_OFFSET 		0x42C
	#define MP1_4DRV_SR_OFFSET 		0x44C
	#define MP1_5DRV_SR_OFFSET 		0x46C
	#define MP1_6DRV_SR_OFFSET 		0x48C
	#define MP1_7DRV_SR_OFFSET 		0x4AC
	#define MP1_8DRV_SR_OFFSET 		0x4CC


	#define MP2_0DRV_SR_OFFSET 		0x4EC
	#define MP2_1DRV_SR_OFFSET 		0x50C
	#define MP2_2DRV_SR_OFFSET 		0x52C
	#define MP2_3DRV_SR_OFFSET 		0x54C
	#define MP2_4DRV_SR_OFFSET 		0x56C
	#define MP2_5DRV_SR_OFFSET 		0x58C
	#define MP2_6DRV_SR_OFFSET 		0x5AC
	#define MP2_7DRV_SR_OFFSET 		0x5CC
	#define MP2_8DRV_SR_OFFSET 		0x5EC


		/* DMC0 Drive Strength (Setting 2X) */
		ldr	r0, =ELFIN_GPIO_BASE

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #0x3cc]
		str	r1, [r0, #MP]
		str	r1, [r0, #MP1_0DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_1DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_2DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_3DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_4DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_5DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_6DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP1_7DRV_SR_OFFSET]

		ldr	r1, =0x00002AAA
		str	r1, [r0, #MP1_8DRV_SR_OFFSET]


		/* DMC1 Drive Strength (Setting 2X) */

		ldr	r0, =ELFIN_GPIO_BASE

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_0DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_1DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_2DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_3DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_4DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_5DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_6DRV_SR_OFFSET]

		ldr	r1, =0x0000AAAA
		str	r1, [r0, #MP2_7DRV_SR_OFFSET]

		ldr	r1, =0x00002AAA
		str	r1, [r0, #MP2_8DRV_SR_OFFSET]


	#define DMC_CONCONTROL 			0x00
	#define DMC_MEMCONTROL 			0x04
	#define DMC_MEMCONFIG0 			0x08
	#define DMC_MEMCONFIG1 			0x0C
	#define DMC_DIRECTCMD 			0x10
	#define DMC_PRECHCONFIG 		0x14
	#define DMC_PHYCONTROL0 		0x18
	#define DMC_PHYCONTROL1 		0x1C
	#define DMC_RESERVED 			0x20
	#define DMC_PWRDNCONFIG 		0x28
	#define DMC_TIMINGAREF 			0x30
	#define DMC_TIMINGROW 			0x34
	#define DMC_TIMINGDATA 			0x38
	#define DMC_TIMINGPOWER 		0x3C
	#define DMC_PHYSTATUS 			0x40
	#define DMC_CHIP0STATUS 		0x48
	#define DMC_CHIP1STATUS 		0x4C
	#define DMC_AREFSTATUS 			0x50
	#define DMC_MRSTATUS 			0x54
	#define DMC_PHYTEST0 			0x58
	#define DMC_PHYTEST1 			0x5C
	#define DMC_QOSCONTROL0 		0x60
	#define DMC_QOSCONFIG0 			0x64
	#define DMC_QOSCONTROL1 		0x68
	#define DMC_QOSCONFIG1 			0x6C
	#define DMC_QOSCONTROL2 		0x70
	#define DMC_QOSCONFIG2 			0x74
	#define DMC_QOSCONTROL3 		0x78
	#define DMC_QOSCONFIG3 			0x7C
	#define DMC_QOSCONTROL4 		0x80
	#define DMC_QOSCONFIG4 			0x84
	#define DMC_QOSCONTROL5 		0x88
	#define DMC_QOSCONFIG5 			0x8C
	#define DMC_QOSCONTROL6 		0x90
	#define DMC_QOSCONFIG6 			0x94
	#define DMC_QOSCONTROL7 		0x98
	#define DMC_QOSCONFIG7 			0x9C
	#define DMC_QOSCONTROL8 		0xA0
	#define DMC_QOSCONFIG8 			0xA4
	#define DMC_QOSCONTROL9 		0xA8
	#define DMC_QOSCONFIG9 			0xAC
	#define DMC_QOSCONTROL10 		0xB0
	#define DMC_QOSCONFIG10 		0xB4
	#define DMC_QOSCONTROL11 		0xB8
	#define DMC_QOSCONFIG11 		0xBC
	#define DMC_QOSCONTROL12 		0xC0
	#define DMC_QOSCONFIG12 		0xC4
	#define DMC_QOSCONTROL13 		0xC8
	#define DMC_QOSCONFIG13 		0xCC
	#define DMC_QOSCONTROL14 		0xD0
	#define DMC_QOSCONFIG14 		0xD4
	#define DMC_QOSCONTROL15 		0xD8
	#define DMC_QOSCONFIG15 		0xDC


	/*
	 * SDRAM Controller
	 */
	#define APB_DMC_0_BASE			0xF0000000
	#define APB_DMC_1_BASE			0xF1400000
	#define ASYNC_MSYS_DMC0_BASE		0xF1E00000

	#define DMC_CONCONTROL 			0x00
	#define DMC_MEMCONTROL 			0x04
	#define DMC_MEMCONFIG0 			0x08
	#define DMC_MEMCONFIG1 			0x0C
	#define DMC_DIRECTCMD 			0x10
	#define DMC_PRECHCONFIG 		0x14
	#define DMC_PHYCONTROL0 		0x18
	#define DMC_PHYCONTROL1 		0x1C
	#define DMC_RESERVED 			0x20
	#define DMC_PWRDNCONFIG 		0x28
	#define DMC_TIMINGAREF 			0x30
	#define DMC_TIMINGROW 			0x34
	#define DMC_TIMINGDATA 			0x38
	#define DMC_TIMINGPOWER 		0x3C
	#define DMC_PHYSTATUS 			0x40
	#define DMC_CHIP0STATUS 		0x48
	#define DMC_CHIP1STATUS 		0x4C
	#define DMC_AREFSTATUS 			0x50
	#define DMC_MRSTATUS 			0x54
	#define DMC_PHYTEST0 			0x58
	#define DMC_PHYTEST1 			0x5C
	#define DMC_QOSCONTROL0 		0x60
	#define DMC_QOSCONFIG0 			0x64
	#define DMC_QOSCONTROL1 		0x68
	#define DMC_QOSCONFIG1 			0x6C
	#define DMC_QOSCONTROL2 		0x70
	#define DMC_QOSCONFIG2 			0x74
	#define DMC_QOSCONTROL3 		0x78
	#define DMC_QOSCONFIG3 			0x7C
	#define DMC_QOSCONTROL4 		0x80
	#define DMC_QOSCONFIG4 			0x84
	#define DMC_QOSCONTROL5 		0x88
	#define DMC_QOSCONFIG5 			0x8C
	#define DMC_QOSCONTROL6 		0x90
	#define DMC_QOSCONFIG6 			0x94
	#define DMC_QOSCONTROL7 		0x98
	#define DMC_QOSCONFIG7 			0x9C
	#define DMC_QOSCONTROL8 		0xA0
	#define DMC_QOSCONFIG8 			0xA4
	#define DMC_QOSCONTROL9 		0xA8
	#define DMC_QOSCONFIG9 			0xAC
	#define DMC_QOSCONTROL10 		0xB0
	#define DMC_QOSCONFIG10 		0xB4
	#define DMC_QOSCONTROL11 		0xB8
	#define DMC_QOSCONFIG11 		0xBC
	#define DMC_QOSCONTROL12 		0xC0
	#define DMC_QOSCONFIG12 		0xC4
	#define DMC_QOSCONTROL13 		0xC8
	#define DMC_QOSCONFIG13 		0xCC
	#define DMC_QOSCONTROL14 		0xD0
	#define DMC_QOSCONFIG14 		0xD4
	#define DMC_QOSCONTROL15 		0xD8
	#define DMC_QOSCONFIG15 		0xDC

		/* DMC0 initialization at single Type*/
		ldr	r0, =APB_DMC_0_BASE

		ldr	r1, =0x00101000				@PhyControl0 DLL parameter setting, manual 0x00101000
		str	r1, [r0, #DMC_PHYCONTROL0]

		ldr	r1, =0x00000086				@PhyControl1 DLL parameter setting, LPDDR/LPDDR2 Case
		str	r1, [r0, #DMC_PHYCONTROL1]

		ldr	r1, =0x00101002				@PhyControl0 DLL on
		str	r1, [r0, #DMC_PHYCONTROL0]

		ldr	r1, =0x00101003				@PhyControl0 DLL start
		str	r1, [r0, #DMC_PHYCONTROL0]

	find_lock_val:
		ldr	r1, [r0, #DMC_PHYSTATUS]		@Load Phystatus register value
		and	r2, r1, #0x7
		cmp	r2, #0x7				@Loop until DLL is locked
		bne	find_lock_val

		and	r1, #0x3fc0
		mov	r2, r1, LSL #18
		orr	r2, r2, #0x100000
		orr	r2 ,r2, #0x1000

		orr	r1, r2, #0x3				@Force Value locking
		str	r1, [r0, #DMC_PHYCONTROL0]

	#if 0	/* Memory margin test 10.01.05 */
		orr	r1, r2, #0x1				@DLL off
		str	r1, [r0, #DMC_PHYCONTROL0]
	#endif
		/* setting DDR2 */
		ldr	r1, =0x0FFF2010				@ConControl auto refresh off
		str	r1, [r0, #DMC_CONCONTROL]

		ldr	r1, =DMC0_MEMCONTROL			@MemControl BL=4, 1 chip, DDR2 type, dynamic self refresh, force precharge, dynamic power down off
		str	r1, [r0, #DMC_MEMCONTROL]

		ldr	r1, =DMC0_MEMCONFIG_0			@MemConfig0 256MB config, 8 banks,Mapping Method[12:15]0:linear, 1:linterleaved, 2:Mixed
		str	r1, [r0, #DMC_MEMCONFIG0]

		ldr	r1, =DMC0_MEMCONFIG_1			@MemConfig1
		str	r1, [r0, #DMC_MEMCONFIG1]

		ldr	r1, =0xFF000000				@PrechConfig
		str	r1, [r0, #DMC_PRECHCONFIG]

		ldr	r1, =DMC0_TIMINGA_REF			@TimingAref	7.8us*133MHz=1038(0x40E), 100MHz=780(0x30C), 20MHz=156(0x9C), 10MHz=78(0x4E)
		str	r1, [r0, #DMC_TIMINGAREF]

		ldr	r1, =DMC0_TIMING_ROW			@TimingRow	for @200MHz
		str	r1, [r0, #DMC_TIMINGROW]

		ldr	r1, =DMC0_TIMING_DATA			@TimingData	CL=4
		str	r1, [r0, #DMC_TIMINGDATA]

		ldr	r1, =DMC0_TIMING_PWR			@TimingPower
		str	r1, [r0, #DMC_TIMINGPOWER]

		ldr	r1, =0x07000000				@DirectCmd	chip0 Deselect
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01000000				@DirectCmd	chip0 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00020000				@DirectCmd	chip0 EMRS2
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00030000				@DirectCmd	chip0 EMRS3
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010400				@DirectCmd	chip0 EMRS1 (MEM DLL on, DQS# disable)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00000542				@DirectCmd	chip0 MRS (MEM DLL reset) CL=4, BL=4
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01000000				@DirectCmd	chip0 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05000000				@DirectCmd	chip0 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05000000				@DirectCmd	chip0 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00000442				@DirectCmd	chip0 MRS (MEM DLL unreset)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010780				@DirectCmd	chip0 EMRS1 (OCD default)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010400				@DirectCmd	chip0 EMRS1 (OCD exit)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x07100000				@DirectCmd	chip1 Deselect
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01100000				@DirectCmd	chip1 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00120000				@DirectCmd	chip1 EMRS2
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00130000				@DirectCmd	chip1 EMRS3
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110400				@DirectCmd	chip1 EMRS1 (MEM DLL on, DQS# disable)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00100542				@DirectCmd	chip1 MRS (MEM DLL reset) CL=4, BL=4
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01100000				@DirectCmd	chip1 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05100000				@DirectCmd	chip1 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05100000				@DirectCmd	chip1 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00100442				@DirectCmd	chip1 MRS (MEM DLL unreset)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110780				@DirectCmd	chip1 EMRS1 (OCD default)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110400				@DirectCmd	chip1 EMRS1 (OCD exit)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x0FF02030				@ConControl	auto refresh on
		str	r1, [r0, #DMC_CONCONTROL]

		ldr	r1, =0xFFFF00FF				@PwrdnConfig
		str	r1, [r0, #DMC_PWRDNCONFIG]

		ldr	r1, =0x00202400				@MemControl	BL=4, 1 chip, DDR2 type, dynamic self refresh, force precharge, dynamic power down off
		str	r1, [r0, #DMC_MEMCONTROL]

		/* DMC1 initialization */
		ldr	r0, =APB_DMC_1_BASE

		ldr	r1, =0x00101000				@Phycontrol0 DLL parameter setting
		str	r1, [r0, #DMC_PHYCONTROL0]

		ldr	r1, =0x00000086				@Phycontrol1 DLL parameter setting
		str	r1, [r0, #DMC_PHYCONTROL1]

		ldr	r1, =0x00101002				@PhyControl0 DLL on
		str	r1, [r0, #DMC_PHYCONTROL0]

		ldr	r1, =0x00101003				@PhyControl0 DLL start
		str	r1, [r0, #DMC_PHYCONTROL0]
	find_lock_val1:
		ldr	r1, [r0, #DMC_PHYSTATUS]		@Load Phystatus register value
		and	r2, r1, #0x7
		cmp	r2, #0x7				@Loop until DLL is locked
		bne	find_lock_val1

		and	r1, #0x3fc0
		mov	r2, r1, LSL #18
		orr	r2, r2, #0x100000
		orr	r2, r2, #0x1000

		orr	r1, r2, #0x3				@Force Value locking
		str	r1, [r0, #DMC_PHYCONTROL0]

	#if 0	/* Memory margin test 10.01.05 */
		orr	r1, r2, #0x1				@DLL off
		str	r1, [r0, #DMC_PHYCONTROL0]
	#endif

		/* settinf fot DDR2 */
		ldr	r0, =APB_DMC_1_BASE

		ldr	r1, =0x0FFF2010				@auto refresh off
		str	r1, [r0, #DMC_CONCONTROL]

		ldr	r1, =DMC1_MEMCONTROL			@MemControl	BL=4, 2 chip, DDR2 type, dynamic self refresh, force precharge, dynamic power down off
		str	r1, [r0, #DMC_MEMCONTROL]

		ldr	r1, =DMC1_MEMCONFIG_0			@MemConfig0	512MB config, 8 banks,Mapping Method[12:15]0:linear, 1:linterleaved, 2:Mixed
		str	r1, [r0, #DMC_MEMCONFIG0]

		ldr	r1, =DMC1_MEMCONFIG_1			@MemConfig1
		str	r1, [r0, #DMC_MEMCONFIG1]

		ldr	r1, =0xFF000000
		str	r1, [r0, #DMC_PRECHCONFIG]

		ldr	r1, =DMC1_TIMINGA_REF			@TimingAref	7.8us*133MHz=1038(0x40E), 100MHz=780(0x30C), 20MHz=156(0x9C), 10MHz=78(0x4
		str	r1, [r0, #DMC_TIMINGAREF]

		ldr	r1, =DMC1_TIMING_ROW			@TimingRow	for @200MHz
		str	r1, [r0, #DMC_TIMINGROW]

		ldr	r1, =DMC1_TIMING_DATA			@TimingData	CL=3
		str	r1, [r0, #DMC_TIMINGDATA]

		ldr	r1, =DMC1_TIMING_PWR			@TimingPower
		str	r1, [r0, #DMC_TIMINGPOWER]


		ldr	r1, =0x07000000				@DirectCmd	chip0 Deselect
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01000000				@DirectCmd	chip0 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00020000				@DirectCmd	chip0 EMRS2
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00030000				@DirectCmd	chip0 EMRS3
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010400				@DirectCmd	chip0 EMRS1 (MEM DLL on, DQS# disable)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00000542				@DirectCmd	chip0 MRS (MEM DLL reset) CL=4, BL=4
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01000000				@DirectCmd	chip0 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05000000				@DirectCmd	chip0 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05000000				@DirectCmd	chip0 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00000442				@DirectCmd	chip0 MRS (MEM DLL unreset)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010780				@DirectCmd	chip0 EMRS1 (OCD default)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00010400				@DirectCmd	chip0 EMRS1 (OCD exit)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x07100000				@DirectCmd	chip1 Deselect
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01100000				@DirectCmd	chip1 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00120000				@DirectCmd	chip1 EMRS2
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00130000				@DirectCmd	chip1 EMRS3
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110440				@DirectCmd	chip1 EMRS1 (MEM DLL on, DQS# disable)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00100542				@DirectCmd	chip1 MRS (MEM DLL reset) CL=4, BL=4
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x01100000				@DirectCmd	chip1 PALL
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05100000				@DirectCmd	chip1 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x05100000				@DirectCmd	chip1 REFA
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00100442				@DirectCmd	chip1 MRS (MEM DLL unreset)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110780				@DirectCmd	chip1 EMRS1 (OCD default)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x00110400				@DirectCmd	chip1 EMRS1 (OCD exit)
		str	r1, [r0, #DMC_DIRECTCMD]

		ldr	r1, =0x0FF02030				@ConControl	auto refresh on
		str	r1, [r0, #DMC_CONCONTROL]

		ldr	r1, =0xFFFF00FF				@PwrdnConfig
		str	r1, [r0, #DMC_PWRDNCONFIG]

		ldr	r1, =DMC1_MEMCONTROL			@MemControl	BL=4, 2 chip, DDR2 type, dynamic self refresh, force precharge, dynamic power down off
		str	r1, [r0, #DMC_MEMCONTROL]

	#else	/* CONFIG_MCP_SINGLE */


	#endif	/* CONFIG_MCP_AC / CONFIG_MCP_H / CONFIG_MCP_B / CONFIG_MCP_D */

		mov	pc, lr

	/*
	 *     v7_flush_dcache_all()
	 *
	 *     Flush the whole D-cache.
	 *
	 *     Corrupted registers: r0-r5, r7, r9-r11
	 *
	 *     - mm    - mm_struct describing address space
	 */
	 


### 课堂修改作业

	1) 修改 uart_init, 把波特率改为 19200
	2) 修改 时钟发生器，把 PCLK 输出改为 33M, 波特率重新计算 115200
	3) 实现 puts("hello, world");  输出 "hello, world" 
		实现 实现 putchar_hex('a') 十六进制, 输出 0x61
	目的：了解 \n换行  \r回车  之间的差别
		
	4) 修改 DRAM Controller，
		把 col address 的宽度改为 9bit
		把 data bit 的32bit 改为 8bit
		用上述 putchar_hex 输出接口，观察读取DDR内存单元的数值变化




[上一节](chp6-5.html)  |  [目录索引](../index.html)  |  [下一节](chp7-1.html)
