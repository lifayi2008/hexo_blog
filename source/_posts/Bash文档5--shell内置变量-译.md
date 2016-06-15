---
title: Bash文档5--shell内置变量(译)
date: 2016-06-15 16:46:18
tags: bash
categories: Bash
---
本章描述bash使用的shell内置变量。bash会自动给部分的这些变量分配一个默认值
<!-- more -->

#### Bourne Shell变量

bash兼容一下sh变量。部分情况下，bash会给这些变量分配默认值

**`CDPATH`**  
冒号分隔的目录列表，作为cd内置命令搜索的路径

**`HOME`**  
当前用户的家目录；`cd`命令切换的默认目录。也是Tilde扩展时`~`代表的目录

**`IFS`**  
分隔字段的一系列字符；shell进行扩展（expansion）时用来分割word

**`MAIL`**  
如果变量被设置为一个文件名或者目录名，并且`MAILPATH`变量未设置，特定文件中或者Maildir格式目录中有邮件到达时shell会通知用户

**`MAILPATH`**  
冒号分隔的文件列表，shell会周期性的检查列表中的文件以确定新邮件的到来。每个文件名后面跟`?`然后再跟一段字符串表示的消息可以在新邮件到来时打印这段消息。可以在消息中使用`$_`变量代表当前的文件名

**`OPTARG`**  
getopts内置命令处理的上一个参数的值

**`OPTIND`**  
getopts内置命令处理的上一个参数的索引

**`PATH`**  
冒号分隔的目录列表，shell在这个列表的目录中搜索命令。一个空的目录名表示当前目录。两个相邻的冒号表示中间有有一个空目录，前置或者后置的冒号也表示一个空目录

**`PS1`**  
首要命令行提示符字符串。默认值是`\s-\v\$`。后面的空值提示符章节描述完整的转移字符序列

**`PS2`**  
次要的命令行提示符字符串。默认值是`>`

#### Bash变量

Bash同时也会设置并使用下面这些变量，但是其他的shell可能不会进行特殊对待

Bash使用的另外一些变量会在其他的章节描述：比如作业控制相关的变量

**`BASH`**  
当前bash可执行文件的路径

**`BASHOPTS`**  
冒号分隔的当前启用的shell选项。列表中的所有word都可以作为`shopt`命令`-s`选项的参数值。这个列表其实也就是`shopt`内置命令打印的内容中为`on`的那些选项。当bash启动时会在读取`startup file`之前应用这些选项。本变量是只读的

**`BASHPID`**  
当前bash进程的pid。在某些情形下不同于`$$`，比如一些不需要bash去初始化的子shell

**`BASH_ALIASES`**  
一个关联数组，数组成员对应于alias内置命令维护的内部的别名列表。增加到这个数组中的元素会成为bash的一个别名，将一个元素移除数组也会导致从bash别名列表中删除这个别名

**`BASH_ARGC`**  
An array variable whose values are the number of parameters in each frame of the current bash execution call stack. The number of parameters to the current subroutine (shell function or script executed with . or source) is at the top of the stack. When a subroutine is executed, the number of parameters passed is pushed onto BASH_ARGC. The shell sets BASH_ARGC only when in extended debugging mode (see The Shopt Builtin for a description of the extdebug option to the shopt builtin)

**`BASH_ARGV`**  
An array variable containing all of the parameters in the current bash execution call stack. The final parameter of the last subroutine call is at the top of the stack; the first parameter of the initial call is at the bottom. When a subroutine is executed, the parameters supplied are pushed onto BASH_ARGV. The shell sets BASH_ARGV only when in extended debugging mode (see The Shopt Builtin for a description of the extdebug option to the shopt builtin)

**`BASH_CMDS`**  
一个关联数组变量成员对应于hash内置变量维护的命令哈希表。增加到这个数组中的成员会成为命令hash表的一个条目，从数组中移除成员也会从hash表中删除对应的条目

