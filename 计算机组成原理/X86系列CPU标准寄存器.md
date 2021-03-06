---
title: X86系列CPU标准寄存器
tags: 计算机组成原理
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文章参考了<font color=blue >唐朔飞的《 计算机组成原理》</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------


# 1 Intel CPU的发展里程碑

```flow
op1=>operation: 4位4004
op2=>operation: 8位8008
op3=>operation: 16位8086
op4=>operation: 32位80386
op5=>operation: 64位Pentium


op1->op2->op3->op4->op5->op6

```

>**字**：一般说来，CPU在同一时间内处理的一组二进制数称为一个计算机的“字”，而这组二进制数的位数就是“**字长**”。通常称处理字长为8位数据的CPU叫8位CPU，32位CPU就是在同一时间内处理字长为32位的二进制数据。

&emsp;&emsp;Intel CPU 系列，最初是 4 位微处理器 4004，然后到到 8 位微处理器的 8008，再到 8 位微处理器 8080,以及稍后的 16 位微处理器 8086，由 8086 开始，Intel 进入现在所谓的x86时代 。


&emsp;&emsp;Intel  **8086 为 16 位  CPU ，地址总线为20根，寻址能力为2<sup>20</sup>\*1B=1MB**。因为在 8086 之前的 CPU 都是 8 位 CPU，这样也就造成了很多的外设也只支持 8 位，因此  Intel  紧接着就推出了 8 位的 8088 CPU。如果是单从汇编语言的角度上来说，8086 和 8088 是没有区别的，即8086上跑的程序可以不加修改的移植到8088，8088上跑的程序也可以不加修改的移植到8086上，当然，还是有些特殊的地方是不同的，而这些基本上在这里可以忽略掉.

&emsp;&emsp;在 8088  CPU 之后，Intel  又推出了  80186 ，80286 ，这两款 CPU 均是 16 位  CPU 。简单来说80186只是8086增加了一些指令， 但 80286 则不同，**80286 的地址总线数目达到了 24 根**，从而最大寻址能力为 $2^{24}$bit即 16MB，由于支持更多的物理内存寻址，因此 80286 便开始成为了多任务，多用户系统的核心。

&emsp;&emsp;在80286之后，Intel  又推出了 80386 ，**80386 为 32 位微处理器**，Intel 80x86 家族的 32 位微处理器始于 80386；同时 80386 也完全兼容先前的 8086/8088，80186，80286，并且 80386 全面支持 32 位数据类型和 32 位操作，80386 的**数据总线根数和地址总线根数均达到了 32 根**，从而可以最大物理寻址为 2<sup>32</sup>bit 即 4GB 。

&emsp;&emsp;而之后的 80486 也是 32 位微处理器，而后又出来了 Pentium 和 Pentium Pro 等等第五代微处理器，这些处理器虽然也是32位微处理器，但是他们的数据总线和地址总线都有所扩展，比如 Pentium 的数据总线达到 64 位，而 Pentium Pro 的地址总线位数达到了 36 位 。

&emsp;&emsp;详细信息参考<https://blog.csdn.net/CLHJSWE/article/details/72598333>
# 2 16位CPU标准寄存器
![1](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/1.png)



8086的可编程寄存器包括：

* 通用寄存器
 * 数据寄存器（16位）：AX 、BX、CX、 DX
 * 指针寄存器（16位）：SP、BP
 * 变址寄存器（16位）：SI、DI 
* 控制寄存器（16位）：IP、FLAGS
* 段寄存器（16位）：CS、DS、ES、SS


## 2.1 通用寄存器
&emsp;&emsp;**通用寄存器包括数据寄存器、指针寄存器、变址寄存器**。除完成规定的专门用途外，均可用于传送和暂存数据，可以保存算术逻辑运算的操作和运算结果。通用寄存器的长度取决于机器字长。
&emsp;&emsp;16位cpu通用寄存器共有8个：AX、BX、CX、DX、BP、SP、SI、DI。八个寄存器都可以作为普通的数据寄存器使用。但有的有特殊的用途：AX为累加器，CX为计数器，BX，BP为基址寄存器，SI，DI为变址寄存器，BP还可以是基指针，SP为堆栈指针。


