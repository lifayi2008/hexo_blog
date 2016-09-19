---
title: sed笔记
date: 2016-09-19 17:44:05
tags: [bash, sed]
categories: Bash
---
#### sed基本语法和命令

示例文本：
```bash
cat employee.txt

101,John Doe, CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```
<!-- more -->
**1.sed命令语法**

    sed [options] {sed-commands} {input-file}
    
sed一次从`input-file`读入一行数据，然后将`sed-commands`作用于这行数据，然后再读入一行，重复这样的过程直到文件结尾

可选的`options`用于指定一个选项

`input-file`可以是一个普通文件，也可以是管道

```bash
sed -n 'p' /etc/passwd
##打印/etc/passwd文件所有行

p表示sed-commands，这样的命令可以同时有一个或者多个
```

```bash
sed -n -f test-script.sed /etc/passwd 
##打印以root开始或者以nobody开始的行

-f 指定一个sed命令的脚本文件

cat test-script.sed
/^root/ p
/^nobody/ p
```

> 注意：这里在一个文件中包含了多个sed命令，多条命令将依次作用于输入的行

```bash
sed -n -e '/^root/ p' -e '/^nobody/ p' /etc/passwd
##打印/etc/passwd文件以root和nobody开头的行

也可以下面这样，当命令很长的时候分成多行。
sed -n \
 -e '/^root/ p' \
 -e '/^nobody/ p' \
  /etc/passwd
  
还可以使用'{}'

sed -n  '{
 /^root/ p
 /^nobody/ p
}' /etc/passwd
```

> sed 不会修改原文件，总是打印输出到标准输出，如果想保存改变请用输出重定向。

> 使用多个命令处理同一行数据时，可以使用`/patten/{command;command}`的形式控制只有特定的行才会应用相关的命令；**`/pattern/`后面可以使用`!`来表示取反的操作** ，即对不匹配的行执行操作

**2. sed脚本流**

sed脚本按照`read`，`execution`，`print`，`repeat`顺序来执行

**`read`**：读一行数据到模式空间(一个存放读入数据的内部临时缓冲区)

**`execution`**：对模式空间中的一行数据执行命令，如果有多个命令，则按照顺序依次执行

**`print`**：打印模式空间中的一行数据，清空模式空间(-n参数可以关闭默认的打印)

**`repeat`**：重复执行上面的三个步骤，直到文件结尾

**3. 打印模式空间**

> 注意：p表示的打印是在sed脚本流中的第二个步骤(`execute`)中执行的，与第三个步骤的`print`不同

```bash
(1)sed 'p' employee.txt ##打印每一行两次
(2)sed -n 'p' employee.txt ##打印每行一次，类似cat
(3)sed -n '2 p' employee.txt ##打印第二行
(4)sed -n '1,4 p' employee.txt ## 打印1到4行
(5)sed -n '2,$ p' employee.txt ##打印第二行到最后一行
(6)sed -n '1~2 p' employee.txt
(7) sed -n '/Jane/ p' employee.txt ##打印每一个匹配Jane的行
(8)sed -n '/Jason/,4 p' employee.txt##打印第一个匹配Jason开始的行到第四行，如果第四行之后又出现Jason那再打印那一行
(9)sed -n '/Raj/,$ p' employee.txt ##打印第一个匹配Raj开始到最后一行
(10)sed -n '/Raj/,/Jane/ p' employee.txt ##打印匹配Raj开始到第一个匹配Jane那行，如果没有匹配Jane那一行则打印到行末，如果出现Raj和Jane后又一次出现Raj那同样匹配打印
(11)sed -n '/Jason/,+2 p' employee.txt ##打印匹配Jason的行和之后的2行，如果两行之后又匹配到Jason后，同样打印
```

**`,`** 范围 n,m n到m行

**`+`** 联合 n,+m n行到 n+m

**`~`** 间隔，1~n，表示匹配1，1+n, 1+n+n行

**`//`** 正则匹配

