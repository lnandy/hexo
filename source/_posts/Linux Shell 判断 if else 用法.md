title: Linux Shell 判断 if else 用法
categories:
- linux
---
> 原文地址 https://www.cnblogs.com/waitig/p/5578022.html

> 判断语句是每个语言都必不可少的关键语法，Shell 命令当然也不例外。今天就给大家介绍下 Shell 判断语句 if else 用法。

[![](http://cdn.linuxdaxue.com/wp-content/uploads/2016/05/shell_thumb-3.jpg)](http://cdn.linuxdaxue.com/wp-content/uploads/2016/05/shell-3.jpg)

if 语句通过关系运算符判断表达式的真假来决定执行哪个分支。

Shell 有三种 if else 格式：

1.  if … fi 格式
2.  if … else … fi 格式
3.  if … elif … else … fi 格式

下面我就分别就这几种格式来为大家详细介绍下。

## 一、Shell 判断语法之 if … else 格式

### if … else 格式的语法：

<pre>if [ expression ]
then
   Statement(s) to be executed if expression is true
fi</pre>

### 说明：

如果 expression 返回 true，then 后边的语句将会被执行；

如果返回 false，不会执行任何语句。

最后必须以 fi 来结尾闭合 if，fi 就是 if 倒过来拼写，后面也会遇见。

注意：expression 和方括号 ([]) 之间必须有空格，否则会有语法错误。

### 使用举例：

<pre>#!/bin/sh

a=400
b=800

if [ $a == $b ]
then
   echo "a is equal to b"
fi

if [ $a != $b ]
then
   echo "a is not equal to b"
fi</pre>

运行结果：

<pre>a is not equal to b</pre>

## 二、Shell 判断语法之 if … else … fi 格式

### if … else … fi 语句的语法

<pre>if [ expression ]
then
   Statement(s) to be executed if expression is true
else
   Statement(s) to be executed if expression is not true
fi</pre>

### 说明：

如果 expression 返回 true，那么 then 后边的语句将会被执行；

否则的话，将会执行 else 后边的语句。

### 使用举例：

<pre>#!/bin/sh

a=400
b=800

if [ $a == $b ]
then
   echo "a is equal to b"
else
   echo "a is not equal to b"
fi</pre>

执行结果：

a is not equal to b

## 三、Shell 判断语法之 if … elif … fi 格式

if … elif … fi 语句可以对多个条件进行判断

### 语法：

<pre>if [ expression 1 ]
then
   Statement(s) to be executed if expression 1 is true
elif [ expression 2 ]
then
   Statement(s) to be executed if expression 2 is true
elif [ expression 3 ]
then
   Statement(s) to be executed if expression 3 is true
else
   Statement(s) to be executed if no expression is true
fi</pre>

### 说明：

哪一个 expression 的值为 true，就执行哪个 expression 后面的语句；

如果都为 false，那么不执行任何语句。

### 使用举例：

<pre>#!/bin/sh

a=400
b=800

if [ $a == $b ]
then
   echo "a is equal to b"
elif [ $a -gt $b ]
then
   echo "a is greater than b"
elif [ $a -lt $b ]
then
   echo "a is less than b"
else
   echo "None of the condition met"
fi</pre>

运行结果：

a is less than b

## 四、其他说明

if … else 语句也可以写成一行，以命令的方式来运行，像这样：

<pre>if test $[2*3] -eq $[1+5]; then echo 'The two numbers are equal!'; fi;</pre>

if … else 语句也经常与 test 命令结合使用，如下所示：

<pre>num1=$[2*3]
num2=$[1+5]
if test $[num1] -eq $[num2]
then
    echo 'The two numbers are equal!'
else
    echo 'The two numbers are not equal!'
fi</pre>

输出：

The two numbers are equal!

test 命令用于检查某个条件是否成立，与方括号 ([]) 类似。

好了，今天对于 Shell 判断（Shell if else）用法就先为大家介绍到这里，还是那句话，大家要多多练习才是。

更多 shell 教程请看：[Linux Shell 系列教程](http://www.linuxdaxue.com/linux-shell-serial-course.html)

* * *

本文由 Linux 技术达人【[daxue](http://www.linuxdaxue.com/author/daxue)】发表在：[Linux 大学](http://www.linuxdaxue.com/)
本文固定链接：[Linux Shell 系列教程之（九）Shell 判断 if else 用法](http://www.linuxdaxue.com/linux-shell-if-else-commands.html)