### 2.1.1 数据寄存器
&emsp;&emsp;由于之前的 CPU 为 8 位 CPU，所以为了兼容以前的 8 位程序，在 8086 CPU 中，每一个数据寄存器都可以当做两个**单独**的寄存器来使用，由此，每一个 16 位寄存器就可以当做 2 个独立的 8 位寄存器来使用了 。
&emsp;&emsp;16位CPU具有4个16位数据寄存器又可分割成8个独立的8位寄存器(AX：AH-AL、BX：BH-BL、CX：CH-CL、DX：DH-DL)，每个寄存器都有自己的名称，可独立存取。 


#### 2.1.1.1 AX寄存器
&emsp;&emsp;AX 寄存器也叫做**累加寄存器**，此外还具有的特殊用途是在使用 **DIV 和 MUL** 指令时使用。

![2](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/2.jpg "2")

![3](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/3.jpg "3")

#### 2.1.1.2 BX寄存器
&emsp;&emsp;BX 寄存器也叫**基址寄存器**，可以**暂存一般的数据**。
&emsp;&emsp;BX 主要还是用于其专属功能：**寻址**（寻址物理内存地址）上，**BX 寄存器中存放的数据一般是用来作为偏移地址**使用的。在 8086 CPU 中，CPU 是根据 <段地址：偏移地址> 来进行寻址操作的，而 BX 中存放的数据表示的是偏移地址的话，自然，便可以通过 <段地址：[BX]> 的方式来完成寻址操作了。
#### 2.1.1.3 CX寄存器
&emsp;&emsp;CX 寄存器也叫**计数寄存器**，可以**暂存一般性的数据**。
&emsp;&emsp;在位操作中，当移多位时，要用**CL来指明移位的位数**；
&emsp;&emsp;当在汇编指令中使用循环 LOOP 指令时，可以通过**CX 来指定需要循环的次数**，而 CPU 在每一次执行 LOOP 指令的时候，都会做两件事：一件就是令 CX = CX – 1，即令 CX 计数器自动减去 1；还有一件就是判断 CX 中的值，如果 CX 中的值为 0 则会跳出循环，而继续执行循环下面的指令，当然如果 CX 中的值不为 0 ，则会继续执行循环中所指定的指令 。

#### 2.1.1.4 DX寄存器
&emsp;&emsp;DX 寄存器也叫**数据寄存器**，可以**暂存一般性的数据**
&emsp;&emsp;当在使用 DIV 指令进行除法运算时，如果除数为 16 位时，**被除数将会是 32 位，而被除数的高 16 位就是存放在 DX 中**，而且执行完 DIV 指令后，本次除法运算所产生的**余数将会保存在 DX 中**，
&emsp;&emsp;在执行 MUL 指令时，如果两个**相乘的数都是 16 位的话**，那么相乘后产生的结果显然需要 32 位来保存，而这**32 位的结果的高 16 位就是存放在 DX 寄存器中** 。


### 2.1.2 指针寄存器

&emsp;&emsp;寄存器BP和SP称为指针寄存器(Pointer Register)，**主要用于存放堆栈内存储单元的偏移量**，用它们可实现多种存储器操作数的寻址方式，为以不同的地址形式访问存储单元提供方便。指针寄存器**不可分割成8位寄存器**。作为通用寄存器，**也可存储算术逻辑运算的操作数和运算结果。**

### 2.1.3 变址寄存器


&emsp;&emsp;寄存器SI和DI称为变址寄存器(Index Register)，它们**主要用于存放存储单元在段内的偏移量**，用它们可实现多种存储器操作数的寻址方式，为以不同的地址形式访问存储单元提供方便。变址寄存器**不可分割成8位寄存器**。作为通用寄存器，**也可存储算术逻辑运算的操作数和运算结果**。它们也可作一般的指针存储器使用。在字符串操作指令的执行过程中，对它们有特定的要求，而且还具有特殊的功能。 

