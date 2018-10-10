---
title: Linux内核编程风格
tags: Linux内核
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>《内核官方文档》。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------


# 0 前言		

&emsp;&emsp;这是一个简短的文档，描述了linux内核的首选编码风格。 编码风格每个有所不同，我不会强迫每个人都接受我的观点。... ... 请至少考虑一下这里提出的观点。


# 1 缩进
* 水平制表符为8个字符
* switch语句中，switch与case或对齐
```cpp
switch (suffix) {
case 'G':
case 'g':
	mem <<= 30;
		break;
default:
	break;
}
```
* 不要在一行中写多个语句（或多个赋值语句），除非需要隐藏什么。
```cpp

if (condition) do_this;
	do_something_everytime;
```
* 除了注释、文档、kconfig之外，不要使用空格进行缩进
* 不要在行尾添加空格

# 2 括号

* 对于非函数（如结构体、联合体、if、while、for、do、switch）中使用花括号，`{`不另起一行，`}`另起一行
* 对于函数，`{`要另起一行，`}`另起一行
* 如果if、for、do 后面只有一行，不加花括号
* 如果if与else if 混用时，else不另起一行，如：

```cpp
if (condition)
	action();

if (condition)
	do_this();
else
	do_that();

if (condition) {
	do_this();
	do_that();
} else {
	otherwise();
}

```
# 3 空格 
* 大多数关键字后需要加一个空格，但` sizeof, typeof, alignof, and __attribute__ defined`不加
* 双目运算符，两侧各加一个空格，如`a + b`;
	* 算术运算符：` +、  -、   * 、 / 、 %`和` +=、  -=、   * =、 / =、 %=`
	* 位运算符：`  | 、 &、  ^ `和`  | =、 &=、  ^ =`
	* 比较运算符`：<、  >、 <= 、 >= 、 == 、 !=`
	* 移位运算符号：`<< 、>>`和`<<= 、>>=`
	* 关系运算符：`|| 、&& `
	*  条件运算符：`? : `
* 单目运算符,不加空格：`&（取地址）、  *（取内容） 、 +（正） 、 -（负）、 ~(按位取反) 、 !（非） 、 ++、 -- 、.（取成员）、->（取成员）[]（数组索引）`
* 逗号运算符，右侧加一个空格，左侧不加
* 定义指针时，类型名和指针符号`*`要加一个空格。如`char *ptr;`
* 中括号和小括号内侧不加空格



# 4 命名
* 标识符 应该以 **字母、数字、下划线**命名，并且不能以数字开头。在用户空间使用时，避免用下划线开头，因为大多时候是系统内置标识符。
* Linux不采用匈牙利命名法（也叫驼峰命名法、大小写混合命名法），而是尽量使用小写+下划线来命名。
* 对于全局变量和全局函数，需要具有描述性名称，其它标识符在保证意义的情况下可以适当缩写
* 不要在名称中添加变量或函数类型相关的信息。
* 对于宏，大多数时候全部大写，对于宏函数有时也会使用小写。
* 对于局部的循环变量，可使用单字符命名

# 5 类型定义

* 不要对结构体和指针使用typedef 来产生一个莫名其妙的新类型
* typedef一般用于下列情况
	*  完全不透明，使用typedef进行隐藏
	*  清楚的表达整数的类型
	*  在某些特殊情况下，与标准C99类型相同的新类型。
	*  在用户空间中安全使用。

# 6 函数
* 函数应该简短，只做一件事。（宽度为80 长度24）
	* 如果函数逻辑简单，那么也可以更长一些
	* 如果函数复杂，那么应该分拆函数。
* 函数的局部变量的数量。他们不应该超过5-10，否则，需要将其拆分成更小的部分。
* 在源文件中，使用一个空行分隔函数。如果要导出该函数，它的EXPORT 宏应该在关闭函数括号行之后立即执行。例如。：
```cpp
int system_is_up（void）
{
return system_state == SYSTEM_RUNNING;
}
EXPORT_SYMBOL（system_is_up）;
```
* 在函数原型中，包含形参的参数名称及其数据类型。尽管C语言不需要这样做。
# 7 集中退出函数

