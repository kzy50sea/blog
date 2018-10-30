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
* HELP [COMMAND]——显示帮助信息
	* COMMAND——  要显示其详细帮助的命令。 如果未指定命令，HELP 将显示所有可能的命令。示例:`HELP CREATE PARTITION PRIMARY`

* LIST DISK|PARTITION|VOLUME|VDISK——显示对象列表
	* DISK —— 显示磁盘列表。例如，`LIST DISK`。
	* PARTITION——显示所选磁盘上的分区列表。例如，`LIST PARTITION`。
	* VOLUME ——显示卷列表。例如，`LIST VOLUME`。
	* VDISK——显示虚拟磁盘列表。

* SELECT DISK|PARTITION|VOLUME|VDISK n —— 选中对象
	* DISK——将焦点移动到磁盘。例如，`SELECT DISK`。
	* PARTITION——将焦点移动到分区。例如，`SELECT PARTITION`。
	* VOLUME——将焦点移动到卷。例如，`SELECT VOLUME`。
	* VDISK——将焦点转移到虚拟磁盘。例如，`SELECT VDISK`。
> partition与volume区别：一个volume可以有多个partition。partition是物理的概念，偏向设备；volume是逻辑的概念，偏向文件系统。

* CLEAN [ALL]——清除选定磁盘的所有数据
	* 指定将当前焦点的磁盘上的每个字节\扇区都设置为 0，这会完全删除该磁盘上包含的所有数据。在主启动记录(MBR)磁盘上，仅会覆盖 MBR 分区信息和隐藏的扇区信息。在 GUID 分区表(GPT)磁盘上，包括保护性 MBR 在内的 GPT 分区信息都会被覆盖。如果不使用 ALL 参数， 则磁盘的第一个 1MB 和最后一个 1MB 都将为零。这将擦除以前应用到该磁盘的任何格式化磁盘。清除该磁盘后的磁盘状态为 'UNINITIALIZED'。

* DELETE DISK|PARTITION|VOLUME——删除指定对象
	* DISK——从磁盘列表删除遗失的磁盘。
	* PARTITION——删除所选分区。
	* VOLUME —— 删除所选卷。
	
* DETAIL  DISK|PARTITION|VOLUME|VDISK ——显示指定对象的详细信息
	* DISK—— 显示选中的磁盘的属性。
	* PARTITION ——显示选中的分区的属性。
	* VOLUME ——显示选中的卷的属性。
	* VDISK —— 显示所选虚拟磁盘的属性。

