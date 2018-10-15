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

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
