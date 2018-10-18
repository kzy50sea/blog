---
title: Linux设备树
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文章参考了<font color=blue >[《Device Tree Usage》](http://www.devicetree.org/Device_Tree_Usage)、[《uboot之fdt介绍》](https://www.cnblogs.com/leaven/p/6295999.html)、[《蜂窝科技的几篇文章》](http://www.wowotech.net/device_model/dt_basic_concept.html)，宋牧春[《Linux设备树文件结构与解析深度分析》](https://blog.csdn.net/woyimibayi/article/details/77574736)</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1 设备树起源
&emsp;&emsp;Linus Torvalds在2011年3月17日的ARM Linux邮件列表宣称“this whole ARM thing is a fucking pain in the ass”，引发ARM Linux社区的地震，随后ARM社区进行了一系列的重大修正。在过去的ARM Linux中，arch/arm/plat-xxx和arch/arm/mach-xxx中充斥着大量的垃圾代码，相当多数的代码只是在描述板级细节，而这些板级细节对于内核来讲，不过是垃圾，如板上的platform设备、resource、i2c_board_info、spi_board_info以及各种硬件的platform_data。社区必须改变这种局面，于是PowerPC等其他体系架构下已经使用的Flattened Device Tree（FDT）进入ARM社区的视野。Device Tree是一种描述硬件的数据结构，它起源于 OpenFirmware (OF)。在Linux 2.6中，ARM架构的板极硬件细节过多地被硬编码在arch/arm/plat-xxx和arch/arm/mach-xxx，采用Device Tree后，许多硬件的细节可以直接透过它传递给Linux，而不再需要在kernel中进行大量的冗余编码。
&emsp;&emsp;Device Tree由一系列被命名的结点（node）和属性（property）组成，而结点本身可包含子结点。所谓属性，其实就是成对出现的键值对（name和value）。在设备树中，可描述的信息包括（原先这些信息大多被hard  code到kernel中）：

* CPU的数量和类别
* 内存基地址和大小
* 总线和桥
* 外设连接
* 中断控制器和中断使用情况
* GPIO控制器和GPIO使用情况
* Clock控制器和Clock使用情况

&emsp;&emsp;基本上设备树就是画一棵电路板上CPU、总线、设备组成的树，Bootloader会将这棵树传递给内核，然后内核可以识别这棵树，并根据它展开出Linux内核中的platform_device、i2c_client、spi_device等设备，而这些设备用到的内存、IRQ等资源，也被传递给了内核，内核会将这些资源绑定给展开的相应的设备。

# 2 设备树组成

![设备树](https://www.github.com/liao20081228/blog/raw/master/图片/Linux设备树/1.jpg "devicetree")

* DTS(Device tree source)是设备树源文件，为了方便阅读及修改，采用文本格式。DTSI类似于C语言的头文件
* DTC(Device tree compiler)是一个小工具，负责将DTS转换成DTB。
* DTB(Device tree blob)是DTS的二进制形式，供内核使用。

&emsp;&emsp;我们首先根据硬件修改DTS文件，然后在编译的时候通过DTC工具将DTS文件转换成 DTB文件，然后将DTB文件烧写到机器上(如emmc，磁盘等存储介质)。系统启动时，Bootloader在启动内核前将DTB文件读到内存中，跳转到内核执行的同时将DTB起始地址传给内核。之后内核会展开Device Tree并创建和注册相关的设备，因此arch/arm/mach-xxx和arch/arm/plat-xxx中大量的用于注册platform、I2C、SPI板级信息的代码被删除，而驱动也以新的方式和.dts中定义的设备结点进行匹配。
binary

# 3  设备树源文件
&emsp;&emsp;.dts文件是一种ASCII文本格式的Device Tree描述，此文本格式非常人性化，适合人类的阅读习惯。基本上，在ARM Linux在，一个.dts文件对应一个ARM的machine，一般放置在内核的arch/arm/boot/dts/目录。
&emsp;&emsp;由于一个SoC可能对应多个machine（一个SoC可以对应多个产品和电路板），势必这些.dts文件需包含许多共同的部分，Linux内核为了简化，把SoC公用的部分或者多个machine共同的部分一般提炼为.dtsi，类似于C语言的头文件。其他的machine对应的.dts就include这个.dtsi。

## 3.1 dts基本结构
```dts
/{
	node1 {
		a-string-property = "A string";
		a-string-list-property = "first string", "second string";
		a-binary-data-property = [0x01 0x23 0x34 0x56];
		a-mixed-property = "a string", [0x01 0x02 0x03 0x04], <0xFF01 412 0x12341283>"
		child-node1 {
			first-child-property=<&label>;
			second-child-property = <1>;
			a-string-property = "Hello, world";
		};
		child-node2 {
		};
	};
	labe1: node2 {
		an-empty-property;
		a-cell-property = <1 2 3 4>; /* each cell is a 32 bit*/
		child-node1 {
		};
	};
};
```
.dts（或者其include的.dtsi）由结点和属性组成，上述.dts文件并没有什么真实的用途，但它基本表征了一个Device Tree源文件的结构：
* 每个设备树都由一个根结点"/"开始，根节点只有一个；
* 每个结点下面含一系列子结点，子节点下又含有一系列子结点；
* 一个节点可以有多个子节点，但只有一个父节点；
* 每个node用节点名字（node name）标识，节点名字的格式是node-name@unit-address，不超过31字符。
	* 如果该node没有reg属性，那么该节点名字中不能包括@和unit-address。
	* 根节点的node name是确定的，必须是“/”。
	* 相同的设备，name相同，而unit-address不同，unit-address的具体格式是和设备挂在那个bus上相关。例如：
		* 对于cpu，其unit-address就是从0开始编址，以此加一。
		* 以太网控制器，其unit-address就是寄存器地址。
* 节点由一系列的属性组成，属性由一系列的`key=value`定义，属性有5种：
	* 空，如"an-empty-property"；
	* 字符串或字符串列表 ，用双引号表示，如"a-string-property"；
	* Cells（单位为u32bit），用尖括号表示，如"second-child-property"，
	* 二进制，用方括号表示，如“a-binary-data-property"。
	* 混合数据，之间用逗号隔开，如"a-mixed-property";
* 引用节点，有两种方法
	* 使用绝对路径full path
	* 为节点设置一个标签label，然后用`&label`来引用该节点，这种引用是通过phandle（pointer handle）进行的。像是这种phandle的节点，在经过DTC工具编译之后，`&label`会变成一个特殊的整型数字n，假设n值为0x00000001，那么在node2节点下自动生成两个属性:`linux,phandle = <0x00000001>;phandle = <0x00000001>;`
## 3.2 dts基本语法
&emsp;&emsp;为了帮助理解device tree的用法，我们从一个简单的计算机开始， 手把手创建一个device tree来描述它。假设有这样一台计算机（基于ARM Versatile）,由“Acme”制造并命名为"Coyote's Revenge"：

* 1个双核ARM Cortex-A9 32位处理器；
* 256MB SDRAM，起始地址为0
* ARM的processor local bus上的内存映射区域分布了：
	* 2个串口，起始地址0x101F1000 和 0x101F2000
	* GPIO控制器，位于0x101F3000
	* SPI控制器，位于0x10170000，并挂载以下设备：
		* MMC插槽，SS管脚连接到GPIO #1
	* 中断控制器，位于0x10140000
	* External bus桥，External bus桥上又连接了：
		* SMC SMC91111 Ethernet，位于0x10100000
		* I2C控制器，位于0x10160000，I2C总线上又连接了
			* Maxim DS1338实时钟，I2C地址为1101000 （0x58）
		* 64MB NOR Flash，位于0x30000000



### 3.2.1 初始结构
&emsp;&emsp;第一步，先构建一个计算机的基本架构，即一个有效设备树的最小架构。在这一步，要唯一地标志这台计算机。
```dts
/{  
    compatible = "acme,coyotes-revenge";  
};  
```
&emsp;&emsp;compatible属性以"`<manufacturer>,<model>`"的格式指定了系统名称。指定具体设备和制造商名称来避免命名空间的冲突，一个操作系统可以使用compatible值来决定如何运行这台计算机，在该属性中填入正确的数据很重要。


### 3.2.2 添加CPU
&emsp;&emsp;接下来就要描述各个CPU了。先添加一个“cpus”容器节点，再将每个CPU作为子节点添加。在本例中，系统是基于ARM的双核Cortex A9系统。
```dts
/ {  
    compatible = "acme,coyotes-revenge";  
  
  
    cpus {  
        cpu@0 {  
            compatible = "arm,cortex-a9";  
        };  
        cpu@1 {  
            compatible = "arm,cortex-a9";  
        };  
    };  
}; 
```
&emsp;&emsp;各个CPU节点的compatible属性是一个字符串，与顶层compatible属性类似，该字符串以“`<manufacturer>,<model>`”的格式指定了CPU的确切型号。


### 3.2.3 添加其他设备
&emsp;&emsp;系统中的每个设备由device tree的一个节点来表示，接下来将为设备树添加设备节点。在我们讲到如何寻址和如何处理中断之前，暂时将新节点置空。
```dts
/ {  
    compatible = "acme,coyotes-revenge";  
  
  
    cpus {  
        cpu@0 {  
            compatible = "arm,cortex-a9";  
        };  
        cpu@1 {  
            compatible = "arm,cortex-a9";  
        };  
    };  
  
  
    serial@101F0000 {  
        compatible = "arm,pl011";  
    };  
  
  
    serial@101F2000 {  
        compatible = "arm,pl011";  
    };  
  
  
    gpio@101F3000 {  
        compatible = "arm,pl061";  
    };  
  
  
    interrupt-controller@10140000 {  
        compatible = "arm,pl190";  
    };  
  
  
    spi@10115000 {  
        compatible = "arm,pl022";  
    };  
  
  
    external-bus {  
        ethernet@0,0 {  
            compatible = "smc,smc91c111";  
        };  
  
  
        i2c@1,0 {  
            compatible = "acme,a1234-i2c-bus";  
            rtc@58 {  
                compatible = "maxim,ds1338";  
            };  
        };  
  
  
        flash@2,0 {  
            compatible = "samsung,k8f1315ebm", "cfi-flash";  
        };  
    };  
};  
```
&emsp;&emsp;在上面的设备树中，系统中的设备节点已经添加进来，树的层次结构反映了设备如何连到系统中。外部总线上的设备就是外部总线节点的子节点，i2c设备是i2c总线控制节点的子节点。总的来说，**层次结构表现的是从CPU视角来看的系统视图**。在这里这棵树是依然是无效的。它缺少关于设备之间的连接信息。稍后将添加这些数据。在这个设备树中应当注意：
* 每个设备节点有一个compatible属性。
* flash节点的compatible属性有两个字符串。请阅读下一节以了解更多内容。 
* 之前提到的，节点命名应当反映设备的类型，而不是特定型号。请参考ePAPR规范2.2.2节的通用节点命名，应优先使用这些命名。


### 3.2.4  compatible 属性
&emsp;&emsp;树中的每一个代表了一个设备的节点都要有一个compatible属性。compatible是OS用来匹配设备驱动的关键。
&emsp;&emsp;compatible是字符串的列表。列表中的第一个字符串指定了"`<manufacturer>,<model>`"格式的节点代表的确切设备，第二个字符串代表了与该设备兼容的其他设备。例如，Freescale MPC8349 SoC有一个串口设备实现了National Semiconductor ns16550寄存器接口。因此MPC8349串口设备的compatible属性为：`compatible = "fsl,mpc8349-uart", "ns16550"`。在这里，fsl,mpc8349-uart指定了确切的设备，ns16550表明它与National Semiconductor 16550 UART是寄存器级兼容的。
>&emsp;&emsp;注：由于历史原因，ns16550没有制造商前缀，但所有新的compatible值都应使用制造商的前缀。这种做法使得现有的设备驱动程序可以绑定到一个新设备上，同时仍能唯一准确的识别硬件。
>&emsp;&emsp;警告：不要使用带通配符的compatible值，如"fsl,mpc83xx-uart"等类似表达，芯片厂商总会改变并打破你的通配符假设，到时候再想修改就为时已晚了。相反，你应当选择一个特定的芯片实现，并与所有后续芯片保持兼容。

 

## 3.3 编址
* 可编址的设备使用下列属性来将地址信息编码进设备树：
	* reg
	* #address-cells
	* #size-cells
	* ranges

* 每个可寻址的设备有一个reg属性，即以下面形式表示的元组列表：`reg = <address1 length1 [address2 length2] [address3 length3] ... >`
* 每个元组表示该设备的地址范围。每个地址值由一个或多个32位整数列表组成，被称做cells。同样地，长度值也可以是cells列表或为空。
* 由于address和length字段是大小可变的变量，**父节点的`#address-cells`和`#size-cells`属性用来说明子节点的各个字段有多少个cells**。换句话说，正确解释一个子节点的reg属性需要父节点的#address-cells和#size-cells值。让我们从CPU开始，添加编址属性到示例设备树。

### 3.3.1 CPU编址

&emsp;&emsp;谈到编址，最简单的例子就是CPU节点。每个CPU被分配了一个唯一的ID，并且不存在与CPU ids的相关大小信息。
```dts
cpus {  
    #address-cells = <1>;  
    #size-cells = <0>;  
    cpu@0 {  
        compatible = "arm,cortex-a9";  
        reg = <0>;  
    };  
    cpu@1 {  
        compatible = "arm,cortex-a9";  
        reg = <1>;  
    };  
};
```
&emsp;&emsp;在cpus节点中，`#address-cells`为1，`#size-cells`为0，这意味着子寄存器值是一个uint32，是一个不包含大小字段的地址。在本例中，两个CPU被分配为地址0和1。cpu节点的`#size-cells`为0，是因为只为每个CPU分配了地址值。你一定注意到了，reg值与节点名中的值是匹配。按照惯例，如果一个节点有reg属性，则节点名称必须包含unit-address属性，unit-address属性值是reg属性中的第一个地址值。

>注：ePAPR中对cell的定义是”一个包含32bit信息的单元“。

### 3.3.2 内存映射设备
&emsp;&emsp;与CPU节点中的单一地址值不同，内存映射设备会被分配一个它能响应的地址范围。`#size-cells`用来说明每个子节点中reg元组的大小的长度。在下面的示例中，每个地址值是1 cell (32位) ，并且每个的长度值也为1 cell，这在32位系统中是非常典型的。64位计算机可以在设备树中使用2作为`#address-cells`和`#size-cells`的值来实现64位寻址。
```dts
/ {  
    #address-cells = <1>;  
    #size-cells = <1>;  
  
  
    ...  
  
  
    serial@101f0000 {  
        compatible = "arm,pl011";  
        reg = <0x101f0000 0x1000 >;  
    };  
  
  
    serial@101f2000 {  
        compatible = "arm,pl011";  
        reg = <0x101f2000 0x1000 >;  
    };  
  
  
    gpio@101f3000 {  
        compatible = "arm,pl061";  
        reg = <0x101f3000 0x1000  
               0x101f4000 0x0010>;  
    };  
  
  
    interrupt-controller@10140000 {  
        compatible = "arm,pl190";  
        reg = <0x10140000 0x1000 >;  
    };  
  
  
    spi@10115000 {  
        compatible = "arm,pl022";  
        reg = <0x10115000 0x1000 >;  
    };  
  
  
    ...  
  
  
};  
```
&emsp;&emsp;每个设备都被分配了一个基地址及该区域大小。本例中的GPIO设备地址被分成两个地址范围:`0x101f3000~0x101f3fff`和`0x101f4000~0x101f400f`。
&emsp;&emsp;有些挂载于总线上的设备有不同的编址方案。例如，设备可以通过独立片选线连接到外部总线。因为父节点定义了它的子节点的地址范围，所以可根据需要选择地址映射来最佳地描述该系统，如下所示：
```dts
external-bus {  
        #address-cells = <2>  
        #size-cells = <1>;  
  
  
        ethernet@0,0 {  
            compatible = "smc,smc91c111";  
            reg = <0 0 0x1000>;  
        };  
  
  
        i2c@1,0 {  
            compatible = "acme,a1234-i2c-bus";  
            reg = <1 0 0x1000>;  
            rtc@58 {  
                compatible = "maxim,ds1338";  
            };  
        };  
  
  
        flash@2,0 {  
            compatible = "samsung,k8f1315ebm", "cfi-flash";  
            reg = <2 0 0x4000000>;  
        };  
    }; 
```	
&emsp;&emsp;在上例中，外部总线用了2个cells来表示地址值;一个是片选号，一个是基于片选的偏移量。长度字段还是一个cell，这是因为只有地址的偏移部分需要一个范围。所以，在本例中，每个reg条目包含3个cell；片选号码，偏移，长度。由于地址范围包含节点及其子节点，父节点可以自由定义任何对该总线而言有意义的编址方案。直接父节点和子节点之外的其他节点，通常不关心本地节点地址域，因而地址不得不从一个域映射到另一个域。

### 3.3.3 非内存映射设备

&emsp;&emsp;其他设备没有映射到处理器本地总线上。虽然这些设备可以有地址范围，但是不能直接被CPU访问，而是由父设备的驱动代表CPU来执行间接访问。

&emsp;&emsp;以I2C设备为例，每个设备分配一个地址，但没有与它相关的长度或范围，这与CPU地址分配很相似。
```dts
i2c@1,0 {  
           compatible = "acme,a1234-i2c-bus";  
           #address-cells = <1>;  
           #size-cells = <0>;  
           reg = <1 0 0x1000>;  
           rtc@58 {  
               compatible = "maxim,ds1338";  
               reg = <58>;  
           };  
       }; 
```	   



### 3.3.4 地址转换

&emsp;&emsp;我们已经讨论过如何分配地址给设备，但在这些地址只是设备节点的本地地址，还没有描述如何将这些地址映射成CPU可使用的地址。
&emsp;&emsp;根节点总是从CPU的角度描述地址空间。如果根节点的子节点已经使用了CPU地址域，就不需要任何显式映射了，例如，串口serial@101f0000被直接分配到地址0x101f0000。
&emsp;&emsp;如果根节点的间接子节点没有使用CPU的地址域，为了获得一个内存映射地址，设备树必须指定如何翻译地址。
```dts
/ {  
    compatible = "acme,coyotes-revenge";  
    #address-cells = <1>;  
    #size-cells = <1>;  
    ...  
    external-bus {  
        #address-cells = <2>;  
        #size-cells = <1>;  
        ranges = <0 0  0x10100000   0x10000     // Chipselect 1, Ethernet  
               1 0  0x10160000   0x10000     // Chipselect 2, i2c controller  
               2 0  0x30000000   0x1000000>;  // Chipselect 3, NOR Flash  
  
  
        ethernet@0,0 {  
            compatible = "smc,smc91c111";  
            reg = <0 0 0x1000>;  
        };  
  
  
        i2c@1,0 {  
            compatible = "acme,a1234-i2c-bus";  
            #address-cells = <1>;  
            #size-cells = <0>;  
            reg = <1 0 0x1000>;  
            rtc@58 {  
                compatible = "maxim,ds1338";  
                reg = <58>;  
            };  
        };  
  
  
        flash@2,0 {  
            compatible = "samsung,k8f1315ebm", "cfi-flash";  
            reg = <2 0 0x4000000>;  
        };  
    };  
}; 
```
&emsp;&emsp;ranges是地址翻译表，由3个字段组成，即`<子地址，父地址，区域大小>`，分别对应子节点的`#address-cells`值，父节点的`#address-cells`值，子节点的`#size-cells`值确定。对于本例中的外部总线，子节点的地址是2个单元，父节点的地址是1个单元，子节点的区域大小是1个单元。3个ranges被转换：

* 从片选0偏移0被映射到地址范围0x10100000~0x1010ffff
* 从片选1偏移0被映射到地址范围0x10160000~0x1016ffff
* 从片选2偏移0被映射到地址范围为0x30000000~0x30ffffff

&emsp;&emsp;另外，如果父节点和子节点的地址空间是相同的，那么一个节点可以添加一个空的ranges属性。一个空的ranges属性的存在意味着子节点地址空间的地址1：1地映射到父地址空间。你可能会问，为如果可以都用一一映射，为什么还需要地址转换？这是因为一些总线（如PCI ）具有完全不同的地址空间，其细节需要暴露给操作系统。其他有DMA engine的计算机需要知道总线上的真实地址。有时设备需要组合在一起，因为他们都有着相同的软件可编程的物理地址映射。是否需要一一映射取决于操作系统所需要的信息，以及硬件设计。
&emsp;&emsp;你也应该注意到，在i2c@1,0节点上没有ranges属性。这样做的原因是，不像外部总线，I2C总线上的设备不是内存映射到CPU地址域的。相反，CPU通过i2c@1,0设备间接访问rtc@58设备。**ranges属性为空意味着设备不能被除了父设备以外的任何设备直接访问**。

&emsp;&emsp;另举一例说明ranges属性：
```dts
soc{

	compatible = "simple-bus";

	#address-cells = <1>;

	#size-cells = <1>;

	ranges = <0x0 0xe0000000 0x00100000>;

	serial {

		device_type = "serial";

		compatible = "ns16550";

		reg = <0x4600 0x100>;

		clock-frequency = <0>;

		interrupts = <0xA 0x8>;

		interrupt-parent = < &ipic >;
	}

}
```
&emsp;&emsp;soc的ranges属性指定了，从物理地址为0x0大小为1024KB的子节点映射到了物理地址为0xe0000000的父地址空间，有个这层映射关系，串口设备节点就可以通过load/store地址0xe0004600来访问，即0x4600的偏移+在ranges中指定的映射0xe0000000。
 
&emsp;&emsp;当然在64位操作系统中也会看到这样的映射，不要感到吃惊啦，"0xf 0x00000000"一起组成父节点地址即f00000000。
``` dts
dcsr: dcsr@f00000000 {
	ranges = <0x0 0xf 0x00000000 0x01072000>;
 };
```

## 3.4 中断如何工作

&emsp;&emsp;与地址范围转换遵循树的天然结构不同，一台计算机的任何设备都可以发起和终止中断信号。不像设备编址，中断信号表现为独立于树的节点之间的链接。描述中断连接有4个属性：

* interrupt-controller ： 一个空的属性定义该节点为接收中断信号的设备

* #interrupt-cells ： 这是中断控制器节点的属性。它声明了中断控制器的中断说明符有多少个cell（类似#address-cells和#size-cells） 。

* interrupt-parent：设备节点的属性，包含一个指向该设备所连接中断控制器的pHandle。那些没有interrupt-parent属性节点则从它们的父节点继承该属性。

* interrupts ：设备节点属性，包含中断说明符列表，对应于该设备上的每个中断输出信号。

&emsp;&emsp;中断说明符是一个或多个cell的数据（由#interrupt-cells指定），指定设备连接到哪些中断输入。下面的例子中，大多数设备只有一个中断输出，但也有一个设备上有多个中断输出的情况。一个中断符的含义完全取决于绑定的中断控制器设备。每个中断控制器可以决定它需要多少cell来唯一地确定一个中断输入。下面的代码将中断添加到我们的Coyote's Revenge：

```dts
/ {  
    compatible = "acme,coyotes-revenge";  
    #address-cells = <1>;  
    #size-cells = <1>;  
    interrupt-parent = <&intc>;  
  
  
    cpus {  
        #address-cells = <1>;  
        #size-cells = <0>;  
        cpu@0 {  
            compatible = "arm,cortex-a9";  
            reg = <0>;  
        };  
        cpu@1 {  
            compatible = "arm,cortex-a9";  
            reg = <1>;  
        };  
    };  
  
  
    serial@101f0000 {  
        compatible = "arm,pl011";  
        reg = <0x101f0000 0x1000 >;  
        interrupts = < 1 0 >;  
    };  
  
  
    serial@101f2000 {  
        compatible = "arm,pl011";  
        reg = <0x101f2000 0x1000 >;  
        interrupts = < 2 0 >;  
    };  
  
  
    gpio@101f3000 {  
        compatible = "arm,pl061";  
        reg = <0x101f3000 0x1000  
               0x101f4000 0x0010>;  
        interrupts = < 3 0 >;  
    };  
  
  
    intc: interrupt-controller@10140000 {  
        compatible = "arm,pl190";  
        reg = <0x10140000 0x1000 >;  
        interrupt-controller;  
        #interrupt-cells = <2>;  
    };  
  
  
    spi@10115000 {  
        compatible = "arm,pl022";  
        reg = <0x10115000 0x1000 >;  
        interrupts = < 4 0 >;  
    };  
  
  
    external-bus {  
        #address-cells = <2>  
        #size-cells = <1>;  
        ranges = <0 0  0x10100000   0x10000     // Chipselect 1, Ethernet  
                  1 0  0x10160000   0x10000     // Chipselect 2, i2c controller  
                  2 0  0x30000000   0x1000000>; // Chipselect 3, NOR Flash  
  
  
        ethernet@0,0 {  
            compatible = "smc,smc91c111";  
            reg = <0 0 0x1000>;  
            interrupts = < 5 2 >;  
        };  
  
  
        i2c@1,0 {  
            compatible = "acme,a1234-i2c-bus";  
            #address-cells = <1>;  
            #size-cells = <0>;  
            reg = <1 0 0x1000>;  
            interrupts = < 6 2 >;  
            rtc@58 {  
                compatible = "maxim,ds1338";  
                reg = <58>;  
                interrupts = < 7 3 >;  
            };  
        };  
  
  
        flash@2,0 {  
            compatible = "samsung,k8f1315ebm", "cfi-flash";  
            reg = <2 0 0x4000000>;  
        };  
    };  
};
```
&emsp;&emsp;需要注意的是：这台机器只有一个中断控制器interrupt-controller@10140000，标签'intc:'被添加到中断控制器节点，该标签用于给根节点中的interrupt-parent属性分配一个phandle。这个interrupt-parent值将成为系统默认值，所有子节点都继承它，除非它被显式覆盖。每个设备用一个interrupt属性来指定一个不同的中断输入线。#interrupt-cells是2，所以每个中断符有2个单元。本例使用通用格式，第一个cell代表中断行号码，第二个cell代表标志位，如高电平触发/低电平触发，边沿触发/水平触发。对于一个给定的中断控制器，请参阅控制器binding文档，以了解如何对说明符进行编码。

 

## 3.5 设备特有的数据

&emsp;&emsp;除了公共属性，一个节点可以添加任意属性和子节点，只要遵循一些规则，操作系统所需要的任何数据可以被添加。
* 首先，设备新的特有属性名应当使用制造商前缀，这样它们不会与现有的标准属性名称冲突。
* 第二，属性和子节点的含义必须有文档来约束，这样一个设备驱动程序的作者才能知道如何解释这些数据。约束记录了一个特定的compatible值代表什么，它应该有什么样的属性，它可能有什么子节点，代表什么设备。每个唯一的compatible值应该有自己的约束（或者声明与其他compatible值的兼容性）。绑定新设备都记录在本wiki页。
* 第三，使用devicetree-discuss@lists.ozlabs.org邮件列表发布新的binding以进行审查。新的binding的审查可以发现了很多未来会导致问题的常见错误。

 

## 3.6 特殊节点

### 3.6.1 别名节点
&emsp;&emsp;引用一个特定的节点通常通过完整路径，如`/external-bus/ethernet@0,0`，但当用户真正想要知道的是哪个设备是eth0时，这很不具有易读性，别名节点可分配一个短的alias给一个完整的设备路径。例如：
```dts
aliases {  
    ethernet0 = &eth0;  
    serial0 = &serial0;  
};  
```
&emsp;&emsp;分配标识符给设备时，使用别名是受操作系统欢迎的。这里使用了一个新的语法`property = &label`;该语法指定通过标签引用的完整节点路径为一个字符串属性。这与phandle = <&label>;不同，它是把一个pHandle值插入到一个cell。

### 3.6.2 可选节点
&emsp;&emsp;可选节点并不代表真正的设备，而是作为固件和操作系统之间传递数据的地方，如启动参数。选择节点中的数据并不代表硬件。通常情况下，选择节点在DTS源文件中为空，并在开机时填充。在我们的示例系统中，固件可以添加以下选择节点：
```dts
chosen {  
    bootargs = "root=/dev/nfs rw nfsroot=192.168.1.1 console=ttyS0,115200";  
}  
```
 ## 3.7 高级主题
 ### 3.7.1 PCI主桥
 >注：本节将假定读者了解 PCI 的基本知识。本文并不是 PCI 教程，想要了解更深入的信息，请阅读 [^1]。你也可以参考 ePAPR 或 PCI Bus Binding to Open Firmware<http://playground.sun.com/1275/bindings/pci/pci2_1.pdf>。还可以访问<http://devicetree.org/MPC5200:PCI>，这里可以找到 Freescale MPC5200 的一个完整工作的例子。
 
 &emsp;&emsp;模型机添加了一个 PCI 主桥，其控制寄存器映射到内存 0x10180000，并且 BARs
编程以地址 0x80000000 为起始。添加设备节点：
```dts
pci@10180000 {
	compatible = "arm,versatile-pci-hostbridge", "pci";
	reg = <0x10180000 0x1000>;
	interrupts = <8 0>;
 };
```
&emsp;&emsp; **PCI 总线编号**：每个 PCI 总线段都是唯一编号的，并且总线的编号是通过使用 bus-ranges 属性在 pci节点中暴露出来的，这个属性有两个 cell。第一个 cell 给出分配给该节点的总线号；第二个 cell 给出任何次级 PCI 总线最大总线号。模型机只有一个 pci 总线，所以两个 cell 都是 0。
```dts
pci@0x10180000 {
	compatible = "arm,versatile-pci-hostbridge", "pci";
	reg = <0x10180000 0x1000>;
	interrupts = <8 0>;
	bus-ranges = <0 0>;
 };
```

&emsp;&emsp;**PCI 地址转换**：类似于前面所描述的本地总线，PCI 地址空间和 CPU 地址空间是完全分离的，所以需要一个从 PCI 地址到 CPU 地址的转换。同样，完成这样的转换将使用 ranges、#address-cells 和 #size-cells 属性。

&emsp;&emsp;正如你所看到的，子地址（PCI 地址）使用 3 个 cell，同时 PCI rangs 被编码为 2 个cell。那么第一个问题就可能是，为什么我们要用三个 32 位 cell 去指定一个 PCI 地址？这三个 cell 分别标记了 phys.hi、phys.mid 和 phys.low[^2]。

* phys.hi cell: npt000ss bbbbbbbb dddddfff rrrrrrrr
* phys.mid cell: hhhhhhhh hhhhhhhh hhhhhhhh hhhhhhhh
* phys.low cell: llllllll llllllll llllllll llllllll

&emsp;&emsp;PCI 地址是 64 位的，编码在phys.mid 和 phys.low。然而真正有意思的是在phys.high 里面，这是一个位域：
* n：代表重申请空间标志（这里没有使用）
* p：代表预读空间（缓存）标志
* t：别名地址标志（这里没有使用）
* ss：空间代码
	* 00： 设置空间
	* 01：IO空间
	* 10：32位存储空间
	* 11：64位存储空间
* bbbbbbbb： PCI总线号。PCI有可能是层次性架构，所以我们可能需要区分一些子-总线
* ddddd：设备号，通常与IDSEL信号关联连接。
* fff：功能序号，有些多功能PCI设备可能用到。
* rrrrrrrr：寄存器号，在设置周期使用。

&emsp;&emsp;对于 PCI 地址转换来说，p 和 ss 是最重要的字段。在 phys.hi 里的 p 和 ss 的值决定了访问哪个 PCI 地址空间。因此，通过查找 ranges 属性，我们将得到三个区域。

* 以 PCI 地址 0x80000000 开始的一个 512 MByte 32 位预取存储区，该区域将映射
到主机 CPU 地址 0x80000000。
* 以 PCI 地址 0xa0000000 开始的一个 265 MByte 32 位非预取存储区，该区域将映
射到主机 CPU 地址 0xa0000000。
* 以 PCI 地址 0x00000000 开始的一个 16 MByte I/O 区，该区域将映射到主机 CPU
地址 0xb0000000。

&emsp;&emsp;phys.hi 位域的存在就意味着操作系统必须知道该节点代表了一个PCI 桥，这样操作系统才能为了地址转换而忽略那些不相关的字段。为了判断应该掩码哪些额外的字段，操作系统需要在 PCI 总线节点中寻找“pci”字符串。

### 3.7.2 高级中断映射 
&emsp;&emsp;现在我们来到了最有趣的部分，PCI 中断映射。一个 PCI 设备可以使用引线 #INTA、#INTB、#INTC 和 #INTD 来触发中断。如果我们没有多功能 PCI 设备，那么设备中断必须使用 #INTA。然而，每个 PCI 插槽或设备通常会连接到中断控制器上不同的输入端。所以设备树需要一种能将各个 PCI 中断信号映射到中断控制器的途径。#interrupt-cells、interrupt-map 和 interrupt-map-mask 属性就被用来描述这个中断映射。

&emsp;&emsp;事实上，这里所描述的中断映射并不仅仅局限于 PCI 总线，任何节点都可以指定复杂的中断映射，但 PCI 是最常见的情况。
&emsp;&emsp;首先你会发现，PCI 中断号只使用了一个 cell，不像系统中断控制器，它使用两个cell，一个用于中断号，另一个用于标志。PCI 中断只使用了一个 cell，因为 PCI 中断确定为始终是低电平触发。
&emsp;&emsp;在这个示例板上，我们有 2 个分别包含 4 个中断行的 PCI 插槽，所以我们需要映射 8
个中断行到中断控制器上。这已经在 interrupt-map 属性中完成了。关于中断映射的具体步骤请参考 [^3]。

&emsp;&emsp;因为要区分单一 PCI 总线上的若干 PCI 设备中断号（#INA 等）是不够用的，所以我们还需要指出是哪个 PCI 设备触发了中断行。幸运的是我们还可以使用每个设备所拥有的唯一设备号。为了区分这些 PCI 设备，我们需要一个元组，该元组由 PCI 设备号和 PCI中断号组成。通俗的说，我们构造了由四个 cell 组成的设备中断指示符。

*  三个#address-cells 由 phys.hi、phys.mid、phys.low 组成，然后
* 一个 #interrupt-cell（#INTA、#INTB、#INTC、#INTD） 

&emsp;&emsp;因为我们只需要 PCI 地址中的设备号部分，所以 interrupt-map-mask 发挥了作用interrupt-map-mask 也是 4 元组，就像设备中断指示符一样。掩码的第一部分指出我们应该考虑设备中断指示符中哪一部分。在本例中，我们可以看到在 phys.hi 中只需要设备号部分，另外我们还需要 3 位来区分四个中断行（PCI 中断行是从 1 开始计数的，不是 0！）。

&emsp;&emsp;现在。我们可以构建 interrupt-map 属性了。该属性是一个表，这个表的每一项都由一个子（PCI 总线）设备中断指示符、一个父句柄（用于中断服务的中断控制器）和一个父设备中断指示符组成。因此，在第一行中我们可以知道 PCI 中断 #INTA 将被映射到中断控制器的 IRQ 9，并且是低电平有效。[^4]

&emsp;&emsp;目前为止，唯一没有讨论的就是 PCI 总线设备中断指示符里古怪的数字了。来自phys.hi 位域的设备号是设备中断指示符中的重要组成部分。设备号是平台特定的，并取决于 PCI 主控制器如何激活各个设备的 IDSEL 管脚。在本例中，PCI slot 1 分配设备id 24（0x18）,PCI slot 2 分配设备 id 25（0x19）。每个 slot 的 phys.hi 值是通过将设备号左移 11 位至位域的 ddddd 段得到的，就像下面： 

* slot 1 的 phys.hi 就是 0xC000， 并且
* slot 2 的 phys.hi 就是 0xC800。

把这些放在一起之后，interrupt-map 属性就显示为： 

* 在主中断控制器上 slot 1 的 #INTA 是 IRQ9，低电平触发
* 在主中断控制器上 slot 1 的 #INTB 是 IRQ10，低电平触发
* 在主中断控制器上 slot 1 的 #INTC 是 IRQ11，低电平触发
* 在主中断控制器上 slot 1 的 #INTD 是 IRQ12，低电平触发and
* 在主中断控制器上 slot 2 的 #INTA 是 IRQ10，低电平触发
* 在主中断控制器上 slot 2 的 #INTA 是 IRQ11，低电平触发
* 在主中断控制器上 slot 2 的 #INTA 是 IRQ12，低电平触发
* 在主中断控制器上 slot 2 的 #INTA 是 IRQ9，低电平触发


&emsp;&emsp;属性 interrupts = <8 0>; 描述了主控制器或 PCI 桥控制器本身有可能触发中断。不要与 PCI 设备触发的中断（使用 INTA，INTB，...）搞混了。

&emsp;&emsp;最后需要注意的事。就像 interrupt-parent 属性一样，节点中 interrupt-map 属性的存在将改变子节点和孙节点的默认中断控制器。在这个 PCI 示例中，这意味着 PCI 主桥变成了默认中断控制器。如果一个通过 PCI 总线连接的设备同时还直接连接至另一个中断控制器，这时就需要指定它自己的 interrupt-parent 属性。

[^1]:Tom Shanley / Don Anderson: PCI System Architecture. Mindshare Inc.
[^2]:PCI Bus Bindings to Open Firmware.
[^3]:Open Firmware Recommended Practice: Interrupt Mapping
[^4]: PCI interrupts are always level low sensitive.

## 3.8 总结
<table class='table table-celled table-component' border='1' style='border-collapse: collapse; width: 78.8668%;' id='tinymcetable' > <tbody>
<tr>
<td style="width: 100%; text-align: center;"><strong>常见节点和属性</strong><br /><br />
<table style="border-collapse: collapse; width: 97.8546%;" border="1">
<tbody>
<tr style="height: 20px;">
<td style="width: 2.79165%; border-style: solid; height: 343px; vertical-align: middle;" rowspan="15">根节点<br />/</td>
<td style="width: 12.5112%; height: 20px; vertical-align: middle;">节点</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">属性</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">属性说明</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">节点说明</td>
</tr>
<tr style="height: 40px;">
<td style="width: 12.5112%; height: 40px; vertical-align: middle;" rowspan="6">一般设备节点</td>
<td style="width: 11.6885%; height: 40px; vertical-align: middle;">#address-cells</td>
<td style="width: 47.9177%; height: 40px; vertical-align: middle;">子节点中的reg属性的地址的长度，也就是说需要用多少个u32的cell来描述该地址</td>
<td style="width: 22.9455%; height: 40px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">#size-cells</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">子节点中的reg属性的大小的长度，也就是说需要用多少个u32的cell来描述该大小</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">Compatible</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">兼容性，格式为"&lt;制造商&gt;,&lt;模块&gt;"</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">interrupt-parent</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">用于标识能产生中断的设备连接到哪个中断控制器</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 40px;">
<td style="width: 11.6885%; height: 40px; vertical-align: middle;">interrupts</td>
<td style="width: 47.9177%; height: 40px; vertical-align: middle;">对于一个能产生中断的设备，必须定义interrupts这个属性。也可以定义interrupt-parent这个属性，如果不定义，则继承其parent node的interrupt-parent属性</td>
<td style="width: 22.9455%; height: 40px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">&nbsp;status</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">&nbsp;</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 12.5112%; height: 40px; vertical-align: middle;" rowspan="2">chosen</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">bootargs</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">传递命令行参数</td>
<td style="width: 22.9455%; height: 40px; vertical-align: middle;" rowspan="2">描述由系统firmware指定的runtime parameter。如果存在chosen这个node，其parent node必须是根节点。</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">initrd-start</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">传递initrd的开始地址</td>
</tr>
<tr style="height: 20px;">
<td style="width: 12.5112%; height: 40px; vertical-align: middle;" rowspan="2">memory</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">device_type</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">对于memory node，其device_type必须为memory</td>
<td style="width: 22.9455%; height: 40px; vertical-align: middle;" rowspan="2">是所有设备树文件的必备节点，它定义了系统物理内存的布局</td>
</tr>
<tr style="height: 20px;">
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">reg</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">该属性的值被解析成任意长度的（address，size）数组， address和size在其父节点中定义（#address-cells和#size-cells）。对于device node，reg描述了memory-mapped IO register的offset和length。对于memory node，定义了该memory的起始地址和长度。</td>
</tr>
<tr style="height: 20px;">
<td style="width: 12.5112%; height: 43px; vertical-align: middle;" rowspan="2">interrupt-controller&nbsp;</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">#interrupt-cells</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">用多少个u32（即cells）来标识一个interrupt source</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 23px;">
<td style="width: 11.6885%; height: 23px; vertical-align: middle;">interrupt-controller</td>
<td style="width: 47.9177%; height: 23px; vertical-align: middle;">为空，表明该设备是一个中断控制器</td>
<td style="width: 22.9455%; height: 23px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 12.5112%; height: 20px; vertical-align: middle;">&nbsp;aliases</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">&nbsp;</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">定义了一些别名，方便引用节点时省写完整路径</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;</td>
</tr>
<tr style="height: 20px;">
<td style="width: 12.5112%; height: 20px; vertical-align: middle;">&nbsp;cpus</td>
<td style="width: 11.6885%; height: 20px; vertical-align: middle;">&nbsp;</td>
<td style="width: 47.9177%; height: 20px; vertical-align: middle;">对于cpus node，#address-cells 是1，而#size-cells是0。</td>
<td style="width: 22.9455%; height: 20px; vertical-align: middle;">&nbsp;对于根节点，必须有一个cpus的child node来描述系统中的CPU信息。</td>
</tr>
</tbody>
</table>
</td>
</tr>
</tbody> </table>

# 4 设备树二进制文件
&emsp;&emsp;经过Device Tree Compiler编译，Device Tree source file变成了Device Tree Blob（又称作flattened device tree）的格式。Device Tree Blob的数据组织如下图所示：

![dtb结构图](https://www.github.com/liao20081228/blog/raw/master/图片/Linux设备树/2.gif "dtb结构")

![4](https://www.github.com/liao20081228/blog/raw/master/图片/Linux设备树/4.png)

&emsp;&emsp; alignment gap为填充域，使用0进行填充。
## 4.1 DTB header
&emsp;&emsp;DTB header主要描述设备树的一些基本信息，例如设备树大小，结构块偏移地址，字符串块偏移地址等。偏移地址是相对于设备树头的起始地址计算的。
```c
struct boot_param_header {
    __be32 magic;          //设备树魔数，固定为OF_DT_HEADER=0xd00dfeed（大端）或者0xedfe0dd0（小端）。
    __be32 totalsize;            //整个设备树的大小
    __be32 off_dt_struct;         //DT structure block在整个设备树中的偏移
    __be32 off_dt_strings;          //DT string block在设备树中的偏移
    __be32 off_mem_rsvmap;        //保留内存区，该区保留了不能被内核动态分配的内存空间
    __be32 version;            //设备树版本
    __be32 last_comp_version;    //向下兼容版本号
    __be32 boot_cpuid_phys;    //在多核处理器中用于启动的主cpu的物理id
    __be32 dt_strings_size;    //DT string block大小
    __be32 dt_struct_size;     //DT structure block大小
};
```


|字段	|描述|
|--|--|
|magic	|用来识别DTB的。通过这个magic，kernel可以确定bootloader传递的参数block是一个DTB还是tag list。|
|totalsize|DTB的total size|
|off_dt_struct|	device tree structure block的offset
|off_dt_strings|device tree strings block的offset
|off_mem_rsvmap| memory reserve map的offset。有些系统，我们也许会保留一些memory有特殊用途（例如DTB或者initrd image），或者在有些DSP+ARM的SOC platform上，有写memory被保留用于ARM和DSP进行信息交互。这些保留内存不会进入内存管理系统。
|version|	该DTB的版本。
|last_comp_version|兼容版本信息
|boot_cpuid_phys|	我们在哪一个CPU（用ID标识）上booting
|dt_strings_size|	DT string block的size。它和off_dt_strings一起确定了strings block在内存中的位置
|dt_struct_size|DT structure block的size。它和和off_dt_struct一起确定了device tree structure block在内存中的位置
|&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;|&emsp;|

## 4.2 memory reserve map

&emsp;&emsp;这个区域包括了若干的reserve memory描述符。每个reserve memory描述符是由address和size组成。其中address和size都是用U64来描述。


## 4.3 DT structure block

&emsp;&emsp;设备树结构块是一个线性化的结构体，是设备树的主体，以节点的形式保存了主板上的设备信息。在结构块中，以宏`OF_DT_BEGIN_NODE`标志一个节点的开始，以宏`OF_DT_END_NODE`标识一个节点的结束，整个结构块以宏`OF_DT_END` 结束。在`/kernel/include/linux/of_fdt.h`中有相关定义，我们把这些宏称之为tag。共计有5种tag，定义如下：
```cpp
#define FDT_BEGIN_NODE  0x1    /*描述一个node的开始位置，紧挨着该tag的就是node name（包括unit address）*/
#define FDT_END_NODE    0x2     /*描述了一个node的结束位置。*/
#define FDT_PROP    0x3    /*描述了一个property的开始位置，该tag之后是两个u32的数据，分别是length和name offset。length表示该property value data的size。name offset表示该属性字符串在device tree strings block的偏移值。length和name offset之后就是长度为length具体的属性值数据。*/
#define FDT_NOP     0x4     
#define FDT_END     0x9  /*标识了一个DTB的结束位置。*/

#define FDT_V1_SIZE (7*sizeof(uint32_t))
#define FDT_V2_SIZE (FDT_V1_SIZE + sizeof(uint32_t))
#define FDT_V3_SIZE (FDT_V2_SIZE + sizeof(uint32_t))
#define FDT_V16_SIZE FDT_V3_SIZE
#define FDT_V17_SIZE (FDT_V16_SIZE + sizeof(uint32_t))

/*节点信息使用struct fdt_node_header结构体描述。*/
struct fdt_node_header  {
    fdt32_t  tag;     	/*一般为
    char  name[0];

};

 
/*属性信息使用struct fdt_property结构体描述*/
struct  fdt_property  {
    fdt32_t  tag;
    fdt32_t  len;     /*len为属性值的长度（包括‘\0’，单位：字节）*/
    fdt32_t  nameoff;   /*nameoff为属性名称存储位置相对于off_dt_strings的偏移地址*/
    char  data[0];          
};
```

一个节点主要由以下几部分组成：

1. 节点开始标志：一般为FDT_BEGIN_NODE。
2. 节点路径或者节点的单元名(version小于3以节点全路径表示，version>=16以节点单元名表示)
3. 填充字段（对齐到四字节）
4. 节点属性。每个属性以宏FDT_PROP开始，后面依次为属性值的字节长度(4字节)、属性名称在字符串块中的偏移量(4字节)、属性值和填充（对齐到四字节）。
5. 如果存在子节点，则定义子节点。
6. 节点结束标志FDT_END_NODE。

节点使用`struct fdt_node_header`组织数据，属性使用`struct  fdt_property `组织数据。


![3](https://www.github.com/liao20081228/blog/raw/master/图片/Linux设备树/3.jpg)

![5](https://www.github.com/liao20081228/blog/raw/master/图片/Linux设备树/5.jpg)

## 4.4 DT strings block
&emsp;&emsp;通过节点的定义知道节点都有若干属性，而不同的节点的属性又有大量相同的属性名称，因此将这些属性名称提取出一张表，当节点需要应用某个属性名称时直接在属性名字段保存该属性名称在字符串块中的偏移量。





# 5 设备树编译与调试
&emsp;&emsp;DTC为编译工具，它可以将.dts文件编译成.dtb文件。DTC的源码位于内核的`scripts/dtc`目录，内核选中CONFIG_OF，编译内核的时候，可执行程序DTC就会被编译出来。 即scripts/dtc/Makefile中
```makefile
hostprogs-y := dtc
always := $(hostprogs-y) 
```
&emsp;&emsp;在内核的arch/arm/boot/dts/Makefile中，若选中某种SOC，则与其对应相关的所有dtb文件都将编译出来。在linux下，`make dtbs`可单独编译dtb。以下截取了TEGRA平台的一部分。
```makefile
ifeq ($(CONFIG_OF),y)
dtb-$(CONFIG_ARCH_TEGRA) += tegra20-harmony.dtb tegra30-beaver.dtb tegra114-dalmore.dtb tegra124-ardbeg.dtb 
```
## 5.1 dtc 
**命令**：dtc [options] input_file
**描述**：设备树编译器dtc将给定格式的设备树作为输入，输出另一种格式设备树，用于在嵌入式系统上引导内核。



|选项|描述|
|:--|:--|
| -h   |  显示帮助|
|-q  |不显示警告
|-qq| 不显示错误
|-qqq|不显示警告和错误|
|-I input_format|输入格式:dts ,dtb， fs |
|-o output_file|输出文件
| -O output_format|输出格式：dts ,dtb，asm|
|-V output version| 在dtb文件中保存blob版本，，默认值为17（仅与dtb和asm输出相关）。
|-d dependency_file|输出依赖文件
| -R num|for  num reserve map entries留出空间（仅与dtb和asm输出相关）
|-S bytes|使blob大小至少为bytes|
| -p bytes|填充到bytes长的blob
|-b number|设置启动CPU的编号。
|-F| 即使输入设备树树有错误，也尝试生成输出。
|-s |在输出之前对节点和属性进行排序（仅对比较树有用）
|-v|打印DTC版本并退出。|
| -H phandle_format|legacy , epapr ,both |

## 5.2 fdtget和fdtput
**命令**：fdtget [options] dt_file [node  property]...
&emsp;&emsp;&emsp;fdtget -p [options] dt file [node]...
**描述**：从二进制设备树中读取数据

|选项|长选项|描述|
|:--|:--|:--|
| -h   |  --help|显示帮助|
|-V|--version|显示版本号|
|-t type|--type arg| s=string, i=int, u=unsigned, x=hex，可选前缀:hh/b=1B, h=2B, l=4B(default)
|-p|--properties|列出每个节点的属性|
|-l|--list|列出每个节点的子节点|
|-d arg|--default arg|当没有指定属性时，要显示的默认值|

**命令**：fdtput [options] dt_file [node property [value...]]
&emsp;&emsp;&emsp;fdtput -c [options] dt_file [node...]
**描述**：写数据到二进制设备树中



|短选项|长选项|描述|
|:--|:--|:--|
| -h   |--help|  显示帮助|
| -c| --create |  如果节点不存在创建节点
|-p|--auto-path|  根据需要节点的路径自动创建节点
|-v|--verbose|显示详细过程
|-t type|--type arg| s=string, i=int, u=unsigned, x=hex，可选前缀:hh/b=1B, h=2B, l=4B(default)


## 5.3 fdtdump
* **命令**：fdtdump [options] DTB_file_name
* **描述**：输出二进制设备树的可读版本
|短选项|长选项|描述|
|:--|:--|:--|
|-d| --debug |解码文件时转储调试信息|
|-s|--scan   |浏览设备树文件|
|-h|--help   | 显示帮助并退出|
|-V|--version |显示版本号并退出|

# 6 设备树工作原理










 
<5> machine_desc结构
内核提供了一个重要的结构体struct machine_desc ，这个结构体在内核移植中起到相当重要的作用，内核通过machine_desc结构体来控制系统体系架构相关部分的初始化。machine_desc结构体通过MACHINE_START宏来初始化，在代码中， 通过在start_kernel->setup_arch中调用setup_machine_fdt来获取。
struct machine_desc {
    unsigned int nr; /* architecture number */
    const char *name; /* architecture name */
    unsigned long atag_offset; /* tagged list (relative) */
    const char *const *dt_compat; /* array of device tree* 'compatible' strings */
    unsigned int nr_irqs; /* number of IRQs */
    
#ifdef CONFIG_ZONE_DMA
    phys_addr_t dma_zone_size; /* size of DMA-able area */
#endif
    
    unsigned int video_start; /* start of video RAM */
    unsigned int video_end; /* end of video RAM */
    
    unsigned char reserve_lp0 :1; /* never has lp0 */
    unsigned char reserve_lp1 :1; /* never has lp1 */
    unsigned char reserve_lp2 :1; /* never has lp2 */
    enum reboot_mode reboot_mode; /* default restart mode */
    struct smp_operations *smp; /* SMP operations */
    bool (*smp_init)(void);
    void (*fixup)(struct tag *, char **,struct meminfo *);
    void (*init_meminfo)(void);
    void (*reserve)(void);/* reserve mem blocks */
    void (*map_io)(void);/* IO mapping function */
    void (*init_early)(void);
    void (*init_irq)(void);
    void (*init_time)(void);
    void (*init_machine)(void);
    void (*init_late)(void);
#ifdef CONFIG_MULTI_IRQ_HANDLER
    void (*handle_irq)(struct pt_regs *);
#endif
    void (*restart)(enum reboot_mode, const char *);
};
 
<6>设备节点结构体
struct device_node {
    const char *name;    //设备name
    const char *type; //设备类型
    phandle phandle;
    const char *full_name; //设备全称，包括父设备名
 
    struct property *properties; //设备属性链表
    struct property *deadprops; //removed properties
    struct device_node *parent; //指向父节点
    struct device_node *child; //指向子节点
    struct device_node *sibling; //指向兄弟节点
    struct device_node *next; //相同设备类型的下一个节点
    struct device_node *allnext; //next in list of all nodes
    struct proc_dir_entry *pde; //该节点对应的proc
    struct kref kref;
    unsigned long _flags;
    void *data;
#if defined(CONFIG_SPARC)
    const char *path_component_name;
    unsigned int unique_id;
    struct of_irq_controller *irq_trans;
#endif
};
 
<7>属性结构体
struct property {
    char *name;        //属性名
    int length;        //属性值长度
    void *value;        //属性值
    struct property *next; //指向下一个属性
    unsigned long _flags; //标志
    unsigned int unique_id;
};


------


&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文章参考了<font color=blue >[《Device Tree Usage》](http://www.devicetree.org/Device_Tree_Usage)、[《uboot之fdt介绍》](https://www.cnblogs.com/leaven/p/6295999.html)、[《蜂窝科技的几篇文章》](http://www.wowotech.net/device_model/dt_basic_concept.html)，宋牧春[《Linux设备树文件结构与解析深度分析》](https://blog.csdn.net/woyimibayi/article/details/77574736)</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------