> 可以混合使用数字和正则

> 当指定两个地址(或regexp)来指定一个范围时检查的规则是这样的：一次检查每一行看是否匹配地址1(没有匹配到不会检查地址2)，如果有匹配到地址1则从下一行开始检查地址2，直到第一个匹配到地址2(这期间会忽略地址1)；循环这样的过程

匹配模式中可以使用shell传过来的变量：

    a=test; sed -n '/'$a'/,$p' test.txt
    sed -n '/'"$a"'/,$p' test.txt

**4. 删除行(d command)**

删除当前模式空间的内容，然后开始下一次脚本流(跳过了`print`)

```bash
(1)sed 'd' employee.txt ##删除所有行
 (2)sed '2 d' employee.txt ##删除第二行
 (3)sed '1,4 d' employee.txt ##删除1到4行
 (4)sed '2,$ d' employee.txt ##删除第二行到最后一行
 (5)sed '1~2 d' employee.txt ##删除奇数的行
 (6)sed '/Manager/ d' employee.txt##删除匹配Manager的行
 (7)sed '/Jason/,4 d' employee.txt##删除匹配Jason开始到第四行
 (8)sed '/Raj/,$ d' employee.txt##删除匹配Raj开始到最后一行
 (9)sed '/Raj/,/Jane/ d' employee.txt##删除匹配开始Raj到匹配Jane的行
 (10)sed '/Jason/,+2 d' employee.txt##删除匹配Jason开始的行和后面2行
 (11)sed '/^$/ d' employee.txt##删除所有空行
 (12)sed '/^#/ d' employee.txt##删除以# 开始的行
```

**5. 写入到一文件(w command)**

```bash
(1)sed 'w output.txt' employee.txt##把employee.txt文件写入到output.txt文件然后再屏幕显示
(2)sed -n 'w output.txt' employee.txt ##和(1)一样，不显示在屏幕。
(3)sed -n '2 w output.txt' employee.txt##仅把第二行写入到output.txt文件
(4)sed -n '1,4 w output.txt' employee.txt##把1到4行学如到output.txt文件
(5)sed -n '2,$ w output.txt' employee.txt##把第二行到最后一行写入到output.txt文件
(6)sed -n '1~2 w output.txt' employee.txt##把奇数行写入到output.txt文件
(7)sed -n '/Jane/ w output.txt' employee.txt##把匹配Jane的行写入到output.txt文件
(8)sed -n '/Jason/,4 w output.txt' employee.txt##匹配Jason开始到第四行写入到output.txt文件
(9)sed -n '/Raj/,$ w output.txt' employee.txt##匹配Raj开始到最后一行写入到output.txt文件
(10)sed -n '/Raj/,/Jane/ w output.txt' employee.txt##匹配Raj开始到匹配Jane那行写入到output.txt文件
(11)sed -n '/Jason/,+2 w output.txt' employee.txt##匹配Jason开始那行后面2行写入到output.txt文件
```

#### 替换命令

**1. sed替换命令语法**
    
    sed '[address-range|pattern-range] s/original-string/replacement-string/[substitute-flags]' inputfile
    
`address`和`pattern`是可选的，如果不指定范围，则对所有行执行替换命令

`s`命令告诉sed执行替换操作

`original-string`表示从输入的行中查找的字符串，可以是一个正则表达式

`rep-string`将会替换掉`original-string`，`rep-string`也可以是shell中的变量，例如`"$var"` `${var}`

`flags`也是可选的，注意和上节的命令有一部分是相同的，`flags`需要配合替换命令组合使用，而上节中的命令单独使用

如果不指定`-i`参数的话，替换只会发生在模式空间，然后通过`print`表现出来，源文件不变

```bash
(1)sed 's/Manager/Director/' employee.txt##把所有出现的Manager替换成Director
(2)sed '/Sales/s/Manager/Director/' employee.txt##包含Sales关键字的行做替换
```

**2. 全局标志(g flag)**

