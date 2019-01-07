---
title: C与C++基本数据类型大小
tags: C_and_C++
---

------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------

<style>table{word-break:initial;}</style>



# 1 VC_64位
|数据类型|	大小|
|--|--|
|_Bool或bool|1B
|char|1B|
|signed char|1B|
|unsigned char|1B|
|short=short int=signed short=signed short int	|2B	|	
|unsigned short=unsigned short int|	2B	|
|int=signed int	|4B	|
|unsigned int|	4B	|
|**long=long int=signed long=signed long int**|	4B	|
|**unsigned long= unsigned long int**|	4B	|
|long long =long long int= signed long long =signed long long int| 8B
|unsigned long long =unsigned long long int|8B
|float|	4B
|double	|8B|
|**long double**|	8B|
|指针|32位4B，64位8B
|sizeof (注：这不是一种数据类型，只是为了说明其结果的大小)|32位4B，64位8B

# 2 GCC_64位
		
 |数据类型|	大小|
|--|--|
|_Bool或bool|1B
|char|1B|
|signed char|1B|
|unsigned char|1B|
|short=short int=signed short=signed short int	|2B	|	
|unsigned short=unsigned short int|	2B	|
|int=signed int	|4B	|
|unsigned int|	4B	|
|**long=long int=signed long=signed long int**|	8B	|
|**unsigned long= unsigned long int**|	8B	|
|long long =long long int= signed long long =signed long long int | 8B
|unsigned long long =unsigned long long int|8B
|float|	4B
|double	|8B|
|**long double**|	16B|
|指针|32位4B，64位8B
|sizeof (注：这不是一种数据类型，只是为了说明其结果的大小)|32位4B，64位8B




------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：<font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------