## 2.2 段寄存器
&emsp;&emsp;**16位CPU的段寄存器是16位，是根据内存分段管理模式而设置的**。
&emsp;&emsp;保护模式下逻辑地址由段寄存器的值（也就是段选择符）和一个偏移量组合而成的。通过段选择符可以找到段描述符，进而找到段基址，线性地址=段基址+偏移量。
&emsp;&emsp;ES为附加段寄存器。CS为代码段寄存器。SS为堆栈段寄存器。DS为数据段寄存器。


## 2.3 控制寄存器
### 2.3.1 指令指针寄存器
&emsp;&emsp;指令指IP(Instruction Pointer)寄存器是**存放下次将要执行的指令在代码段的偏移量**。在具有预取指令功能的系统中，下次要执行的指令通常已被预取到指令队列中，除非发生转移情况。所以，在理解它们的功能时，不考虑存在指令队列的情况。逻辑地址=CS:IP
 

### 2.3.2 标志寄存器（PSW）
![4](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/4.jpg)

![5](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/5.jpg)


&emsp;&emsp;标志寄存器在16位CPU中用于反映处理器的状态和运算结果的某些特征，其中只有9位有定义）这些标志位分为两类：


* 运算结果标志位
 * 进位标志**CF**(Carry Flag)——进位标志CF主要用来反映运算是否产生进位或借位。如果运算结果的最高位产生了一个进位或借位，那么，其值为1，否则其值为0。使用该标志位的情况有：多字(字节)数的加减运算，无符号数的大小比较运算，移位操作，字(字节)之间移位，专门改变CF值的指令等。
 
 * 奇偶标志**PF**(Parity Flag)——奇偶标志PF用于反映运算结果中"1"的个数的奇偶性。如果“1”的个数为偶数，则PF的值为1，否则其值为0。利用PF可进行奇偶校验检查，或产生奇偶校验位。在数据传送过程中，为了提供传送的可靠性，如果采用奇偶校验的方法，就可使用该标志位。
 * 辅助进位标志**AF**(Auxiliary Carry Flag)——在发生下列情况时，辅助进位标志AF的值被置为1，否则其值为0：
      * 在字操作时，发生低字节向高字节进位或借位时；
      * 在字节操作时，发生低4位向高4位进位或借位时。

 * 零标志**ZF**(Zero Flag)——零标志ZF用来反映运算结果是否为0。如果运算结果为0，则其值为1，否则其值为0。在判断运算结果是否为0时，可使用此标志位。

 * 符号标志**SF**(Sign Flag)——符号标志SF用来反映运算结果的符号位，它与运算结果的最高位相同。在微机系统中，有符号数采用补码表示法，所以，SF也就反映运算结果的正负号。运算结果为正数时，SF的值为0，否则其值为1。

 * 溢出标志**OF**(Overflow Flag)——溢出标志OF用于反映有符号数加减运算所得结果是否溢出。如果运算结果超过当前运算位数所能表示的范围，则称为溢出，OF的值被置为1，否则，OF的值被清为0。

* 状态控制标志位——状态控制标志位是用来控制CPU操作的，它们要通过专门的指令才能使之发生改变。

 * 追踪标志**TF**(Trap Flag)——当追踪标志TF被置为1时，CPU进入单步执行方式，即每执行一条指令，产生一个单步中断请求。这种方式主要用于程序的调试。指令系统中没有专门的指令来改变标志位TF的值，但程序员可用其它办法来改变其值。

 * 中断允许标志**IF**(Interrupt-enable Flag)——中断允许标志IF是用来决定CPU是否响应CPU外部的可屏蔽中断发出的中断请求。但不管该标志为何值，CPU都必须响应CPU外部的不可屏蔽中断所发出的中断请求，以及CPU内部产生的中断请求。CPU的指令系统中也有专门的指令来改变标志位IF的值。具体规定如下：
     * 当IF=1时，CPU可以响应CPU外部的可屏蔽中断发出的中断请求；
     * 当IF=0时，CPU不响应CPU外部的可屏蔽中断发出的中断请求。

 * 方向标志**DF**(Direction Flag)——方向标志DF用来决定在串操作指令执行时有关指针寄存器发生调整的方向。当其为0时，si、di寄存器增大；当其为1时，si、di寄存器减小




