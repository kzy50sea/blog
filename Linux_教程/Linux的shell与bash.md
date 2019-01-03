---
title: Linux的shell与bash
tags: Linux教程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>


# 1 shell的概念
&emsp;&emsp;shell就是向用户提供操作系统的接口。

&emsp;&emsp;系统支持的shell存放在`/etc/shells`文件中。

&emsp;&emsp;Bash shell的特点：
 
> * 命令历史——`~/.bash_history`记录的是此次登入以前所执行过的指令， 而此次登入所执行的指令都被暂存在内存中，当你成功的注销系统后，该指令记忆才会记录到`~/.bash_history `当中。
 * TAB键命令与文件补全
  * [Tab] 接在一串指令的第一个字的后面，则为命令补全；
  * [Tab] 接在一串指令的第二个字以后时，则为『文件补齐』！
* 命令别名
  * alias new_commad='old_commad'——用新命令名取代原命令名
  * `alias` ——查看当前命令别名。
* 工作控制、前景背景控制
* shell脚本
* 支持通配符



#2 shell环境配置
##2.1 欢迎信息与登陆提示
&emsp;&emsp;本地登陆时欢迎信息记录在`/etc/issue`中，telnet登陆时欢迎信息记录在`/etc/issue.net`中。支持特殊代码。
&emsp;&emsp;登陆后的提示信息记录在`/etc/motd`中。不支持特殊代码。
|特殊代码|含义|
|--|--|
|\4或\4{interface}|将IPv4地址插入指定的网络接口（例如\ 4 {eth0}），如果未指定接口参数，则选择第一个完全配置的接口。如果未找到任何已配置的接口，则返回到机器主机名的IP地址。
|\6或\6{interface}|与\ 4相同，但对于IPv6。
|\b|插入当前行的波特率。
|\d|插入当前日期。
|\s|插入系统名称，操作系统的名称。与'uname -s'相同。
|\S或\S {VARIABLE}|从/etc/ os-release插入VARIABLE数据，如果文件不存在，回退到/usr/lib/ os-release。如果未指定VARIABLE参数，则使用文件中的PRETTY_NAME或系统名称
|\l|插入当前tty的名称。
|\m|插入机器的架构标识符。与`uname -m'相同。
|\n|插入机器的节点名称，也称为主机名。与`uname -n'相同。
|\o|插入机器的NIS域名。与`hostname -d'相同。
|\O|插入机器的DNS域名。
|\r|插入操作系统的版本号。与`uname -r'相同。
|\t|插入当前时间。
|\u|插入当前登录用户的数量。
|\U|插入字符串“<n> users”，其中<n>是当前登录用户的数量。
|\v|插入操作系统的版本，例如。建造日期等
|&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;||

## 2.2 配置文件载入过程
* login shell——取得bash时需要完整的登入流程。login shell 只读取两个配置文件：
 * `/etc/profile`——系统整体设定，每个使用者登入取得bash 时一定会读取该文件！
     * 设置一些环境变量
         * PATH：会依据 UID 决定 PATH 变量要不要含有 sbin 的系统指令目录；
         * MAIL：依据账号设定好使用者的 mailbox 到 `/var/spool/mail/`账号名；
         *  USER：根据用户的账号设定此一变量内容；
         * HOSTNAME：依据主机的 hostname 指令决定此一变量内容；
         * HISTSIZE：历史命令记录笔数。CentOS 7.x 设定为 1000 ；
         * umask：包括 root 默认为 022 而一般用户为 002 等！ 
     * 读取设定文件`/etc/bash.bashrc`
     * 读取外部设定文件 `/etc/profile.d/*.sh`——只要在 `/etc/profile.d/ `这个目录内且扩展名为 .sh ，使用者能够具有 r 的权限， 那么该文件就会被 `/etc/profile` 调用进来。在 CentOS 7.x 中，这个目录底下的文件规范了 bash 操作接口的颜色、 语系、ll 与 ls 指令的命令别名、vi的命令别名、which的命令别名等。如果你需要帮所有使用者设定一些共享的命令别名时， 可以在这个目录底下自行建立扩展名为 .sh 的文件，并将所需要的数据写入即可！
         * `/etc/locale.conf`——这个文件是由 `/etc/profile.d/lang.sh` 调用进来的！决定bash预设使用何种语系的配置文件！文件里最重要的就是LANG/LC_ALL 这变量的设定。
         * `/usr/share/bash-completion/completions/*`—— [tab]进行指令的选项/参数补齐功能,就是从这个目录里面找到相对应的指令来处理！其实这个目录底下的内容是由`/etc/profile.d/bash_completion.sh` 这个文件载入！
 * `~/.bash_profile`或`~/.bash_login`或`~/.profile`——属于使用者个人设定。login shell 设定只会读取上面三个文件的其中一个，而读取的顺序则是依照上面的顺序。另外，图形模式登录时，此文件将被读取，即使存在`~/.bash_profile`和`~/.bash_login`。这三个文件的功能在于：
     * 将`$HOME/.local/bin`和`$HOME/bin`添加到环境变量PATH。
     * 读取配置文件`~/.bashrc`。