默认只会替换每一行第一次出现的，如果每一行所有出现都做替换使用`g` flag
 
```bash
(1)sed 's/a/A/' employee.txt##每一行第一次出现的a替换A
(2)sed 's/a/A/g' employee.txt##每一行出现做全局替换
```

**3. 数字标志(1,2,3..flag)**

指定第几个出现的做替换`1-512`

```bash
(1)sed 's/a/A/2' employee.txt ##每一行第二个出现a做替换
```
> `sed 's/a/A/2g'`  ##每一行的第二个到最后一个做替换

**4. 打印标志(p flag)**

打印替换成功的行，更多与`-n`组合使用

```bash
(1)sed -n 's/John/Johnny/w output.txt' employee.txt##把替换成功的行写入到output.txt文件
(2)sed -n 's/locate/find/2w output.txt' employee.txt##每行第二次出现的locate替换成find，并把成功的行写入到output.txt文件里。
```

**5. 写入标志(w flag)**

替换成功的行写入到一个文件里。

```bash
(1)sed -n 's/John/Johnny/w output.txt' employee.txt##把替换成功的行写入到output.txt文件
(2)sed -n 's/locate/find/2w output.txt' employee.txt##每行第二次出现的locate替换成find，并把成功的行写入到output.txt文件里。
```

**6. 忽略大小写(i flag)**

```bash
(1)sed 's/john/Johnny/' employee.txt##没有任何匹配可以替换，打印出所有行。
(2)sed 's/john/Johnny/i' employee.txt##忽略大小写，john替换为Johnny
```

**7. 执行标志(e flag)**

执行匹配替换之后任何shell命令

```bash
cat files.txt
/etc/passwd
/etc/group
(1)sed  's/^/ls -l /' files.txt## 在每一行开头加入 "ls -l "
(2)sed  's/^/ls -l /e' files.txt##执行每一行 ls -l 命令
```

以上标志只要有意义就可以组合在一起使用：

    sed -n 's/Manager/Directory/gipw output.txt' employee.txt##组合g,i,p,w四种flag
    
**8. sed替换定界符**

当`'s///'`模式中有`/`需要用`\`进行转义。

```bash
cat path.txt
reading /usr/local/bin directory

sed 's/\/usr\/local\/bin/\/usr\/bin/' path.txt##把/usr/local/bin替换成/usr/bin
```

这样容易造成混乱，可以使用其他符号代替`/`，例如这样的模式：s|||,s^^^,s@@@等等

```bash
sed 's|/usr/local/bin|/usr/bin|' path.txt
sed 's^/usr/local/bin^/usr/bin^' path.txt
sed 's@/usr/local/bin@/usr/bin@' path.txt
sed 's!/usr/local/bin!/usr/bin!' path.txt
```

**9. 多个替换命令影响同一行**

```bash
(1)sed '{
s/Developer/IT Manager/
s/Manager/Director/
}' employee.txt  ##把Developer替换为IT Manager然后再把Manager替换成Directory
```

**10. & (取得匹配的样式)**

经常用来字符替换，用来代替原来字符串或者正则表达式

```bash
(1)sed 's/^[0-9][0-9][0-9]/[&]/g' employee.txt##把所有的雇员id都用"[]"括起来。
(2)sed 's/^.*/<&>/' employee.txt##把整行用<>括起来
```

**11. 分组代替**

分组像一个正则表达式，一个组的格式像这样：`\(..\)`

```bash
(1)sed 's/\([^,]*\).*/\1/g' employee.txt
## 正则表达式\([^,]*\)匹配字符串到第一个逗号。
## \1 取代第一个匹配的组。
    
(2)sed 's/\([^:]*\).*/\1/' /etc/passwd ##显示第一个区域/etc/passwd的用户名
(3)echo "The Geek Stuff" | sed 's/\(\b[A-Z]\)/\(\1\)/g' ##每一个单词的第一个字母加入一个()
```
> 分组的范围是`1-9`

**12. sed execution**

sed作为解释程序:`#!/bin/sed -f`