**`BASH_COMMAND`**  
The command currently being executed or about to be executed, unless the shell is executing a command as the result of a trap, in which case it is the command executing at the time of the trap.

**`BASH_COMPAT`**  
用来设置shell的兼容性级别。shopt内置命令章节描述各种shell兼容级别以及相关的影响。值可以是一个整数或者小数表示想要的设置的兼容级别。如果`BASH_COMAPT`为空或者未设置，则表示设置为默认值，一般为当前bash的版本。如果设置的值不是一个合法的级别值，则bash会将值置为默认值，并且打印一个错误消息。可用的级别值在前面的shopt章节中描述（比如，4.2和42都是一个合法的级别值）

**`BASH_ENV`**  
如果在执行shell脚本时设置本变量，则变量的值会作为一个startup file被执行脚本的bash读取和执行

**`BASH_EXECUTION_STRING`**  
`-c`选项的参数

**`FUNCNAME`**  
一个数组变量包含shell当前函数调用栈中的所有函数名。索引0表示任何当前执行的shell函数名。最底端的元素（索引值最大）是`main`。只有在执行shell函数时这个变量才存在。给变量分配值不会有任何结果并且会返回一个错误。如果FUNCNAME被取消设置，则它不在有任何特殊意义，即使后面进行重新设置

这个变量可以配合`BASH_LINENO`和`BASH_SOURCE`变量使用。`FUNCNAME`中的每一个值在`BASH_LINENO`和`BASH_SOURCE`都有对应的值来描述调用栈。比如，`${FUNCNAME[$i]}`被在文件`${BASH_SOURCE[$i+1]}`中第`${BASH_LINENO[$i]}`行调用。`caller`内置命令使用这些信息来显示当前调用栈的情况

**`BASH_LINENO`**  
一个数组变量，成员是每一个被调用的`FUNCNAME`在源文件中的行号。`${BASH_LINENO[$i]}`表示`${FUNCNAME[$i]}`在源文件（`${BASH_SOURCE[$i+1]}`）中被调用的行号（or `${BASH_LINENO[$i-1]}` if referenced within another shell function）。使用LINENO得到当前的行号

**`BASH_SOUCE`**  
一个数组变量，成员是FUNCNAME数组变量中成员对应的函数名所在的源文件名。shell函数`${FUNCNAME[$i]}`在文件`${BASH_SOURCE[$i]}`中定义并且在`${BASH_SOURCE[$i+1]}`中调用

**`BASH_REMATCH`**  
一个数组变量，成员是`[[`条件命令的`=~`二元操作符右边的正则表达式匹配到的各部分字符串。索引0对应的值是匹配整个正则表达式的字符串。索引n对应的元素是正则表达式中第n个括号表达式匹配到的部分。本变量是只读的

**`BASH_SUBSHELL`**  
Incremented by one within each subshell or subshell environment when the shell begins executing in that environment. The initial value is 0

**`BASH_VERSINFO`**  
一个只读的索引数组变量，成员保存了当前bash实例的版本的信息

**`BASH_VERSION`**  
当前bash实例的版本信息

**`BASH_XTRACEFD`**  
如果设置为一个合法的文件描述符的整数值，Bash会将启用`set -x`而产生的追踪信息输出到这个文件描述符。这样可以区分开分析输出和错误输出。如果将`BASH_XTRACEFD`取消或者分配一个新值则这个文件描述符被关闭。取消`BASH_XTRACEFD`或者设为空值则Bash会将追踪分析输出打印到标准错误输出。注意：将`BASH_XTRACEFD`设置为2，然后再取消设置会关闭标准错误输出

**`CHILD_MAX`**  
Set the number of exited child status values for the shell to remember. Bash will not allow this value to be decreased below a POSIX-mandated minimum, and there is a maximum value (currently 8192) that this may not exceed. The minimum value is system-dependent.

**`COLUMNS`**  
用来设置select命令打印选项列表时的终端宽度。如果checkwinsize选项启用则会自动设置，或者依赖于在交互式shell中收到一个SIGWINCH信号