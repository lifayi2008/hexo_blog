---
title: Bash文档1--介绍和术语(译)
date: 2016-06-13 15:51:33
tags: bash
categories: Bash
---
#### 介绍

##### 什么是Bash

Bash是GNU操作系统的shell，或者说是命令语言解释器。Bash是"Broune-Again Shell"的一个简称，也是对当前Unix shell 'sh'最初作者的一个调侃，sh最早出现在第七版的贝尔实验室的Unix系统上
<!-- more -->
Bash在最大限度上兼容sh，并且从Korn shell和C shell中借鉴了很多有用的特性。Bash目标是成为IEEE POSIX规范（IEEE标准1003.1）中的一个符合IEEE POSIX标准的SHELL和工具。它在交互和编程方面都对sh进行了功能性的改进

GUN操作系统同时也提供了其他的shell，包括C shell，Bash是默认的shell。和其他的GUN软件一样，Bash有很强的可移植性。现在几乎可以运行在所有版本的Unix上面甚至一些其他的操作系统

##### 什么是shell

从根本上说，shell是一个简单的可执行命令的宏处理器。宏处理器意味着这样一个功能：文本和符号都会被展开为更大的一个表达式

Unix shell是一个命令解释器和编程语言。作为一个命令解释器，shell提供了一个用户接口来执行各种各样的GNU工具命令。作为一种编程语言，我们可以将那些命令组合起来一起执行。可以创建一个包含命令的文件，而这个文件自己也成了一个命令。这些命令就和系统命令比如/bin下面的命令一样，让用户和组可以使用这些命令建立自定义的环境和执行自动化任务

Shell可以以交互式和非交互式执行。在交互模式中，它从键盘接收输入。当以非交互式执行时，shell从一个文件中读取命令执行

Shell可以同步或者异步的方式执行GUN命令。如果是同步的方式，shell会等待命令返回才能接收新的输入；异步执行的命令可以和shell并行执行，shell可以同时接收新的命令。redirection结构可以更细粒度的控制命令的输入和输出。另外，shell也可以让我们控制命令的执行环境

Shell也提供了一系列内置命令(builtins)来实现那些不可能或者不便于使用外部可执行文件实现的功能。比如cd、break、continue和exec，这些命令不能使用外部命令来实现，因为这些命令是直接对shell自己进行操作。history、getopts、kill或者pwd命令可以使用外部命令实现，但是使用内置命令更方便。所有的内置命令在后面的章节中描述

执行命令是必不可少的功能，但是shell更强大之处是内置的编程语言。就像高等级的编程语言，shell提供了变量、控制结构、引用和函数

shell也提供了一些为交互式特别准备的特性。这些交互式特性包括作业控制、命令行编辑、命令历史和别名，这些特性都将在本手册中描述

#### 术语（未按字母顺序）

下面的这些术语将在整个手册中使用

**POSIX**：一系列基于Unix的开放系统标准。Bash主要关心POSIX1003.1标准的Shell和工具部分

**blank**：空格或者tab字符

**operator**：包括控制操作符(control operator)和重定向操作符(redirection operator)。operator至少要包括一个未被引用的元字符(metacharacter)

**word**：被shell作为单个单元的一串字符。words不包括未被引用的元字符（operator）

**token**：被shell作为单个单元的一串字符。是一个word或者operator

**control operator**：起控制作用的token，包括换行和 `||` `&&` `&` `;` `;;` `|` `|&` `(` `)`单引号中的字符

**matecharacter**：未被引用的元字符用来分割words。元字符包括blank和`|` `&` `;` `(` `)` `<` `>`单引号中的字符

**builtin**：shell内部实现的一个命令

**exit status**：命令返回给调用者的值。值被限制为8位，所以最大为255

**return status**：等同于exit status

**field**：经过shell一些扩展之后形成的文本单元。如果是执行一个命令，扩展之后的field作为命令名和参数

**filename**：用来标示文件的一串字符

**job**：组成管道线的一系列命令，和这些命令的衍生进程，所有的这些进程都在一个进程组中

**job control**：用户可以选择性的停止或者继续某些进程的机制

**name**：一个只包含字母数字下划线并且以字母或者下划线开始的word。name用来标示变量或者函数名，或者说是一个标识符

**process group**：有相同进程组ID的一系列进程

**reserved word**：对shell有特殊意义的word。大部分保留字开始一个控制流程，比如for或者while

**signal**：系统中的某种事件发生时，内核通知进程的一种机制


