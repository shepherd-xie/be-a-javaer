# L1. 弱弱的问：什么是操作系统？

什么是计算机？

计算机是我们吃饭的家伙。

计算机用于解决实际问题。例如：向屏幕输出 `hello world`

什么是操作系统？

是计算机硬件和应用之间的一层软件。

方便我们使用软件，高校的使用硬件。

管理那些硬件?

**CPU** **内存** **中断** **磁盘** **文件** _网络_ _电源_ _多核_

# L2. 揭开钢琴的盖子

从白纸到图灵机

计算机怎么工作？说到底是一个计算模型

图灵机：纸带 - 控制器

从图灵机到通用图灵机

通用图灵机：纸带（设置控制器动作，控制器状态，数据对象） - 控制器（修改控制器）

从通用图灵机到计算器

冯诺依曼存储程序思想：将程序和数据存放到计算机内存的存储器中，计算机在程序的控制下一步一步进行处理，取址执行

打开电源，计算机第一条执行的指令是什么？

由硬件设计者决定，x86 PC

1. x86 PC 刚开机时CPU处于实模式
2. 开机时，CS=0xFFFF;IP=0x0000
3. 寻址0xFFFF0（ROM BIOS映射区）
4. 检查RAM，键盘，显示器，软硬磁盘
5. 将磁盘0磁道0扇区读入0x7c00处
6. 设置cs=0x07c0，ip=0x0000

0x7c00处存放的代码

就是从



```assembly
.globl begtext,begdata,begbss,endtext,enddata,endbss
.text // 文本段
begtext:
.data // 数据段
begdata:
.bss  // 未初始化数据段
begbss:
entry start
start:
  mov ax, #BOOTSEG    mov ds, ax
  mov ax, #INITSEG    mov es, ax
  mov cx, #256
  sub si, si          sub di, di
  rep   movw
  jmpi  go, INITSEG
```

