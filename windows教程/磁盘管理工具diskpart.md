---
title: 磁盘管理工具diskpart
tags: windows教程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1 简介
&emsp;&emsp;diskpart可实现对硬盘的分区管理，包括创建分区、删除分区、合并（扩展）分区，完全可取代分区魔术师等第三方工具软件，它还有分区魔术师无法实现的功能，如设置动态磁盘、镜像卷等，而且设置分区后不用重启电脑也能生效。只不过它运行于命令提示符下。

# 2 运行环境
&emsp;&emsp;Diskpart是Windows环境下的一个命令，正常运行该命令时需要系统服务的支持，这几个服务是：Logical Disk Manager Administrative Service(dmadmin)、Logical Disk Manager(dmserver)、Plug and Play(PlugPlay)、Remote Procedure Call (RPC) (RPCss)。而这四个服务的依存关系为：dmserver依赖于PlugPlay和RPCss，dmadmin/依赖于dmserver。
&emsp;&emsp;如果这四个服务没有运行，那么是不可以成功运行Diskpart的，所以在纯DOS下面是不能够运行这个命令的,但在WINPE下是可以运行DISKPART的，现在很多ODM厂商在安装操作系统时都采用WINPE环境，就是因为在WINPE下可方便的对硬盘进行操作(WINPE环境本身启动在内存中)
# 3 启动
&emsp;&emsp;在命令提示符窗口，再输入“Diskpart”即可启动它，此时屏上显示为“Diskpart>”，不像普通的命令提示符那样是一个分区或目录后跟一个“>”，而且普通的DOS命令也无法在此运行，退出它只有输入“Exit”命令。从这三种迹象表明，它是一个集成的环境，只有特定的命令可在其下执行。输入“Help”命令，屏上会列出所有的可执行命令及各命令的简要说明。

# 4 命令详解

ACTIVE      - 将选中的分区标记为活动的分区。
ADD         - 将镜像添加到一个简单卷。
ASSIGN      - 给所选卷分配一个驱动器号或装载点。
ATTRIBUTES  - 操纵卷或磁盘属性。
ATTACH      - 连接虚拟磁盘文件。
AUTOMOUNT   - 启用和禁用基本卷的自动装载。
BREAK       - 中断镜像集。
CLEAN       - 从磁盘清除配置信息或所有信息。
COMPACT     - 尝试减少文件的物理大小。
CONVERT     - 在不同的磁盘格式之间转换。
CREATE      - 创建卷、分区或虚拟磁盘。
DELETE      - 删除对象。
DETAIL      - 提供对象详细信息。
DETACH      - 分离虚拟磁盘文件。
EXIT        - 退出 DiskPart。
EXTEND      - 扩展卷。
EXPAND      - 扩展虚拟磁盘上可用的最大大小。
FILESYSTEMS - 显示卷上当前和支持的文件系统
FORMAT      - 格式化卷或分区
GPT         - 给选择的 GPT 分区分配属性。
HELP        - 显示命令列表。
IMPORT      - 导入磁盘组。
INACTIVE    - 将所选分区标为不活动。
LIST        - 显示对象列表。
MERGE       - 将子磁盘与其父磁盘合并。
ONLINE      - 使当前标为脱机的对象联机。
OFFLINE     - 使当前标记为联机的对象脱机。
RECOVER     - 刷新所选包中所有磁盘的状态。
              尝试恢复无效包中的磁盘，并
              重新同步具有过时丛或奇偶校验数据
              的镜像卷和 RAID5 卷。
REM         - 不起任何作用。用来注释脚本。
REMOVE      - 删除驱动器号或装载点分配。
REPAIR      - 用失败的成员修复一个 RAID-5 卷。
RESCAN      - 重新扫描计算机，查找磁盘和卷。
RETAIN      - 在一个简单卷下放置一个保留分区。
SAN         - 显示或设置当前启动的操作系统的 SAN 策略。
SELECT      - 将焦点移动到对象。
SETID       - 更改分区类型。
SHRINK      - 减小选定卷。
UNIQUEID    - 显示或设置磁盘的 GUID 分区表(GPT)标识符或
              主启动记录(MBR)签名。


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------