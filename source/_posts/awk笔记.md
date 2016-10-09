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

> awk在使用正则选取行时和sed不同：awk会在匹配第一个正则的行中继续查找是否匹配第二个正则；而sed则从下一行中开始匹配查找

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

#### awk数组

awk数组和其他程序语言的数组相比较，awk 数组非常强大。在awk中，数组是关联的。即一个数组包含多个多个 由index/value组成的对，index不需要设置成一个数字，事实上可以是一个字符串或一个数字，不要指定一个数组的大小。awk自动为初次使用的数组元素分配空或者0，另外在awk4以后的版本中支持多维数组，可以任意进行第n个维度的遍历

awk总是将索引看作是一个字符串，即使你使用数字也是一样的，所以`a[1]`和`a["1"]`是同一个元素

可以使用`if`语句检查一个索引是否存在于一个数组中：`if(index in arrayName)`

可以使用`for`语句来遍历一个数组：`for(var in arrayName)`，`var`会依次被使用数组中的所有索引赋值

可以使用`delete arrayName[index]`来删除数组中的一个元素，或者使用`delete arrayName`来删除整个数组

可以使用`asort(arrayName)`来排序数组元素值；awk将数组元素`value`进行排序并且生成新的`key`为`1~n`来覆盖原来的`key`；如果想保留原来的数组不变，可以使用另外一个参数来作为数组名生成一个新的数组；这个函数的返回值是数组元素的个数

可以使用`asorti(arrayName)`来排序一个数组的`key`，排序之后数组的`key`为`1~n`原来的`key`变为现在的`value`；同样可以使用另外一个参数来作为新数组名，而让原来的数组不变

> `asort` `asorti`还有第三个可选参数用来指示如何排序，详细内容参考awk手册

#### awk函数

##### 字符串相关的函数

**`sub gsub gensub`**  
这些函数用来在记录或者目标字符串中查找匹配正则表达式的字符串，然后使用替换字符串替换这些匹配到的字符串；默认在当前记录中查找；正则表达式使用`/`包围

`sub(r, s [, t])`在目标字符串`t`或者当前记录中查找匹配正则表达式`r`的子串并且使用`s`来替换，只替换第一次匹配到的

`gsub(r, s [, t])`可以替换所有匹配到的子串，并且返回值为替换的次数

`gensub(r, s, h [, t])` `h`如果是以`G`或者`g`字符开头则替换所有匹配到的，否则替换第`h`个

> `\\0`和`&`可以被用在替换串中表示匹配到的字符串，如果替换串中出现`&`要使用`\&`的形式，可以在正则表达式中使用`()`，然后在替换串中使用`\\n`来引用

> `gensub`不同于另外两个函数，`gensub`不会修改源字符串，只是将修改后的字符串返回；而另外两个函数则是修改源字符串

**`index`**  
`index(s, t)` 返回字符串`t`在`s`中第一次出现的位置，偏移量从1开始

**`length`**   
`length([s])` 返回字符串`s`的长度或者记录的长度

**`substr`**   
`substr(s, l[, p])` 返回字符串`s`中从`l`开始的`p`长度的子串；如果`p`为空则返回到字符串末尾

**`match`**  
`match(s, r [, a])` 返回正则表达式`r`在字符串`s`中出现的位置，如果没有出现则返回0；`match`函数会将内置变量`RSTART`设置为子串在字符串中的起始位置，将`RLENGTH`变量设置为匹配的子串长度，这样就可以使用这两个变量和`substr`函数来提取匹配的子串（弥补了`substr`不能使用正则的问题）；如果指定`a`参数则表示一个数组名，则awk会使用匹配到的子串来填充这个数组：`a[0]`表示正则`r`匹配到的整个子串，`a[1]`表示正则`r`中的第一个`()`表达式匹配到的子串，依次类推；同时每个元素还有另外两个子元素`a[n, "start"]` `a[n, "length"]`来表示每一个子串的起始位置和长度

**`split patsplit`**  
`split(s, a [, r [, seps] ])` 将字符串`s`分隔，并将所有元素放入数组`a`，返回数组元素的个数；默认使用`FS`作为分隔符，如果指定`r`则使用其作为分隔符；如果指定第四个参数`seps`则表示一个数组名，这个数组的第`i`个元素是数组`a[i]`和`a[i+1]`之间的分隔符；如果使用空格作为分隔符，则所有前导和末尾的空格分别作为`seps[0]`和`spes[n]`的值，`n`表示`split`函数的返回值

`patsplit(s, a [, r [, seps] ])`同`split`但是在`r`为空时使用`FPAT`内置变量作为分隔符

**`sprintf`**  
用法同`printf`表达式，但这是一个函数；并且不会向终端打印字符串，而是返回这个字符串

**`strtonum`**  
`strtonum(num)`将字符串转换为数字，如果字符串第一个字符非数字则返回0，否则将前缀中的数字字符串提取出来转换为数字；如果第一个字符是`0`或者`0x`则分别被当作8进制和十六进制进行转换

**`tolower toupper`**  
分别将字符串中的字母转换为小写或者大写，非字母字符不变

##### 数值相关的函数

