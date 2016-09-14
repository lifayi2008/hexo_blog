---
title: awk笔记
date: 2016-09-14 11:43:23
tags: [bash, awk]
categories: Bash
---
#### awk基本语法和命令

##### awk命令语法

    awk -Fs  '/pattern/ {action}' input-file
    awk -Fs '{action}' input-file
<!-- more -->
**`-F`**  指定域分隔符，如果不指定，将使用一个空格域作为分隔符

`/pattern/`和`{action}`都使用引号引起来（最好使用单引号，如果awk的参数中不包含bash中的特殊字符则可以使用双引号）

`/pattern/`是可选的。如果不指定，则表示匹配任何一行内容；如果指定一个模式，则对应的`action`只应用于匹配的行；如果指定两个模式则应用于两个模式之间的内容

> 注意：在awk中不能使用`$`表示文件末尾，可以使用`0`来表示 如awk '/2013-11-17 13:22:18/,0' test.txt

`{action}`中的`action`是awk的程序命令，可以是一个或者多个用逗号分隔的多个。整个`action`都使用`{}`括起来；程序命令中可以有多个`/pattern/{action}`，表示针对符合特定的`pattern`的行做对应的`action`操作；但是在`action`中不能在嵌套`/pattern/{action}`了（可以使用程序来达成同样的效果）

`input-file`操作输入的文件，可以是一个或者多个用空格分开

也可以将awk程序命令放置在一个文件中，然后使用`awk -F -f myscript.awk input-file`来使用awk程序操作后面的文件

##### 应用实例

    $ awk -F : '/mail/ {print $1}' /etc/passwd

`-F:`表示域的分隔符是`:`，分隔符可以使用引号括起来

`/mail/`是一个匹配模式，表示后面的程序命令将作用于包含`mail`的行

`{print $1}`awk的程序命令，表示打印前面匹配到的行的第一个字段

`/etc/password`输入的文件名

##### awk程序结构

一个典型的awk程序可以有三部分：

1.  BEGIN Block
```bash
语法：
BEGIN{awk-command}
```
BEGIN Block是可选的，仅在程序开始的时候（未读入文件的时候）执行一次；是打印表头和初始化变量的好位置；在BEGIN Block中可以运行多个awk命令，需要使用`;`分隔；关键字`BEGIN`必须是大写的

> 当只使用`BEGIN block`时可以不指定输入内容， 如果有其他的Block时必须指定输入文件，否则awk会报错

2.  Body Block
```bash
语法:
/pattern/{action}
```
body block对输入的每一行执行`pattern`和`action`；body block不需要使用关键字指示

3.  END Block
```bash
语法:
END { awk-commands }
```
END Block是可选的，只在所有body block执行完之后执行一次；END Block是打印页脚和清除其他活动的好地方；在END Block中可以运行多个awk命令，需要使用`;`分隔；关键字`END`必须是大写的

> 即使你指定有多个操作文件，BEGIN和END Block也只会各执行一次，而body block则会依次应用于每一个文件的每一行

##### 打印命令

**`print打印`**  

默认awk打印命令（不使用参数）打印所有字段（行的内容）：

    $ awk '{ print }' employee.txt

`$0`也用来表示所有字段：
    
    $ awk '{print $0}' employee.txt

可以指定打印某一个字段：

    $ awk '{print $2}' employee.txt

使用`-F`指定分隔符 可以使用`单引号` `双引号`或者没有任何引号都行，也可以紧挨着F也可以有空格

    $ awk -F ',' '{print $2}' employee.txt
    $ awk -F "," '{print $2}' employee.txt
    $ awk -F, '{print $2}' employee.txt

    $ awk -F ',' 'BEGIN{ print "--------\nName\tTitle\n--------"}{ print $2,"\t",$3;}END{ print "---------"; }' employee.txt

> 可以将每一条命令写在一行上，结尾使用`\`转接到下一行；但是仍要按在同一行的格式来写，不能省略符号

> 也可以使用`-f`参数指定一个包含awk block的文件，文件中可以使用`#`指定注释，而且可以省略引号

**`printf打印`**  

类似于c语言中的`printf`:

    printf "print format",variable1,variable2,...

可以在`print format`中使用字符常量中的转义字符：`\n` `\t` `\v` `\b` `\r` `\f`

可以在`print format`中使用的占位符：`%s` `%c` `%d` `%e` `%f` `%g` `%o` `%x` `%%`分别表示`字符串` `单个字符` `整数` `浮点数` `固定浮点数` `e或者f` `八进制` `十六进制` `%字面值`

