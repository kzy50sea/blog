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
u-boot目录结构主要经历过2次变化，u-boot版本第一次发生变化，主要增加了api的内容；变化最大的是第二次，从2010.6版本开始。


| 目录|描述|
|:--|:--|
|api|存放硬件无关的接口函数。从u-boot-1.3.2开始出现|
|arch | 与体系结构相关的代码，从u-boot-2010.06版本开始把原先的cpu与lib_arch内容纳入arch中，并且其中增加inlcude文件夹；分离出通用库文件lib。
| board  |  根据不同开发板定制的代码，代码也不少，每个子目录表示一个开发板
| common|   实现u-boot命令行下所支持的命令。在该目录下，每条命令对应一个独立的文件。 
|~~cpu~~    |  与体系结构相关的代码，uboot的重头戏
| disk   |             磁盘分区相关代码
| doc   |             文档，一堆README.xxx的文件
| drivers  |          驱动，很丰富，每种类型的设备驱动占用一个子目录
| examples|           示例程序
| fs          |       文件系统，支持嵌入式开发板常见的文件系统
| include  |          头文件，已通用的头文件为主
| ~~lib_xxxarch~~  |      与体系结构相关的通用库文件
| nand_spl       |    NAND存储器相关代码
| net          |      网络相关代码，小型的协议栈
| onenand_ipl|
| post  |             加电自检程序
|tools  |            辅助程序，用于编译和检查uboot目标文件


 7). drivers: 在该目录下保存着u-boot所支持癿设备驱劢程序。典型癿如各种网卡、支持癿CFI癿Flash存储器、串口和USB等。 8). fs: 对亍Ubuntu所支持癿文件系统，在该目录下都有一个文件夹不乊对应。典型癿，u-boot现在支持cramfs、jffs2和ext4等文件系统。 9). include:该目录下保存着u-boot所使用癿头文件，对各种硬件平台支持癿汇编文件、系统癿配置文件以及对文件系统支持癿文件。该目录下configs目录有不开収板相兰癿配置头文件，如zynq_common.h是亍zynq开収板相兰癿配置文件。 10). lib: 该目录下保存着体系结极相兰癿库文件。
ALINX 黑金 Zynq7000 系列开发宝典 http://www.heijin.org
283
ALINX 黑金 Zynq7000 开发平台配套教程
11). net: 该目录下保存着不网络协议相兰癿代码。比如BOOTP协议、TFTP协议、RARP协议和NFS文件系统癿实现。 12). tools: 该目录下保存着用亍生成u-boot癿工具，包括mkimage、crc、Makefile和boards.cfg配置文件。
(4)common。common是普遍的普通的，这个文件夹下放的是一些与具体硬件无关的普遍适用的一些代码。譬如控制台实现、crc校验的。但是更多的主要是两类：一类是cmd开头的，是用来实现uboot的命令系统的；另一类是env开头的，是用来实现环境变量的。

(5)cpu。这个目录是SoC相关的，里面存放的代码都是SoC相关初始化和控制代码（譬如CPU的、中断的、串口等SoC内部外设的，包括起始代码start.S也在这里）。里面很多子文件夹，每一个子文件夹就是一个SoC系列。
注意：这个问价是严格和硬件相关的，因此移植时也是要注意的。但是因为这个文件夹内都是SoC有关的，我们自己的开发板和三星的开发板虽然板子设计不同但是SoC都是同一个，因此实际移植时这个目录几乎不用动。
(6)disk。磁盘有关的，没研究过，没用过。
(7)doc。文档目录，里面存放了很多uboot相关文档，这些文档可以帮助我们理解uboot代码。但是因为是纯英文的，而且很杂乱，所以几乎没用。
(8)drivers。顾名思义，驱动。这里面放的就是从linux源代码中扣出来的原封不动的linux设备驱动，主要是开发板上必须用到的一些驱动，如网卡驱动、Inand/SD卡、NandFlash等的驱动。要知道：uboot中的驱动其实就是linux中的驱动，uboot在一定程度上移植了linux的驱动给自己用。但是linux是操作系统而uboot只是个裸机程序，因此这种移植会有不同，让我说：uboot中的驱动其实是linux中的驱动的一部分。
(9)examples。示例代码，没用过。
(10)fs。filesystem，文件系统。这个也是从linux源代码中移植过来的，用来管理Flash等资源。
(11)include。头文件目录。uboot和linux kernel在管理头文件时都采用了同一个思路，就是把所有的头文件全部集中存放在include目录下，而不是头文件跟着自己对应的c文件。所以在uboot中头文件包含时路径结构要在这里去找。
(12)lib_开头的一坨。（典型的lib_arm和lib_generic）架构相关的库文件。譬如lib_arm里面就是arm架构使用的一些库文件。lib_generic里是所有架构通用的库文件。这类文件夹中的内容移植时基本不用管。
(13)libfdt。设备树有关的。linux内核在3.4左右的版本的时候更改了启动传参的机制，改用设备树来进行启动传参，进行硬件信息的描述了。
(14)nand_spl。nand相关的，不讲。
(15)net。网络相关的代码，譬如uboot中的tftp nfs ping命令 都是在这里实现的。
(16)onenand开头的，是onenand相关的代码，是三星加的，标准uboot中应该是没有的。
(17)post。没关注过，不知道干嘛的。
(18)sd_fusing。这里面代码实现了烧录uboot镜像到SD卡的代码。后面要仔细研究的。
(19)tools。里面是一些工具类的代码。譬如mkimage。



2、各文件介绍

(1).gitignore。git工具的文件，git是一个版本管理工具（类似的还有个svn），这个文件和git有关，和uboot本身无关的，不用去管。
(2)arm_config.mk。后缀是.mk，是一个Makefile文件，将来在某个Makefile中会去调用它。
(3)三个Changelog文件，修改记录文件，该文件记录了这个uboot项目的版本变迁以及每个版本较上个版本修改的记录。正式的项目都有这些记录的。可以直接忽略，主要是给维护uboot的人用的。
(4)config.mk。和arm_config.mk差不多性质。
(5)COPYING。版权声明，uboot本身是GPL许可证的。
(6)CREDITS。鸣谢，里面记录了对uboot有贡献的人，感谢目录。
(7)image_split。一个脚本，看说明是用来分割uboot.bin到BL1的，暂时用不到，先不管。
(8)MAINTAINERS。维护者，就是当前在参与维护uboot源码的社区工作者。
(9)MAKEALL。一个脚本，应该是帮助编译uboot的。
(10)Makefile。这个很重要，是uboot源代码的主Makefile，将来整个uboot被编译时就是用这个Makefile管理编译的，所以我们在下个课程中研究uboot配置编译过程时就要分析这个Makefile。
(11)mk。快速编译的脚本，其实就是先清理然后配置然后编译而已。
(12)mkconfig。这个很重要，是uboot配置阶段的主要配置脚本。uboot的可移植性很大程度就是靠这个配置脚本在维护的。我们在下个课程中研究uboot配置编译过程时就要分析这个配置脚本。
(13)mkmovi。暂时不去管他，一个脚本，和iNand/SD卡启动有关
(14)README。所有的软件都有README，一般拿到一个东西要先读README，这个东西其实就是个简单的使用说明书。
(15)rules.mk。这个文件是我们uboot的Makefile使用的规则，本身非常重要，但是我们不去分析他，不去看他。

总结：以上这些文件中，对我们比较重要，需要认真看的有2个：mkconfig和Makefile。一个负责uboot的配置，一个负责编译。

3、文件夹介绍






 

 

 

 


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
