---
title: U-boot
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1  U-boot简介
&emsp;&emsp; U-Boot，全称 Universal Boot Loader，是遵循GPL条款的开放源码项目。U-Boot的作用是系统引导。U-Boot从FADSROM、8xxROM、PPCBOOT逐步发展演化而来。其源码目录、编译形式与Linux内核很相似，事实上，不少U-Boot源码就是根据相应的Linux内核源程序进行简化而形成的，尤其是一些设备的驱动程序，这从U-Boot源码的注释中能体现这一点。
 &emsp;&emsp;U-Boot不仅仅支持嵌入式Linux系统的引导，它还支持NetBSD, VxWorks, QNX, RTEMS, ARTOS, LynxOS, android嵌入式操作系统。其目前要支持的目标操作系统是OpenBSD, NetBSD, FreeBSD,4.4BSD, Linux, SVR4, Esix, Solaris, Irix, SCO, Dell, NCR, VxWorks, LynxOS, pSOS, QNX, RTEMS, ARTOS, android。这是U-Boot中Universal的一层含义，另外一层含义则是U-Boot除了支持PowerPC系列的处理器外，还能支持MIPS、 x86、ARM、NIOS、XScale等诸多常用系列的处理器。这两个特点正是U-Boot项目的开发目标，即支持尽可能多的嵌入式处理器和嵌入式操作系统。就目前来看，U-Boot对PowerPC系列处理器支持最为丰富，对Linux的支持最完善。其它系列的处理器和操作系统基本是在2002年11 月PPCBOOT改名为U-Boot后逐步扩充的。从PPCBOOT向U-Boot的顺利过渡，很大程度上归功于U-Boot的维护人德国DENX软件工程中心Wolfgang Denk本人精湛专业水平和执着不懈的努力。当前，U-Boot项目正在他的领军之下，众多有志于开放源码BOOT LOADER移植工作的嵌入式开发人员正如火如荼地将各个不同系列嵌入式处理器的移植工作不断展开和深入，以支持更多的嵌入式操作系统的装载与引导。


* 2008年8月及以前，按版本号命名：u-boot-1.3.4.tar.bz2
* 2008年8月以后均按日期命名。目前最新版本：u-boot-2011.06.tar.bz2
# 2 U-boot目录结构
|目录|描述|
|:--|:--|
|api | 存放uboot提供的硬件无关的接口函数。u-boot-1.3.2开始增加|
| arch |与体系结构相关的代码，从u-boot-2010.06开始把原先的cpu与lib_arch内容全部纳入arch中，并且其中增加inlcude文件夹；分离出通用库文件lib。|
| board  |            根据不同开发板定制的代码，代码也不少
|cmd|
|common |通用的代码，涵盖各个方面，已命令行处理为主，实现u-boot命令行下所支持的命令。在该目录下，每条命令对应一个独立的文件。
| cpu      |          与体系结构相关的代码，从u-boot-2010.06开始把原先的cpu与lib_arch内容全部纳入arch中
|disk     |           磁盘分区相关代码
| doc      |          文档，一堆README开头的文件
| drivers  |          驱动，很丰富，每种类型的设备驱动占用一个子目录
| examples |          示例程序
| fs         |        文件系统，支持嵌入式开发板常见的文件系统
| include            头文件，已通用的头文件为主
| lib_【arch】 |       与体系结构相关的通用库文件
| nand_spl    |       NAND存储器相关代码
| net          |      网络相关代码，小型的协议栈
| onenand_ipl|
| post          |     加电自检程序
|tools         |     辅助程序，用于编译和检查uboot目标文件
| lib                通用库文件









 

 

 

 


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
