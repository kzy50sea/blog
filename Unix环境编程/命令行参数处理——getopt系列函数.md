---
title: 命令行参数处理——getopt系列函数
tags: unix环境编程
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>

# 1 getopt
```cpp
#include <unistd.h>
int getopt(int argc, char * const argv[],const char *optstring);

extern char *optarg;
extern int optind, opterr, optopt;
```
* **功能：** 解析命令行参数。
* **参数**：
  * **argc**是程序启动时传递给main（）函数的参数。argc为传入程序的参数的个数，默认为1，代表程序自身。
  * **argv**是程序启动时传递给main（）函数的参数。argv为指针数组，存放命令行参数，传入的命令行参数以空白符分界，并以字符串的形式存放在指针数组argv中，argv数组的每个元素存放一个字符串的指针，指向一个传入的命令行参数，argv[0]表示程序自身名字。argv数组的每个元素就是一个命令行参数，以`-`（而不是"_"或"--"）开头的argv元素就是一个**命令行选项**。该元素除了初始'-'之外的其它字符就是选项字符和参数(如果参数紧跟“`-`”)。如果重复调用了getopt（），它每次返回一个选项字符。如：argv[1] = “-aabc”或 argv[1] ="-a",那么此时getopt返回值就是选项字符a。

  * **optstring**是选项字符串，比如："a:b:cd::e"，这就是一个选项字符串。对应到命令行就是-a ,-b ,-c ,-d, -e 。
     * **一个冒号**就表示这个选项后面必须带有参数（没有带参数会报错,optarg置为null，且返回？），但是这个参数可以和选项连在一起写（此时optarg指向同一个argv元素的剩余部分），也可以用空格隔开（此时optarg指向下一个argv元素），比如`-a123 `和`-a 123`（中间有空格）都表示123是-a的参数。
     * **两个冒号**的就表示这个选项可以有参数，也可以没有参数，但要注意有参数时，**参数与选项之间不能有空格**（若有空格，则认为该选项不带参数），这一点和一个冒号时是有区别的。
     * **不带冒号**的表示这个选项没有参数。
     * 如果optstring的第一个字符为“+”或设置了环境变量POSIXLY_CORRECT，则一旦遇到非选项也非选项参数的元素，就认为没有更多选项存在了。
     * 如果optstring的第一个字符为'-'，那么每个非选项也非选项参数的argv元素都将被处理为字符代码为1的选项的参数。（这被编写用于期望选项和其他argv元素以任何顺序使用的程序，并且关心两者的顺序。）。
     * 此外, GNU有额外的扩展。如果optstring包含`W;`，则`-W foo`被视为长选项`--foo`。（-W选项由POSIX.2保留，用于实现扩展。）此行为是GNU扩展，在glibc 2之前的库中不可用。

* **全局变量**
  * **optind**是要在argv数组中处理的**下一个含有选项的元素**的下标（也叫索引）系统将该值初始化为1,因为索引0就是程序的名，调用者可以将其重置为1，以重新扫描argv数组，或扫描新的参数向量。

  * **optarg**是用来保存选项的参数的；如果选项(不论是否合法)无参数，则置为NULL。
 
  * **oprerr**表示是否将错误信息输出到stderr，为0时表示不输出.
 
  * **optopt**表示不在选项字符串optstring中的选项字符。

* **返回值**：
  * 如果getopt（）找到一个选项字符，则返回该字符，更新外部变量optind指向下一个选项元素所在argv数组的下标。 

  * 如果没有更多选项字符(无论该选项字符是否合法，只要是选项字符即可)，getopt（）返回-1，optarg置为null，然后将optind置为第一个argv中第一个非选项也非选项参数的元素的索引，如果没有这样元素也视为有。

     * 默认情况下，getopt（）在每次调用时会排列argv的内容，最终所有既不是选项（无论合不合法）也不是选项参数的元素都会排到数组末尾，其它元素的顺序不会变。

     * 特殊元素“--”强制结束选项扫描，无论扫描模式如何。

  * 如果getopt（）在argv中找到一个未包含在optstring中的选项字符，则会向stderr打印一条错误消息，将该字符存储在optopt中，并返回'？'。将opterr设置为0可以阻止显示错误消息。

  * 如果getopt（）检测到选项缺少参数，则返回'？'并将外部变量optopt设置为实际选项字符。如果optstring的第一个字符（如果时'+'或'-'则看第二个字符）是冒号（'：'），那么getopt（）返回'：'而不是'？'以表示缺少选项参数。并打印错误消息，将opterr设置为0可以阻止显示错误消息。



