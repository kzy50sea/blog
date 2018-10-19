---
title: Linux内核配置与编译
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>《Linux内核官方文档》。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------



# 1 准备工作

## 1.1 获取并解压内核源码
```
$tar -xvJf linux-x.x.x.tar.xz
$cd linux-x.x.x.tar.xz
```
## 1.2 检查硬件环境
```
$cat /proc/cpuinfo
$ls pci
```
## 1.3 获取干净的源码
* `$make clean`—— 删除大多数生成的文件，但保留配置和足够的构建支持以构建外部模块
* `$make mrproper` —— 删除所有生成的文件+ config +各种备份文件
* `$make distclean` —— `$make mrproper` +删除编辑器备份文件和补丁文件

# 2 配置和编译内核
## 2.1 配置内核
* `make [ARCH=xxx] xxxconfig`，配置内核，每个选项要么是二选一yes或no，要么是三选一yes、no、module。yes表示把代码编译进内核映像，module表示以模块方式编译。
* 常见配置目标
 * 手动配置 
     * `$ make [ARCH=xxx] config`——基于文本的配置界面
     * `$ maken[ARCH=xxx] nconfig`——基于ncurses的配置界面
     * `$ maken[ARCH=xxx] menuconfig`——基于菜单程序的配置界面
     * `$ maken[ARCH=xxx] xconfig`——使用基于Qt的配置界面
     * `$ maken[ARCH=xxx] gconfig`——使用基于GTK+的配置界面
 * 快速配置
     * `$ maken[ARCH=xxx] oldconfig`——使用之前配置好的.config作为基础更新当前配置
     * `$ maken[ARCH=xxx] defconfig`——使用ARCH提供的defconfig默认配置
     * `$ maken[ARCH=xxx] tinyconfig`——配置最小的内核

* 补充
 * 内核配置好后，配置项存放在源代码内核源码根目录的`.config`文件中，可以直接修改该文件，修改后应该更新用`$ make oldconfig`更新配置。
 * 选项`CONFIG_IKCONFIG_PROC`把压缩过的内核配置文件放在`/proc/config.gz`中，如果当前内核启用了该选项，则可以直接克隆该配置。
 * 如果不想在命令行输入ARCH=XXX，则可以直接在顶层Makefile中进行修改。

##2.2 编译内核与模块 

* `$make [ARCH=xxx] [编译目标][编译选项与参数]`，编译内核，2.6版本之前每次编译需要运行`$make dep`。 
 * 如果想减少编译时的垃圾信息可以使用
     * `$make XXXX > 文件名` ： 把编译细节、警告、错误输出到指定文件。
     *  `$make XXXX > /dev/null`：不记录任何编译信息
 *  如果不想在命令行输入ARCH=XXX和CROSS_COMPILE=XXX，则可以直接在顶层Makefile中进行修改。
 *  如果想编译快一些，可以使用-jn选项，n为cpu个数
* 常见编译目标
 * all
 * vmlinux 
 * Image
 * zImage/bzImage
 * modules

#3 安装内核
* `$make modules_install`，安装模块
* 安装内核
 * 自动安装
     *  `$make install`
 *  手动安装
     *  拷贝
     *  建立
     *  设置



#4 内核配置及编译过程详解
## 4.1 内核配置过程详解
* Linux内核的配置系统由三个部分组成，分别是：

 * `Makefile`——分布在 Linux 内核源代码根目录及各层目录中，定义 Linux 内核的编译规则；
 * `Kconfig`——分布在 Linux 内核源代码根目录及各层目录中，给用户提供配置选择的功能；
 * `scripts`——分布在 Linux内核源代码根目录下，包括配置命令解释器（对配置脚本中使用的配置命令进行解释）和配置用户界面（提供基于字符界面、基于 Ncurses 图形界面以及基于 Xwindows 图形界面的用户配置界面，各自对应于 Make config、Make menuconfig 和 make xconfig）。


* 当我们使用`make menuconfig`这个命令时（其它配置命令类似）：

 * 首先由make编译生成`scripts/kconfig/mconf.c`生成`scripts/kconfig/mconf`。（xconfig对应qconf，gconfig对应gconf，config对应conf）
 * 然后执行`scripts/kconfig/mconf Konfig`
 * mconf程序读取内核根目录下的Kconfig文件，Kconfig载入了`arch/$SRCARCH/Kconfig`，`arch/$SRCARCH/Kconfig`又分别载入各目录下的Kconfig文件，以此递归下去，最后生成主配置界面以及各级配置菜单。`$SRCARCH`是由顶层Makefile中定义的，它等于`$ARCH`，而`$ARCH`由Makefile或make的命令行参数指定。
 * 在完成配置后，mconf会将配置保存在Linux内核源代码根目录下的`.config`文件中。

* 当我们使用`make defconfig`这个命令时：
 * 系统直接将`arch/$SRCARCH/configs`（**该目录存放内核的默认配置文件**）下的对应的默认配置文件拷贝到Linux内核源代码根目录下的`.config`文件。


## 4.2 内核编译过程详解
* 在输入编译命令后，make首先调用脚本来读取`.config`文件，并根据内容载入对应文件到`include/config/`，并将一些配置项写入`include/config/auto.conf`。
* 脚本程序将`include/config/auto.conf`中的配置项`CONFIG_XXXX=y|m|xxx`翻译为宏定义`#define CONFIG_XXXX[_MODULE] 1|xxx`，并写入`include/generate/autoconf.h`中。
* autoconf.h就是`include/config/auto.conf`中的配置项的内容的C语言写法，以便在以后使用的时候作为宏定义出现，以实现条件编译。
* make根据Makefile执行编译。

#5 在内核中添加程序
* 将源代码拷贝到内核源码的相应目录
* 修改对应目录下的Kconfig文件，按照Kconfig语法增加对应的选项；
* 修改对应目录下的Makefile文件完成编译选项的添加`obj-$(CONFIG_symbol)+= filename.o`；



---

&emsp;&emsp;***<font color=blue>版权声明</font>***：*本文章参考了<font color=blue >**《Linux内核官方文档》。**</font><font color=red>未经作者允许，**<font color=blue>严禁用于商业出版</font>**，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>*

---


 

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