* 编译器经常以无条件跳转指令的形式使用等效的goto语句。

* 当函数从多个位置退出并且必须完成一些常见工作（如清理）时，goto语句会派上用场。如果不需要，则直接返回。

* 标签名称应说明goto的作用或goto存在的原因。如果goto释放“buffer”，那么好的名称可以是“out_buffer：”。避免使用GW-BASIC名称，如“err1：”和“err2：”。也不要在goto位置之后命名它们，例如“err_kmalloc_failed：”

* 使用gotos的基本原理是：
	* 无条件陈述更容易理解和遵循
	*  减少嵌套
	*   防止在进行修改时不更新单个出口点的错误
	*   节省编译器工作以优化冗余代码;


		

# 8 注释
* 注释使用C89的`/* */`，而不是C99的`//`
* 注释只需说明做什么，不需说明怎么做
* 在注释内核API函数时，请使用**内核文档格式**，详见`Documentation/kernel-doc-nano-HOWTO.txt`和` scripts/kernel-doc`
* 长行注释风格：
```cpp
	/*
	 * This is the preferred style for multi-line
	 * comments in the Linux kernel source code.
	 * Please use it consistently.
	 *
	 * Description:  A column of asterisks on the left side,
	 * with beginning and ending almost-blank lines.
	 */
```
* 但在` net/ `和 `drivers/net/` 长行注释风格为：
```cpp
	/* The preferred comment style for files in net/ and drivers/net
	 * looks like this.
	 *
	 * It is nearly the same as the generally preferred comment style,
	 * but there is no initial almost-blank line.
	 */
```

		Chapter 9: You've made a mess of it

That's OK, we all do.  You've probably been told by your long-time Unix
user helper that "GNU emacs" automatically formats the C sources for
you, and you've noticed that yes, it does do that, but the defaults it
uses are less than desirable (in fact, they are worse than random
typing - an infinite number of monkeys typing into GNU emacs would never
make a good program).

So, you can either get rid of GNU emacs, or change it to use saner
values.  To do the latter, you can stick the following in your .emacs file:

(defun c-lineup-arglist-tabs-only (ignored)
  "Line up argument lists by tabs, not spaces"
  (let* ((anchor (c-langelem-pos c-syntactic-element))
	 (column (c-langelem-2nd-pos c-syntactic-element))
	 (offset (- (1+ column) anchor))
	 (steps (floor offset c-basic-offset)))
    (* (max steps 1)
       c-basic-offset)))

(add-hook 'c-mode-common-hook
          (lambda ()
            ;; Add kernel style
            (c-add-style
             "linux-tabs-only"
             '("linux" (c-offsets-alist
                        (arglist-cont-nonempty
                         c-lineup-gcc-asm-reg
                         c-lineup-arglist-tabs-only))))))

(add-hook 'c-mode-hook
          (lambda ()
            (let ((filename (buffer-file-name)))
              ;; Enable kernel mode for the appropriate files
              (when (and filename
                         (string-match (expand-file-name "~/src/linux-trees")
                                       filename))
                (setq indent-tabs-mode t)
                (setq show-trailing-whitespace t)
                (c-set-style "linux-tabs-only")))))

This will make emacs go better with the kernel coding style for C
files below ~/src/linux-trees.

But even if you fail in getting emacs to do sane formatting, not
everything is lost: use "indent".

Now, again, GNU indent has the same brain-dead settings that GNU emacs
has, which is why you need to give it a few command line options.
However, that's not too bad, because even the makers of GNU indent
recognize the authority of K&R (the GNU people aren't evil, they are
just severely misguided in this matter), so you just give indent the
options "-kr -i8" (stands for "K&R, 8 character indents"), or use
"scripts/Lindent", which indents in the latest style.

"indent" has a lot of options, and especially when it comes to comment
re-formatting you may want to take a look at the man page.  But
remember: "indent" is not a fix for bad programming.


		Chapter 10: Kconfig configuration files

For all of the Kconfig* configuration files throughout the source tree,
the indentation is somewhat different.  Lines under a "config" definition
are indented with one tab, while help text is indented an additional two
spaces.  Example:

config AUDIT
	bool "Auditing support"
	depends on NET
	help
	  Enable auditing infrastructure that can be used with another
	  kernel subsystem, such as SELinux (which requires this for
	  logging of avc messages output).  Does not do system-call
	  auditing without CONFIG_AUDITSYSCALL.

Seriously dangerous features (such as write support for certain
filesystems) should advertise this prominently in their prompt string:

config ADFS_FS_RW
	bool "ADFS write support (DANGEROUS)"
	depends on ADFS_FS
	...

For full documentation on the configuration files, see the file
Documentation/kbuild/kconfig-language.txt.


		Chapter 11: Data structures

Data structures that have visibility outside the single-threaded
environment they are created and destroyed in should always have
reference counts.  In the kernel, garbage collection doesn't exist (and
outside the kernel garbage collection is slow and inefficient), which
means that you absolutely _have_ to reference count all your uses.

Reference counting means that you can avoid locking, and allows multiple
users to have access to the data structure in parallel - and not having
to worry about the structure suddenly going away from under them just
because they slept or did something else for a while.

Note that locking is _not_ a replacement for reference counting.
Locking is used to keep data structures coherent, while reference
counting is a memory management technique.  Usually both are needed, and
they are not to be confused with each other.

Many data structures can indeed have two levels of reference counting,
when there are users of different "classes".  The subclass count counts
the number of subclass users, and decrements the global count just once
when the subclass count goes to zero.

Examples of this kind of "multi-level-reference-counting" can be found in
memory management ("struct mm_struct": mm_users and mm_count), and in
filesystem code ("struct super_block": s_count and s_active).

Remember: if another thread can find your data structure, and you don't
have a reference count on it, you almost certainly have a bug.


		Chapter 12: Macros, Enums and RTL

Names of macros defining constants and labels in enums are capitalized.

#define CONSTANT 0x12345

Enums are preferred when defining several related constants.

CAPITALIZED macro names are appreciated but macros resembling functions
may be named in lower case.

Generally, inline functions are preferable to macros resembling functions.

Macros with multiple statements should be enclosed in a do - while block:

#define macrofun(a, b, c) 			\
	do {					\
		if (a == 5)			\
			do_this(b, c);		\
	} while (0)

Things to avoid when using macros:

1) macros that affect control flow:

#define FOO(x)					\
	do {					\
		if (blah(x) < 0)		\
			return -EBUGGERED;	\
	} while(0)

is a _very_ bad idea.  It looks like a function call but exits the "calling"
function; don't break the internal parsers of those who will read the code.

2) macros that depend on having a local variable with a magic name:

#define FOO(val) bar(index, val)

might look like a good thing, but it's confusing as hell when one reads the
code and it's prone to breakage from seemingly innocent changes.

3) macros with arguments that are used as l-values: FOO(x) = y; will
bite you if somebody e.g. turns FOO into an inline function.

4) forgetting about precedence: macros defining constants using expressions
must enclose the expression in parentheses. Beware of similar issues with
macros using parameters.

#define CONSTANT 0x4000
#define CONSTEXP (CONSTANT | 3)

The cpp manual deals with macros exhaustively. The gcc internals manual also
covers RTL which is used frequently with assembly language in the kernel.


		Chapter 13: Printing kernel messages

Kernel developers like to be seen as literate. Do mind the spelling
of kernel messages to make a good impression. Do not use crippled
words like "dont"; use "do not" or "don't" instead.  Make the messages
concise, clear, and unambiguous.

Kernel messages do not have to be terminated with a period.

Printing numbers in parentheses (%d) adds no value and should be avoided.

There are a number of driver model diagnostic macros in <linux/device.h>
which you should use to make sure messages are matched to the right device
and driver, and are tagged with the right level:  dev_err(), dev_warn(),
dev_info(), and so forth.  For messages that aren't associated with a
particular device, <linux/printk.h> defines pr_notice(), pr_info(),
pr_warn(), pr_err(), etc.