# 3 32位CPU标准寄存器
![6](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/6.jpg)

80386共提供7种类型的可编程寄存器，如下：

* 通用寄存器（32位）：EAX、EBX、ECX、EDX、ESP、EBP、ESI、EDI
* 段寄存器（16位）：CS、SS、DS、ES、FS、GS
* 指令指针寄存器和标志寄存器（32位）：EIP、EFLAGS
* 系统表寄存器：
 * 48位：GDTR、IDTR
 * 16位：LDTR、TR
* 控制寄存器（32位）：CR0、CR1、CR2、CR3、CR4
* 调试寄存器（32位）：DR0、DR1、DR2、DR3、DR4、DR5、DR6、DR7
* 测试寄存器（32位）：TR0、TR1、TR2、TR3、TR4、TR5、TR6、TR7



    
## 3.1 通用寄存器

![7](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/7.jpg)

&emsp;&emsp;80386有8个32位的通用寄存器，这8个通用寄存器都是由8086相应16位通用寄存器扩展成32位而得，用法相似。名字分别是：EAX，EBX，ECX，EDX，ESI，EDI，EBP，ESP。每个寄存器可以掰成两个16位寄存器使用，也可以掰成4个8位寄存器使用。
 
* EAX：一般用作累加器
* EBX：一般用作基址寄存器（Base）
* ECX：一般用来计数（Count）
* EDX：一般用来存放数据（Data）
* ESP：一般用作堆栈指针（Stack Pointer）
* EBP：一般用作基址指针（Base Pointer）
* ESI：一般用作源变址（Source Index）
* EDI：一般用作目标变址（Destinatin Index）

&emsp;&emsp;在16位CPU中，数据寄存器不能作为指针寄存器，**在32位CPU中，其数据寄存器不仅可传送数据、暂存数据、保存算术逻辑运算结果，而且也可作为指针寄存器**，所以，这些32位寄存器更具有通用性。

## 3.2 段寄存器
![8](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/8.jpg)

&emsp;&emsp;80386有6个16位段寄存器，分别是：CS（代码段寄存器），DS（数据段寄存器），SS（堆栈段寄存器），ES、FS 、GS（附加数据段寄存器）。前4个段寄存器的名称与8086相同，在实地址方式下使用方式也和8086相同。80386又增加了FS与GS，主要为了减轻对DS段和ES段的压力。除CS支持代码段，SS支持堆栈段外，程序员可以利用其它的所有段寄存器支持数据段。


&emsp;&emsp;段寄存器中存放的不再是某个段的基地址，而是某个段的选择符（Selector）。因为16 位的寄存器无法存放32位的段基地址，段基地址只好存放在段的描述符（Descriptor）中。

&emsp;&emsp;每个段寄存器对应这一个64位段描述符寄存器，这在8086中是没有的，它的作用是**快速访问段描述符**。当段寄存器载入段选择符时，64位的段描述符寄存器就载入GDT中的对应的段描述符。



## 3.3 指令指针寄存器和标志寄存器
![9](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/9.jpg)
### 3.3.1 指令指针寄存器
&emsp;&emsp; 80386的指令指针寄存器EIP是一个32位寄存器，是从8086的IP寄存器扩充而来。EIP中存放下一条将要执行指令的偏移量（offset），这个偏移量是相对于目前正在运行的代码段寄存器（CS）而言的。偏移量加上当前代码段的基地址，就形成了下一条指令的地址。

### 3.3.2 标志寄存器
&emsp;&emsp; 80386的标志寄存器EFLAGS也是一个32位寄存器，其中只使用了15位，从8086的FLAGS寄存器扩展而来。如下图所示：

![10](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/10.jpg)