```bash
cat mycript.sed

#!/bin/sed -f
s/\([^,]*\),\([^,]*\),\(.*\).*/\2,\1,\3/g 
s/^.*/<&>/  
s/Developer/IT Manager/ 
s/Manager/Director/

chmod u+x myscript.sed
./myscript.sed employee.txt
```

不管是使用解释器程序，还是使用sed -f sedfile的形式，文件中都可以出现#开头的注释

> 在sed解释程序里，可以使用sed -nf 但是不能调换成 -fn 会出现错误

#### 其他sed命令

**1. 加入新行(a command)行后**

    sed '[address] a the-line-to-append' input-file
    
```bash
(1)sed '2 a 203,Jack Johnson,Engineer' employee.txt##在第二行后加入一个新记录
(2)sed '$ a 106,Jack Johnson,Engineer' employee.txt##在尾行加入一个新记录
加入多行记录
(3)sed '/Jason/a \
203,Jack Johnson,Engineer\
204,Mark Smith,sales Engineer' employee.txt ##匹配Jason的行后加入2行记录

```

**2. 插入新行(i command)行前**

    sed '[address] i the-line-to-insert' input-file
    
```bash
(1)sed '2 i 203,Jack Johnson,Engineer' employee.txt ##在第二行前插入一个新记录
(2)sed '$ i 108,Jack Johnson,Engineer' employee.txt ##在行尾前插入一个新记录
也可以插入多行记录
(3)sed '/Jason/i \
203,Jack Johnson,Engineer\
204,Mark Smith,sales Engineer' employee.txt##匹配Jason的行前插入2个记录
```

**3. 改变行 (c command)**

    sed '[address] c the-line-to-insert' input-file
    
```bash
(1)sed '2 c 202,Jack Johnson,Engineer' employee.txt##删除第二行内容用新内容替换
(2)sed '/Raj/c\
203,Jack Johnson,Engineer\
204,Mark Smith Sales Engineer' employee.txt ##匹配Raj的行删除用新记录代替
```

**4. 变形(y command)**

```bash
(1)sed 'y/abcde/ABCDE/' employee.txt ##把a变换成A，b变换成B，c变换成C等等。
(2)sed 'y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/' employee.txt##小写字母全部变为大写
```

**5. 退出sed (q command)**

sed普通流程 读，执行，打印，重复 q命令可以终止执行命令

```bash
(1)sed 'q' employee.txt ##仅打印第一行内容，打印一行的内容就退出
(2)sed '5 q' employee.txt##在第5行后退出，所以打印前5行
(3)sed '/Manager/q' employee.txt##打印所有行直到关键字为Manager那行退出
```
> q 命令不能使用地址范围，只能用单一地址或者单一匹配，另外如果没有指定-n选项则q之前会打印模式空间的内容

**6. 从文件读入(r command)**

```bash
(1)sed '$ r log.txt' employee.txt##在行尾读入文件
(2)sed '/Raj/ r log.txt' employee.txt##在关键字Raj后插入文件
```

**7. (n command)打印模式空间内容并且从输入文件中读取下一行**

这可以改变sed流的执行，比如在`n`命令之前和之后都有其他命令，则执行`n`命令之后会导致现在处理的这一行不会应用`n`命令之后的命令，而下一行内容不会应用n命令之前的命令

> 如果指定`-n`选项的话，`n`命令也不会打印

还有其他的比如：`|`可以打印文本中的隐藏字符；`=`可以打印出行号

sed命令一些选项:

**`-n(--quiet,--slient)`** 禁止默认的打印  
**`-f(--file)`** 指定一个sed命令文件  
**`-e(--expression)`** 执行sed命令表达式  
**`-i(--in-place)`** 直接修改文件内容  
**`-c(--copy)`** 和`-i`选项连用 保持所有者权限  
**`-l(--line-length)`** 和`l`命令连用指定行长度   

