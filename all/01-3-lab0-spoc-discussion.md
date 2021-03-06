# lab0 SPOC思考题

##**提前准备**
（请在上课前完成）

- 完成lec2的视频学习和提交对应的在线练习
- git pull ucore_os_lab, os_tutorial_lab, os_course_exercises  in github repos。这样可以在本机上完成课堂练习。
- 了解代码段，数据段，执行文件，执行文件格式，堆，栈，控制流，函数调用,函数参数传递，用户态（用户模式），内核态（内核模式）等基本概念。思考一下这些基本概念在linux, ucore, v9-cpu中是如何具体体现的。
- 安装好ucore实验环境，能够编译运行ucore labs中的源码。
- 会使用linux中的shell命令:objdump，nm，file, strace，gdb等，了解这些命令的用途。
- 会编译，运行，使用v9-cpu的dis,xc, xem命令（包括启动参数），阅读v9-cpu中的v9\-computer.md文档，了解汇编指令的类型和含义等，了解v9-cpu的细节。
- 了解基于v9-cpu的执行文件的格式和内容，以及它是如何加载到v9-cpu的内存中的。
- 在piazza上就学习中不理解问题进行提问。

---

## 视频相关讨论题

---

- 你理解的物理地址、线性地址、逻辑地址分别是什么？



- x86的实模式和保护模式有什么区别？



- 实模式的地址空间不能大于1MB的原因是什么？



- 队列数据结构有4个基本操作函数，如何基于这些函数来实现支持保存不同数据的多种队列？操作系统内核中的队列数据结构是如何实现的？



---

## 个人思考题

---

- 能否读懂ucore中的AT&T格式的X86-32汇编语言？

  - [Intel格式和AT&T格式汇编区别](http://www.cnblogs.com/hdk1993/p/4820353.html)

  - ##### [x86汇编指令集  ](http://hiyyp1234.blog.163.com/blog/static/67786373200981811422948/)

  - ##### [PC Assembly Language, Paul A. Carter, November 2003.](https://pdos.csail.mit.edu/6.828/2016/readings/pcasm-book.pdf)

  - ##### [*Intel 80386 Programmer's Reference Manual*, 1987](https://pdos.csail.mit.edu/6.828/2016/readings/i386/toc.htm)

  - ##### [[IA-32 Intel Architecture Software Developer's Manuals](http://www.intel.com/content/www/us/en/processors/architectures-software-developer-manuals.html)]

    ​

- 虽然学过计算机原理和x86汇编（根据THU-CS的课程设置），但对ucore中涉及的哪些x86硬件设计或功能细节不够了解？

  - [Phil Storrs PC Hardware book,Phil Storrs, December 1998.](http://web.archive.org/web/20040603021346/http://members.iweb.net.au/%7Epstorr/pcbook/)
  - [Bochs technical hardware specifications directory](http://bochs.sourceforge.net/techdata.html)

- 哪些困难（请分优先级）会阻碍你自主完成lab实验？

  - 硬件细节
  - 复杂的C代码和汇编
  - 不能象应用程序那样很方便地调试

- 如何把一个在gdb中或执行过程中出现的物理/线性地址与你写的代码源码位置对应起来？

> 1. 在gdb中通过break加行号得到物理地址，list加*物理地址得到行号。
> 2. 用nm, objdump工具可以看到

- 了解函数调用栈对lab实验有何帮助？

> 除了错可以调试 
> 对于函数的调用过程和程序的运行过程有更好的理解。
> 便于调试以及检查。 

- 你希望从lab中学到什么知识？
  - 尝试理解和实现一个面向真实硬件的简单OS，对理解OS原理有很好的补充。

---

## 小组讨论题

---

- 搭建好实验环境，请描述碰到的困难和解决的过程。

> 困难：在virtualbox中设置虚拟机的时候找不到Linux的64位选项。
> 解决：需要通过BIOS设置将电脑的虚拟化功能打开（本电脑LenovoY480的VT功能是锁的，需要打开）。
> 开始时选择了UBUNTU 32位，不能启动，后来换成64位就能顺利运行

> 熟悉基本的git命令行操作命令，从github上的 http://www.github.com/chyyuu/ucore_os_lab 下载ucore lab实验
>
> clone 仓库 

> gitclone http://www.github.com/chyyuu/ucore_os_lab  
>
> 尝试用qemu+gdb（or ECLIPSE-CDT）调试lab1

> 清除文件夹：make clean 
> 编译lab1：make 
> 调出debug命令行：make debug

对于如下的代码段，请说明”：“后面的数字是什么含义
```
 /* Gate descriptors for interrupts and traps */
 struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
 };
```

- [x] ​

> 每一个filed(域，成员变量)在struct(结构)中所占的位数; 也称“位域”，用于表示这个成员变量占多少位(bit)。



- 对于如下的代码段，

```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
如果在其他代码段中有如下语句，
```
unsigned intr;
intr=8;
SETGATE(intr, 0,1,2,3);
```
请问执行上述指令后， intr的值是多少？

> 0x10002

> https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab0/lab0_ex3.c

- 请分析 [list.h](https://github.com/chyyuu/ucore_lab/blob/master/labcodes/lab2/libs/list.h)内容中大致的含义，并能include这个文件，利用其结构和功能编写一个数据结构链表操作的小C程序

---

## 开放思考题

---

- 是否愿意挑战大实验（大实验内容来源于你的想法或老师列好的题目，需要与老师协商确定，需完成基本lab，但可不参加闭卷考试），如果有，可直接给老师email或课后面谈。



- 更新和完善ucore实现环境Linux、Mac OS和Windows操作系统下的实验环境部署帮助。

---

## v9-cpu相关题目
---

### 提前准备
```
sudo apt-get install gcc-multilib build-essential firefox openssh-client openssh-server, qemu-system-x86 python2.7 hexedit git
git clone https://github.com/chyyuu/os_tutorial_lab.git
```



- 分析和实验funcall.c，需要完成的内容包括： 
  - 修改代码，可正常显示小组两位同学的学号（用字符串） 
  - 生成funcall.c的汇编码，理解其实现并给汇编码写注释
  - 尝试用xem的简单调试功能单步调试代码
  - 回答如下问题：
    - funcall中的堆栈有多大？是内核态堆栈还是用户态堆栈
    - funcall中的全局变量ret放在内存中何处？如何对它寻址？
    - funcall中的字符串放在内存中何处？如何对它寻址？
    - 局部变量i在内存中的何处？如何对它寻址？
    - 当前系统是处于中断使能状态吗？
    - funcall中的函数参数是如何传递的？函数返回值是如何传递的？
    - 分析并说明funcall执行文件的格式和内容




- 分析和实验os0.c，需要完成的内容包括： 
  - 生成os0.c的汇编码，理解其实现并给汇编码写注释

  - 尝试用xem的简单调试功能单步调试代码

  - 回答如下问题：
    - 何处设置的中断使能？   

    - 系统何时处于中断屏蔽状态？

    - 如果系统处于中断屏蔽状态，如何让其中断使能？

    - 系统产生中断后，CPU会做哪些事情？（在没有软件帮助的情况下）

    - CPU执行RTI指令的具体完成工作是哪些？

      ​

- [HARD]分析和实验os1/os3.c，需要完成的内容包括： 

  - os1中的task1和task2的栈的起始和终止地址是什么？
  - os1是如何实现任务切换的？
  - os3中的task1和task2的栈的起始和终止地址是什么？
  - os3是如何实现任务切换的？
  - os3的用户态task能够破坏内核态的系统吗？