* non-login shell——取得bash接口的方法不需要重复登入的举动。non-login shell只会读取`/etc/bashrc`（redhat）或`/etc/bash.bashrc`（debian）和`~/.bashrc`。  作用
 * 依据不同的 UID 规范出 umask 的值；
 * 依据不同的 UID 规范出提示字符 (就是 PS1 变量)；
 * 设置命令别名
 * 设置环境变量HISTFILESIZE与HISTSIZE

* 其它配置文件
 * `/etc/man_db.conf`——这的文件的规范了使用 man 的时候， man page 的路径到哪里去寻找！，如果以 tarball 的方式来安装，man page 可能会放置在 /usr/local/softpackage/man ，这个时候就得以手动的方式将该路径加到 /etc/man_db.conf ，否则使用 man 的时候就会找不到相关的说明档。
 * `~/.bash_history`——该文件用于记录历史命令。这个文件能够记录几笔数据与 HISTFILESIZE 有关。每次登入bash后，bash会先读取这个文件，将所有的历史指令读入内存。
 * `~/.bash_logout`——这个文件则记录了当我注销bash后，系统再帮我做完什么动作后才离开的意思。 



#3 环境变量PS1语法

命令提示符的格式和颜色由环境变量PS1设置。三种设置命令提示符的方式：

* 在命令行执行 `export PS1="xxxx"` 
* 在全局配置文件`/etc/profile`中添加 `export PS1="xxxx"` ，每个用户使用相同的提示符。
* 在用户配置文件`~/.bashrc`中添加中添加 `export PS1="xxxx"` ，每个用户使用不同的提示符。

##3.1 颜色与特殊显示
|前景	|背景 	|颜色|前景	|背景 	|颜色|
|:--:|:--:|:--:|:--:|:--:|:--:|
|30|40 |黑色|34 |44 |蓝色
|31 |41 |红色|35 |45| 紫红色
|32 |42 |绿色|36 |46 |青蓝色
|33 |43 |黄色|37 |47| 白色 

 |代码|描述|代码|描述|代码|描述|
 |:--:|:--:|:--:|:--:|:--:|:--:|
| 0 |OFF关闭所有显示效果 | 4 |显示下划线 |7 |反白显示 
|1 |高亮显示 |5 |闪烁显示 |8 |颜色不可见 