#### sed多行处理，保留空间和循环

首先，应该明白模式空间的定义。模式空间就是读入行所在的缓存，sed对文本行进行的处理都是在这个缓存中进行的

在正常情况下，sed将待处理的行读入模式空间，脚本中的命令就一条接着一条的对该行进行处理，直到脚本执行完毕，然后该行被输出，模式空间清空；然后重复刚才的动作，文件中的新的一行被读入，直到文件处理完毕

但是，各种各样的原因，比如用户希望在某个条件下脚本中的某个命令被执行，或者希望模式空间得到保留以便下一次的处理，都有可能使得sed在处理文件的时候不按照正常的流程来进行。这个时候，sed设置了一些高级命令来满足用户的要求

总的来说，这些命令可以划分为以下三类：

1.  `N` `D` `P`：处理多行模式空间的问题
2.  `H` `h` `G` `g` `x`：将模式空间的内容放入存储空间以便接下来的编辑
3.  `:` `b` `t`：在脚本中实现分支与条件结构

##### 多行模式空间的处理

由于正则表达式是面向行的，因此，如若某个词组一部分位于某行的结尾，另外一部分又在下一行的开始，这个时候用`grep`等命令来处理就相当的困难。然而，借助于sed的多行命令`N`、`D`、`P`，却可以轻易地完成这个任务

**`N和n命令`**

多行`Next(N)`命令是相对于`next(n)`命令的，后者将模式空间中的内容输出，然后把下一行读入模式空间，脚本从当前的n 命令之后开始执行；而前者则保存原来模式空间中的内容，再把新的一行读入，两者之间依靠一个换行符"\n"来分隔。在N命令执行后，和`n`一样，控制流将继续用`N`命令以后的命令对模式空间进行处理

> 注意：在多行模式中，特殊字符"^"和"$"匹配的是模式空间的最开始与最末尾，而不是内嵌"\n"的开始与末尾。

```bash
cat expl.1
Consult Section 3.1 in the Owner and Operator
Guide for a description of the tape drives
available on your system

现在要将"Owner and Operator Guide"替换为"Installation Guide"

sed '/Operator$/{N;s/Owner and Operator\nGuide/Installation Guide }' expl.1
```

在上面的例子中要注意的是，行与行之间存在内嵌的换行符；另外在用于替代的内容中要插入换行符的话，要用如上的`\`的转义

```bash
cat expl.2
Consult Section 3.1 in the Owner and Operator
Guide for a description of the tape drives
available on your system.
Look in the Owner and Operator Guide shipped with your system.
Two manuals are provided including the Owner and
Operator Guide and the User Guide.
The Owner and Operator Guide is shipped with your system.

sed 's/Owner and Operator Guide/Installation Guide/;/Owner/{N;s/*\n/ /;s/Owner and Operator Guide */Installation Guide\/}' expl.2