可以在占位符中使用数字指定最小宽度，如果实际宽度小于指定的宽度则使用0或者空格补齐，默认是在左侧补齐，可以在数字之前加一个`-`表示右侧补齐，：

    $ awk 'BEGIN { printf "%6s\n", "Good" }'
    $ awk 'BEGIN { printf "|%-6s|\n", "Good" }' ##加"|"是为了能看到显示的空格

在指定的宽度数字之前加一个`.`；用在字符串时表示可以打印绝对的宽度，如果后面指定的数据长度超过指定长度则会被从后面截断（绝对宽度在awk4.0版本之前可能无效，可靠的方法是使用`substr`函数进行截断）；用在数值时表示小数的精度

    $ awk 'BEGIN { printf "%.6s\n", "Good Boy!" }'
    $ awk 'BEGIN { printf "%6s\n", substr("Good Boy!",1,6) }'
    
`printf`不使用`ORS` `OFS`指定的分隔符，它只使用自己的格式

`print`和`printf`都可以将打印内容使用`>`操作符打印到一个文件中


##### 行选取

行选取包括行号选取、模式匹配以及逻辑操作符选取：

awk在执行命令时可以使用模式匹配，模式匹配使用两个`/`包围的正则表达式用来匹配行的内容，可以使用两个模式匹配用来表示两个匹配行之间的所有行，两个模式匹配使用逗号分隔

同样可以使用一个数字表示要选取的行，也可以使用两个数字表示选取的行的范围，两个数字之间使用逗号分隔

可以混合使用行号选取和匹配模式选取

可以在记录选取中使用逻辑运算符和正则表达式配合字段进行行选取：

    $ awk -F "," '$1 == 103 {print $2}' items.txt
    $ awk -F ':' '$NF ~ /\/bin\/sh/ { n++ }; END { print n }' /etc/passwd

#### awk内置变量

1.  `FS`输入域分隔符

awk默认使用空格作为字段分隔符，也可以指定其他的字符作为分隔符，甚至可以使用正则表达式匹配到的字符作为分隔符

    $ awk -F ',' '{print $2, $3}' employee.txt
    
同样可以使用`FS`变量来设置分隔符，`FS`一般指定在BEGIN Block中，并且需要使用双引号（这意味着同时外层不能使用双引号）

可以使用部分的正则规则来同时指定多个字段分隔符：

    $ awk 'BEGIN {FS="[,:%]"} {print $2, $3}' employee-multiple-fs.txt
    $ awk -F'[,:%]' '{print $2, $3}' employee-multiple-fs.txt

2.  `OFS`输出域分隔符

默认使用空格作为输出域分隔符，只有在使用`,`连接要输出的域的时候才会使用`OFS`:

    $ awk -F ',' 'BEGIN { OFS=":" } { print $2, $3 }' employee.txt
    
未使用`,`作为域分隔符，则awk不会使用`OFS`作为输出域分隔符：

    awk 'BGEIN{FS=","}{print $2":"$3}' employee.txt、
    这个例子中手动指定一个字符串:连接两个输出字段
    
> awk中使用空字符来连接两个字符串

> awk字符串只能使用双引号引用（这意味着同时外层不能使用双引号）

> 注意 : 在BEGIN中指定的`OFS`和`FS`值需要加双引号  如：`OFS=":"`，因为给这些特殊变量赋值必须使用字符串常量

> 注意 : 如果在BODY block中使用`print` 打印`$0`时，前面指定的`OFS`值不会生效，必须在打印操作前面加上`$1=$1`

> 如果要使用`print`打印`'` `"`时，可以使用这两个字符的`ASCII`码的八进制或者十六进制值表示，比如：`print "\xxx"`

3.  `RS`记录分隔符

前面说的awk一次处理一行内容是不准确的，准确的说awk一次处理一条记录，而awk默认的一条记录就是一行，但是可以使用`RS`变量来改变

> 注意：记录选取（行号和匹配模式）应用到行，而非应用到记录

    $ awk -F, 'BEGIN { RS=":" } { print $2 }' employee-one-line.txt
    
这样`:`被用来分隔记录，上面的一行内容被分隔为多条记录，而awk一次处理一条记录

> 注意：如果指定`RS=""`则表示使用单个或者多个空行作为记录分隔符

4.  `ORS`输出记录分隔符

awk默认使用换行作为输出记录分隔符，但是可以使用`ORS`变量来改变：

    $ awk 'BEGIN { FS=","; ORS="\n---\n" } {print $2, $3}' employee.txt
    $ awk 'BEGIN { FS=","; OFS="\n";ORS="\n---\n" } {print $1,$2,$3}' employee.txt 
    