*  CREAT PARTITION|VOLUME|VDISK——创建指定对象
	*  CREAT PARTITION ——创建分区
		* CREAT PARTITION PRIMARY [SIZE=N] [OFFSET=N] [ID={BYTE|GUID}] [ALIGN=N] [NOERR]——创建主分区
			*  SIZE=N    分区大小，以兆字节(MB)为单位。如果 未给定大小，则会继续创建分区，直到当前区域中没有 更多未分配的空间。
			*  OFFSET=N 创建分区所处的偏移量，以千字节(KB)为单位。如果未给定偏移量，则将分区放置在有足够空间容纳它的第一个磁盘盘区中。
			*  ID={BYTE | GUID}指定分区类型。仅适合原始设备制造商(OEM)使用。对于主启动记录(MBR)磁盘，可以为分区指定十六进制形式的 分区类型字节。如果未为 MBR 磁盘指定此参数，该命令将创建类型为 0x06 (指定不安装任何文件系统)的分区。
				*   LDM数据分区:0x42
				*   恢复分区:0x27
				*   识别的 OEM ID: 0x12、 0x84、0xDE、 0xFE、  0xA0
				*   对于 GUID 分区表(GPT)磁盘，可以为要创建的分区指定分区类型 GUID。已识别的 GUID 包括:
					*   EFI 系统分区: c12a7328-f81f-11d2-ba4b-00a0c93ec93b
					*   Microsoft 保留分区: e3c9e316-0b5c-4db8-817d-f92df00215ae
					*   基本数据分区:ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
					*   动态磁盘上的 LDM 元数据分区:5808c8aa-7e8f-42e0-85d2-e1e90434cfb3
					*   动态磁盘上的 LDM 数据分区:af9b60a0-1431-4f62-bc68-3311714a69ad
					*   恢复分区:de94bba4-06d1-4d40-a16a-bfd50179d6ac
				*   如果未对 GPT 磁盘指定此参数，该命令将创建基本数据分区。
				*   可以使用此参数指定任何分区类型字节或 GUID。DiskPart 不会检查分区类型是否有效，只是确保此分区类型为十六进制形式字节或 GUID。
				*   警告: 使用此参数创建分区可能导致 计算机发生故障或无法启动。除非你是 OEM 或熟悉 GPT 磁盘的IT 专业人员，否则不要使用此参数在 GPT 磁盘上创建分区。而是应该在 GPT 磁盘上使用 CREATE PARTITION EFI 命令创建 EFI 系统分区，使用 CREATE PARTITION MSR命令创建 Microsoft 保留分区，使用不带此参数的 CREATE PARTITION PRIMARY 命令创建主分区。
			*   ALIGN=N   通常与硬件 RAID 逻辑单元号(LUN)阵列一同使用来提高性能。分区偏移将是 N 的倍数。如果指定了 OFFSET 参数，则将四舍五入为最接近的 N的倍数。
			*   NOERR 仅用于脚本。遇到错误时，DiskPart会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回错误代码。
			*   注意：创建分区之后，焦点会自动移动到新分区。该分区未接收驱动器号。必须使用 assign 命令为该分区分配一个驱动器号。必须选择一个基本磁盘才能成功执行此操作。 如果未指定分区类型，磁盘未初始化且磁盘大小大于 2TB，则该磁盘将初始化为 GPT。	
		* CREAT PARTITION EFI   [size=n] [offset=n]  [NOERR]——创建EFI系统分区。创建分区之后，焦点会自动移动到新分区，只用于GPT磁盘。
		* CREAT PARTITION EXTENDED [size=n] [offset=n]  [ALIGN=n] [NOERR]——创建扩展分区。创建分区之后，焦点会自动移动到新分区，只用于MBR磁盘,每个磁盘只能有一个扩展分区。
		* CREAT PARTITION LOGICAL [size=n] [offset=n]  [NOERR] —— 创建逻辑驱动器。创建分区之后，焦点会自动移动到新分区，只用于MBR磁盘。
		* CREAT PARTITION MSR      [size=n] [offset=n]  [NOERR] —— 创建 Microsoft 保留分区。使用 create partition msr 命令时要小心。GPT 磁盘要求特定的分区布局，因此创建 Microsoft 保留分区可能导致磁盘不可读。在用于启动 Windows XP 64 位版本或 Windows Server 2003 家族 64 位版本的 GPT 磁盘上，EFI 系统分区是磁盘上的第一个分区，随后是 Microsoft 保留分区。仅用来储存数据的 GPT 磁盘没有 EFI 系统分区。Microsoft 保留分区是第一个分区。 
	* CREAT VOLUME——创建卷。
		* CREAT VOLUME RAID  [SIZE=N] DISK=N,N [ALIGN=N] [NOERR]——创建软件 RAID-5 卷集。
			* SIZE=N该卷在每个磁盘上占用的磁盘空间量， 单位为兆字节(MB)。如果未给定大小，新卷将占据较小磁盘上的剩余可用空间，并在其他磁盘上占据与此相同的空间量。
			* DISK=N,N 在其上创建镜像卷的动态磁盘。需要两个动态磁盘才能创建镜像卷。每个磁盘上分配等于 SIZE=N中指定的大小的空间量。
			* ALIGN=N   通常与硬件 RAID 逻辑单元号(LUN)阵列一起使用以提高性能。将所有的卷扩展到与最近的对齐边界对齐。扩展偏移将是 N 的倍数。
			* NOERR 仅用于脚本。遇到错误时，DiskPart会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回错误代码。
		* CREAT VOLUME SIMPLE    [SIZE=N] DISK=N,N [ALIGN=N] [NOERR] ——创建简单卷。
		* CREAT VOLUME STRIPE    [SIZE=N] DISK=N,N [ALIGN=N] [NOERR]——创建带区卷集。
		* CREAT VOLUME MIRROR [SIZE=N] DISK=N,N [ALIGN=N] [NOERR]——创建镜像卷集。			
	* CREAT VDISK  FILE="filename" MAXIMUM=N [TYPE={FIXED|EXPANDABLE}] [SD="SDDL string"] [PARENT="filename"] [SOURCE="filename"] [NOERR]——创建虚拟磁盘
		* FILE="filename" 指定虚拟磁盘文件的完整路径和文件名。该文件可以位于网络共享上。
		*  MAXIMUM=N 虚拟磁盘公开的最大空间量，以兆字节(MB)为单位。
		*  TYPE={FIXED|EXPANDABLE}
			*  FIXED 指定固定大小的虚拟磁盘文件。
			*  EXPANDABLE指定重新调整大小以适应所分配数据的 虚拟磁盘文件。默认值为 FIXED。
		*   [SD="SDDL string"]指定安全描述符定义语言(SDDL)格式的安全描述符。默认情况下，安全描述符是从父目录中获取的。SDDL 字符串可能比较复杂，但很灵活。 其最简单的格式是保护访问的安全描述符，称为自定义访问控制列表(DACL)。其格式为:`D:<DACL_FLAGS>(<STRING_ACE>)(<STRING_ACE>)...(<STRING_ACE>)`
			*  DACL_FLAGS 为:
				*   "P" - DACL 不应被来自父容器的任何 ACL 所覆盖(保护)。VHD 或 VHDX 文件的容器就是 其目录。
				*   "AI"- DACL 应该从父容器自动继承。
			*  STRING_ACE 的格式为 `<ACE_TYPE>;;<RIGHTS>;;;<ACCOUNT_ID>`
				* 常用的 ACE_TYPE 为:
					*   "A" - 允许访问。
					* "D" - 拒绝访问。
				* 常用的 RIGHTS 为:
					* "GA" - 所有访问权限。
					* "GR" - 读取访问权限。
					* "GW' - 写入访问权限。
				* 常用的 ACCOUNT_ID 为:
					* "BA" - 内置管理员
					*  "AU" - 通过身份验证的用户。
					*  "CO" - 创建者所有者。
					*  "WD" - 任何人。
		*  [PARENT="filename"] 用于创建差异磁盘的现有父虚拟磁盘文件的路径。 对于 PARENT 参数，不应指定 MAXIMUM， 因为差异磁盘从其父目录获取大小。 而且，也不应指定 TYPE，因为只能创建 EXPANDABLE 差异磁盘。
		*   [SOURCE="filename"]用于预填充新虚拟磁盘文件的现有虚拟磁盘文件的路径。 当指定 SOURCE 时，输入虚拟磁盘文件中的数据 将从输入虚拟磁盘文件逐块复制到创建的 虚拟磁盘文件。但不建立父子关系。

