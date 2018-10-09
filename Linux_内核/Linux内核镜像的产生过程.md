---
title: Linux内核镜像的产生过程
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------


&emsp;&emsp;在嵌入式Linux中，内核的启动过程分为两个阶段。其中，第一阶段启动代码放在arch/arm/kernel/head.S文件中，该文件与体系结构相关，与用户的开发板无关，主要是初始化ARM内核等。第二阶段启动代码是init目录下的main.c。现以执行命令`make zImage`为例来说明，arm-linux内核镜像的产生过程。

* 当用户对Linux内核源码进行编译时，kernel的第1/2阶段代码会生成可执行文件vmlinux，该文件是未被压缩的镜像文件，非常大，不能直接下载到NAND中，通常放在PC机上，这也是最原始的Linux镜像文件。试验时该文件约50M。

* 镜像文件vmlinux由于很大，肯定不能直接烧入NAND中，因此需要进行二进制化，即经过objcopy处理，使之只包含二进制数据的内核代码，去除不需要的文件信息等，这样就制作成了image镜像文件。该镜像文件也是未压缩，只是经过了二进制化而变小。试验时该文件约5M。

* 一般来说，内存SDRAM中的内核镜像是经过压缩的，只是在运行时再将其解压。所以，编译时会先使用gzip将镜像文件image进行压缩(压缩比约为 2:1),再将压缩后的镜像文件和源码中的两个文件arch/arm/boot/compressed/head.S、arch/arm/boot /compressed/misc.c一起链接生成压缩后的镜像文件compress/vmlinux。试验时该文件约为2.5M。注意，这两个源码文件 是解压程序，用于将内存SDRAM中的压缩镜像zImage进行解压。

* 压缩后的镜像文件compress/vmlinux经过二进制化，最终生成镜像文件zImage，试验时该文件约为2.5M。当然，在内存 SDRAM中运行压缩镜像文件zImage时，会首先调用两个解压程序arch/arm /boot/compressed/head.S、arch/arm/boot/compressed/misc.c将自身解压，然后再执行kernel 的第一阶段启动代码arch/arm/kernel/head.S。简而言之，在内存中运行内核时，kernel先自身解压，再执行第一阶段启动代码。试 验时运行在内存中的镜像文件约为5M，与image镜像文件大小相同。

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
