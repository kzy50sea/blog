---
title: Linux内核构建系统
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

# 1 简介
Linux的内核构建系统主要由几个组成：
* Kbuild
* Kconfig
* Makefile
* scripts/


# 2 内核配置详解
## 2.1 内核配置过程详解
* Linux内核的**配置系统**由三个部分组成，分别是：
	* `Makefile`——分布在 Linux 内核源代码根目录及各层目录中，定义 Linux 内核的编译规则；
	* `Kconfig`——分布在 Linux 内核源代码根目录及各层目录中，给用户提供配置选择的功能；
	* `scripts`——分布在 Linux内核源代码根目录下，包括配置命令解释器（对配置脚本中使用的配置命令进行解释）和配置用户界面（提供基于字符界面、基于 Ncurses 图形界面以及基于 Xwindows 图形界面的用户配置界面，各自对应于 Make config、Make menuconfig 和 make xconfig）。
* 当我们使用`make menuconfig`这个命令时（其它配置命令类似）：
	* 首先由make编译生成`scripts/kconfig/mconf.c`生成`scripts/kconfig/mconf`。（xconfig对应qconf，gconfig对应gconf，config对应conf）
	* 然后执行`scripts/kconfig/mconf Kconfig`
	* mconf程序读取内核根目录下的Kconfig文件，Kconfig载入了`arch/$(SRCARCH)/Kconfig`，`arch/$(SRCARCH)/Kconfig`又分别载入各目录下的Kconfig文件，以此递归下去，最后生成主配置界面以及各级配置菜单。`SRCARCH`是由顶层Makefile中定义的，它等于`$(ARCH)`，而`$ARCH`由Makefile或make的命令行参数指定。
	* 在完成配置后，mconf会将配置保存在Linux内核源代码根目录下的`.config`文件中。

* 当我们使用`make defconfig`这个命令时：系统直接将`arch/$SRCARCH/configs`（**该目录存放内核的默认配置文件**）下的对应的默认配置文件拷贝到Linux内核源代码根目录下的`.config`文件。


## 5.2 内核编译过程详解
* 在输入编译命令后，make首先调用脚本来读取`.config`文件，并根据内容载入对应文件到`include/config/`，并将一些配置项写入`include/config/auto.conf`。
* 脚本程序将`include/config/auto.conf`中的配置项`CONFIG_XXXX=y|m|xxx`翻译为宏定义`#define CONFIG_XXXX[_MODULE] 1|xxx`，并写入`include/generate/autoconf.h`中。
* autoconf.h就是`include/config/auto.conf`中的配置项的内容的C语言写法，以便在以后使用的时候作为宏定义出现，以实现条件编译。
* make根据Makefile执行编译。

# 6 在内核中添加程序
* 将源代码拷贝到内核源码的相应目录
* 修改对应目录下的Kconfig文件，按照Kconfig语法增加对应的选项；
* 修改对应目录下的Makefile文件完成编译选项的添加`obj-$(CONFIG_symbol)+= filename.o`；




# 2 Linux内核Kconfig语法

标签（空格分隔）： Linux内核

&emsp;&emsp;***<font color=blue>版权声明</font>***：*本文章参考了<font color=blue >**《Linux内核官方文档》。**</font><font color=red>未经作者允许，**<font color=blue>严禁用于商业出版</font>**，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>*

---

# 1 Kconfig格式
```
菜单入口 "菜单入口名"
   [依赖]
   [反向依赖]
   [引入其它Kconfig文件]
   ... ...
   [帮助]
    
    
配置项 symbol
   值类型
   [输入提示]
   [默认值]
   [依赖]
   [反向依赖]
   ... ...
   [帮助]
```

#2 属性
* `bool/tristate/int/hex/string`
值类型，只有配置选项有值类型。包括： bool——值为y或n、 tristate——值为y或m或n、string——值为字符串、int——值为十进制整数、 hex——值为十六进制整数

* `prompt "提示字符串" [if <expr>]`
输入提示：每个菜单入口最多只能有一个展示给用户看的输入提示，可以使用“if”来表示输入提示的依赖性，这个依赖性是可选的。