* ACTIVE
	* 在格式化主启动记录(MBR)磁盘的磁盘上，将带焦点的分区标记为活动分区。将一个值写入启动时由基本输入输出系统(BIOS)读取的磁盘。该值指定分区是有效的系统分区。必须选择一个分区才能成功执行此操作。
	* 警告: DiskPart 仅验证该分区是否能够包含操作系统启动文件。 DiskPart 不会检查分区中的内容。如果错误地将某个分区标记为活动分区，而该分区不包含 操作系统启动文件，则计算机可能无法启动。

* ADD DISK=N [ALIGN=N] [WAIT] [NOERR]——将带焦点的简单卷镜像到指定的磁盘。
	* DISK=N   指定一个含有现有简单卷的磁盘以外的磁盘来包含镜像。 可以仅镜像简单卷。指定的磁盘必须具有不低于要镜像的简单卷大小的未分配空间。
	* ALIGN=N   通常与硬件 RAID 逻辑单元号(LUN)阵列一起使用以提高性能。 将所有卷范围与最近的对齐边界对齐。范围偏移将为 N 的倍数。
	*  WAIT返回前，等待卷完成与添加的磁盘的同步。如果不使用 wait
                参数，DiskPart 会在创建镜像卷后返回，而不等待同步完成。
	* NOERR       仅用于脚本。遇到错误时，DiskPart 会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回 错误代码。
	* 必须选择一个卷才能成功执行此操作。Windows Vista 的任何版本都不支持容错卷(如 RAID-5)和镜像卷。