结果得到：
Consult Section 3.1 in the Installation Guide
for a description of the tape drives
available on your system.
Look in the Installation Guide shipped with your system.
Two manuals are provided including the Installation Guide
and the User Guide.
The Installation Guide is shipped with your system.
```

看上去sed命令中作了两次替换是多余的。实际上，如果去掉第一次替换，再运行脚本，就会发现输出存在两个问题。一个是结果中最后一行不会被替换(在某些版本的sed中甚至不会被输出)。这是因为最后一行匹配了`Owner`,执行`N`命令，但是已经到了文件末尾，某些版本就会直接打印这行再退出，而另外一些版本则是不作出打印立即退出。对于这个问题可以通过命令 **`$!N`** 来解决。这表示`N`命令对最后一行不起作用。另外一个问题是"look manuals"一段被拆为两行，而且与下一段的空行被删除了。这是因为内嵌的换行符被替换的结果。因此，sed中做两次替换一点也不是多余的。

**`d和D命令`**

命令`d`作用是删除模式空间的内容，然后读入新的行，sed脚本从头再次开始执行。而命令`D`的不同之处在于它删除的是直到第一个内嵌换行符为止的模式空间的一部分，但是不会读入新的行，脚本将回到开始对剩下内容进行处理

**`p和P命令`**

sed对文件中每一行(不管处理与否)的默认动作是将其输出，如果加上选项`-n`，则输出动作会被抑制，这时还希望输出就需要打印命令。`P`命令打印的是模式空间中直到第一个内嵌换行符为止的一部分；`p`命令则打印整个模式空间中的内容

`P`命令通常出现在`N`命令之后`D`命令之前，由此构成一个输入输出循环。在这种情况下，模式空间中始终存在两行文本，而输出始终是一行文本。使用这种循环的目的在于输出模式空间中的第一行，然后脚本回到起始处，再对空间中的第二行进行处理。设想一下，如果没有这个循环，当脚本执行完备，模式空间中的内容都会被输出，可能就不符合使用者的要求或者降低了程序执行的效率

##### 对行进行存储

前面已经解释了模式空间的定义，而在sed中还有一个缓存叫作存储空间。在模式空间和存储空间中的内容可以通过一组命令互相拷贝

`Hold h`或`H` 将模式空间的内容拷贝或附加到存储空间  
`Get g`或`G` 将存储空间的内容拷贝或附加到模式空间  
`Exchange x` 交换模式空间和存储空间中的内容

命令的大小写的区别在于大写的命令是将源空间的内容附加到目标空间，而小写的命令则是用源空间的内容覆盖目标空间。值得注意的是，不管是`Hold`命令还是`Get`命令，**都会在目的空间的原有内容之后加上一个换行符**，然后才把源空间中的内容加到换行符的后面

```bash
cat expl.7
1
2
11
22
111
222

我们要做的工作就是将第一行与第二行，第三行与第四行，第五行与第六行互换

sed '/1/{h;d;};/2/{G}' expl.7
```

这个过程是这样的：首先，sed将第一行读入模式空间，然后`h`命令将其放入存储空间保存起来，一个`d`命令又把模式空间中的内容清空；接着sed把第二行读入模式空间，然后`G`命令把存储空间中的内容附加到模式空间(注意的是在模式空间的原内容末尾是加了一个换行符的)

使用`H`或`h`命令的时候，比较常见的是在这个命令之后加上`d`命令，这样一来，sed脚本不会到达最后，因而模式空间中的内容也就不会输出了。另外，如果把`d`换作`n`，或者把`G`换作`g`，都不会达到目的的

> 在正则的`[]`中，仅仅只有`\`是有特殊含意的，言下之意就是`*`、`.`都是理解为字面意思，要使他们具有特殊意义就必须使用`\`的转义了

##### 流程控制命令

为了使使用者在书写sed脚本的时候真正的自由，sed还允许在脚本中用`:`设置记号，然后用`b`和`t`命令进行流程控制

**`标签`** 的格式：

    :[lablename]
    
这个标签放置在你希望流程所开始的地方，以冒号开始。冒号与标签之间不允许有空格或者制表符，标签最后如果有空格的话，也会被认为是标签的一部分

**`b命令`**  它的格式：

    [address]b[lablename]
    
它的含意是，如果满足`address`，则sed流程跟随标签跳转至`labelname`处执行；如果这个标签不存在或者未指定`labelname`，则控制流程就直接跳到脚本的末尾

在某些情况下，`b`命令和`!`命令有些相似，但是`!`命令只能对紧挨它的`{}`中的内容起作用，而`b`命令则给予使用者足够的自由在sed脚本中选择哪些命令应该被执行，哪些命令不应该被执行

**`t命令`** 的格式：

    s/pattern/replacement/;t[lablename]
    
它表示的是如果前面的`s`命令替换成功的话，sed脚本就会根据`t`命令指示的标签进行流程转移。而标签的规则和上面讲的`b`命令的规则是一样的
