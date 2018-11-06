---
title: Linux二进制文件查看
tags: Linux教程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
<style>table{word-break:initial;}</style>
# 1 `od`
**命令**：od [OPTION]... [FILE]...
&emsp;&emsp;&emsp;od [-abcdfilosx]... [FILE] [[+]OFFSET[.][b]]
&emsp;&emsp;&emsp;od --traditional [OPTION]... [FILE] [[+]OFFSET[.][b] [+][LABEL][.][b]]
**描述**：读取FILE并以指定格式输出到标准输出，默认为每两个字节用六个八进制数表示。 如果有多个FILE参数，以列出的顺序连接它们以形成输入。如果没有FILE，或者FILE是 - ，则读取标准输入。如果第一和第二调用格式都适用，则假设最后一个操作数以+或（如果有2个操作数）以数字开头，则假定第二种格式。 OFFSET操作数表示-j OFFSET。 LABEL是打印的第一个字节的伪地址，在转储进行时递增。 对于OFFSET和LABEL，0x或0X前缀表示十六进制; 后缀可以是八进制，b是乘以512。长选项的参数也适用于短选项。

       
|短选项|长选项|秒速|
|:--|:--|:--|
|-A| ---address-radix=RADIX| 文件偏移量的输出格式; RADIX是[doxn]之一，用于十进制，八进制，十六进制或不出出偏移量



|-t TYPE |a 利用默认的字符来输出；c 使用 ASCII 字符来输出;d[size]利用十进制来输出数据，每个整数占用 size bytes ；f[size] 利用浮点数来输出数据，每个数占用 size bytes ；o[size] 利用八进制来输出数据，每个整数占用 size bytes ；x[size] 利用十六进制来输出数据，每个整数占用 size bytes ；





#  2 `hexdump`
**命令**：hexdump [-bcCdovx] [-e format_string] [-f format_file] [-n length] [-s skip] file ...
&emsp;&emsp;&emsp;hd [-bcdovx] [-e format_string] [-f format_file] [-n length] [-s skip] file ...
**描述**：hexdump实用程序是一个过滤器，它以用户指定的格式显示指定的文件或标准输入（如果未指定文件）。

|选项|描述|
|:--|:--|
|-b|每字节用三个八进制数显示，每行16个字节，用空格分开，第一列为十六进制的偏移量|
|-c|每字节用ASCII字符显示。每行16个字节，用空格分开，第一列为十六进制的偏移量|
|-C|每字节用两个十六进制数显示，末尾用对应的ASCII字符显示，每行16个字节，用空格分开，第一列为十六进制的偏移量。hd命令默认此选项|
|-d|每两字节用五个十进制数表示，每行16个字节，用空格分开，第一列为十六进制的偏移量|
|-e format_string|根据指定的格式字符串显示数据|
|-f format_file|根据指定的格式文件显示数据，该文件包含一个或多个换行符分隔的格式字符串。以#开头的行和空行会被忽略|
|-n length| 只显示length个字节|
|-o|每两字节用六个八进制数表示，每行16个字节，用空格分开，第一列为十六进制的偏移量|
|-s offset|从第offset个字节开始显示，可以带有单位b表示512，k表示1024,m表示1024^2^。offset带有前导0但不是十六进制数时被视为8进制数。
|-v|显示所有数据，对于连续重复的16字节数据，如果不加-v则其余的所有重复行用一个`*`表示。
|-x|每两字节用四个十六进制数表示，每行16个字节，用空格分开，第一列为十六进制的偏移量


&emsp;&emsp;**格式字符串**由任意数量的以空格隔开的格式单元组成，包含在一对单引号中。每个格式单元包括字节计数，迭代计数，格式 三部分。形如：`'迭代计数/字节计数 "format1" "format2" 迭代计数/字节计数 "format1" "format2"  ...'`。

* 字节计数是一个可选的正整数，表示每几个字节使用format1来显示（比如每字节用三个八进制数显示）
	*  `%_c, %_p, %_u, %c ` 只支持字节计数为1。
    *   `%d, %i, %o, %u, %X, %x`  支持字节计数为1，2，4（默认）。
    * `%E, %e, %f, %G, %g `    支持字节计数为4，8（默认），12。 
* 迭代计数是一个可选的正整数，缺省为1，表示每几个字节使用format2来显示。通常迭代计数要大于字节计数。（比如每行16个字节）
* format1 format2 ....必须用双引号包围，且format1不可省略。
	* **与C函数printf类似**，但有几点不同：
		* 星号（\*）不能用作域宽或精度。在printf中如果域宽和精度用星号则表示其由紧随的参数决定。
		* 对每个转换字符`s`都需要指定字节计数或精度（与fprintf不同，printf如果未指定精度则打印整个字符串）。
		* 不支持转换字符“％”，“h”，“l”，“n”，“p”和“q”。
		* 支持C标准中描述的单字符转义序列：
			* NUL  \0
			* 警告  \a
			* 退格  \b
			* 换页  \f
			* 换行   \n
			* 回车   \r
			* 水平制表   \t
			* 垂直制表   \v
	* 支持以下附加转换字符串：
		* `_a[d|o|x]`   以十进制，八进制,十六进制显示偏移量。
		* `_A[dox]`   与`_a`转换字符串类似，只是在处理完所有输入数据后才执行一次。
        * `_c` 输出默认字符集中的字符。除了可通过标准转义符号表示的那些非打印字符外，其余非打印字符显示为三个八进制字符，用零填充。
        * ` _p`输出默认字符集中的字符。非打印字符显示为单个“.”。
        * `_u`  输出US ASCII字符，但使用小写的控制字符名来显示控制字符。大于0xff的十六进制的字符显示为十六进制字符串。
	* 如果未指定格式字符串，则默认显示等效于指定-x选项。
------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