Coming up with good debugging messages can be quite a challenge; and once
you have them, they can be a huge help for remote troubleshooting.  However
debug message printing is handled differently than printing other non-debug
messages.  While the other pr_XXX() functions print unconditionally,
pr_debug() does not; it is compiled out by default, unless either DEBUG is
defined or CONFIG_DYNAMIC_DEBUG is set.  That is true for dev_dbg() also,
and a related convention uses VERBOSE_DEBUG to add dev_vdbg() messages to
the ones already enabled by DEBUG.

Many subsystems have Kconfig debug options to turn on -DDEBUG in the
corresponding Makefile; in other cases specific files #define DEBUG.  And
when a debug message should be unconditionally printed, such as if it is
already inside a debug-related #ifdef section, printk(KERN_DEBUG ...) can be
used.


		Chapter 14: Allocating memory

The kernel provides the following general purpose memory allocators:
kmalloc(), kzalloc(), kmalloc_array(), kcalloc(), vmalloc(), and
vzalloc().  Please refer to the API documentation for further information
about them.

The preferred form for passing a size of a struct is the following:

	p = kmalloc(sizeof(*p), ...);

The alternative form where struct name is spelled out hurts readability and
introduces an opportunity for a bug when the pointer variable type is changed
but the corresponding sizeof that is passed to a memory allocator is not.

Casting the return value which is a void pointer is redundant. The conversion
from void pointer to any other pointer type is guaranteed by the C programming
language.

The preferred form for allocating an array is the following:

	p = kmalloc_array(n, sizeof(...), ...);

The preferred form for allocating a zeroed array is the following:

	p = kcalloc(n, sizeof(...), ...);

Both forms check for overflow on the allocation size n * sizeof(...),
and return NULL if that occurred.


		Chapter 15: The inline disease

There appears to be a common misperception that gcc has a magic "make me
faster" speedup option called "inline". While the use of inlines can be
appropriate (for example as a means of replacing macros, see Chapter 12), it
very often is not. Abundant use of the inline keyword leads to a much bigger
kernel, which in turn slows the system as a whole down, due to a bigger
icache footprint for the CPU and simply because there is less memory
available for the pagecache. Just think about it; a pagecache miss causes a
disk seek, which easily takes 5 milliseconds. There are a LOT of cpu cycles
that can go into these 5 milliseconds.

A reasonable rule of thumb is to not put inline at functions that have more
than 3 lines of code in them. An exception to this rule are the cases where
a parameter is known to be a compiletime constant, and as a result of this
constantness you *know* the compiler will be able to optimize most of your
function away at compile time. For a good example of this later case, see
the kmalloc() inline function.

Often people argue that adding inline to functions that are static and used
only once is always a win since there is no space tradeoff. While this is
technically correct, gcc is capable of inlining these automatically without
help, and the maintenance issue of removing the inline when a second user
appears outweighs the potential value of the hint that tells gcc to do
something it would have done anyway.


		Chapter 16: Function return values and names

Functions can return values of many different kinds, and one of the
most common is a value indicating whether the function succeeded or
failed.  Such a value can be represented as an error-code integer
(-Exxx = failure, 0 = success) or a "succeeded" boolean (0 = failure,
non-zero = success).

Mixing up these two sorts of representations is a fertile source of
difficult-to-find bugs.  If the C language included a strong distinction
between integers and booleans then the compiler would find these mistakes
for us... but it doesn't.  To help prevent such bugs, always follow this
convention:

	If the name of a function is an action or an imperative command,
	the function should return an error-code integer.  If the name
	is a predicate, the function should return a "succeeded" boolean.

For example, "add work" is a command, and the add_work() function returns 0
for success or -EBUSY for failure.  In the same way, "PCI device present" is
a predicate, and the pci_dev_present() function returns 1 if it succeeds in
finding a matching device or 0 if it doesn't.

All EXPORTed functions must respect this convention, and so should all
public functions.  Private (static) functions need not, but it is
recommended that they do.

Functions whose return value is the actual result of a computation, rather
than an indication of whether the computation succeeded, are not subject to
this rule.  Generally they indicate failure by returning some out-of-range
result.  Typical examples would be functions that return pointers; they use
NULL or the ERR_PTR mechanism to report failure.


		Chapter 17:  Don't re-invent the kernel macros

