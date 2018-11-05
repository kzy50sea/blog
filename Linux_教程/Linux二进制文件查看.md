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
**描述**：查看非纯文本文档。
|常用选项&emsp;|作用|
|:----|--|
|-t TYPE |a 利用默认的字符来输出；c 使用 ASCII 字符来输出;d[size]利用十进制来输出数据，每个整数占用 size bytes ；f[size] 利用浮点数来输出数据，每个数占用 size bytes ；o[size] 利用八进制来输出数据，每个整数占用 size bytes ；x[size] 利用十六进制来输出数据，每个整数占用 size bytes ；
|**更多信息**|<http://linux.51yip.com/search/od> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/od>|




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


------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