##3.2 特殊字符
|序列	|意义|序列	|意义|
|:--:|--|:--:|--|
|`\`|	ASCII转义字符（也可以键入 \033）|`\\`|	反斜杠
|`\xxx`|一个用三位数xxx表示的 ASCII 字符|`\a`|	ASCII响铃字符（\007）
|`\!`|	当前命令在历史缓冲区中的位置|`\#`|	 命令编号（会在每次输入命令时时累加）
|`\$` |	普通用户 "\$"；超级用户 "#"|`\u`|	用户名	
|`\[`|	出现在非打印字符序列之前。使bash能够正确计算自动换行。|`\]`|`这个序列应该出现在非打印字符序列之后。
|`\h`|	主机名的第一部分|`\H`|	主机的全称
|`\j`|	在此shell中通过按 ^Z挂起的进程数
|`\l`|	此 shell的终端设备名|`\s`|	shell的名称	
|`\V`|	Bash版本（包括补丁级别）|`\v`|	bash的版本
|`\w`|	当前工作目录包括根目录|`\W`|当前工作目录	
|`\n`|	换行 符	|`\r`|	回车 符
|`\t`|	24小时制时间|`\T`|	12小时制时间
|`\@`|	带有 am/pm的 12小时制时间|`\d`|	"Wed Sep 06"格式的日期
	
 
##3.3 设置颜色
```
\033[特殊显示;前景;背景m
#或
\e[特殊显示;前景;背景m
```
* 为了正确换行应当用`\[\033[特殊显示;前景;背景m\]`
* 颜色的作用范围是下一个颜色设置之前







#4 命令搜寻顺序
* 先查询命令别名，如果没找到;
* 再到shell内建命令中查询，如果没找到；
* 然后到环境变量PATH中查询；

# 5 通配符与特殊符号
|通配符|意义|
|--|--|
|*| 代表0 个到无穷多个任意字符
|?|代表一个任意字符
|[abcd]| 代表 a, b,c, d 这四个任何一个字符
|[0-9]|[0-9] 代表 0 到 9 之间的任意一个
| [^abc]| 代表 除a, b, c 以外的任意一个的其他字符。

|特殊符号| 内容|
|:--:|--|
|# | 注释符号，在后的数据均不执行
|\ | 转义符号：将『特殊字符或通配符』还原成一般字符
|\|| 管道，将前一个命令的输出作为后一个命令的输入；
|;| 连续指令执行分隔符
|~ |用户的家目录
| \$ | 取变量值
|& |工作控制 (job control)：将指令变成背景下工作
|! |逻辑运算意义上的『非』 not 的意思！
|/ |目录符号：路径分隔的符号
| \>, >>| 数据流重导向：输出导向，分别是『替换』与『添加』
| <, <<|数据流重导向：输入导向 (这两个留待下节介绍)
|' ' |单引号，不具有变量置换的功能 ($变为纯文本)
|" " |具有变量置换的功能！ ($可保留相关功能)
|\` \` |两个『 ` 』中间为可以先执行的指令，亦可使用 $( )
|( ) |在中间为子 shell 的起始与结束
|{ } |在中间为命令区块的组合！
|&emsp;&emsp;&emsp;&emsp;||

#6  快速编辑命令与快捷键
|按键|作用
|--|--|
|\enter |命令续行
|!n|执行命令历史的第几个命令|
|!command|执行最近以command开头的命令|
|!!|执行上一个命令|
|Ctrl + C |终止目前的命令|
|Ctrl + \ |终止程序，并产生core文件|
|Ctrl + D |输入结束 (EOF)，例如邮件结束的时候；|
|Ctrl + M |就是 Enter 啦！|
|Ctrl + S |暂停屏幕的输出|
|Ctrl + Q |恢复屏幕的输出|
|Ctrl + U |删除光标到行首|
|Ctrl + k |删除光标到行尾|
|Ctrl + a|移动行首|
|Ctrl + e |移动到行尾|
|Ctrl + Z |『暂停』目前的命令|

#7  命令执行的判断依据
|命令|作用|
|--|--|
|\enter |命令续行|
|cmd1;cmd2|连续执行多个命令|
|cmd1&&cmd2|若 cmd1 执行完毕且正确执行( \$?=0)，则开始执行 cmd2。若 cmd1 执行完毕且为错误 (\$?≠0)，则 cmd2 不执行。$?表示上个命令执行返回值，0表示成功，非0表示失败。
|cmd1\|\|cmd2|若 cmd1 执行完毕且正确执行(\$?=0)，则不执行 cmd2。若 cmd1 执行完毕且为错误 (\$?≠0)，则 cmd2 执行。

#8 重定向
&emsp;&emsp;特殊文件`/dev/null`。任何输入该文件的内容都将被丢弃。
&emsp;&emsp;重定向：就是将标准输入（fd=0）、标准输出（fd=1）、标准出错输出（fd=2）重定向到文键或者装置。
|命令|	说明|
|--|--|
|command > file|将标准输出重定向到文件或设备。
|commmd >! file|将标准输出重定向到文件或设备，并强制覆盖
|commmd >> file|将标准输出以追加的方式重定向到文件或设备
|commmd 2> file|将标准出错输出重定向到文件或设备。
|commmd 2>> file|将标准输错输出以追加的方式重定向到文件或设备
|commmd < file |将标准输入重定向到文件或设备 。
| << tag  INPUT_DATAS tag |将tag和tag之间的内容 INPUT_DATAS作为输入。
|fd1 >& fd2|fd1的输出重定向等同于fd2，&表示等同于|
|fd1 <& fd2 |fd1的输入重定向等同于fd2，&表示等同于|
|fd>&-|关闭输出fd|
|fd<&-|关闭输入fd|

#9 管道与命令替换
|命令|	说明|
|--|--|
|cmd1\|cmd2|将cmd1正确执行时的输出作为cmd2的输入
|cmd1 \`cmd2` 或者 cmd1 $(cmd2)|将cmd2的正确执行时的输出作为cmd1的输入|


#10 shell相关命令
##10.1 查询指令类别——`type`

&emsp;&emsp;**命令**：type [-tpa] name
&emsp;&emsp;**描述**：查询指令是bash内建指令还是外部指令或者命令别名。
> |常用选项|作用|
|:--|--|
|-t |当加入 -t 参数时，type 会将 name 以底下这些字眼显示出他的意义：file ：表示为外部指令；alias ：表示该指令为命令别名所设定的名称；builtin ：表示该指令为 bash 内建的指令功能；
|-p |     如果后面接的 name 为外部指令时，才会显示完整文件名；
|-a |会由 PATH 变量定义的路径中，将所有含 name 的指令都列出来，包含 alias
|**更多信息**|<http://linux.51yip.com/search/type> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/type>|



##10.2 命令别名设置与取消——`alias`、`unalias`

&emsp;&emsp;**命令**：alias [newcommand=oldcommand]
&emsp;&emsp;**描述**：查询或者新建命令别名。
> |常用选项|作用|
|:--|--|
|**更多信息**|<http://linux.51yip.com/search/alias> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/alias>|

&emsp;&emsp;**命令**：unalias [-a] command
&emsp;&emsp;**描述**：取消命令别名。
> |常用选项|作用|
|:--|--|
|-a|取消所有的命令别名|
|**更多信息**|<http://linux.51yip.com/search/unalias> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/unalias>|

##10.3 命令历史——`history`
&emsp;&emsp;**命令**：history n
&emsp;&emsp;&emsp;&emsp;&emsp;history [-c]
&emsp;&emsp;&emsp;&emsp;&emsp;history [-r|a|w] histfiles
&emsp;&emsp;**描述**：查看、写入、新增命令历史。
> |常用选项|作用|
|:--|--|
|n |数字，意思是『要列出最近的 n 笔命令行表』的意思！
|-c|将目前的 shell 中的所有 history 内容全部消除
|-a|将目前新增的 history 指令新增入 histfiles 中，若没有加 histfiles ，则预设写入 ~/.bash_history
|-r|将 histfiles 的内容读到目前这个 shell 的 history 记忆中；
|-w|将目前的 history 记忆内容写入 histfiles 中！
|**更多信息**|<http://linux.51yip.com/search/history> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/history>|


###10.4 配置文件载入——`source`
&emsp;&emsp;**命令**：source config_file
&emsp;&emsp;**描述**：载入配置.
> |常用选项|作用|
|:--|--|
|**更多信息**|<http://linux.51yip.com/search/source> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/source>|

### 10.5 终端机环境配置——`stty`、`set`

&emsp;&emsp;**命令**：stty -ag
&emsp;&emsp;&emsp;&emsp;&emsp;stty option=value
&emsp;&emsp;**描述**：打印终端机设置或设置终端机。^表示CTRL键。
> |常用选项|作用|
|:--|--|
|-a|以人类可读格式|
|-g|以tty格式|
|**更多信息**|<http://linux.51yip.com/search/stty> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/stty>|

>|参数|意义|
|--|--|
|  intr | 送出一个 interrupt (中断) 的讯号给目前正在 run 的程序 (就是终止啰！)；
| quit | 送出一个 quit 的讯号给目前正在 run 的程序；
| erase| 向后删除字符，
|  kill| 删除在目前指令列上的所有文字；
| eof | End of file 的意思，代表『结束输入』。
|  start |在某个程序停止后，重新启动他的 output
|  stop |停止目前屏幕的输出；
|  susp | 送出一个 terminal stop 的讯号给正在 run 的程序。

&emsp;&emsp;**命令**：set [-uvCHhmB]
&emsp;&emsp;**描述**：设置命令输入或输出环境。`echo $-` 可以查看所有的set设置。
> |常用选项|作用|
|:--|--|
|-u |默认不启用。若启用后，当使用未设定变量时，会显示错误讯息；
|-v |默认不启用。若启用后，在讯息被输出前，会先显示讯息的原始内容；
|-x |默认不启用。若启用后，在指令被执行前，会显示指令内容(前面有++ 符号)
|-h |默认启用。与历史命令有关；
|-H |默认启用。与历史命令有关；
|-m |预设启用。与工作管理有关；
|-B |预设启用。与刮号 [] 的作用有关；
|-C |预设不启用。若使用 > 等，则若文件存在时，该文件不会被覆盖。
|**更多信息**|<http://linux.51yip.com/search/set> 和 man 手册|
|&emsp;&emsp;&emsp;&emsp;|<http://man.linuxde.net/set>|

&emsp;&emsp;其他终端机配置文件还包括` /etc/inputrc `、`/etc/DIR_COLORS*`与 `/usr/share/terminfo/*` 等。






------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《鸟哥的Linux私房菜》](http://linux.vbird.org "点击跳转")、[《Linux命令手册》](http://linux.51yip.com "点击跳转")、[《Linux命令大全》](http://man.linuxde.net "点击跳转")以及[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