* `default <expr> [if <expr>]`
默认值：一个配置选项可以有任意多个默认值，但只有第一个有效的。当输入提示是可见时，才能看到默认值，并且可以输入一个值将默认值覆盖。与输入提示一样，可以使用“if”来表示默认值的依赖性，这个依赖性是可选的。

* `def_bool/def_tristate <expr> ["if" <expr>]`
值类型+默认值：说明值类型时同时说明默认值。可以使用“if”来表示默认值的依赖性，这个依赖性是可选的。

* `requires(或depends on）<expr>`，
依赖: 依赖项给菜单入口或配置项定义了一个依赖规则，只有当expr为真或被选中时，该菜单入口或配置项才有效，依赖对菜单入口或配置项中的其它属性都有效。

* `select <symbol> ["if" <expr>]`
反向依赖：当前配置项被选中时，选中symbol，忽略依赖项和手动设置值。**只有boolean和tristate类型的symbol可以使用反向依赖**。

* `imply <symbol> [if <expr>]`
弱反向依赖：当前配置项被选中时，选中symbol，但是symbol仍然可由依赖或者手动配置为n。

* `range <value1> <value2> ["if"<expr>]`，
值范围：**限定int和hex类型symbol的输入值**。用户只能输入一个大于等于第一个symbol的值，并且小于等于第二个symbol的值。

* `visible if <expr>`
只能用于menu/endmenu中，当expr为真时菜单可见。

* `---help---/help`
帮助:定义了一个帮助文本。帮助文本的结尾是根据缩进级别来决定的，这就意味着如果帮助文本中某一行相对于第一行有更小的缩进，那么这一行就是帮助文档的最后一行。"---help---"和“help”在使用功能上没有区别，"---help---"是用来作为对开发者的一种提示，它显式区别于文档中的配置选项“help”。

* `option <symbol>[=<value>]`
其它属性:各种不常见的选项的通过这个选项来定义，比如修改菜单入口的行为和配置symbol。下面这些配置当前是允许的：
 
 * `defconfig_list`
 定义了一系列默认入口，当使用默认配置时可以从这里寻找（当主.config文件不存在时会使用默认配置）
 * `modules`
 声明了一个symbol将被当做MODULES symbol，MODULES symbol是所有配置symbol的第三种模块化状态。
 * `env=<value>`，
 导入了一个环境变量到Kconfig中。环境变量就像是Kconfig中的一个默认值，但是它是从外部环境中导入的。正因为它从外部环境导入，所以赋值的这个时候它相对于正常的默认值来说是没有定义的。这个symbol当前没有导出到构建环境中（如果想要这样的话，可以通过另一个symbol导出）
 * `allnoconfig_y`
 声明symol默认值为y。

>**补充**：
>
>* 值类型后可以紧跟一个输入提示（也可以单独使用一个提示属性），所以下面的这两个例子是等价的：
>```
>bool "Networking support"
>#和
>bool
>prompt "Networking support"
>```
>* 依赖对菜单入口中的其它属性都有效，下面两种写法是等价的
>```
>bool"foo" if BAR
>default y if BAR
>#和
>depends on BAR
>bool "foo"
>default y
>```
>* 并非每个菜单入口或者配置项都具有所有的属性。
>* 限制一个配置项只能编译为模块或不选择。
>```
>config FOO
>       dependson m
>```
>* 限制一个配置项只能编译或模块化
>```
>config FOO
>   tristate "foo"
>   select FOO if m
>   default m
>```
> * 如果一个配置项只有值类型属性，则它的选中只能通过其他配置项的select属性


#3 表达式expr
```
<expr> ::= <symbol>                             (1)
           <symbol> '=' <symbol>                (2)
           <symbol> '!=' <symbol>               (3)
           <symbol1> '<' <symbol2>              (4)
           <symbol1> '>' <symbol2>              (4)
           <symbol1> '<=' <symbol2>             (4)
           <symbol1> '>=' <symbol2>             (4)
           '(' <expr> ')'                       (5)
           '!' <expr>                           (6)
           <expr> '&&' <expr>                   (7)
           <expr> '||' <expr>                   (8)


``` 