* ASSIGN [LETTER=D | MOUNT=PATH] [NOERR]——给所选卷分配一个驱动器号或装载点
	* LETTER=D  分配给卷的驱动器号。
	* MOUNT=PATH分配给卷的装入文件夹的路径名称。
	* NOERR       仅用于脚本。遇到错误时，DiskPart 会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回错误代码。
	* 如果没有指定驱动器号或装入的文件夹，则会分配下一个可用的驱动器号。如果驱动器号或装入的文件夹已经在使用，则会生成一个错误。通过使用 ASSIGN 命令，你可以更改与可移动驱动器关联的 驱动器号。
   * 你无法为启动卷或含有页面文件的卷分配驱动器号。此外，你无法为原始设备制造商(OEM)分区(除非启动到 Windows PE)或除基本数据分区以外的任何 GUID 分区表(GPT)分区、ESP 分区或恢复分区分配驱动器号。必须选择一个卷才能成功执行此操作。
* ATTRIBUTES——操纵卷或磁盘属性。
	* ATTRIBUTES DISK [SET | CLEAR] [READONLY] [NOERR]
		* SET         设置所选磁盘的指定属性。
		* CLEAR       清除所选磁盘的指定属性。
		* READONLY    指定磁盘为只读磁盘。
		* NOERR       仅用于脚本。遇到错误时，DiskPart会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回错误代码。
		*  显示当前的磁盘标志。READONLY 当前是可以进行更改的唯一磁盘标志。BOOT DISK 属性表示用于启动的磁盘。对于动态镜像卷，为包含镜像启动卷的启动丛的磁盘显示 BOOT DISK 属性。
		*  不带参数显示所有属性
	* ATTRIBUTES VOLUME [SET | CLEAR] [HIDDEN | READONLY | NODEFAULTDRIVELETTER | SHADOWCOPY] [NOERR]
		*  SET 为所选卷设置指定属性(HIDDEN、READONLY 和NODEFAULTDRIVELETTER 或 SHADOWCOPY)。
		*  CLEAR       从所选卷清除指定属性(HIDDEN、READONLY、NODEFAULTDRIVELETTER 或 SHADOWCOPY)。
		*  HIDDEN      指定卷为隐藏卷。
		*  READONLY    指定卷为只读卷。
		*  NODEFAULTDRIVELETTER 指定默认情况下卷不接收驱动器号。
		*  SHADOWCOPY  指定卷为卷影副本卷。
		*  NOERR       仅用于脚本。出现错误时，DiskPart继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart 退出并返回错误代码。
		*  在基本主启动记录(MBR)磁盘上，HIDDEN、READONLY 和 NODEFAULTDRIVELETTER 属性适用于该磁盘上的所有卷。在基本 GUID 分区表(GPT)磁盘以及动态 MBR 和 GPT磁盘上，HIDDEN、READONLY 和 NODEFAULTDRIVELETTER 属性仅适用于所选卷。
		*  不带参数显示所有属性
