---
title: ramfs,ramdisk,tmpfs,rootfs,initramfs,initrd,romfs
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>

# 1 ramfs
其实tmpfs, rootfs, initramfs都是ramfs的一种,它们或者是对它的一些特殊的应用,或者是对它某一方面能力的改进加强.
# 1.1 ramfs

Ramfs是一个非常简单的文件系统，它将Linux的磁盘缓存机制（页面缓存和dentry缓存）导出为可动态调整大小的基于RAM的文件系统。

通常，Linux的所有文件在内存中都有缓存。需要读取的数据页从后备存储设备(block device)中读取后，缓存于内存，并标记为干净的（mark as clean），当虚拟文件系统需要这些数据页的内存时，可以释放。基于同样的机制，在数据写回后备存储时，数据一写回文件就立即被标记为干净的，这些缓存被保留着直至VM(虚拟内存)重新分配内存。对于文件目录占用的缓存(dentry: directory entry)，也存在同样的机制。

但是，ramfs中没有后备存储设备(但是有缓存)。也就是说，写入ramfs的文件可以正常的分配page cache and dentry cache，但是不能写入后备存储设备。这些page cache 和 dentry cache不能被VM释放、回收。这意味着页的数据标记为脏的（mark as dirty），因此当希望回收内存时，内存不能通过VM来释放。

在ramfs的下面可以一直写入数据，直到写满内存为止。由于VM(Vitual Memory)认为文件应该被写回后备存储设备，而不是交换空间(swap space)，所以VM不能释放ramfs分配的内存。只有root用户(or trusted user)才能进行ramfs写操作。

由于ramfs可以基于现有的Linux的文件系统结构，所以用于实现ramfs的代码很小。一般而言，后备存储设备的缓存被安装为一个文件系统。所以，ramfs并不是一个可通过菜单配置项来卸载的可选组件。



## 1.2 tmpfs
ramfs的一个不利之处是你将保留写回到ramfs的数据直至你填补所有的内存，并且VM不能释放它，因为VM考虑到文件将写回后备存储(而不是交换空间)，但是ramfs并不能获得任何的后备存储。据此，只有root(或者一个受信任的用户)可允许回写到一个ramfs挂载中。

tmpfs是从ramfs派生出的,添加了大小的限制以及回写数据到交换空间能力的特殊ramfs。它使用了虚拟内存的机制，它将所有文件保存在虚拟内存中。tmpfs占用的内存有大小的限制，可以在mount时设置，当写入数据会导致超过size时会报错。tmpfs可以把暂时不用的东西回写到swap分区。普通用户可以允许回写到tmpfs中。

## 1.3 rootfs

rootfs是ramfs或tmpfs的特殊实例，用于挂载真实文件系统，在2.6的内核中必然存在。rootfs不能被卸载这个理由近似于你不能杀死init进程，它小巧且简单的为内核确保某些列表不能为空，而不是用特定的代码来检查和处理一个空列表。大部分其他的文件系统安装于rootfs之上。一个空白ramfs实例的空间总量占用是极小的。

## 1.2 ramdisk


"RAM disk"是一种实现文件系统的后备存储的、过时的机制(2.6不用了)，就是在一个内存空间中创建一个合成块设备并使用它作为一个文件系统的后备存储。这个块设备是固定大小的以至于挂载在它上面的文件系统也是固定大小的。ram disk的使用需要从这个假的块设备到page cache之间拷贝内存，生成和销毁dentry，而且需要文件系统的驱动来格式化和解释上面的数据。它是initrd所必需的，也是需要加载模块以访问根文件系统时使用的初始文件系统（请参阅Documentation / initrd.txt）。它还可以用于加密工作的临时文件系统，因为内容在重新启动时被擦除。

与ramfs相比较，ram disk浪费了内存，也浪费了内存总线的带宽。为CPU造成了不必要的工作并污染了CPU的高速缓存。(尽管有避免污染的方法，但是非常耗费资源)。ramfs机制非常自然，因为文件访问可 以通过page cache和dentry cache全，部的工作ramfs都要执行。ram disk被弃用的另外一个原因是环回设备(loopback)引入。环回设备提供了一种更加灵活、方便的从文件而不是从内存块中创建综合块设备的方法。



## 1.5 initramfs
### 1.5.1 initramfs是什么

所有的2.6 Linux内核包含了一个gzip压缩过的cpio格式存档，当内核启动时它将被提取到rootfs。
在提取之后，内核检测rootfs是否包含了一个"init"文件，如果包含就来执行它并设置PID为1。
这个init进程将负责引导系统的其余内容，包扩定位和挂载真正根设备(若有的话)。
如果rootfs在提取cpio存档以后并不包含一个init程序，内核将失败并通过旧的代码来寻找和挂载一个根分区，接着执行一些/sbin/init的变种。

rootfs可以被打包为一个cpio压缩包，并通过initrd=命令行参数传递给内核，这就是initramfs。也可以通过INITRAMFS_SOURCE选项直接编译进内核。



------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
