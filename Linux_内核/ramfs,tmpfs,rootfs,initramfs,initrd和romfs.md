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
# 1.1 ramfs是什么

Ramfs是一个非常简单的文件系统，它将Linux的磁盘缓存机制（页面缓存和dentry缓存）导出为可动态调整大小的基于RAM的文件系统。

通常，Linux的所有文件在内存中都有缓存。需要读取的数据页从后备存储设备(block device)中读取后，缓存于内存，并标记为干净的（mark as clean），当虚拟文件系统需要这些数据页的内存时，可以释放。基于同样的机制，在数据写回后备存储时，数据一写回文件就立即被标记为干净的，这些缓存被保留着直至VM(虚拟内存)重新分配内存。对于文件目录占用的缓存(dentry: directory entry)，也存在同样的机制。

但是，ramfs中没有后备存储设备(但是有缓存)。也就是说，写入ramfs的文件可以正常的分配page cache and dentry cache，但是不能写入后备存储设备。这些page cache 和 dentry cache不能被VM释放、回收。这意味着页的数据标记为脏的（mark as dirty），因此当希望回收内存时，内存不能通过VM来释放。

在ramfs的下面可以一直写入数据，直到写满内存为止。由于VM(Vitual Memory)认为文件应该被写回后备存储设备，而不是交换空间(swap space)，所以VM不能释放ramfs分配的内存。只有root用户(or trusted user)才能进行ramfs写操作。

由于ramfs可以基于现有的Linux的文件系统结构，所以用于实现ramfs的代码很小。一般而言，后备存储设备的缓存被安装为一个文件系统。所以，ramfs并不是一个可通过菜单配置项来卸载的可选组件。

## 1.2 ramdisk

"RAM disk"是一种实现文件系统的后备存储的、过时的机制(2.6不用了)，就是在一个内存空间中创建一个合成块设备并使用它作为一个文件系统的后备存储。这个块设备是固定大小的以至于挂载在它上面的文件系统也是固定大小的。ram disk的使用需要从这个假的块设备到page cache之间拷贝内存，生成和销毁dentry，而且需要文件系统的驱动格式化和解释上面的数据，所以ram disk的机制不再使用。

与ramfs相比较，ram disk浪费了内存，也浪费了内存总线的带宽。为CPU造成了不必要的工作并污染了CPU的高速缓存。(尽管有避免污染的方法，但是非常耗费资源)。ramfs机制非常自然，因为文件访问可 以通过page cache and dentry cache。ram disk被弃用的另外一个原因是环回设备(loopback)引入。环回设备提供了一种更加灵活、方便的从文件而不是从内存块中创建综合块设备的方法。

更为重要的是，由于所有的文件都通过页和目录缓存进行访问，全部的工作ramfs都要执行。内存磁盘是简单且多余的，ramfs在内部更为简单。

另一个理由是：ramdisks是半过时的，它引进的loop设备提供一个更灵活和方便的方式从文件而不是从大块的内存中来创建一个合成块设备。

参见losetup (8) 来获得有关细节。

r

与ramfs相比，同时，ram disk还为CPU加重了不必要的负担，污染了CPU的cache


RAM磁盘驱动程序是一种将主系统内存用作块设备的方法。它是initrd所必需的，如果您需要加载模块以访问根文件系统，则使用初始文件系统（请参阅Documentation / initrd.txt）。它还可以用于加密工作的临时文件系统，因为内容在重新启动时被擦除。