# 2 getopt_long和getopt_long_only
```c
extern char *optarg;
extern int optind, opterr, optopt;

#include <getopt.h>
int getopt_long(int argc, char * const argv[],const char* optstring,
                  const struct option *longopts, int *longindex);
int getopt_long_only(int argc, char * const argv[],const char* optstring,        
                  const struct option *longopts, int *longindex);


struct option 
{
      const char *name;
      int      has_arg;
      int        *flag;
      int         val;
};


```
* **功能**：
  * getopt_long（）函数的作用类似于getopt（），但它可以接收接受长选项，以`--`开头。如果程序仅接受长选项，那么optstring应该被指定为一个空字符串（“”），而不是NULL。如果缩写是唯一的或者可以精确的匹配一些定义的选项，则可以缩写长选项名称。长选项可能会采用`--arg = param`或`--arg param`形式的参数。

  * getopt_long_only（）类似于getopt_long（），但' - '以及“ -- ”都表示长选项。如果以“-”（而不是“--”）开头的选项与长选项不匹配，但与短选项匹配，则会将其解析为短选项。
* **参数**
  * **argc、argv、optstring**和getopt函数中的一样。 
  * **longopts**是指向getopt.h中声明的`struct option`数组的第一个元素的指针，通常情况下是一个数组。**注意：struct option数组的最后一个元素必须用{0,0,0,0}填充。**
  * **longindex**不为NULL，则它指向一个变量，函数返回时将该变量设置为长选项在longopts所指数组的索引。有点类似**optind**。
* **option结构体**
  * 成员**name**:长选项的名称。
  * 成员**has_arg**：选项是否带参数。
     * no_argument（或0）如果该选项没有参数; 
     * required_argument（或1）如果该选项需要参数;
     * optional_argument（或2）如果该选项具有可选参数。
  * 成员**flag**指定如何为长选项返回结果。
     * 如果flag为NULL，那么getopt_long（）返回成员**val**。 （**一般，设flag为NULL，将val设置为等效的短选项字符）**，
     * 如果flag不为NULL ，getopt_long（）返回0，并且**flag**指向一个变量，如果找到该变量(该变量必须是全局或者静态变量)，则将该变量值设置为val，但如果没有指定该选项则**flag**指向的变量不做改变。

    * 成员**val**是要返回的值，或者加载到由flag指向的变量中的值。
 
* **返回值**
  * 当识别短选项时，getopt_long（）和getopt_long_only（）也返回选项字符。 
  * 对于长选项,如果flag为NULL，则返回val，否则返回0。 
  * 出错和返回-1的情况与getopt（）相同，加上'？'用于模糊匹配或无关的参数。
# 3 示例
```c
#include<stdlib.h>
#include<stdio.h>
#include<unistd.h>
#include <getopt.h>
#include<string.h>
void ShowHelpInfo()
{
	printf("Usage: cmd [-i file] [-o file] [-h] [-v]\n");
	printf("  -h, --help	    display this help\n");
	printf("  -v, --version       show version\n");
	printf("  -i, --input 	      input file\n");
	printf("  -o, --output        output file\n");
	printf("  -t,                 test short option \n");
	printf("       --test1        test long option \n");
	printf("       --test2        test long option \n");
	printf("\n");
}


void ShowVersionInfo()
{
	printf("version 1.1 \n");

}


int main(int argc , char * argv[])
{

	
	char input_file_name[256],
		  output_file_name[256];
 
	int ret = 0;
	int option_index = 0;
	static int flag=0;
	
 	static struct option arg_options[] =
	{
		{"help", 0, 0, 'h'},
		{"version", 0, 0, 'v'},
		{"input", 1, 0, 'i'},
		{"ouput", 1, 0, 'o'},
		{"test1",0,&flag, 520},
		{"test2",0,&flag, 1314},
		{0, 0, 0, 0}
	};
 

	while ((ret = getopt_long(argc, argv, ":hvi:o:t", arg_options, &option_index)) != -1) 
	{
		switch (ret) 
		{
		case 'h':
			ShowHelpInfo();
			return 0;
		case 'v':
			ShowVersionInfo();
			return 0;
		case 'i':
			strncpy(input_file_name, optarg, sizeof(input_file_name));
			printf("input file name is :%s\n",input_file_name);
			break;
		case 'o':
			strncpy(output_file_name, optarg, sizeof(output_file_name));
			printf("output file name is :%s\n",output_file_name);
			break;
		case 't':
			printf("独立短选项\n");
			break;
		case ':':
			printf("miss argument\n");
			return -1;
		case '?':
			printf("unrecognizable argument\n");
			return -1;
		case 0://longindex参数的作用
			if (0 == strcmp(arg_options[option_index].name, "test1"))
			{
				printf("i love you ,%d,\n",flag);
			}
			else if (0 == strcmp(arg_options[option_index].name, "test2"))
			{
				printf("forever !%d,\n",flag);
			}
			break;
		default:
			return -1;
		}
	}
}

```












------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《Linux man pages》](https://linux.die.net/man/ "点击跳转")。。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