The header file include/linux/kernel.h contains a number of macros that
you should use, rather than explicitly coding some variant of them yourself.
For example, if you need to calculate the length of an array, take advantage
of the macro

  #define ARRAY_SIZE(x) (sizeof(x) / sizeof((x)[0]))

Similarly, if you need to calculate the size of some structure member, use

  #define FIELD_SIZEOF(t, f) (sizeof(((t*)0)->f))

There are also min() and max() macros that do strict type checking if you
need them.  Feel free to peruse that header file to see what else is already
defined that you shouldn't reproduce in your code.


		Chapter 18:  Editor modelines and other cruft

Some editors can interpret configuration information embedded in source files,
indicated with special markers.  For example, emacs interprets lines marked
like this:

-*- mode: c -*-

Or like this:

/*
Local Variables:
compile-command: "gcc -DMAGIC_DEBUG_FLAG foo.c"
End:
*/

Vim interprets markers that look like this:

/* vim:set sw=8 noet */

Do not include any of these in source files.  People have their own personal
editor configurations, and your source files should not override them.  This
includes markers for indentation and mode configuration.  People may use their
own custom mode, or may have some other magic method for making indentation
work correctly.


		Chapter 19:  Inline assembly

In architecture-specific code, you may need to use inline assembly to interface
with CPU or platform functionality.  Don't hesitate to do so when necessary.
However, don't use inline assembly gratuitously when C can do the job.  You can
and should poke hardware from C when possible.

Consider writing simple helper functions that wrap common bits of inline
assembly, rather than repeatedly writing them with slight variations.  Remember
that inline assembly can use C parameters.

Large, non-trivial assembly functions should go in .S files, with corresponding
C prototypes defined in C header files.  The C prototypes for assembly
functions should use "asmlinkage".

You may need to mark your asm statement as volatile, to prevent GCC from
removing it if GCC doesn't notice any side effects.  You don't always need to
do so, though, and doing so unnecessarily can limit optimization.

When writing a single inline assembly statement containing multiple
instructions, put each instruction on a separate line in a separate quoted
string, and end each string except the last with \n\t to properly indent the
next instruction in the assembly output:

	asm ("magic %reg1, #42\n\t"
	     "more_magic %reg2, %reg3"
	     : /* outputs */ : /* inputs */ : /* clobbers */);


		Chapter 20: Conditional Compilation

Wherever possible, don't use preprocessor conditionals (#if, #ifdef) in .c
files; doing so makes code harder to read and logic harder to follow.  Instead,
use such conditionals in a header file defining functions for use in those .c
files, providing no-op stub versions in the #else case, and then call those
functions unconditionally from .c files.  The compiler will avoid generating
any code for the stub calls, producing identical results, but the logic will
remain easy to follow.

Prefer to compile out entire functions, rather than portions of functions or
portions of expressions.  Rather than putting an ifdef in an expression, factor
out part or all of the expression into a separate helper function and apply the
conditional to that function.

If you have a function or variable which may potentially go unused in a
particular configuration, and the compiler would warn about its definition
going unused, mark the definition as __maybe_unused rather than wrapping it in
a preprocessor conditional.  (However, if a function or variable *always* goes
unused, delete it.)

Within code, where possible, use the IS_ENABLED macro to convert a Kconfig
symbol into a C boolean expression, and use it in a normal C conditional:

	if (IS_ENABLED(CONFIG_SOMETHING)) {
		...
	}

The compiler will constant-fold the conditional away, and include or exclude
the block of code just as with an #ifdef, so this will not add any runtime
overhead.  However, this approach still allows the C compiler to see the code
inside the block, and check it for correctness (syntax, types, symbol
references, etc).  Thus, you still have to use an #ifdef if the code inside the
block references symbols that will not exist if the condition is not met.

At the end of any non-trivial #if or #ifdef block (more than a few lines),
place a comment after the #endif on the same line, noting the conditional
expression used.  For instance:

#ifdef CONFIG_SOMETHING
...
#endif /* CONFIG_SOMETHING */


		

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>《内核官方文档》。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>


------