## 1.3 tmpfs
ramfs的一个不利之处是你将保留写回到ramfs的数据直至你填补所有的内存，并且VM不能释放它，因为VM考虑到文件将写回后备存储(而不是交换空间)，但是ramfs并不能获得任何的后备存储。据此，只有root(或者一个受信任的用户)可允许回写到一个ramfs挂载中。
tmpfs是从ramfs派生出的,添加了大小的限制以及回写数据到交换空间能力的特殊ramfs。普通用户可以允许回写到tmpfs挂载中。
使用tmpfs
$ mount tmpfs /dev/shm -t tmpfs -o size=32m
## 1.4 rootfs
rootfs是一个特定的ramfs(或tmpfs，如果那被启用)的实例，它始终存在于2.6的系统。
你不能卸载rootfs，这个理由近似于你不能杀死init进程。它小巧且简单的为内核确保某些列表不能为空，而不是拥有特定的代码来检查和处理一个空列表。
大多数的系统挂载另一个文件系统到rootfs并忽略它。一个空白ramfs实例的空间总量占用是极小的。
## 1.5 initramfs
### 1.5.1 initramfs是什么
所有的2.6 Linux内核包含了一个gzip压缩过的"cpio"格式存档，当内核启动时它将被提取到rootfs。
在提取之后，内核检测rootfs是否包含了一个"init"文件，如果包含就来执行它并设置PID为1。
这个init进程将负责引导系统的其余内容，包含了要寻找和挂载的真正根设备(若有的话)。
如果rootfs在提取cpio存档以后并不包含一个init程序，内核将失败并通过旧的代码来寻找和挂载一个根分区，接着执行一些/sbin /init的变种。
### 1.5.2 与旧的initrd不同点
旧的initrd通常是一个独立的文件，而initramfs存档是连接到linux内核映像的。(Linux系统目录-*/usr 在构建时就是为了生成这个存档)
旧的initrd文件是一个gzip压缩过的文件系统映像(在一些文件格式，如ext2,需要构建一个驱动到内核)，而这个新的initramfs存档是一个gzip压缩过的cpio存档(象tar一样简单，参见cpio(1) and Documentation/early-userspace/buffer-format.txt)。内核的cpio提取代码不仅极少，而且init文本和数据可以在启动过程中被丢弃。
程序通过旧的initrd(叫做/initrd，而不是/init)运行一些设定并接着返回内核，而init程序从initramfs并不预计返回内核。(如果/init需要手工关闭并控制它，可以与一个新的根设备一起覆盖挂载 / 并执行另一个init程序。参见switch_root utility的后部分)
当切换到另一个根设备，initrd会pivot_root并且接着卸载内存磁盘。但是initramfs是rootfs：你可以不用 pivot_root rootfs和卸载它。而是删除除rootfs之外的所有东西来释放空间(find -xdev / -exec rm '{}' ';'),与新的根(cd /newmount; mount --move . /; chroot .)一起覆盖挂载rootfs,附加stdin/stdout/stderr到新的/dev/console并执行新的init。
由于这是一个非常谨慎的进程(以及在你运行它们之前涉及到删除命令)，klibc包为你引进了一个帮助程序(utils/run_init.c)来做这些。大部分其他软件包(如，busybox)已命名在此命令"switch_root"中。

### 1.5.3 提供 initramfs
v2.6 内核构建进程总是创建一个gzip压缩过的cpio格式的initramfs存档并连接它到生成的内核二进制文件。默认的，这些存储是空的(在x86上占用134 bytes)
配置选项CONFIG_INITRAMFS_SOURCE(存在于menuconfig的General Setup选项和usr/Kconfig中)可为initramfs存档使用特定的一个源文件，它自动合并到生成的二进制文件中。这个选项可以指定一个现有的gzip压缩的cpio存档，这个存档可以包含一个目录文件到或者是一个文本文件详述，如下面的示例:
dir /dev 755 0 0
nod /dev/console 644 0 0 c 5 1
nod /dev/loop0 644 0 0 b 7 0
dir /bin 755 1000 1000
slink /bin/sh busybox 777 0 0
file /bin/busybox initramfs/busybox 755 0 0
dir /proc 755 0 0
dir /sys 755 0 0
dir /mnt 755 0 0
file /init initramfs/init.sh 755 0 0
在文件格式之上运行"usr/gen_init_cpio"(在构建内核之后)来获得一个用法文档。
一个高级的配置文件是root访问不需要在新的存档上设置权限或者创建设备节点。(注意示例的那两个"file"期望在linux-2.6.*目录下，在"initramfs"目录中找到名为"init.sh"和"busybox"的文件。参见Documentation/early- userspace/README 以获得更多细节。)
内核并不依赖外部的cpio工具。如果你指定一个目录而不是一个配置文件，内核的构建结构将从那个目录(usr/Makefile 调用
scripts/gen_initramfs_list.sh)创建一个配置文件，并封装该目录来使用配置文件(通过释放它到从 usr/gen_init_cpio.c 创建的 usr/gen_init_cpio)。在内核构建时cpio创建的代码是完全独立的，在内核开启时提取器也是(完全)独立的。