**`int`**  
`int(expr)` 将`expr`截断为整数

**`rand`**  
`rand()`返回一个大于等于0，小于1的随机数

**`srand`**  
`srand([expr])`为`rand()`函数产生一个随机数种子，默认使用当前时间，返回值为前一个随机数种子

##### 时间相关的函数

**`systime`**  
`systime()`返回自`1970-01-01 00:00:00 UTC`以来的秒数

**`strftime`**  
`strftime([format [, timestamp[, utc-flag]]])` 根据`format`提供的格式，将`timestamp`进行格式化处理，`utc-flag`如果非空则表示使用`UTC`时间，否则使用系统指定的时区

    %a  简写的星期名   %A完整的星期名    %b简写的月名称  %B 完整的月份名称  %d用数字表示月份  %D使用月/日/年的形式
    %c本地日期和时间   %e月份中的某一天,如果只有一个数字用0填充    %H数字表示24小时的中小时   %I数字表示12小时中的小时
    %j从1月1日到现在的天数   %m数字表示的月份   %M数字表示的分钟数   %p12小时中的AM/PM  %S数字表示的秒数
    %U数字表示一年中的周数，周日作为一周的开始   %W数字表示一年中的周数，周一作为一周的开始     %w数字表示星期几 
    %x本地日期   %X本地时间    %y数字表示的年份    %Y完整的数字年份   %%  %字符标记
    
#### getline

awk可以使用`getline`函数从`管道` `标准输入`或者`其他文件`读取输入
`getline`函数用于读取输入的下一行，并且设置内置变量`NF` `NR` `FNR`如果读取到一条记录函数就返回1，如果读取到EOF则返回0，如果发生错误，比如文件不存在则返回-1

`getline` 会获取输入文件的下一行赋给`$0`，同时设置`NF` `NR` `FNR`变量

`getline < filename` 表示从`filename`中获取下一行并赋给`$0`，同时设置`NF`变量

`getline var` 会获取输入文件的下一行并且赋值给`var`，同时设置`NR` `FNR`变量

`getline var < filename` 表示从`filename`中获取下一行并且赋值给`var`

`command | getline [var]`  将command命令的结果的第一行通过管道赋值给`$0`或者`var`；command是一个系统命令并且需要使用双引号，command命令显示结果通过管道发送，调用一次`getline`命令只能获取第一行
```bash
awk BEGIN{"date" | getline a; print a}''         ##从命令获取输入
awk 'BEGIN{while("cal" | getline a) print a}'      ##从命令获取多行输入
awk 'BEGIN{print "what is your name?";getline name < "/dev/tty";print "you name is",name,"bye bye"}'      ##从标准输入获取输入
awk 'BEGIN{while(getline <"/etc/passwd" > 0)count++;print count}'    ##统计文件的行数，注意这里一定要添加>0，否则当/etc/passwd不存在时候，会进入死循环，因为此时getline返回-1  不为0
```

#### awk中的重定向和管道

当使用`print`或者`printf`命令打印时可以使用`>`或者`>>`重定向操作符将内容重定向到某个文件，文件名字使用双引号包围

    awk '{print $1 > "another.txt"}' test.txt 
    
> 在文本中的所有行未处理完毕之前>不会清空文件another.txt文件内容

如果在awk中打开了管道，就必须先关闭它才能打开另一个管道(在整个文件处理过程中也只能使用一个管道)

    awk '{print $1,$2,$3 | "sort -r +1 -2 +0 -1"}END{close("sort -r +1 -2 +0 -1")}'
    
close(file [, how])
Close file, pipe or co-process. The optional how should only be used when closing one  end of a two-way pipe to a co-process. It must be a string value, either "to" or "from".

在重定向和管道以及`getline`中可以使用一系列的特殊文件名，通过这些文件名可以访问继承自父进程(通常是shell)打开的文件描述符，从而可以进行一些读写操作

    /dev/stdin     The standard input.
    /dev/stdout     The standard output.
    /dev/stderr     The standard error output.
    /dev/fd/n     The file associated with the open file descriptor n.

#### awk其他使用技巧

在awk中执行系统命令：

`print ... | command` 将打印内容通过管道发送，`command`通过管道获取输入然后执行命令

`command | getline [var]` 在前面已经介绍过

`system(command)`函数将系统命令作为参数，执行这个命令并且将退出状态返回给awk

> 上面三种情况的`command`都必须加双引号

`FIELDWIDTHS`变量可以指定每个字段为特定的字符数，如：awk -vFIELDWIDTHS="1 2 9999"指定第一个字段为行中的第一个字符，第二个字段为接下来的2个字符第三个字段为接下来的9999个字符(因为linux行字符数的限制，这个基本上可以表示剩下的所有字符了)，注意如果实际行的字符数大于指定的字符数的和，则还会按照指定数字的字符显示，剩下的被舍弃，但是$0仍表示整行

`next/nextfile`命令读取文件的下一行/下一个文件，并从脚本的顶部开始执行

可以在awk的`{action}`后面加一个1或者其他非0数字来让awk打印每一行(不管前面的action是否有打印命令)，注意1表示`1{print}`，其中`1`和`{print}`可以省略其中一个都表示同样的意思