* ATTACH VDISK [NOERR] [READONLY] { [SD="SDDL string"] | [USEFILESD] }
	* NOERR       仅用于脚本。遇到错误时，DiskPart会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart退出并返回错误代码。
	*  READONLY    以只读方式连接虚拟磁盘。任何写入操作都将返回输入/输出设备错误。
	*  SD="SDDL string" 以安全描述符定义语言(SDDL)格式指定安全描述符。 默认情况下，安全描述符号允许访问任何物理磁盘等内容。有关 SDDL 的详细信息，请参阅 CREATE VDISK 命令。
	* USEFILESD   指定应该在虚拟磁盘上使用虚拟文件自身上的安全描述符号。如果不指定，则磁盘将没有显式安全描述符，除非使用 SD=(SDDL string)指定。必须选择虚拟磁盘才能成功完成此操作。
*  AUTOMOUNT [ENABLE] [DISABLE] [SCRUB] [NOERR]——启用或禁用自动装载功能
	*  ENABLE      使 Windows 可以自动将驱动器号分配给已添加到系统的卷。
	*  DISABLE     防止 Windows 自动将驱动器号分配给已添加到系统的卷。
	*  SCRUB       删除不再位于系统中的卷的装入文件夹路径名称、驱动器号、装入文件夹目录和注册表设置。这可防止在将之前位于系统中的卷重新引入到系统时自动为这些卷分配它们以前的驱动器号和装入文件夹路径名称。
	*  NOERR       仅用于脚本。遇到错误时，DiskPart 会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart退出并返回错误代码。
	*  启用自动装载功能(默认在 Windows Server 的同一版本上)时，Windows 会在将卷添加到系统时自动使该卷联机，并且为该卷分配一个驱动器号和卷 GUID 路径名称。在存储区域网络(SAN)配置中，禁用自动装载可防止 Windows 自动使该卷联机，并防止为系统中可见的任何新卷分配一个驱动器号和卷 GUID 路径名称。
	*  请注意，在 Windows Vista 之前发布的 Windows 版本上，自动装载功能只能用于基本磁盘卷。从 Windows Vista 开始，自动装载功能可用于基本磁盘卷和动态磁盘卷。

* BREAK DISK=N [NOKEEP] [NOERR]——将带焦点的镜像卷分为两个简单卷。
	* DISK=N    指定含有镜像卷一个副本的磁盘。命令完成后，此磁盘会获得焦点
但此磁盘上使用镜像卷范围创建的新卷不会保留已与该镜像卷关联的驱动器号、卷 GUID 路径名称或装入文件夹路径。如果将指定磁盘上的镜像卷用作系统或启动分区，则命令会失败。
	* NOKEEP      指定仅保留镜像卷的一个副本。磁盘 N 上的镜像卷范围会转换为可用空间。该镜像卷的其余副本和磁盘 N 上可用空间 都不会收到焦点。
	* NOERR       仅用于脚本。遇到错误时，DiskPart 会继续处理命令，如同没有出现错误一样。如果不使用 NOERR 参数，错误会导致 DiskPart退出并返回错误代码。
	* 仅适用于动态磁盘。将带焦点的镜像卷分为两个简单卷。 一个简单卷保留已与该镜像卷关联的驱动器号、卷 GUID 路径名称或装入文件夹路径。另一个简单卷接收焦点，因此你可以为它分配一个驱动器号(将为其自动分配一个卷 GUID 路径名称)。
	* 默认情况下，镜像这两个副本的内容都会保留。每个副本都成为一个简单卷。通过使用 NOKEEP 参数，你可以仅将镜像卷的一个副本保留为简单卷，将另一个副本转换为可用空间。任何卷不会接收焦点。必须选择一个镜像卷才能成功执行此操作。