你唯一可能需要为创建或提取你自己的cpio文件以提供给内核构建(而不是一个配置文件或目录)而安装外部的cpio工具。
以下的命令行可提取一个cpio映像(不管是通过以上脚本或是通过内核构建成的)回退到它的组成文件：
    cpio -i -d -H newc -F initramfs_data.cpio --no-absolute-filenames
以下的shell脚本可创建一个预制的cpio存档，你可以使用在上述的配置文件中：
#!/bin/sh

# Copyright 2006 Rob Landley  and TimeSys Corporation.
# Licensed under GPL version 2

if [ $# -ne 2 ]
then
    echo "usage: mkinitramfs directory imagename.cpio.gz"
    exit 1
fi

if [ -d "$1" ]
then
    echo "creating $2 from $1"
    (cd "$1"; find . | cpio -o -H newc | gzip) > "$2"
else
    echo "First argument must be a directory"
    exit 1
fi
注意：如果你是按照cpio的man页做的，那么它包含的一些不良建议将会破坏你的initramfs存档。它说"生成文件名列表的一个典型方式是使用 find命令，你应该提供-depth选项来搜索"。而在创建initramfs.cpio.gz映像时不要这么做，因为它并不可行。Linux内核的 cpio提取器不会在一个不存在的目录中创建文件，因此目录项必须在文件被提取到该目录之前被提取。以上脚本保证了它们的顺序正确性。
1.5.4. 外部initramfs映像
如果内核有启用initrd支持，一个外部的cpio.gz存档也可替换一个2.6 内核的initrd。既然这样，内核将自动检测文件类型(initramfs, 而不是initrd)并在尝试运行/init前，提取外部的cpio存档到rootfs。
initramfs有内存效率的优势(没有内存磁盘块设备)，而initrd可单独封装(如果你有非GPL许可协议的代码，而你想从initramfs运行，这还是不错的。不要与GPL许可的二进制的Linux内核混为一谈)。
外部initramfs映像也可以用来补充内核内建的initramfs映像。外部存档文件将覆盖与内建initramfs存档中任意不一致的文件。一些发行商也偏爱定制一个单一内核映像和特定initramfs映像，而不用重编译。
1.5.5. initramfs的内容
一个initramfs存档是一个完整独立的Linux根文件系统。如果你还不能理解共享库、设备和路径，你需要获得一个小的根文件系统来启动和运行，这里有一些参考资料：
http://www.tldp.org/HOWTO/Bootdisk-HOWTO/
http://www.tldp.org/HOWTO/From-PowerUp-To-Bash-Prompt-HOWTO.html
http://www.linuxfromscratch.org/lfs/view/stable/
软件包"klibc"(http://www.kernel.org/pub/linux/libs/klibc)被设计为一个极小的C库来静态连接早期的用户空间代码，以及一些相关的工具。它是BSD授权。
我使用uClibc (http://www.uclibc.org)和busybox (http://www.busybox.net)。这些分别是LGPL和GPL授权。(一个独立的initramfs软件包被计划在busybox 1.3 release。)
从理论上说你可以使用glibc，但是对于小型嵌入式系统象这样使用并适合。(一个"hello world"程序静态连接glibc将近400k。用uClibc的话它是7k。还要注意，glibc dlopens libnss作为名称查找，即使是使用静态连接。)
一个好的开头是获得initramfs来运行一个静态连接的"hello world"程序作为init，并在一个类似qemu(www.qemu.org)的仿真器或用户模式的Linux中测试它，象这样：
cat > hello.c << EOF
#include 
#include 

int main(int argc, char *argv[])
{
    printf("Hello world!/n");
    sleep(999999999);
}
EOF
gcc -static hello.c -o init
echo init | cpio -o -H newc | gzip > test.cpio.gz
# Testing external initramfs using the initrd loading mechanism.
qemu -kernel /boot/vmlinuz -initrd test.cpio.gz /dev/zero
当调试一个普通根文件系统时，能够用"init=/bin/sh"来启动将是不错的。与initramfs等价的是"rdinit=/bin/sh"，而且它很有用。
1.5.6. 为什么是cpio而不是tar?
这个决定的发布是在2001年12月。讨论浏览在这里:
http://www.uwsg.iu.edu/hypermail/linux/kernel/0112.2/1538.html
并且催生了第二件事情(tar与cpio的比较)，浏览这里
http://www.uwsg.iu.edu/hypermail/linux/kernel/0112.2/1587.html
快速且直接的总结版本(这些是无法替代上述内容的阅读的)：
cpio是一个标准。它十来岁了(从 AT&T起)，并已被广泛应用在Linux上(在RPM中，Red Hat的设备驱动磁盘上)。这里有从1996年起有关它的Linux日志：
http://www.linuxjournal.com/article/1213
它并没有象tar一样流行，因为传统的cpio命令行工具要求使用极可怕的命令行。同时没有其他有关存档格式的方式，并有可选的工具。如:
http://freshmeat.net/projects/afio/
cpio存档格式是由内核选择的，它比(许多国家的)任意的tar存档格式都简单且干净(并极容易来创建和解析)。完整的initramfs存档格式说明在buffer-format.txt，创建在usr/gen_init_cpio.c，并提取在init/initramfs.c。所有三个可阅读的文本一起不到26k。
GNU项目使用tar标准似乎是与Windows使用zip的标准相关。Linux并不是它们中任一一个，并自由的作出了自己的技术决策。
由于这是一个内核的内部格式，它可以很容易做一些更新。内核提供它自己的工具来创建和提取这个格式。使用现有的标准是可取的，但不是必要的。
Al Viro 作出决定(引用: "tar is ugly as hell and not going to be supported on the kernel side"):
http://www.uwsg.iu.edu/hypermail/linux/kernel/0112.2/1540.html
解释它的理由：
http://www.uwsg.iu.edu/hypermail/linux/kernel/0112.2/1550.html
http://www.uwsg.iu.edu/hypermail/linux/kernel/0112.2/1638.html
最重要的是，设计和实现initramfs的代码。
1.5.7. 发展的方向
当前(2.6.16), initramfs总被编译进，但并没总被使用。内核做到回退到原来的启动代码只需initramfs中不包含一个/init程序。回退
是原来的代码，以此确保一个平滑的过渡并允许尽早的开启功能逐步移动到"前期用户空间"(即initramfs)。

移动到前期用户空间是必要的，因为寻找和挂载真正的根设备是复杂的。根分区可以跨多个设备。它们可在网络上进行(要求dhcp，设定一个特定MAC地址，登录到一台服务器等)。它们可用在可移动媒体，动态分配主/次号以及要求udev执行清理的持续命名的问题。它们可被压缩、加密、写时拷贝，回环挂载、异常分区等。
这种复杂性(必将包含的策略)是在用户空间的处理的正确性。klibc和busybox/uClibc两者一起在简单的initramfs软件包中工作以组成一个内核构建。
软件包klibc现在已被Andrew Morton's 2.6.17-mm 接受到树中。内核当前的提前启动代码(分区检测等)可能会迁移到默认的initramfs中，由内核构建自动创建并使用。
2. romfs
romfs 是只读的文件系统,本文不讨论它,由于它与ramfs写法很相近,所以在此列出它,可以参考外部链接:
http://wxpjiujiang.blog.163.com/blog/static/203994030201222295944637/

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
