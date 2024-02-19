#
## RISC
### Y86-64 Processor State
- **program registers**
<img src="./image/2024-02-19 102324.png" alt="program registers" style="max-width: 40%;">
其中一个为空，这样就可以使用4个bits来表示寄存器
- **program counter**(也就是PC)
- **condition codes**(也就是CC:OF,ZF,SF)
- **status code**
程序正常执行或发生事件
- **memory**

### Y86-64 Instructions
一共有12条变长指令，通过其编号就可以判断其长度
<img src="./image/2024-02-19 104816.png" alt="Y86-64 Instructions" style="max-width: 50%;">
其中cmovXX是rrmovq的子集
- **Arithmetic and Logical Operations**
这会设置CC
<img src="./image/2024-02-19 105337.png" alt="Arithmetic and Logical Operations" style="max-width: 25%;">
- **Move Operations**
注意到这些指令都要通过寄存器作为媒介，所以会有一些x86没有的指令
<img src="./image/2024-02-19 105514.png" alt="Move Operations" style="max-width: 40%;">
- **Conditional Move Operations**
- **Jump Instructions**
<img src="./image/_6_U9AH7X($(U%2542G%25L__$O.png" alt="Jump Instructions" style="max-width: 25%;">
- **Stack Operations**
y86的栈基本类似于x86
一些trick:
pushq %rsp -> save old %rsp
popq %rsp -> movq (%rsp) %rsp
- **Subroutine Call and Return**
- **Miscellaneous Instructions**
nop不做任何事情
halt中止执行指令

### Y86-64 Programs
```assembly
1   # Execution begins at address 0
2 	.pos 0                          # assembler directives,告诉汇编器从地址0产生代码 
3 	irmovq	    stack, %rsp 		# Set up stack pointer,可以认为这里的stack类似于宏
4 	call     	    main 		# Execute main program
5 	halt 				# Terminate program
6
7   # Array of 4 elements
8 	.align 8                        # 同为伪指令，指出8字节对齐
9   array:                              # 声明一个数组
10 	.quad 0x000d000d000d
11 	.quad 0x00c000c000c0
12 	.quad 0x0b000b000b00
13 	.quad 0xa000a000a000
14
15   main:
16 	irmovq     array,%rdi
17 	irmovq     $4,%rsi
18 	call           sum 		# sum(array, 4)
19 	ret
20
21   # long sum(long *start, long count)
22   # start in %rdi, count in %rsi
23   sum:
24 	irmovq     $8,%r8 		# Constant 8
25 	irmovq     $1,%r9 		# Constant 1
26 	xorq         %rax,%rax 		# sum = 0,相当于置零
27 	andq 	    %rsi,%rsi 		# Set CC,判断count是否为零，同时不改变其值
28 	jmp 	    test 			# Goto test
29   loop:
30 	mrmovq (%rdi),%r10		# Get *start
31 	addq %r10,%rax               	# Add to sum
32 	addq %r8,%rdi                	# start++
33 	subq %r9,%rsi                	# count--. Set CC
34   test:
35 	jne loop 			# Stop when 0
36 	ret 				# Return
37
38   # Stack starts here and grows to lower addresses
39   	.pos 0x200
40   stack:                             # 指明栈从0x200开始
```
通过观察每条指令执行后的状态码来debug
- **Assembling Y86-64 Program**
   ```
   unix> yas eg.ys
   ```
   小端序
<img src="./image/[D58JKAYJ3EZM~RQYU@K8%25T.png" alt="Assembling Y86-64 Program" style="max-width: 50%;">
- **Simulating Y86-64 Program**
   ```
   unix> yis eg.yo
   ```


