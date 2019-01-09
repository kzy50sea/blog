---
title: vim插件——ultisnips(未完) 
tags: vim插件
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《ultisnips 官方文档》](https://github.com/SirVer/ultisnips "点击跳转")。。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>



# 1 简介
* **插件介绍**：代码片补全。
* **仓库地址**：<https://github.com/SirVer/ultisnips>

#2 安装教程
* `$vim ~/.vimrc`
* 在`call vundle#begin()`和`call vundle#end()`之间添加`Plugin 'SirVer/ultisnips'`
* `:wq`
* `$vim`
* `:PluginInsttall`
* **注意vim版本必须大于7.4且支持python2或python3。**

#3 基本配置
* 在`~/.vimrc`中添加以下内容
```
"设置vim为不兼容模式
set nocompatible

"设置触发键
let g:ultisnipsexpandtrigger="<tab>"      

"后一个
let g:ultisnipsjumpforwardtrigger="<c-j>"  

"前一个
let g:ultisnipsjumpbackwardtrigger="<c-k>"  

"打开补全列表
let g:ultisnipslistsnippets="<c-tab>" 

"指定snippets文件的目录，可以有多个且该目录runtimepath 中的某一项之下
"let g:ultisnipssnippetsdir='~/.vim/Ultisnips' 

"指定snippets文件的搜索路径
"let g:ultisnipssnippetdirectories=['ultisnips'] 

"设置窗口打开的方式：normal当前窗口 horizontal水平vertical垂直context智能 
let g:ultisnipseditsplit="normal" 


"指定ultisnip使用python2
let g:ultisnipsusepythonversion=2 
```


#4 选项、命令、函数
##4.1 命令

* `UltiSnipsEdit`命令
 * 打开当前文件类型的私有snippets文件。如果不存在，则会创建一个新文件。如果用`UltiSnipsEdit!`则所有公用的snippets文件也将考虑在内。如果多个文件匹配，将提示用户选择哪一个文件。
* `UltiSnipsAddFiletypes`命令
 * 允许使用其它文件类型类型的snippets文件，不同文件类型使用点隔开。`UltiSnipsEdit`将打开此列表中的第一个文件类型的私有snippets文件，相同的触发代码片段按照列表顺序排序。

## 4.2 选项
* `let g:UltiSnipsEditSplit='normal' `
 * 定义 `UltiSnipsEdit`命令窗口打开的方式，可选值为
     * normal， 默认。在当前窗口中打开。
     * tabdo ，       在新的标签窗口打开
     * horizontal ，    水平分割窗
     * vertical ，     垂直拆分窗户。 
     * context  ，    依赖于内容，垂直或水平拆分窗口
* `let g:UltiSnipsSnippetsDir='./UltiSnips'`    
 * 定义存储专用代码段文件的目录。例如，如果变量设置为`~/.vim/mydir/UltiSnips`，当前的“filetype”为“cpp”， 如果文件不为空则：`UltiSnipsEdit`将打开`~/.vim/mydir/UltiSnips/cpp.snippets`，如果为空`UltiSnipsEdit`将在`g：UltiSnipsSnippetDirectories`查找文件，如果没有找到，`UltiSnipsEdit`将在`g：UltiSnipsSnippetsDir`创建新的文件。注意，名为“snippets”的目录是为snipMate的snippets文件保留的，不能使用。
* `let g:UltiSnipsSnippetDirectories=["UltiSnips"]`
 * 设置snippets文件的搜索路径。snippets文件搜索目录必须是“runtimepath”选项中定义的目录的子目录。
* `let g:UltiSnipsEnableSnipMate=1`
 * 允许在`＆runtimepath`中查找SnipMate的snippets文件。 UltiSnips将只在名为“snippets”的目录搜索SnipMate代码片段。默认为“1”，所以UltiSnips会查找SnipMate的snippets文件。
* `let g:UltiSnipsExpandTrigger="<TAB>" `       
 * 展开代码片段
* `let g:UltiSnipsJumpForwardTrigger="<c-j>"`    
 * 跳转到展开的代码片中的后一个自定义位置 
* `let g:UltiSnipsJumpBackwardTrigger="<c-k>"`   
 * 跳转到展开的代码片中的前一个自定义位置 
* `let g:UltiSnipsListSnippets="<c-tab>"`          
 * 打开候选的代码片段列表
* `let g:UltiSnipsRemoveSelectModeMappings = 1`
 * 全局禁用选择模式映射
* `let g:UltiSnipsMappingsToIgnore = [ "somePlugin", "otherPlugin" ]`
 * 特定插件禁用选择模式映射
* `let g:UltiSnipsNoPythonWarning = 1`
 * vim不支持python时不发出警告 

## 4.3 函数
* `UltiSnips＃AddSnippetWithPriority(trigger, value, description,options, filetyp, priority)`
 * 将新的触发器，值，描述和选项的代码片段添加到当前的片段列表中。优先级是一个数字，定义哪个代码片段应该比其他代码片段优先。 
* `UltiSnips＃Anon（trigger,description,options）`
 * 展开一个匿名代码段。匿名片段在当场定义，展开后立即丢弃。匿名代码段不会添加到全局代码段列表中，因此无法再次展开，除非该函数再次被调用。该函数采用三个可选参数。
* `UltiSnips＃SnippetsInCurrentScope`
 * 该函数等价与snipmate的`GetSnipsInCurrentScope`函数。该函数返回触发器与当前词匹配的所有代码片段的vim型字典，如果您需要当前缓冲区的所有代码片段信息，您可以简单地将1（这意味着所有）作为此函数的第一个参数传递，并使用全局变量g：current_ulti_dict_info获取结果（参见下面的示例）。此函数不会直接为ultisnips添加任何新功能，但允许使用第三方插件来收集当前可用的片段。

# 5 编写snippets文件
## 5.1 基本语法
* vim打开文件时UltiSnips遍历由选项`g:UltiSnipsSnippetDirectories设立的`snippet文件搜索路径，查找具有以下名称的snippets文件：`ft.snippets`，`ft_*.snippets`或`ft/*`，其中“ft”是“filetype“，“*”是一个类似shell的通配符，匹配任何字符串包含空字符串。下表显示了一些典型的snippets文件名及其相关的文件类型。
| snippet filename   |      filetype | snippet filename   |      filetype ~| snippet filename |filetype ~|
|--|
|  ruby.snippets   |         ruby
    |perl.snippets   |         perl
   | c.snippets    |           c
   | c_my.snippets |          c
    |c/a        |            c
    |c/b.snippets |            c
   | all.snippets  |           *all
  |  all/a.snippets   |        *all
注意：all文件类型是唯一的。它代表不管文件类型如何，都可以可以使用的片段。例如，日期插入代码片段适用于all.snippets文件。
UltiSnips支持Vim的点缀文件类型语法。例如，如果您为CUDA C ++框架定义了一个虚拟文件类型，例如“：set ft = cuda.cpp”，UltiSnips将搜索并激活cuda和cpp文件类型的片段。
snippets文件的语法很简单。以＃字符开头的所有行都被视为注释。被UltiSnips忽略。
以关键字'extends'开头的行提供了一种组合片段文件的方式。当“'extends”指令包含在某个snippets文件中时，该代码片段等效于该代码片段于列出文件类型的相应代码片段的组合。即此时可以使用列出文件类型的相应代码片段。
语法如下所示：
extends ft1，ft2，ft3
例如，cpp.snippets中的第一行如下所示：
extends  c
当UltiSnips激活cpp文件的片段时，它首先查找所有c片段并激活它们。这是一种sinippet代码片跨文件类型重用的办法。在片段文件中允许使用多个“扩展”行，并且它们可以在文件的任何位置。
以关键字“priority”开头的行设置了此snippet文件中所有位于该行之后的代码片段的优先级。文件的默认优先级始终为0.当代码片段展开时，UltiSnips将从所有源中收集与触发器匹配的所有代码段定义，并仅保留优先级最高的。例如，所有默认片段都具有优先级<0，因此用户自定义的片段总是覆盖默认定义的片段。
以关键字“snippet”开头的行表示代码段定义的开始，以关键字“endsnippet”开头的行表示代码定义结束。代码段定义位于这两行之间。下面时shell文件的'if'语句的代码片段。
snippet if "if ... then (if)"
if ${2:[[ ${1:condition} ]]}; 
then
${0:#statements}
fi
endsnippet
起始行采用以下形式：
snippet tab_trigger [ "description" [ options ] ]
tab_trigger是必需的，但描述和选项是可省略的。'tab_trigger'是用于触发代码段的单词或字符串序列，通常使用单个单词，也可以是正则表达式。但tab_trigger不能含有空格，当tab_trigger含有空格或者是正则表达式时必须加上双引号。如：
snippet "tab trigger" [ "description" [ options ] ]
双引号并不是触发器的一部分。要激活该片段时：输入tab trigger，按下tab键就会展开片段。
含有空格的触发器必须使用双引号并非技术需要。任何匹配的字符都可以。例如，这是一个有效的片段起始行：
snippet !tab trigger! [ "description" [ options ] ]
引号可以作为触发器的一部分，但必须在触发器外加上另一对匹配的字符。如：
snippet !"tab trigger"! [ "description" [ options ] ]
要激活此片段，键入：“tab trigger”
'description'是描述触发器的字符串。 用于记录代码片段并将其与其他片段具有相同触发器的代码片段进行区分。 当代码段被激活并且多个代码段匹配时（即有多个代码段触发器相同），UltiSnips会显示匹配的代码片段列表及其描述，用户根据这些描述来选择他们想要的片段。
'options'控制代码段的行为。 选项由单个字符指示。 代码段的“选项”字符可以是多个参数的组合。
基本格式：
snippet <tigger> "注解" <选项> 
${1:name}
${0}
endsnippet
支持直接用shell script，也可以通过!v嵌入vimscript或者!p嵌入python
snippet  <tigger> "注解" <选项>
`shellcode或者!v vimscriot 或者!p pythoncode`
endsnippet
python自动定义的变量
fn文件名
path文件全路径
t占位符t[1]相当于${1}
snip方法
也可用global !p直接嵌入通用的python脚本
global !p
endglobal
tigger可以是关键字或者时正则表达式。关键字snippet和endsnippet以及global和endglobal都需顶格书写




------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《ultisnips 官方文档》](https://github.com/SirVer/ultisnips "点击跳转")。。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
