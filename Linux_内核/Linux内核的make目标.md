---
title: Linux内核的make目标
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1 清理目标
* `$make clean`—— 删除大多数生成的文件，但保留配置和足够的构建支持以构建外部模块
* `$make mrproper` —— 删除所有生成的文件+ config +各种备份文件
* `$make distclean` —— `$make mrproper` +删除编辑器备份文件和补丁文件

# 2 配置目标
* 手动配置
 * `$ make [ARCH=xxx] config`——基于文本的配置界面
 * `$ maken[ARCH=xxx] nconfig`——基于ncurses的配置界面
 * `$ maken[ARCH=xxx] menuconfig`——基于菜单程序的配置界面
 * `$ maken[ARCH=xxx] xconfig`——使用基于Qt的配置界面
 * `$ maken[ARCH=xxx] gconfig`——使用基于GTK+的配置界面
* 快速配置
 * `$ maken[ARCH=xxx] oldconfig`——使用之前配置好的.config作为基础更新当前配置
 * `$ maken[ARCH=xxx] localmodconfig `——更新当前配置禁用未加载的模块
 * `$ maken[ARCH=xxx] localyesconfig`——更新当前配置并将模块转为内核改为内核内建
 * `$ maken[ARCH=xxx] deconfig`——使用ARCH提供的defconfig默认配置
 * `$ maken[ARCH=xxx] savedefconfig `——将当前系统的内核配置保存为./defconfig（最小配置）
 * `$ maken[ARCH=xxx] allnoconfig`——新配置，其中所有选项均设置为no
 * `$ maken[ARCH=xxx] allyesconfig`——新配置，其中所有选项均设置为yes
 * `$ maken[ARCH=xxx] allmodconfig`——新配置，其中所有选项均尽可能设置为module
 * `$ maken[ARCH=xxx] alldefconfig`——新配置，其中所有选项均设置为默认值
 * `$ maken[ARCH=xxx] randconfig`——新配置，其中所有选项均设置为随机值
 * `$ maken[ARCH=xxx] listnewconfig`——列出新选项
 * `$ maken[ARCH=xxx] olddefconfig`——与oldconfig相同，但在没有提示的情况下将新符号设置为其默认值
 * `$ maken[ARCH=xxx] kvmconfig`——为kvm来宾内核支持启用其他选项
 * `$ maken[ARCH=xxx] xenconfig`——为xen dom0和来宾内核支持启用其他选项
 * `$ maken[ARCH=xxx] tinyconfig`——配置最小的内核



------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------