* COMPACT VDISK——压缩虚拟磁盘文件以减少文件的物理大小。只能对已分离的 EXPANDABLE 虚拟磁盘或以只读 方式连接的 EXPANDABLE 虚拟磁盘执行压缩操作。 必须选择虚拟磁盘才能成功完成此操作。

* CONVERT BASIC|DYNAMIC|MBR|GPT——在不同的磁盘格式之间转换。
	* CONVERT BASIC [NOERR]
BASIC       - 将磁盘从动态转更换为基本。
DYNAMIC     - 将磁盘从基本转更换为动态。
GPT         - 将磁盘从 MBR 转更换为 GPT。
MBR         - 将磁盘从 GPT 转更换为 MBR。

DISKPART> help convert basic

    将空的动态磁盘转换为基本磁盘。

语法: 

    NOERR       仅用于脚本。遇到错误时，DiskPart 会
                继续处理命令，如同没有出现错误一样。
                如果不使用 NOERR 参数，错误会导致
                DiskPart 退出并返回错误代码。

    必须选择一个动态磁盘才能成功执行此操作。

    重要信息:

    磁盘必须为空磁盘才能转换为动态磁盘。请在转换磁盘前
    备份数据，然后删除所有分区或卷。

示例:

    CONVERT BASIC

DISKPART> help convert dynamic

    将基本磁盘转换为动态磁盘。

语法:  CONVERT DYNAMIC [NOERR]

    NOERR       仅用于脚本。遇到错误时，DiskPart 会继续
                处理命令，如同没有出现错误一样。如果
                不使用 NOERR 参数，错误会导致
                DiskPart 退出并返回错误代码。

    基本磁盘上的任何现有分区都会变为简单卷。

    必须选择一个基本磁盘才能成功执行此操作。

示例:

    CONVERT DYNAMIC

DISKPART> help convert mbr

    将具有 GUID 分区表(GPT)分区形式的空基本磁盘转换为
    具有主启动记录(MBR)分区形式的基本磁盘。

语法:  CONVERT MBR [NOERR]

    NOERR       仅用于脚本。遇到错误时，DiskPart 会继续
                处理命令，如同没有出现错误一样。如果不使用
                NOERR 参数，错误会导致 DiskPart
                退出并返回错误代码。

    必须选择一个基本 GPT 磁盘才能成功执行此操作。

    重要信息:

    若要将磁盘转换为 MBR 磁盘，该磁盘必须为空。请在转换
    磁盘前备份数据，然后删除所有分区或卷。

示例:

    CONVERT MBR

DISKPART> help convert gpt

    将具有主启动记录(MBR)分区形式的空基本磁盘转换为
    具有 GUID 分区表(GPT)分区形式的基本磁盘。

语法:  CONVERT GPT [NOERR]

    NOERR       仅用于脚本。遇到错误时，DiskPart 会
                继续处理命令，如同没有出现错误一样。
                如果不使用 NOERR 参数，错误会导致
                DiskPart 退出并返回错误代码。

    必须选择一个基本 MBR 磁盘才能成功执行此操作。

    转换为 GPT 所需的最小磁盘大小是 128MB。

    重要信息:

    若要将磁盘转换为 GPT 磁盘，该磁盘必须为空。请在转换
    磁盘前备份数据，然后删除所有分区或卷。

示例:

    CONVERT GPT


DETACH      - 分离虚拟磁盘文件。
EXIT        - 退出 DiskPart。
EXTEND      - 扩展卷。
EXPAND      - 扩展虚拟磁盘上可用的最大大小。
FILESYSTEMS - 显示卷上当前和支持的文件系统
FORMAT      - 格式化卷或分区
GPT         - 给选择的 GPT 分区分配属性。

IMPORT      - 导入磁盘组。
INACTIVE    - 将所选分区标为不活动。

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

SETID       - 更改分区类型。
SHRINK      - 减小选定卷。
UNIQUEID    - 显示或设置磁盘的 GUID 分区表(GPT)标识符或
              主启动记录(MBR)签名。


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------