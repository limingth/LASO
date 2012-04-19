# 基本开发流程

## 开发工具是如何使用的？
	armcc main.c -> .axf
	armasm start.s -> .o
	armlink -ro-base 0x?? -entry xxx -> .axf
	fromelf -bin -> .bin
	fromelf -c -> .txt (反汇编)
	