* OF、DF、IF、TF、SF、ZF、AF、PF和CF在8086中就已经存在，请参考上面[2.3.2 标志寄存器](https://blog.csdn.net/liao20081228/article/details/79734887#t9 "点击跳转")。

* 下面的标志位是80286新增的标志位
	 * **IOPL**（I/O Privilege Level）是从80286开始出现的，占2个bit表示I/O特权级，如果当前特权级小于或等于IOPL，则可以执行I/O操作，否则将出现一个保护性异常。IOPL只能由特权级为0的程序或任务来修改。

	 * **NT**（Nested Task）也是从80286开始出现的，表示嵌套任务，用于控制中断返回指令IRET，当NT=0时，用堆栈中保存的值恢复EFLAGS、CS和EIP，从而实现返回；若NT=1，则通过任务切换实现中断返回。

* 下面的标志位是80386新增的标志位
	 * **VM**（Virtual-8086 mode）表示虚拟8086模式，如果VM被置位且80386已处于于保护模式下，则CPU切换到虚拟8086模式，此时，对段的任何操作又回到了实模式，如同在8086下运行一样。

	 * **RF**（Resume flag）表示恢复标志(也叫重启标志)，与调试寄存器一起用于断点和单步操作，当RF＝1 时，下一条指令的任何调试故障将被忽略，不产生异常中断。当RF=0时，调试故障被接受，并产生异常中断。用于调试失败后，强迫程序恢复执行，在成功执行每条指令后，RF自动复位。
* 下面的标志位是80486新增的标志位
	 * **AC**（Alignment check）表示对齐检查。这个标志是80486以后的CPU才有的。当AC=1且CR0中的AM=1时，允许存储器进行地址对齐检查，若发现地址未对齐，将产生异常中断。所谓地址对齐，是指当访问一个字（2字节长）时，其地址必须是偶数（2的倍数），当访问双字（4字节长）时，其地址必须是4的倍数。但是只有运行在特权级3的程序才执行地址对齐检查，特权级0、1、2忽略该标志。

* 下面的标志位是Pentium新增的标志位  
	 * **VIF**（Virtual interrupt flag）表示虚拟中断标志。当VIF=1时，可以使用虚拟中断，当VIF=0时不能使用虚拟中断。该标志要和下面的VIP和CR4中的VME配合使用。
	 * **VIP**（Virtual interrupt pending flag）表示虚拟中断挂起标志。当VIP=1时，VIF有效，VIP=0时VIF无效。
	 * **ID**（Identification flag）表示鉴别标志。该标志用来指示Pentium CPU是否支持CPUID的指令。

## 3.4 系统表寄存器
![11](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/11.jpg)

&emsp;&emsp;80386有4个系统地址寄存器，用来存储操作系统需要的保护信息和地址转换表信息、定义目前正在执行任务的环境、地址空间和中断向量空间。由于只能在保护方式下使用，因此又称为保护方式寄存器。

* 全局描述符表寄存器**GDTR**（Global Descriptor Table Register ），是48 位寄存器，用来保存全局描述符表（GDT）的32 位基地址和GDT 的大小（16位）。（全局描述符表最大为2<sup>16</sup>B，共2<sup>16</sup>B/8B=8K个全局描述符）。

>注：GDT表里面的每一项都表明一个段的信息，或者是一个LDT表的相关信息。其实一个LDT表也是一个段。所以也可以说GDT表的每一项都描述一个段。就像一个文件夹下面可以有文件，也可以有文件夹一样，GDT表里面既可以有段描述符，也可以有LDT的表。

* 中断描述符表寄存器**IDTR**（Interrupt Descriptor Table Register），是48 位寄存器，用来保存中断描述符表（IDT）的32 位基地址和IDT 的大小（16位）。（中断描述符表最大为2<sup>16</sup>B，共2<sup>16</sup>B/8B=8K个中断描述符）。

* 局部描述符表寄存器**LDTR**（Local Descriptor Table Register ），是16 位寄存器，保存局部描述符表LDT 段的选择符。一旦16位的选择符（也叫选择子）放入LDTR，CPU会自动将选择符所指定的局部描述符装入64位的局部描述符寄存器中。

* 任务状态寄存器**TR**（Task State Register）是16 位寄存器，用于保存任务状态段TSS段的16位选择符。与LDTR类似，一旦16位的选择符放入TR，CPU会自动将该选择符所指定的任务描述符装入64位的任务描述符寄存器中。


>![12](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/12.jpg)
>&emsp;&emsp;LDT和TSS都是一个段，所以在GDT中有对应的表项描述。LDTR和TR是由16位选择字段和64位描述符寄存器组成。用来指定局部描述符表和任务状态段TSS在物理存储器中的位置和大小。64位描述符寄存器是自动装入的，程序员不可见。LDTR与TR只能在保护方式下使用，程序只能访问16位选择符寄存器。（注意区分描述符表寄存器与描述符寄存器）。
>&emsp;&emsp;IDTR是48位的（大于32位），与GDTR位数一样，大于32位的结构自然就有4G内任意寻址的能力。所以可以直接访问中断向量表，不需要通过选择子（16位结构）来间接寻找。所以IDT表可以与GDT表完全独立，不需要像LDT一样还要在GDT表建立相关表项。但是TSS段和LDT表两个结构就不行，他们的寄存器只有16位，不可能在4G内存中任意寻址，所以只能将其插入到GDT表中，然后借用GDT和自身的选择子的组合来间接寻址。
>
>![13](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/13.png)

## 3.5 控制寄存器
![14](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/14.jpg)

![15](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/15.jpg)

![16](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/16.jpg)
&emsp;&emsp;80386的控制寄存器有4个：CR0（机器状态字）、CR1（Intel 预留）、CR2（页故障地址）、CR3（页目录地址）。其中CR1保留以后使用，从Pentium开始，又增加了一个CR4。

* CR0的低16位包含了与80286的MSW一致的位定义，保持了和80286的兼容，同时也兼容了从80286开始的两条指令LMSW/SMSW
 * 下面四个定义从80286开始存在。
     * **PE**（Protection Enable）保护模式允许，PE=0表示CPU工作在实模式，PE=1表示CPU工作在保护模式
     * **MP**（Monitor Coprocessor）监控协处理器，MP=1表示协处理器在工作，MP=0表示协处理器未工作。
     * **EM**（Emulation）协处理器仿真，当MP=0，EM=1时，表示正在使用软件仿真协处理器工作。
     * **TS**（Task Switched）任务转换，每当进行任务转换时，TS=1，任务转换完毕，TS=0。TS=1时不允许协处理器工作。
 * 下面的2个定义从80386开始存在
     * **ET**（Extension Type）处理器扩展类型，反映了所扩展的协处理器的类型，ET=0为80287，ET=1为80387。
     * **PG**（Paging）页式管理机制使能，PG=1时页式管理机制工作，否则不工作。
   
 * 下面的4个定义从80386开始存在
     * **NE**（Numeric Error）数值异常中断控制，NE=1时，如果运行协处理器指令发生故障，则用异常中断处理，NE=0时，则用外部中断处理。
     * **WP**（Write Protect）写保护，当WP=1时，对只读页面进行写操作会产生页故障。
     * **AM**（Alignment Mask）对齐标志，AM=1时，允许对齐检查，AM=0时不允许，关于对齐，在EFLAGS的AC标志时介绍过，在80486以后的CPU中，CPU进行对齐检查需要满足三个条件，AC=1、AM=1并且当前特权级为3。
     * **NW**（Not Write-through）禁用通写。当CD=0时NW有效。当NW有效且NW=0时，采用回写策略（写数据时只写cache不写RAM）；当NW有效且NW=1时采用通写策略（写数据时既写cache又写RAM）。
     * **CD**（Cache Disable）禁用高速缓存。CD=0时启用高速缓存，CD=禁用高速缓存。

* CR1保留未用。

* CR2存放引起页故障的线性地址，只有在PG=1时，CR2才有效，当页故障处理程序被激活时，压入页故障处理程序堆栈中的错误码提供页故障的状态信息。

* CR3的高20位存放页目录的基地址，因为也目录总是页对齐的（一页为4K），所以页目录基地址从bit12开始就可以了。只有当CR0中的PG=1时，CR3的页目录基地址才有效。
	 * 从80486开始，在CR3的低12位定义了两个控制位，如下：
		 * **PCD**（Page-level Cache Disable）页CACHE禁止，当PCD=0时，页目录表进行高速缓存，PCD=1时，不进行高速缓存；该位控制PCD引脚控制外部CACHE工作还是不工作。
		  * **PWT**（Page-level Writes Transparent），CACHE的写入分为通写（Write-Through）和回写（Write-Back）,80486以上的CPU内部的CACHE都是通写的，但对外部CACHE而言，允许某些页是回写的，而另一些页是通写的，当PWT=1时，外部CACHE对页目录进行通写，否则进行回写；此位驱动PWT引脚以控制外部CACHE是通写还是回写。

* CR4是从Pentium CPU开始出现的。
	 * **VME**（Virtual-8086 Mode Extensions）虚拟8086方式扩展，VME=1允许使用虚拟8086扩展模式，否则只能使用80386/80486的虚拟8086模式。
	 * **PVI**（Protected-Mode Virtual Interrupts）保护模式虚拟中断，PVI=1时，在保护模式下支持虚拟中断标志VIF(EFLAGS中)，PVI=0则不支持虚拟中断标志。
	 * **TSD**（Time Stamp Disable）时间戳禁止，TSD=1时，允许在特权级为0的程序中执行RDTSC指令（读时间戳计数指令），TSD=0时，允许任何特权级执行RDTSC指令。
	 * **DE**（Debugging Extensions）调试扩展。
	 * **PSE**（Page Size Extensions）页大小扩展，PSE=1时，页大小可以扩展到2M或4M，PSE=0时，页大小只能是4K.
	 * **PAE**（Physical Address Extension）物理地址扩展，PAE=1时，页物理地址可以扩展到36bits以上，PAE=0时只能用32bits的物理地址。
	 * **MCE**（Machine-Check Enable）硬件检查使能，Pentium以后的CPU有一种硬件检测功能，MCE=1时允许使用该功能。
	 * **PGE**（Page Global Enable）全局页使能，PGE=1时，允许使用全局页，PGE=0时禁止使用全局页。
	 * **PCE**（Performance-Monitoring Counter Enable）性能监视计数器使能，当PCE=1时，允许在任何保护级下执行RDPMC指令，PCE=0时，只有特权级0的程序可以执行RDPMC指令。
	 * **OSFXSR**（Operating System Support for FXSAVE and FXRSTOR instructions）
	 * **OSXMMEXCPT**（Operating System Support for Unmasked SIMD Floating-Point Exceptions）
	 * **VMXE**（VMX-Enable Bit）VMX使能位，VMXE=1时，允许VMX操作。
	 * **SMXE**（SMX-Enable Bit）SMX使能位，SMXE=1时，允许SMX操作。
	 * **OSXSAVE**（XSAVE and Processor Extended States-Enable Bit）  

## 3.6 调试寄存器
![17](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/17.jpg)
   
&emsp;&emsp; 一共有8个调试寄存器DR0-DR7，DR0-DR3可以分别设置4个断点的线性地址，DR4-DR5保留未用，DR6是断点状态寄存器，DR7是断点控制寄存器（包括断点类型、断点长度，断点开放/禁止）

## 3.7 测试寄存器

&emsp;&emsp; 一共有8个测试寄存器TR0-TR7，TR0-TR2保留，TR3-TR5用作CACHE测试，TR6为命令测试寄存器，TR7为测试数据寄存器。


# 4 64位CPU标准寄存器
![18](https://www.github.com/liao20081228/blog/raw/master/图片/X86系列CPU标准寄存器/18.jpg)


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文章参考了<font color=blue >唐朔飞的《 计算机组成原理》</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