* 表达式以降序的顺序排列在下面。
 1. 将一个symbol转换成表达式。Bool和tristate symbol简单地转换成相应的表达式值。其它类型的symbol就转换成‘n’。
 2. 如果两个symbol的值相等，就返回‘y’，否则返回‘n’。
 3. 如果两个symbol的值不相等，就返回‘n’，否则返回‘y’。
 4. 如果symbol1的值小于、大于、小于等于、大于等于symbol2的值返回y，否则返回n。
 5. 返回表达式的值。用来覆盖之前的值
 5. 返回（2-表达式的值）
 6. 返回min（expr,expr2）
 7. 返回max（expr,expr2）

* 一个表达式的值可以是'n','m'或'y'（或者相对于0,1,2）。当表达式的值是m或者y的时候，菜单入口就是可见的。

* 存在两种类型的symbol：常数symbol和非常数symbol。非常数symbol是最常见的一类symbol，定义的时候使用‘config’来声明。非常数symbol由字母和下划线组成。

* 常数symbol只是表达式的一部分。常数symbol通常被单引号或者双引号包围着。在引号中，任何字母都是允许的，并且可以使用‘\’进行转义。

 





#4 菜单入口

* 主菜单——最顶层的菜单
```
mainmenu "主菜单名字"
    
```
* 多选菜单——带配置项但本身不可配置。**它的属性只能是依赖项和可见性**。
```
menu "string"
    ... ...
endmenu 
```
* 可选菜单——带配置项且本身是配置项。配置关键字前面添加CONFIG_后就构成了“.config”文件中的配置项名字。
```
menuconfig 配置关键字
    ... ...
```
* config——配置项，配置关键字前面添加CONFIG_后就构成了“.config”文件中的配置项名字，但不是配置界面显示的字符，配置界面显示的是提示字符。
```
config 配置关键字
    ... ...
```

* 单选菜单，**单选菜单只能是bool类型或tristate类型** ，并且布尔选择只允许一个单一的配置项被选中，三态选择还允许任何配置项被设置为“M”。这可以用在下面的情况：如果一个硬件存在多个驱动程序，并且只有一个驱动程序可以编译/加载到内核中，但所有的驱动程序可以编译成模块。
```
 choice
    ... ...
 endchoice
```

* 注释，这定义了一条在用户配置过程中显示的注释，同时会写入导出文件。**它的属性只有依赖项**。
```
 comment
```
* 条件——当expr为真或选中时，中间的内容才有效。
```
if <expr>
 ... ...
endif
```

* 引入其他Kconfig文件，方便菜单嵌套。
```
source "...dir/Kconfig"
```


#5 菜单结构
* 一种是使用了菜单入口明确指定，如下中所有位于“menu”…和“endmenu”之间的入口都是"Network device support"的一个子菜单入口。所有的子入口都继承了菜单入口的依赖项，例如，依赖项”NET”就会被加入到子菜单”NETDEVICESx”的依赖项列表中。
```
menu "Network device support"
    depends on NET
    config NETDEVICES1
       ...
    config NETDEVICES2
       ... 
    menuconfig NETDEVICES3
       ...
        config NETDEVICES1
            ...
        config NETDEVICES2
            ... 
endmenu
```

* 另外一种生成菜单结构的方法是通过分析依赖项。如果一个菜单入口依赖依赖于前一个入口，那么它就是前一个入口的一个子菜单。首先，之前的（父）symbol一定位于子入口的依赖列表中，其次，下面两个条件中有一个必须是真的：
 * 子入口必须是不可见的，当父symbol被设置成’n’
 * 子入口必须是可见的，当父菜单是可见的
```
config MODULES
    bool"Enable loadable module support"
config MODVERSIONS
    bool"Set version information on all module symbols"
    dependson MODULES
comment "module support disabled"
    dependson !MODULES
```
&emsp;&emsp;***<font color=blue>版权声明</font>***：*本文章参考了<font color=blue >**《Linux内核官方文档》。**</font><font color=red>未经作者允许，**<font color=blue>严禁用于商业出版</font>**，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>*






------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