5.  `NR` `FNR`当前处理的记录号

经常用在循环中用于获取当前处理的记录数，用在END Block中可以获取文件中的记录数

    $ awk 'BEGIN{FS=","}{print "Emp Id of record number",NR,"is",$1;}END{print "Total number of records:",NR}' employee.txt
    
两者的区别是在处理第二个文件（如果指定第二个文件）时，`FNR`会重置为1，而`NR`则不回重置，而是在上一个文件的基础上继续增加
    
6.  `NF`当前处理的记录的字段数

`NF`表示使用前面指定分隔符分隔每条记录后的字段总数

我们可以使用`$NF`取得最后一个字段，`$(NF-1)`取得倒数第二个字段

> 注意：  `FS` `OFS` `RS` `ORS` 在 `BEGIN block`中指定的在处理所有行时都有效，而在`BODY block`中指定的到处理下一行时才生效，下面一个小技巧可以让在当前行生效
    
    awk 'NR==FNR{a[$5]=$0;next}{FS=",";$0=$0;print $0""a[$3]}' test2.txt test.txt

7.  `FILENAME`当前处理的文件名

当使用awk程序处理多个文件时，这变量可以获取当前处理的文件名
    
    $ awk '{ print FILENAME }'  employee.txt employee-multiple-fs.txt
    
    重点在于$0=$0这一条语句，这表示根据设置的分隔符将当前行内容重新赋值给$0
    
当从标准输入获取输入内容时，`FILENAME`是`-`

> 注意：BEGIN Block中没有`FILENAME`变量，因为这时还没有开始处理文件

#### awk变量和运算符

##### 变量

awk变量名可以使用`字母` `数字` `下划线`；关键字不能作为awk变量名；awk的变量名也不需要任何特殊符号
可以使用awk的`-v`选项定义一个变量，比如：`awk -vname='hello' 'BEGIN{print name}'`；不需要显式的定义一个变量，awk会根据上下文未首次使用到的变量赋0或者空。在awk里没有数据类型，不管awk变量是一个数字或者字符串取决于该变量使用的上下文。

如果想将shell中的变量传到awk中可以使用如下的形式：

    awk -vHOSTNMAE=$HOSTNAME 'BEGIN{print HOSTNAME}'
    awk 'BEGIN{print "'$HOSTNAME'"}'
    
    第二种形式中，$HOSTNAME首先会被shell解释并做变量名扩展，扩展后的内容和前后的两个awk参数合起来构成一个参数，awk在解释这个参数的时候$HOSTNAME的值已经在双引号中了
    
`OFMT`变量 指定awk默认输出的浮点数格式`%.6gd` ` g`为`printf`输出格式的一种表示选择`e`或者`f`中较短的形式  `d`表示十进制整数

`ARGC`和`ARGC`分别用来表示参数的个数和所有参数组成的数组：

    awk 'BEGIN{for (i in ARGV)print ARGC,ARGV[i]}' a b c

`IGNORECASE`设为非0的值，表示模式使用正则匹配时不区分大小写

##### 运算符

1.  单目运算符

awk支持`+` `-` `++` `--`单目运算符

    $ awk -F ':' '$NF ~/\/bin\/bash/ { n++ }; END { print n }' /etc/passwd      ##统计shell用户数量
    
2.  二元运算符

awk支持`+` `-` `*` `/` `%`二元运算符

    $ awk 'NR % 2 == 0' items.txt ##显示偶数的行
    
3.  赋值运算符

awk支持`=` `+=` `-=` `*=` `/=` `%=`赋值运算符

4.  逻辑运算符

awk支持`>` `<` `>=` `<=` `==` `!=` `&&` `||`逻辑运算符

这些逻辑运算符可以用在行选取中

5.  正则表达式操作符

`~`和`!~`分别表示匹配和不匹配，正则表达式使用`/`包围

正则表达式也可以用在行选取中

6.  三元操作符`? :`

7.  字符串连接操作符

awk中可以使用`空` `空字符串（""）` `空格`来连接字符串，效果是一样的

#### 条件和循环语句

同c语言，awk也支持条件和循环语句

    if (conditional-expression)
                   action 
                   
    if (conditional-expression){
        action1;
        action2;
    }
    
    if (conditional-expression)
        action1
    else
        action2
        
    
    while(condition)
      actions
      
    do
        action
    while(condition)
    
    for(initialization;condition;increment/decrement)
        actions
        
awk同样也支持`break`和`continue`用来控制循环

awk另外还支持`exit`语句，它会立即停止脚本，开始执行END Block中的内容
