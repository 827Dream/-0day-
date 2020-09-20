# 一：mips函数调用堆栈学习实例
## 1：源码
~~~
#include<stdio.h>
int more_arguments(int a,int b,int c,int d,int e,int f)
{
        char dst[100]={0};
        sprintf(dst,"%d%d%d%d%d%d\n",a,b,c,d,e,f);
        return 1;
}

int main()
{
        int a1 = 1;
        int a2 = 2;
        int a3 = 3;
        int a4 = 4;
        int a5 = 5;
        int a6 = 6;
        more_arguments(a1,a2,a3,a4,a5,a6);
        return 1;
}
~~~

## 2：堆栈
~~~
//more_arguments调用时
.....                                                 more_arguments函数
00000000407FFF90  407FFF98  MEMORY:00000000407FFF98   fp（main函数栈顶指针）
00000000407FFF94  004004B4  main+68		      ra（返回地址）	
//more_arguments调用前		
00000000407FFF98  00000000  MEMORY:0000000000000000   main函数	
00000000407FFF9C  00000000  MEMORY:0000000000000000
00000000407FFFA0  00000000  MEMORY:0000000000000000
00000000407FFFA4  00000000  MEMORY:0000000000000000
00000000407FFFA8  00000005  MEMORY:0000000000000005 
00000000407FFFAC  00000006  MEMORY:0000000000000006	   
~~~

## 3：问题
### 参数如何传递？
小于等于四个参数的使用a0-a3寄存器传参，超过四个参数的使用堆栈传参
### 如何返回？
#### 对于叶子函数，直接使用jr $ra（跳转到寄存器所指向的地址）即可。
#### 对于非叶子函数，more_arguments会将返回到main函数的地址保存图示堆栈位置，返回时候取出存入$ra寄存器随后跳转。
### 如何回溯到上个函数的栈帧？
同x86相同，也是通过一个类似ebp的寄存器，寄存器$fp，在被调用的子函数more_arguments中除了保存返回地址到堆栈同时会保存main函数的堆栈栈顶指针到自身的堆栈中。

## 4：栈溢出可行性
由2可知，对于非叶子函数而言，子函数会将返回地址存放到自己的堆栈中，最后将其取出赋值给ra，最后实现返回，从这个方向来说，只要我们能够修改其在堆栈中的返回地址即可修改程序的执行流程。

  
































