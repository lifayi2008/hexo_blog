---
title: 《JavaScript高级程序设计》读书笔记
date: 2016-10-18 16:22:08
tags: JavaScript
categories: JavaScript
---
#### JavaScript简介

一个完整的JavaScript实现包含下面三个组成部分：

    *   核心（ECMAScript）
    *   文档对象模型（DOM）
    *   浏览器对象模型（BOM）
<!-- more -->
`ECMAScript`与特定的浏览器没有关系，甚至没有包含输入和输出，它只是定义了语言的基础部分：`语法` `类型` `语句` `关键字` `保留字` `操作符` `对象`

`JavaScript`实现了`ECMAScript`；其他的比如：`Adobe ActionScript`也实现了`ECMAScript`

`文档对象模型（DOM）`是浏览器提供的用于操作`XML/HTML`文档的接口，通过这个接口用户在无需重新加载页面的情况下就可以改变浏览器显示的页面的元素

`浏览器对象模型（BOM）`是浏览器提供的用于操作浏览器本身行为和表现的接口

#### 在HTML中使用JavaScript

`<script>`元素中的常用属性：`src` `type`  `async`  `defer`

`src`表示包含要执行的代码的外部文件（如果直接在页面中写js脚本则不需要指定这个属性值）

`type`表示编写代码使用的脚本语言的内容类型（MIME类型），这个值通常设置为`text/javascript`

`async`表示文档和js文件的加载是异步的，只对外部脚本有效；两个异步的脚本不能有依赖关系，因为这两个脚本的执行顺序不确定；异步脚本一定会在页面`load`事件前执行，但可能会在`DOMContentLoaded`事件触发之前或者之后执行

`defer`表示脚本可以延迟到文档加载完毕之后再执行，只对为外部脚本有效

    <script type="text/javascript" src="example.js"></script>
    注意后面的</script>是必须的
    
可以使用`<noscript>`元素来让不支持或者禁用JavaScript的浏览器显示一条信息

#### 基本概念

`ECMAScript`中的一切都区分大小写（变量、函数名、操作符）

`ECMAScript`中个的标识符规则类似于`JAVA`：第一个字符必须是`字母` `_` `$`，其他的字符可以是`字母` `数字` `_` `$`；标识符中也可以包含扩展的`ASCII`和`Unicode`中的**字母字符**；按照惯例`ECMAScript`标识符也采用驼峰式的命名方法

`ECMAScript`中的注释规则同`JAVA`：`//` `/* */`

`ECMAScript5`引入了严格模式，这中模式在某些情况下会报错而不是忽略错误的代码，可以在整个脚本或者函数的第一行加上`use strict`来启用脚本或者函数的严格模式

`ECMAScript5`的语句以一个分号结尾，不适用分号在大多数情况下也是允许的，但是你应该总是使用分号；条件控制语句应该总是使用`{}`将后面的语句括起来，即使只有一条语句

`ECMAScript5`的变量是松散类型的，变量使用`var`关键字加变量名来定义；定义时未直接初始化的变量会被赋一个特殊的`undefined`**字面值**

> 使用`var`关键字定义的变量为当前作用域中的局部变量，在离开当前作用于后即被销毁，如果要创建一个全局的变量可以不加`var`关键字

> ECMAScrip不推荐在局部作用域中定义全局变量

可以使用一条语句定义多个变量，中间使用`,`分隔

`ECMAScript`中有五种简单的数据类型：`Undefined` `Null` `Boolean` `Number` `String`；还有一种复杂数据类型：`Object`，`Object`本质上是由一组无序键值对组成。`ECMAScript`不支持自定义类型，所有类型都是由以上几种类型组成

可以使用`typeof`来检查一种变量的类型，它可以返回以下字符串之一：`"undefined"` `"boolean"` `"string"` `"number"` `"object"`(如果是一个对象或者`null`) `"function"`

> `typeof`是一个操作数，因此可以使用`typeof(var)`，也可以使用`typeof var`的形式

##### `undefined`和`Null`类型

`undefined`类型只有一个值`undefined`，通常用来表示未定义的变量。在对变量进行操作之前，变量必须是已经声明或者定义的；对未经声明的变量只能执行一种操作：使用`typeof`取得类型--返回`"undefined"`字符串（和未定义的变量一样）

`Null`类型也只有一个值`null`；使用`typeof`对`null`字面值进行检查时返回`"Object"`字符串。如果定义一个变量将来用于保存对象，则最好将这个变量初始化为`null`，这样以来后面只要检查这个值是否等于`null`就知道它是否已经保存了对象：`if(var == null)`

##### `Boolean`类型

`Boolean`有两个值`true` `false`（注意大小写）；布尔值和数字值不同，因此`true`不一定等于1，`false`也不一定等于0；`ECMAScript`中其他类型都有和`Boolean`等价的值：

    Boolean     true            false
    String      任何非空字符串  ""（空字符串）
    Number      任何非0数值     0和NaN
    Object      任何对象        null
    Undefined                   undefined
    
> 可以使用`Boolean()`函数将其他类型转换为对应的`Boolean`类型；在`Boolean`上下文中，解释器会自动进行转换

##### `Number`类型

`ECMAScript`可以使用如下的整数数值字面量来给数值型变量赋值：`整数` `以0开始的八进制数值` `以0x开始的十六进制数值`。在进行运算时八进制和十六进制数值都转换为十进制数值进行算术运算。

> 八进制数值在严格模式下是无效的，会导致JavaScript引擎抛出错误

浮点数值就是该数值中包含一个小数点，并且小数点后面至少有一位数字。浮点数值所占的空间是整数数值的两倍，所以解释器会将一些浮点数转换为整数，比如：`1.` `10.0`；浮点数也可以使用科学记数法表示

`ECMAScript`能保存的最大和最小数值分别是：`Number.MAX_VALUE` `Number.MIN_VALUE`；如果计算结果超出这个数值范围，则结果是`Infinity`，这个值不能再参与下一次运算了

> 可以使用`isFinite()`函数来测试一个数值是否是可计算的

`NaN`字面量是一个特殊的数值，表示一个本来应该返回数值的表达式未返回数值的情况。`NaN`与任何值都不相等，并且所有对`NaN`的操作都返回`NaN`

> 可以使用`isNaN()`函数来测试一个表达式是否是`NaN`，只有在参数的结果是一个非`NaN`的数值或者能转换为数值时结果才为真

有三个函数用来把其他类型转换为数值类型：`Number()` `parseInt()` `parseFloat()`

*   `Number()`函数的转换规则请参考`《JavaScript高级程序设计》`p30
*   `parseInt()`会忽略前面的空格直到找到第一个非空字符，并检查看是否是能识别的格式（比如：`0x` `0`）或者是一个数字，否则的话就返回`NaN`（空字符串返回`NaN`），然后继续检查第二个字符，直到解析完所有的字符或者遇到一个非数字字符

> 注意：`ECMAScript 5`不再能识别八进制格式数值，所以`"070"`会被识别为十进制的70

这个函数的第二个参数可以指定解析字符串时的基数，如果指定为16，则字符串中可以不以`0x`开始。`ECMAScript`建议在任何时候都应该指定基数

*   `parseFloat()`和`parseInt`类似，一个一个解析字符串中的字符直到不能识别的字符为止；字符串中的第一个小数点会识别为有效的小数点，但是第二个则被作为无效值；十六进制格式被转换为0；如果字符串中不包含小数点则被转换为整数

##### `String`类型

`String`类型有0个或者多个`Unicode`字符组成，使用单引号或者双引号引用（两种写法完全一样）

`ECMAScript`中可以使用一下字符串字面量：`\n` `\t` `\b` `\r` `\f` `\\` `\'` `\"` `\xnn` `\unnnn`

*   `\xnn` : 使用十六进制字符`nn`表示一个`ASCII`字符
*   `\unnnn` : 使用十六进制字符`nnnn`表示一个`Unicode`字符

任何字符串的长度都可以通过其`length`属性获取；如果字符串中包含双字节的字符则`length`可能获取到的长度不准确

和`JAVA`语言相同，`ECMAScript`中的字符串也是不可变的

除`null` `undefined`类型以外其他类型都可以调用`toString()`方法转换为字符串；`Number`类型在转换为字符串时可以指定一个参数表示基数

可以使用`String()`函数将任意类型转换为字符串，规则如下：

    如果值有`toString()`方法则调用这个方法
    如果值是 null 则返回 "null"
    如果值是 undefined 则返回 "undefined"

##### `Object`类型

`ECMAScript`中的对象就是一组数据和功能的集合。对象可以通过`new`关键字后跟要创建对象的*类型名称*来创建。创建`Object`类型的实例并且为其添加属性和方法就创建了*自定义的类型*：

    var o = new Object();

> 类型名称包括各种简单类型和`Object`类型

就像`JAVA`中的`Object`类型，`ECMAScript`中的`Object`类型是所有它的实例的基础；`Object`类型中存在的属性和方法同样也存在与更具体的对象中：

*   `constructor` 保存这创建当前对象的函数
*   `hasOwnProperty(propertyName)` 用于检查给定的属性在当前对象实例（不是实例原型）中是否存在；`propertyName`必须是字符串
*   `isPropertyOf(object)` 用于检查传入对象是否是传入对象的原型
*   `propertyIsEnumerable(propertyName)` 用于检查给定的属性是否可以使用`for-in`语句来枚举；`propertyName`必须是字符串
*   `toLocaleString() toString()`返回对象的字符串表示
*   `valueOf()` 返回对象的字符串、数值或者布尔值表示

> `ECMAScript-262`不负责定义宿主对象（浏览器中的BOM和DOM对象），因此宿主对象不一定会继承`Object`对象

##### 操作符

将操作符应用于对象时，解释器通常会调用对象的`toString()`或者`valueOf()`方法返回一个可操作的值

`ECMAScript`同样支持`JAVA`中的各种操作符；但是`ECMAScript`中的操作符可以应用于任意类型；非数值类型往往要先做各种各样的转换后在进行操作（详细的规则参考`《JavaScript高级程序设计》`p37）

`ECMAScript`中的位操作规则和`JAVA`一样

`ECMAScript`中支持`!`（逻辑非操作符） `&&`（逻辑与操作符） `||`（逻辑或操作符）和`JAVA`中不同的是操作数可以是任意类型，当两边或者任意一边操作数不是布尔值时，解释器会安装一定的规则进行计算，详细参考`《JavaScript高级程序设计》`p44

`ECMAScript`中的乘性操作符（`*` `/` `%`）和加性操作符（`+` `-`）和`JAVA`中不同的是操作数可以是任意类型，解释其会根据一定的规则将其转换为数值再进行计算

`ECMAScript`中包括如下关系操作符：`>` `>=` `<` `<=`；根据操作数的类型会按如下规则比较：1）如果两边都是数值则进行数值比较，2）如果两边都是字符串则进行字符编码比较，3）如果一个操作数是数值则将另外一个操作数也转换为数值，4）如果操作数是对象则先调用对象的`valueOf()`方法，使用返回值按上面的规则进行比较，如果没有这个方法则调用`toString()`方法

`ECMAScript`中包括`==` `!=`相等性操作符，具体比较规则参考`《JavaScript高级程序设计》`p51；另外`ECMAScript`中还有一个全等操作符`===`，它只有在两边数据类型相同并且值相等的情况下才返回`true`

`ECMAScript`中还支持如下形式的三元操作符:
    
    variable = boolean_expression ? true_value : false_value
    
表示如果`boolean_expression`结果为`true`则使用`true_value`为`variable`赋值，否则使用`false_value`为`variable`赋值

`ECMAScript`中支持如下赋值操作符：`=` `+=` `-=` `*=` `/=` `%=` `<<=` `>>=` `>>>=`

`ECMAScript`中的`,`操作符有两种使用方法：1）在定义变量是可以分隔多个变量定义；2）使用`(2,0)`这中形式的表达式会返回最后一个值0，这在大多数情况下没有什么意义

##### 语句

`ECMAScript`中的语句类似与`JAVA`中的语句，但是有些地方不同：

*   `if`语句的条件表达式结果可以是任意类型，解释器会调用`boolean()`函数将这个值转换为`Boolean`类型

> 各种语句中的执行语句块应该总是使用`{}`代码块，即使只有一条语句

*   `while` `do-while`语句也和`if`一样，条件表达式的结果可以是任意类型
*   除了传统的`for`语句之外，`ECMAScript`还支持`for-in`语句用来枚举对象的属性：
```javascript
for(var property in expression) statement
```

> 同`for`循环，`var`关键字在这里不是必须的，但是你应该总是使用这个关键字，这样可以创建一个局部变量

*   和`JAVA`中一样，`ECMAScript`也支持`label`语句，并且可以在`break` `continue`语句后面加上之前定义的关键字来直接跳到这个地方执行

> 同`JAVA` `label`语句最好放在循环之前，然后在多层循环中可以直接跳到这里执行

`ECMAScript`支持`with`语句，但是因为可能会降低性能，所以不推荐使用

`ECMAScript`也支持`switch-case`语句：
```javascript
switch(expression) {
    case value: statement
    break;
    case value: statement
    break;
    default: statement
}
```
和`JAVA`中不同的是`case`可以是任意类型，并且也可以是变量、表达式

> `expression`在和各个`case`比较的时候使用的是全等操作符`====`所以不会进行类型转换

##### 函数

`ECMAScript`使用关键字`function`来声明函数：
```javascript
function functionName(arg0, arg1, ....) {
    statements
}
```
`ECMAScript`中的函数不用声明返回值，但是可以在任意时候使用`return`关键字后面加上一个表达式来返回一个值；也可以在`return`关键字后面不加表达式，这样返回值是`undefined`字面量值，并且函数从这里结束

我们可以在任意情况下给函数传0个或者多个参数，这不受函数声明时指定的参数数量的限制。我们可以在函数内部使用一个对象`arguments`来获取所有的参数，这个对象的`length`属性保存了实际传递的参数的个数；`arguments`对象中的元素是可读写的

> 虽然访问函数的参数的方法是：`arguments[0]` `arguments[1]` `...`，但是`arguments`并不是一个数组

虽然可以通过`arguments`对象获取所有参数，但是函数声明时的命名参数也是有用的，我们也可以通过命名参数访问对应的实参

> 注意：命名参数和保存在`arguments`对象中的参数是同步的，但是并不是同一个对象；而且当声明了两个命名参数，我们调用函数时只传递一个的话，另外一个被赋`undefined`值

> `ECMAScript`中没有函数重载，如果定义了两个名称相同，参数不同的函数，则实际只有第二个是有效的

#### 变量、作用域和内存问题

##### 基本类型和引用类型

`ECMAScript`中变量的值可以是基本类型（基本数据类型）和引用类型（对象）；基本类型是指简单的数据段，而引用类型是指那些可能由多个值构成的对象

创建一个基本类型或者引用类型的变量的方式是相同的，但是当相应的类型赋值到这个变量之后，我们可以对这个变量名的操作就不同了。对于引用类型我们可以动态的给它添加属性和方法，也可以改变和删除其属性和方法；但是我们不能给基本类型添加属性和方法

`ECMAScript`中基本类型和引用类型的复制区别和`JAVA`一样，即：基本类型复制值，引用类型复制地址（引用）；不同的是`ECMAScript`中的`String`是基本类型，`JAVA`中的字符串是一个对象；但是他们表现是一样的

> 注意：变量的访问有按值访问和按引用访问的区别；但是复制时只按值访问（参数传递同复制）

`typeof`操作符用来确定一个变量的类型（通过返回一个表示类型的字符串）：`"string"` `"number"` `"boolean"` `"object"` `"undefined"` `"function"`

`instanceof`操作符用来确定一个对象是什么类型的对象：
```javascript
result = variable instanceof constructor
```

> 因为所有引用类型的值都是`Object`的实例，所以在检测一个引用类型和`Object`构造函数时，始终返回`true`；如果检测一个基本类型的变量，则始终返回`false`

##### 执行环境及作用域

执行环境定义了变量或者函数有权访问的其他数据，决定了他们各自的行为。每个执行环境都有一个与之相关联的变量对象，环境中定义的所有变量和函数都保存在这个对象中。代码无法访问这个对象，但解析器在处理数据时会在后台访问它

某个执行环境中所有代码执行完毕后，该环境即被销毁，保存在其中的所有变量和函数

在WEB浏览器中全局执行环境是`window`对象，因此所有全局变量和函数都是作为`window`对象的方法和属性创建的；这个全局执行环境直到所有代码执行完毕或者浏览器关闭时才会被销毁

当代码在一个环境中执行时会创建变量对象的一个作用域链。作用域链的用途是保证对执行环境有权访问的所有变量和函数的有序访问。作用域链的前端，始终都是当前执行代码所在的环境的变量对象。如果这个环境是一个函数，则将其*活动对象*作为变量对象。活动对象在一开始时只包含一个变量：`arguments`对象（这个对象在全局中是不存在的）。作用域链中的下一个变量对象来自包含（包含当前环境）环境，这样一直延续到全局环境。全局环境的变量对象始终是作用域链中的最后一个对象

标识符解析是沿着作用域链一级一级搜索标识符的过程：从前端开始，直到找到标识符为止；如果找不到通常会导致错误发生

`JavaScript`中有两种方法可以延长作用域链（在作用域链的前端临时增加一个变量对象）：1）`with`语句会将指定的对象添加到作用域链中；2）`try-catch`语句会创建一个新的变量对象，其中包含的是被抛出的错误对象的声明

`JavaScript`中*没有块级作用域* ：在代码块中（`if` `while` `for`等，不包含函数）定义的变量会被添加到当前执行环境，在代码块结束后这变量也存在；但是在其他语言，比如`C` `C++` `JAVA`中，任何代码块都是一个作用域

> `for`循环的`()`中创建的变量也被添加到当前执行环境中

#### 引用类型

##### Object类型

尽管`ECMAScript`是一门面向对象的语言，但是它不具备传统的面向对象的类和接口的概念。`ECMAScript`中的引用类型被称为*对象定义*，它描述的是一类对象所具有的属性和方法

> 当使用`new Object()`创建一个实例时，我们并不说`Object`是一个类，而是一个*引用类型*，同时也说`Object()`是这个实例的构造函数

除了使用构造函数创建一个引用类型的实例之外还可以使用*对象字面量*的创建和表示方法：
```javascript
var person = {
    name : "test",
    age : 29
}

var person = {}     //这个等价与new Object()
```

`ECMAScript`推荐使用对象字面量的方法创建引用类型实例；这种表示对象的方法在向函数传递大量可选参数时也是常用的方法

> 推荐函数的必选参数使用命名参数的方式，而可选参数则封装到一个对象中传递

`ECMAScript`中访问对象的属性一般使用`.`；但是也可以使用`[""]`的方法来访问；后者在指定属性名时可以使用变量，并且可以包含空格等其他字符；除非必须要使用后面一种类型来访问属性，通常总是应该使用`.`

##### Array类型

`Array`在`ECMAScript`中是一种引用类型，数组中可以同时保存任意类型的值，并且数组的长度可以自动扩展，创建数组可以使用两种方法：

*   **`构造函数的方法`**：
```javascript
var colors = new Array();
var colors = new Array(20);
var colors = new Array("red", "blue");
//如果给构造函数传递的是一个数值则会创建一个大小为数值的数组
//如果给构造函数传递的是其他类型或者逗号分隔的多个数值则会创建一个包含这些值的数组
```

*   **`数组字面量的方法`**：
```javascript
var colors = ["red", "blue"];
var colors = [];
var colors = [1,2,] //不要使用这样有歧义的创建方法
var colors = [,,,]  //不要使用这样有歧义的创建方法
```

`ECMAScript`的数组是比较松散类型的数组，可以直接给数组的任意元素赋值，如果赋值的元素不是紧挨着当前数组索引最大的元素，则中间的元素都被赋`undefined`的值，数组的长度属性`length`也会变化反应当前数组的元素个数；另外`length`属性是可写的，可以直接改变这个熟悉来增加或者减少元素的个数

`ECMAScript 3`中要检测一个变量是不是一个数组引用可以使用`value instanceof Array`的表达式形式，但是这个只能检查位于同一个全局执行环境的数组；`ECMAScript 5`中使用`Array.isArray()`的方法来做检查可以避免上述问题

调用数组的`toString()` `valueOf()` `toLocaleString()`方法时，解释器会自动调用数组中的每一个元素的对应方法，将结果使用`,`分隔组成一个字符串返回；可以使用数组的`join()`方法，在参数中指定一个分隔符

> 如果数组中的某一个元素是`null` `undefined`则拼接字符串时为空字符串

`ECMAScript 3`数组提供了`push()`和`pop()`方法可以将这个数组当成是一个栈来操作，`push()`可以接收任意数量的参数，这些数值被一次添加到栈的末尾

`ECMAScript 3`同样提供了另外一个可以将数组当作队列的方法`unshift()`；这方法可以接收任意数量的参数，这写数值被入队；同时仍可以使用`pop()`方法在数组（队列）的另外一段出队元素

`reverse()`和`sort()`两个方法可以将数组元素重排列；前者将数组元素进行反序，后者调用每一个元素的`toString()`方法后按照字符编码进行排序；`sort()`方法可以接收一个比较函数作为参数来指定排序方法，这个函数有两个参数：如果第一个参数应该未于第一个参数之前则返回一个负数，否则返回一个正数

> 这两个方法都改变了原引用的数组元素的顺序，同时也返回重排列后的数组

**`concat()`** 方法可以将作为参数的元素添加到当前数组的后面返回一个新的数组  
**`slice()`** 方法接收两个指定位置的参数（分别表示开始的位置，和结束的下一个位置）来从数组中截取元素返回一个新数组  

> `slice()`的两个参数都可以是负值，表示使用数组长度加上这个值来取元素，任何不合理的范围或者数值都会返回一个空数组

**`splice()`** 方法可以用来删除、插入、替换数组中的元素：
```javascript
使用两个参数时表示删除，第一个参数表示开始的位置，第二个参数表示删除的元素个数
使用三个以上参数时可以作为插入，第一个参数表示插入的位置，第二个参数指定为0，第三个参数以后的参数表示插入的元素
使用三个以上参数时可以作为替换，第一个参数表示替换开始的位置，第二个参数指定一个数值可以删除任意数量的元素，第三个以后的参数表示要重这里开始插入的元素
```
> 注意：`concat()` `slice()`都不改变原数组，使用返回值表示新数组；而`splice()`则改变原数组，返回值表示删除的元素组成的数组

**`indexOf() lastIndexOf()`** 方法可以开始或者结尾查找数组元素并返回找到的元素的位置（位置都是从第0个元素开始的位置），比较时候会使用全等来比较；如果没有找到元素则返回`-1`

**`every() filter() forEach() map() some()`**  
这五个方法是`ECMAScript 5`为数组定义的迭代方法。每个方法都接受两个参数：第一个参数表示在每一个元素上运行的函数，第二个参数表示运行该函数的作用域对象（影响this的值）；这函数会接收到解释器传来的三个参数：当前操作的数组元素的值、数组元素的索引和数组名，函数的返回值根据使用方法的不同可能会影响方法的返回值：

*   `every()` 对数组中的每一项运行给定函数，如果函数调用每一次都返回`true`，则返回`true`
*   `some()` 对数组中的每一项运行给定函数，如果函数调用的某一次返回`true`，则返回`true`
*   `filter()` 对数组中的每一项运行给定函数，返回那些调用函数返回`true`的元素组成的数组
*   `map()` 对数组中的每一项运行给定函数，返回每一次函数调用的返回值组成的数组
*   `forEach()` 对数组中的每一项运行给定函数，没有返回值

**`reduce() reduceRight()`**  
这两个方法是`ECMAScript 5`为数组定义的归并方法。每个方法接受两个参数：在每一个元素上调用的函数和作为归并基础的初始值；函数要接收四个参数：前一次函数调用的返回值、当前元素、当前元素的索引和数组；第一次迭代发生在第二个元素，因此第一次函数调用第一个参数是数组的第一个元素

##### Date类型

`ECMAScript`的`Date`类型是在早期的JAVA的`java.util.Date`类型基础上构建，使用自`UTC 1970/1/1 00:00:00`以来的毫秒数保存日期。`ECMAScript`中的`Data`类型可以保存`epoch`前后的285616年

创建一个`Date`类型可以使用：
```javascript
var now = new Date();
//不传参数的情况下，会自动获取当前日期时间，创建一个Data类型对象；也可以传递一个数值表示的毫秒数来创建一个基于那个时间的对象
```
> 也可以给`Date()`构造函数传递一个下面指定的时间格式字符串，解释器会在内部调用`Date.parse()`

`Data.parse()`方法可以接受一个表示日期的字符串参数，然后返回相应的毫秒数；字符串参数可以是如下的形式：
```javascript
"月/日/年" 如： 6/13/2014
"英文月份 日,年" 如：January 12,2004
"英文星期 英文月份 日 年 时:分:秒 时区" 如：Tue May 25 2004 00:00:00 GMT-0700
ISO 8601扩展格式 YYYY-MM-DDTHH:mm:ss.sssZ 如：2004-05-25T00:00:00   只有ECMAScrip 5支持这种格式
```
`Date.UTC()`方法同样返回表示日期的毫秒数，但是参数不同，参数分别为：年份、基于0的月份、月中的哪一天（1~31）、基于0的小时数、分钟、秒、毫秒；只有前两个参数是必须的，其他参数如果没有提供都假设是可取的最小值

`ECMAScript 5`添加了另外一个方法`Date.now()`，这个方法返回方法调用时距`epoch`时间的毫秒数；这个方法可以用来分析一段代码的执行时间

`Date`重写了`toString() toLocalString() valueOf()`方法，前两个方法返回对象转换为日期字符串的形式（toString()会带有时区信息），这两个方法返回的形式在不同的浏览器有很大的差别所以一般只在调试时使用；`valueOf()`返回日期对象的时间戳数值，所以可以对时间进行比较

`ECMAScript`提供了如下日期时间格式化方法：
```javascript
toDateString()      //以特定于实现的格式显示星期、月、日、年
toTimeString()      //以特定于实现的格式显示时、分、秒、时区
toLocaleDateString()        //根据地区显示信息
toLocaleTimeString()        //根据地区显示信息
toUTCString()       //以特定于实现的格式显示完整的UTC日期
```
> 上面的方法显示格式根据实现也有不同程度的区别

`Date`对象还有一些其他的方法用于直接获取日期时间中的某一个部分，详细参考《Javascrip高级程序设计》 p102或者Javascript手册

##### RegExp类型

`ECMAScript`通过`RegExp`来支持正则表达式，可以使用下面正则表达式字面量的语法来创建一个正则表达式：
```javascrip
var expression = /pattern/flag
```
`pattern`可以是任意的简单或者复杂的正则表达式，可以包括字符类、限定符、分组、向前查找、反向引用；`flag`可以是下面三种：

*   `g` 表示全局标志，即模式将被应用于所有字符串
*   `i` 表示忽略大小写
*   `m` 表示多行模式，即模式匹配的对象不再局限于一行内容

正则中的元字符包括：`(` `[` `{` `\` `^` `$` `|` `)` `?` `*` `+` `.` `]` `}`；如果模式中要匹配这些字符必须使用`\`转义

除了可以使用字面量的语法之外，还可以使用`RegExp()`构造函数来创建：
```javascript
var parttern = new RegExp("[bc]at", "i");
//第一个参数表示正则模式，第二参数表示标志
```

这两种方法的**模式**写法有所不同：后一种模式中的转义字符`\`要写两遍`\\`才表示转义，否则会被当作字符串中的转义字符

在`ECMAScript 3`中，字面量方法创建的正则表达式共享一个`RegExp`的实例，而使用构造函数创建时每一次构造函数调用都是一个新的实例

在`ECMAScript 5`中，两种方法创建的都是多个实例

`RegExp`实例有如下属性，可以用来获取有关模式的各种信息：

*   `global` : 布尔值，表示是否设置了`g`标志
*   `ignoreCase` ：布尔值，表示是否设置了`i`标志
*   `lastIndex` ：整数，表示开始下一次匹配的搜索位置
*   `multiline` ：布尔值，表示是否设置了`m`标志
*   `source` ：正则表达式的字符串表示，按照字面量的形式返回

这些方法在实际应用中用处不大

`RegExp`实例有如下方法：

**`exec()`**  
这个方法是专门为捕获组而设计的。这个方法接受一个要应用匹配的目标字符串，返回包含第一次匹配到的内容的信息数组，如果没有匹配到则返回`null`；虽然返回的数组是`Array`的实例但是包含两个特殊的属性：`index`表示匹配到的字串在目标字符串中的位置（从0开始），`input`表示目标字符串（即传递给`exec()`的参数）；数组的第一个元素是与整个正则匹配的字串，其他的元素依次是各个组匹配到的字串

`exec()`总是只获取一次匹配到正则模式的信息；但是如果正则模式设置`g`标志，则下次调用`exec()`时将从上一次匹配到的右边的位置继续进行搜索匹配，而如果未设置`g`标志则只能总是获取第一次匹配的信息

**`test()`**  
这个方法只是简单的对作为参数的目标字符串进行匹配查找，如果有匹配的内容则返回`true`，否则返回`false`

**`toLocalString() toString()`**  
这两个方法总是返回正则表达式模式的字面量形式

`RegExp`的构造函数`RegExp()`中包含一些（静态）属性，这些属性适用于作用域中的所有正则所共享，并且会根据最近一次正则操作的实例的信息而变化；这些属性都有两种访问方法（为了兼容性应该总是使用长格式）：

*   `input $_`：最近一次要匹配的目标字符串
*   `lastMatch $&`：最近一次匹配到的字串
*   `lastParen $+`：最近一次匹配到的捕获组
*   `leftContext $``：目标字符串中lastMatch字串之前的字串
*   `rightContext $'`：目标字符串中`lastMatch`字串之后的字串
*   `multiline $*`：布尔值，表示是否所有表达式都使用了多行模式
*   `$1....$9`：最近一次捕获组匹配到的内容

> `ECMAScript`目前还不支持`Perl`中的一些高级的正则特性：\A\Z锚、向后查找、并集与交集类、原子组、Unicode支持、命名捕获组、单行和无间隔模式、条件匹配等

##### Function类型

`ECMAScript`中的函数实际是对象，每一个函数都是`Function`的实例，同其他引用类型一样函数也有属性和方法；函数名也是指向某一个对象的指针，不会与某一个函数绑定；函数的定义可以使用下面两种语法：

```javascript
function sum(argu1, argu2) {
    return argu1 + argu2;
}

var sum = function(argu1, argu2) {
    return argu1 + argu2;
}
```

也可以使用另外一种不推荐的语法：`var sum = new Function("argu1", "argu2", "return argu1 + argu2");`

因为函数名实际上是指向函数对象的指针，所以可以将一个函数名赋值给另外一个变量，这两个变量都指向同一个函数对象；另外这也是`JavaScript`函数不能重载的一个原因

> 注意：函数声明和函数表达式的区别是：在任何位置声明的函数在js代码开始执行之前就已经存在；而使用表达式方法创建的函数直到执行到这段代码时才存在

因为函数是一个指针可以相互赋值，所以可以将函数当作函数的参数或者返回值

在函数内部有两个特殊的对象：

*   `arguments`：除了保存了函数的参数之外，这个对象还有一个`callee`属性，这个属性是一个指针，指向拥有这个`arguments`对象的函数

*   `this`：这个和JAVA中的`this`类似，指向当前函数执行所在的环境对象

> `ECMAScript`中的函数是一个指针，因此可以将函数赋值给不同作用域的变量，但这样也只是执行环境不同而已，实际执行的是同一段函数代码

函数的属性和方法：

*   `caller` : 这个属性只在`ECMAScript 5`中有效；这个属性保存着调用当前函数的函数的引用

*   `length` : 表示函数期望接收到的参数的个数

*   `prototype` ：保存了函数

*   `apply() call()`：这两个方法都是非继承而来的；用来在特定的作用域中调用这个函数，实际上等于设置函数体内`this`对象的值。`apply()`方法接受两个参数：一个是要运行函数的作用域，另外一个是表示参数的数组（这个参数可以是`Array`的实例，也可以是`arguments`对象）。`call()`方法同`apply()`，但是第二个参数及以后的参数直接传给当前函数

> 这两个方法的用途通常是扩充函数执行的作用域，可以给这两个函数的第一参数传不同的值，让这个函数的环境不同

> `JavaScript`中的作用于其实也就是一个键值对对象 ？？？

*   `bind()` ：`ECMAScript 5`中定义了这个方法，用来将一个函数绑定到一个作为参数的对象

*   `toString() toLocalString()` ：这两个继承的方法始终返回函数的代码，但是格式因实现而不同

##### 基本包装类型

`ECMAScript`提供了三个特殊的引用类型：`Boolean` `Number` `String`；每当读取一个基本类型的值时，后台就会创建了一个对应的基本包装类型对象，让我们可以调用对象的方法来操作这个数据；比如：我们将一个字符串字面量赋值给一个变量时，这个变量是一个基本类型，然后我们可以直接调用这个基本类型的方法，这时解释器会自动创建一个值相同的引用类型的变量，然后对这个引用类型调用指定的方法，将结果返回后再自动将这个引用类型销毁

引用类型与自动创建的基本包装类型最主要的区别是：引用类型直到你手动销毁这个对象，否则是一直存在的；而基本包装类型则只存在于你调用这个类型的方法或者访问/设置属性时存在，当这个句代码执行完成后就被销毁了，比如：
```javascript
var test = "test";
alert(test.name = "test");  //返回test

var test = "test";
test.name = "test1";    //创建一个包装类型，并且给这个包装类型的name属性赋值，这句代码执行完毕后，这个基本包装类型即被销毁
alert(test.name);   //返回undefined，因为这行代码又创建了另外一个包装类型，然后获取name属性的值，而这个属性值还未定义
```

也可以调用相应的构造方法`Boolean() String() Number()`来显式的创建一个基本包装类型，使用`typeof`操作符测试时这些类型的变量会返回`Object`表明这是一个引用类型

> 注意这些构造函数要和类型转换函数区别开来

> 注意在一个布尔值上下文中，所有类型的基本包装类型都会被转换为`true`

也可以将对应的字面量值直接传递给`Object()`构造方法来创建一个对应类型的基本包装类型

**`Boolean类型`**  

`Boolean`类型的包装类型重写了`toString()`和`valueOf()`，这两个方法都会返回字符串`"true" "false"`

> 注意不要把`Boolean`基本包装类型放在布尔上下文中，因为他们总是返回`true`;建议在开发中永远不要使用这个基本包装类型

**`Number类型`**  

`Number`类型的`valueOf()`返回对象表示的基本类型的数值；`toString() toLocalString()`方法返回对象表示的基本类型数值字符串，可以给这两方法传递一个参数表示显示数值字符串时使用的进制

`toFixed()`方法返回保留指定的小数位数的数值字符串（0~20）；在进行小数位数截取时会进行四舍五入

`toExponential()`方法返回对象表示的数值的指数表示形式的字符串；这个方法也可以指定一个参数表示保留的小数位数

`toPrecision()`方法则会根据数值的大小返回小数或者指数形式的字符串

**`String类型`**  

`String`类型对象的方法也可以在**字符串字面值**中直接使用；`valueOf() toLocaleString() toString()`方法返回对象表示的字符串的字符串形式

`String`类型的每个实例都有一个`length`属性表示字符串中包含多少个字符

`String`类型提供了很多方法来完成对字符串的解析和操作：

```javascript
char()  //接受一个数值参数，返回这个位置的字符，位置从0开始
charCodeAt()    //接受一个数值参数，返回这个位置的字符的编码值，位置从0开始

concat()    //拼接字符串，在编程中通常使用+来进行字符串连接

slice()     //取字串操作，第一个参数指定开始的位置，第二个参数指定结束的位置，两个参数都可以识别负值的位置
substring()     //取字串操作，第一个参数指定开始的位置，可以识别负值；第二个参数指定结束的位置，负值被当作0
substr()    //取字串操作，第一个参数指定开始的位置，第二个参数指定字串长度，负值都被当作0

indexOf()
lastIndexOf()
//搜索给定字串的位置，然后返回数值；如果未找到则返回-1；这两个函数一个从前面开始一个从后面开始；可以指定第二个参数表示开始的位置，一个从前面计数，一个从后面计数

trim()  //删除前缀和后缀的空格

toLowerCase()
toLocalLowerCase()
toUpperCase()
toLocalUpperCase()
//进行大小写转换

以上返回字符串的方法都不会改变原字符串的值

match() //接受一个正则表达式字面量或者一个RegExp对象，返回一个数组，数组的第一个元素是与整个正则匹配的字串，第二个元素之后的元素是捕获组匹配到的字串
searche() //参数类型和match()相同，但是会返回匹配到的位置，如果未匹配到返回-1
replace()   //将匹配到的字串进行替换；第一个参数是一个字符串或者RegExp对象，字符串不会被当作正则解释；第二个参数如果是字符串则进行替换（要想替换所有的需要在RegExp中使用g标志），也可以在这个字符串中使用一些特殊的标记来引用正则匹配到的某些部分：$$表示$ $&表示正则匹配到的整个字串 $'表示匹配到的字串之前的字串 $`表是之后的字串 $nn表示匹配到的nn个捕获组
//第二个参数也可以是一个函数，在只有一个匹配项的情况下，会给这个函数传递单个参数：匹配到的字串、匹配到的字串的位置、原始字符串，如果正则中定义了多个捕获组则会在第一个参数之后插入捕获组匹配到的所有字串
split() //用于分割字符串，第一个参数可以是一个字符串或者RegExp对象为分隔符，第二个参数指定了返回数组的大小，如果分割后的字串数量大于数组大小则最后一个元素放置所有剩余字串

localeCompare() //根据参数字符串在字母表中的顺序是否在原字符串之前返回：1 0 -1

fromCharCode()  //这是String类型的静态方法，参数是一个或者多个编码后的字符，将他们转换为字符串
```

##### 单体内置对象

这些对象是由`ECMAScript`实现提供的、不依赖于宿主环境的对象，这些对象在程序执行之前已经存在了，并且不能在创建这些对象的实例

**`Global对象`**  

`Global`对象是`ECMAScript`中的一个”兜底对象“，任何不属于其他对象的属性和方法都是`Global`的对象，前文中介绍的那些全局的函数，实际上都是`Global`对象的方法；`Global`中还包含一些其他的方法：

`encodeURI() encodeURIComponent()`方法用于对URI进行编码，将一些URI中不能存在的特殊字符按UTF8进行编码转换；前者对整个URI进行编码，后者对部分进行编码；前者不会将URI中可以包含的一些特殊字符进行编码，而后者对所有的特殊字符进行编码

`decodeURI() decodeURIComponent()`方法对上面两种编码之后的文本进行解码，不能交叉使用

`eval()`方法将作为参数的字符串当作是`ECMAScript`代码进行解释执行；通过`eval()`方法执行的代码被认为是调用`eval()`环境的一部分，因此被执行的代码和当前执行环境有相同的作用域链

> 在严格模式下，在`eval()`中定义的变量不能在当前环境中访问到

`Global`对象还包括如下属性：
```javascript
undefined   //特殊值undefined
NaN         //特殊值NaN
Infinity    //特殊值Infinity

一下都是各种引用类型的构造函数，也被当作是Global对象的属性
Object
Array
Function
Boolean
String
Number
Date
RegExp
Error
EvalError
RangeError
ReferenceError
SyntaxError
TypeError
URIError
```

`ECMAScript`并没有指出如何直接访问`Global`对象，但Web浏览器都是将这个对象作为`window`对象实现的；因此所有全局的属性和方法都是`window`对象的属性和方法

> `JavaScript`中`window`对象除了表示`ECMAScript`中的`Global`对象之外，还承担了很多其他的任务

**`Math对象`**  

`ECMAScript`为进行数学计算提供了一个专门的单体对象`Math`，使用这个对象中提供的方法进行数学运算比使用操作符进行算术运算要快的多

`Math`中包含如下属性：
```javascript
Math.E  //自然对数的底数，即常量e表示的值
Math.LN10   //10的自然对数
Math.LN2    //2的自然对数
Math.LOG2E  //以2为底e的对数
Math.LOG10E     //以10为底e的对数
Math.PI     //π的值
Math.SQRT1_2    //1/2的平方根
Math.SQRT2  //2的平方根
```

`Math.max() Math.min()`方法求一组数中最大值和最小值，这两个方法都可以接受任意数量的参数，可以使用如下方法来使用一个数组作为参数：
```javascript
var values = [1,2,3,4,5,6,7];
var max = Math.max.apply(Math, values);
```

`Math.ceil() Math.floor() Math.round()`方法提供了对小数值舍入为整数的方法：第一个为向上舍入，第一个为向下舍入，第三个为标准四舍五入

`Math.random()`方法返回一个大于等于0小于1的一个随机数；可以使用下面的公式来生成一个指定开始值指定范围的整数随机数：

    value = Math.floor(Math.random() * 范围 + 开始值)
    
其他的方法：
```javascript
Math.abs(num)   //返回绝对值
Math.exp(num)   //返回Math.E的num次幂
Math.log(num    //返回num的自然对数
Math.pow(num,power) //返回num的power次幂
Math.sqrt(num)  //返回num的平方根
Math.acos(x)    //返回x的反余弦
Math.asin(x)    //返回x的反正弦
Math.atan(x)    //返回x的反正切
Math.atan2(y,x) //返回y/x的反正切
Math.cos(x)     //返回x的余弦值
Math.sin(x)     //返回x的正弦值
Math.tan(x)     //返回x的正切值
```

#### 面向对象的程序设计

`ECMAScript`中的对象是无序属性的集合，其属性可以包含基本值、对象、函数。每个对象都是基于一个引用类型创建的，这引用类型可以是原生的类型，也可以是自定义类型

> 注意：因为`ECMAScript`中函数也是一个引用类型，所以函数名称在对象中也可以称作是属性，下文中提到的属性如果未明确标明，都是属性和方法的统称

##### 理解对象

对象的创建可以使用全面章节介绍的方法：创建一个`Object`引用类型的变量，然后再这个变量上绑定各种属性（方法）；也可以首选的使用如下**对象字面量**的方法创建：

```javascript
var person = {
    name: "Nicholas",
    age: 29,
    job: "Software Engineer",
    
    sayName: function() {
        alert(this.name);
    }
}
```

`ECMAScript`的属性创建时会带有一些**特征值（characterristic）**，`ECMAScript`使用这些特征值来定义他们的行为

**`属性类型`**  

`ECMAScript`中对象有两种属性：数据属性和访问器属性

*   `数据属性` ：数据属性有四个描述其行为的特性：`[[Configurable]]`表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性；`[[Enumerable]]`表示能否通过`for-in`循环返回属性；`[[Writable]]`表示能否修改属性的值；`[[Value]]`表示属性的值；对于直接在对象上定义的属性，前三个的默认值是`true`，最后一个的默认值是`undefined`

`ECMAScript 5`提供了一个`Object.defineProperty()`方法来修改属性默认特性，这个方法接受三个参数：属性所在的对象、属性的名字（字符串）、一个描述符**对象**，描述符对象的属性必须是：`configurable` `enumerable` `writable` `value`，可以设置其中的一个或者几个来修改相应的属性

> 注意：如果将`configurable`属性配置为`false`，就不能再将它配置回`true`；这时就只能配置`writable`属性了

> 注意：在设置属性时如果不指定`configurable` `enumerable` `writable`三个值都会被设置为`false`

*   `访问器属性` ：访问器属性有四个特征：`[[Configurable]]`表示能否通过delete删除属性而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性；`[[Enumerable]]`表示能否通过`for-in`循环返回属性；`[[Get]]`在读取属性时调用的函数；`[[Set]]`在写入属性时调用的函数；对于直接在对象上定义的属性，前两个的默认值是`true`，后两个的默认值是`undefined`

访问器属性不能直接定义，必须使用`Object.defineProperty()`定义：

```javascript
var book = {
    _year : 2004,
    edition : 1
};
Object.defineProperty(book, "year", {
    get: function() {
        return this._year;
    },
    set: function(newValue) {
        if(newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
book.year = 2005;
alert(book.edition);
```

`book`对象的`_year`属性前面的`_`表示这个属性只能通过对象方法访问属性（实际可以直接访问，`JavaScript`解释器没做任何限制），`year`表示为对象定义的访问器属性名；`getter`和`setter`方法可以只指定其一表示相应的属性只读或者只写，在非严格模式下未被允许写的写操作会被忽略，读操作会返回`undefined`，严格模式下回抛出异常

`ECMAScript 5`之前可以使用非标准的对象的`__defineGetter__()`和`__definedSetter__()`方法创建访问器属性；不支持`Object.defineProperty()`方法的浏览器中不能修改访问器属性的前两个特征


可以使用`Object.defineProperties()`方法一次性定义多个属性，这个方法的第一个参数是要定义属性的对象，第二个参数也是一个对象，对象中的属性名和第一个参数的属性名一一对应，属性值表示要修改/定义的各种属性特性

可以使用`Object.getOwnPropertyDescriptor()`方法读取属性的特性，这个方法接受两个参数：属性所在的对象、要读取其描述符的属性名称；返回值是一个对象，如果读取的是一个访问器属性，则返回值对象的属性有：`configurable` `enumerable` `get` `set`，如果读取的是一个数据属性，则返回值对象的属性有：`configurable` `enumerable` `writable` `value`

在`Javascript`中可以针对任何对象（包括`BOM` `DOM`对象）调用`Object.getOwnPropertyDescriptor()`方法

##### 创建对象

**`工厂模式`**  

可以定义一个函数将创建对象的过程：定义一个对象、给对象绑定属性（方法）放置在这个函
数中，然后返回定义好的对象，这就是工厂模式：

```javascript
function createPerson(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    };
    return o;
}

var persion = createPerson("test", 29, "Software Engineer");
```

使用工厂模式的问题是创建的对象的类型无法被识别

**`构造函数模式`**  

`ECMAScript`中除了原生的构造函数之外，也可以创建自定义的构造函数来定义自定义对象的属性（方法）：

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        alert(this.name);
    };
}

var person = new Person("test", 29, "Software Engineer");
```

构造函数的特征：不需要显式的创建对象，直接将属性（方法）赋值给`this`，没有`return`语句；按照惯例构造函数的第一个字母大写

使用构造函数创建一个对象时解释器会执行4个步骤：创建一个对象、将构造函数的作用域赋值给这个对象（this指向这个对象）、执行构造函数中的代码、返回新对象

使用这个构造函数创建出来的对象都有一个`constructor`（构造函数）属性指向这个构造函数；这时使用`instanceof`操作符测试这个对象是否是`Object`和构造函数的实例时都返回`true`

构造函数也是一个函数，除了可以使用`new`关键字创建实例之外，还可以直接调用这个函数，但是在函数中定义的属性（方法）都会被添加到调用这个函数的作用域（对象）中了（在全局中是`windows`），当然也可以使用函数的`call()`方法来指定一个作用域：

```javascript
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName();
```

使用构造函数模式的一个问题是：使用同一个构造函数创建的实例中的方法虽然具有相同的名字，但是却不是同一个`Function`实例。这个问题的一个解决方法是将函数定义转移到构造函数的外部（全局作用域），然后再构造函数内部引用这个函数；但是这样又有新的问题：放在全局中的函数看似可以被所有对象实例调用，但实际上只能被特定对象调用时才有意义

**`原型模式`**  

`ECMAScript`中的每一个函数都有一个`prototype`（函数）属性，这个属性是一个指针，指向一个对象，这个对象的用途是包含由特定类型的所有实例共享的属性（方法）；将这个概念应用在构造函数中就是：构造函数的`prototype`属性所指向的一个对象保存着使用这个构造函数所创建的实例所共享的属性（方法）。所以我们不必在构造函数中定义对象实例的信息，而是将这些信息添加到对象原型中：

```javascript
function Person() {};
Person.prototype.name = "test";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person = new Person();
person.sayName();
```

> 使用这种方法创建的实例与构造函数模式不同的是：新对象的属性和方法由所有实例所共享

在`ECMAScript`中无论何时只要创建一个新的函数，就会根据一组规则为该函数创建一个`prototype`属性，这个属性指向函数的原型对象。在默认情况下，所有的原型对象都会自动获得一个`constructor`（构造函数）属性，这个属性包含一个指向`prototype`属性所在函数的指针（即拥有这个`prototype`的构造函数）。通过这个构造函数，我们还可以继续为原型对象添加其他属性（方法）

创建了自定义构造函数之后，其原型对象默认只会取得`constructor`属性，其他的属性都是从`Object`对象继承而来的。当调用构造函数创建一个新的实例后，该实例内部将包含一个指针（内部属性）指向构造函数的原型对象。`ECMA-262 `第五版将这个指针称作`[[Prototype]]`，标准中这个属性无法访问，但是很多浏览器提供了`__proto__`属性名来访问

> 通过上面的分析要注意的一点是：通过原型模式创建的对象实例与构造函数的原型对象之间有直接联系，而与构造函数之间没有直接联系

`ECMAScript 5`中提供了一个`Object.getPrototypeOf()`方法来取得一个对象实例的`[[Prototype]]`的值；也可以使用原型对象的`isPrototypeOf()`方法来确定一个对象实例的`[[Prototype]]`指向这个原型对象

当访问一个对象实例的属性或者方法时，首先在对象实例中查找，如果没有找到就访问对象实例的原型对象；如果我们在对象实例中添加一个和原型中同名的属性（方法），则原型中的方法即被屏蔽，因此，虽然可以访问原型中的值，但是却不能通过对象实例修改

> 要想重新访问原型中的属性（方法），则可以通过`delete`删除对象实例中的同名属性（方法）来实现

调用一个对象实例的`hasOwnProperty()`方法，将属性（方法）名作为**字符串**传入，如果返回`true`则表示这个属性（方法）存在于对象实例中；返回`false`则表示不存在或者存在于原型中

通过`in`操作符可以确定一个属性（方法）是否存在于对象实例或者原型中，格式为：`"name" in o`；这个操作符和上面的方法结合也能确定一个属性（方法）是否存在于原型中

使用`for-in`循环可以取出所有通过对象可访问、可枚举的属性（方法），包括存在于实例中的和存在于原型中的，也包括屏蔽了原型中不可枚举的实例属性（方法）（自定义的属性（方法）默认是可枚举的）

`ECMAScript 5`中还可以使用`Object.keys()`方法返回对象上所有可枚举的**实例属性**，这个方法接受一个对象作为参数，返回一个包含所有可枚举属性的字符串数组

使用`Object.getOwnPropertyNames()`方法可以返回所有可枚举和不可枚举的属性（方法）

也可以使用如下所示的对象字面量的方法来创建一个原型对象：

```javascript
function Person() {};
Person.prototype = {
    name : "test",
    age : 29,
    job : "Software Engineer",
    sayName : function() {
        alert(this.name);
    }
};

var person = new Person();
```

虽然使用这种方式创建的对象实例还可以使用`instanceof`操作符正确的确定实例的类型，但是这个实例的`constructor`属性并不是指向构造函数（直接为构造函数原型赋值的方法改变了默认内部创建过程）；当然我们可以手动的在原型对象中加上这个属性`constructor : Person,`，这种方式重设的`constructor`属性的`[[Enmumerable]]`特性被设置为`true`，默认原生的是不可枚举的，这时在`ECMAScript 5`中我们可以调用`Object.defineProperty()`方法将其改回不可枚举的特性

由于在对象中查找属性实际上是一次搜索的过程，所以对原型的修改会立即在实例上反映出来；但是如果重写了整个原型对象，而对象实例中的`[[Property]]`并不能立即指向这个新的原型对象，这是如果访问新原型对象中的属性（方法）时就会出错（原来原型中的属性还可以访问）

不仅仅自定义引用类型存在原型，原生引用类型也一样，并且我们可以通过修改原生类型的原型来为原生类型添加方法；**但是在实践中不推荐对原生类型进行更改**

原型模式存在的最大的问题就是所有实例共享属性的问题

**`组合使用构造函数模式和原型模式`** 

在实际的开发中最常用的就是这种形式，构造函数模式用于定义实例属性，原型模式用于定义方法和共享属性，这种方式还支持向构造函数传递参数

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
}

Person.prototype = {
    constructor : Person,
    sayName : function() {
        alert(this.name);
    }
}

var person1 = new Person("test", 29, "Software Engineer");
var person2 = new Person("test2", 27, "Doctor");

person1.friends.push("Van");
```

**`动态原型模式`**  

还可以使用另外一种在构造函数中根据条件进行原型定义的方法：

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    
    if(typeof this.sayName != "function") {
        Person.prototype.sayName = function() {
            alert(this.name);
        };
    }
}
```

上面的为原型添加方法的代码只有在首次创建对象实例时才会执行，并且使用这种方法修改原型属性也会立刻在所有的实例中得到反应；还可以使用`instanceof`操作符对实例的类型进行检测

> 如果要在构造器中给原型绑定多个方法，不需要每一个方法都使用`if`进行检查，只要检查一个就可以了，把其他的绑定语句都放在同一个`if`语句块中即可

> 使用动态原型模式同样不能使用对象字面量重写原型对象，因为这样会切断实例和原型之间的联系

**`寄生构造函数模式`**  

寄生构造函数模式的形式类似于工厂模式，但是在创建对象时需要使用`new`关键字：

```javascript
function Person(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    };
    
    return o;
}

var person = new Person("test", 29, "Software Engineer");
```

构造函数在没有指定返回值的情况下回返回使用这个构造函数创建的对象的实例，通过明确指定返回一个对象，重写了调用构造函数的返回值

这个模式一般在特殊情况下用来为对象创建构造函数，比如我们想为`Array`添加一个函数，由于不能直接修改构造函数，所以我们可以使用这个模式将`Array`的构造函数包装一下添加其他方法或者属性：

```javascript
function SpecicalArray() {
    //创建数组
    var values = new Array();
    
    //添加值
    values.push.apply(values, arguments);
    
    //添加方法
    values.toPipedString = function() {
        return this.join("|");
    };
    
    return values;
}
```

> 注意：在寄生构造函数模式中，返回的对象于构造函数和构造函数的原型属性之间没有关系；同时构造函数返回的对象和在构造函数外部创建的对象也是相同的类型，所以不能使用`instanceof`操作符来检查实例是否是我们创建的类型

**`稳妥构造函数模式`**  
todo

##### 继承

`ECMAScript`中只支持实现继承（相对于接口继承），而且其实现继承主要是靠原型链来实现的

**`原型链继承`**

`ECMAScript`中每一个构造函数都有一个原型对象，原型对象中包含一个指向构造函数的指针`[[constructor]]`，而实例都包含一个指向原型对象的内部指针`[[prototype]]`，假如让**原型对象等于另外一个类型的实例**，这时原型对象将包含一个指向另外一个原型的指针，，另外一个原型中也包含着指向相应构造函数的指针，假如另外一个原型又是另外一个实例类型，那么上述关系依然成立，这样层层递进就构成了**原型链**的概念

实现原型链的基本模式：

```javascript
function SuperType() {
    this.property = true;
}

SuperType.prototype.getSuperValue = function() {
    return this.property;
};

function SubType() {
    this.subproperty = false;
}

//继承了SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function() {
    return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue());
```

基本原型链还应注意一下几个问题：

*   所有的引用类型都继承自`Object`，而且这种继承也是通过原型链实现的；因此默认原型对象中都会包含一个内部指针指向`Object.prototype`
*   可以使用`instanceof`操作符来确定实例和原型之间的关系：用这个操作符来测试实例的所有在原型链中出现过的构造函数都会返回`true`；还可以使用任意原型对象的`isProtottypeOf()`方法来测试，结果和`instanceof`相同：

```javascript
alert(Object.prototype.isPrototypeOf(instance));    //true
alert(SuperType.prototype.isPrototypeOf(instance));     //true
alert(SubType.prototype.isPrototypeOf(instance));   //true
```

*   因为我们使用更改原型对象的方式创建了继承所依赖的原型链，所以要重写超类中的某个方法或者添加超类中不存在的方法，**需要在替换原型的语句之后**

> 这里同样不能使用对象字面量方式创建原型方法，这样做会重写原型链

*    基本原型链存在的问题：因为子类型原型被更改为父类型的实例，所以父类型的实例属性就成了子类型的原型属性，如果父类型实例属性中有引用类型的话就会被所有子类型所共享；不能向超类型的构造函数传递参数（没有办法在不影响所有对象实例的的情况下，给超类型的构造函数传递参数）

> 鉴于这两个问题，在实际中很少单独使用基本原型链

**`借用构造函数`**  

借用构造函数是在子类型的构造函数中调用超类型的构造函数；因为函数只不过是在特定环境中执行代码的对象，因此通过使用`apply()`和`call()`方法可以在未来新创建的对象上执行构造函数：

```javascript
function SuperType() {
    this.colors = ["red", "blue", "green"];
}

function SubType() {
    //继承了SuperType
    SuperType.call(this);
}

var instance = new SubType();
instance.colors.push("black");
alert(instance.colors);     //"red", "blue", "green", "black"

var instanceOther = new SubType();
alert(instanceOther.colors);    //"red", "blue", "green"
```

使用这种方式，可以在子类型的构造函数中向超类型的构造函数传递参数；但是无法避免构造函数存在模式问题（方法都在构造函数中定义，因此无法复用函数）；所以实际开发中很少单独使用借用构造函数方式

**`组合继承`**  

组合继承将原型链和借用构造函数结合在一起，使用原型链实现对原型属性和方法的继承，通过借用构造函数实现对实例属性的继承；这样即通过在原型上定义方法实现函数复用，又能保证每个实例都有自己的属性：

```javascript
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];

}

SuperType.prototype.sayName = function() {
    alert(this.name);
};

function SubType(name, age) {
    //继承属性
    SuperType.call(this, name);

    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
    alert(this.age);
};

var instance1 = new SubType("test", 29);
instance1.colors.push("black");
alert(instance1.colors);    //"red", "blue", "green", "black"
instance1.sayName();    //"test"
instance1.sayAge();     //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors);    //"red", "blue", "green"
instance2.sayName();    //"Greg"
instance2.sayAge();     //27
```

使用`instanceof`操作符和`isPrototypeOf()`方法也能够识别组合继承创建的对象；因此这种方法是最常用的方法

**`原型式继承`**  

原型式继承就是基于已有的对象创建新的对象，同时还不必因此创建自定义的类型：

```javascript
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

var person = {
    name : "test",
    friends : ["Shelby", "Court", "Van"]
};

var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob")

var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends);      //"Shelby, Court, Van, Rob, Barbie"
```

将一个对象作为另外一个对象的原型因此后者的对象实例将会获取前者所有的属性和方法，并且基于后者创建的实例共享这些属性和方法

`ECMAScript 5`添加了一个创建原型继承的规范化方法`Object.create()`，这个方法接受两个参数：作为新对象原型的对象，可选的为新对象定义属性（方法）的对象

**`寄生式继承`**  

寄生式继承是与原型式继承紧密相关，并且和寄生构造函数及工厂模式的思路类似：创建一个仅用于封装继承过程的函数，在函数内部以某种方式来增强对象，然后返回对象：

```javascript
function createAnother(original) {
    var clone = object(original);
    clone.sayHi = function() {
        alert("Hi");
    };
    
    return clone;
}
```

**`寄生组合式继承`**  

组合继承是一种比较常用的继承模式，但是它总是调用两次超类型构造函数：一次是在创建子类型原型的时候，另外一次是在子类型构造函数内部；这样同时也造成了超类属性的重写

寄生式组合继承通过借用构造函数来继承属性，通过原型链的混成形式来继承方法；即，不必为了指定子类型的原型而调用超类型的构造函数，我们需要的无非是超类型原型的一个副本而已；所以可以使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型：

```javascript
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype);    //创建对象
    prototype.constructor = subType;                //增强对象
    subType.prototype = prototype;                  //制定对象
}

function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];

}

SuperType.prototype.sayName = function() {
    alert(this.name);
};

function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function() {
    alert(this.age);
};
```
上面的例子中只调用了一次`SuperType`构造函数，并且因此避免了在`SubType.prototype`上面创建不必要的多余的属性；于此同时，原型链还能保持不变，因此还能使用`instancof`和`isPrototypeOf()`来检测类型。所以这是一种比较理想的继承范式

#### 函数表达式

`ECMAScript`中函数的定义有两种方式：`函数声明`和`函数表达式`；两者的重要区别是：前者有**函数声明提升**的过程，即解释器在执行代码之前会首先读取函数声明，所以函数的调用可以在函数定义之前

因为函数声明提升的特性存在，所以不要在`if else`中根据条件使用**函数声明**定义名称相同但是行为不同的函数（后者会覆盖前者）；但是可以使用**函数表达式**定义这样的函数

因为可以先创建函数然后在赋给变量，所以可以将函数当做参数或者返回值使用

##### 递归

在递归的调用函数时，如果在函数中显式的依赖函数名称那么如果我们将函数赋值给另外一个变量，并且将原函数名称置`null`，则使用这个变量调用递归时会失败，我们可以在函数中使用`arguments.callee`属性来引用函数名而避免这种情况：

```javascript
function factorial(num) {
    if(num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num - 1);
    }
}
```

但是在严格模式下不能在程序中访问`arguments.callee`属性，我们可以通过使用**命名函数表达式**来解决：

```javascript
var factorial = (function f(num) {
    if(num <= 1) {
        return 1;
    } else {
        return num * f(num - 1);
    }
});
```

命名函数表达式名称`f`不管在函数被赋给任意变量都可以被正确的引用

##### 闭包

**`闭包`**是指有权访问另外函数作用域变量的函数；创建闭包的常见方式是在一个函数内部创建另外一个函数；

```javascript
function createComparisonFunction(propertyName) {
    return function(object1, object2) {
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];
        
        if(value1 < value2) {
            return -1;
        } else if(value1 > value2){
            return 1;
        } else {
            return 0;
        }
    };
}
```

上面的例子中内部函数定义中引用了外部函数的参数，即使在外部函数已经返回的情况下，内部函数依旧可以访问到那个参数，这是因为内部函数的作用域链中已经包含了外部函数的作用域

前面曾讲过，函数调用时会创建一个执行环境及相应的作用域链，函数的局部变量会在作用域链的顶端，调用这个函数的函数的作用域链位于第二，全局作用域位于底端；函数执行过程中要读取和写入变量值都在这个作用域链中进行查找；解释器内部使用`[[Scope]]`属性保存作用域链

每一个执行环境都有一个表示变量的对象--`变量对象`，全局环境的变量对象一直存在，而函数局部环境变量对象只在函数调用时存在，函数返回时局部变量对象即被销毁；当在全局作用域中调用一个函数时，这个函数的作用域链包含两个变量对象：本地变量对象和全局变量对象；因此作用域链实际上是指向变量对象的一个指针列表，它引用但是不包含实际的对象

当在一个函数中定义另外一个函数时（闭包），被定义函数的变量对象将包含外部函数的变量对象，并且即使外部函数返回其变量对象也不会被销毁

> 因为闭包会携带包含它的作用域的变量，因此会占用更多的内存，所以应该谨慎并且在必要的时候才使用闭包

**`闭包中的变量问题`**  

因为闭包引用的是外部函数的整个变量对象，所以如果外部函数中的变量在被内部函数多次引用的过程中发生改变，则返回的内部函数在调用的过程中只能访问到外部变量最后一次被赋的那个值：

```javascript
function createFunctions() {
    var result = new Array();
    
    for(var i = 0; i < 10; i++) {
        result[i] = function() {
            return i;
        };
    }
    
    return result;
}
```

上面的示例中，result数组的所有元素都是`i`变量被赋的最后一个值10；但是我们可以使用另外一种方法来达到这样的预期：

```javascript
function createFunctions() {
    var result = new Array();
    
    for(var i = 0; i < 10; i++) {
        result[i] = function(num) {
            return function() {
                return num;
            };
        }(i);
    }
    
    return result;
}
```

上面的例子还未完全理解，后续给出解释

**`this对象`**  

`this`对象是在运行时基于函数的执行环境绑定的：在全局函数中，`this`等于`window`，当函数被当做某个对象的方法时调用时，`this`等于那个对象；但是匿名函数的执行环境具有全局性，`this`指向`window`:

```javascript
var name = "The Window";

var object = {
    name : "My Object",
    
    getNameFunc : function() {
        return function() {
            return this.name;  
        };
    }
};

alert(object.getNameFunc()());  //  "The Window"
```

因为只有当函数被调用时才可以引用`this`和`arguments`两个特殊对象，当闭包函数被调用时外部函数已经返回，这两个变量就不存在了（而全局的变量还是存在的）；所以闭包中使用的`this`是全局的执行环境

不过我们可以将外部作用域中的`this`对象保存在闭包可以访问到的变量中，就可以让闭包访问这个对象了：


```javascript
var name = "The Window";

var object = {
    name : "My Object",
    
    getNameFunc : function() {
        var that = this;
        return function() {
            return that.name;  
        };
    }
};

alert(object.getNameFunc()());  //  "My Object"
```

> `arguments`对象也存在这样的问题，解决的方法同样是在外部函数中将这个对象赋给一个变量

`this`值也会在下面的情况下被改变：

```javascript
var name = "The Window";

var object = {
    name : "My Object",
    getName : function() {
        return this.name
    }
};

object.getName();   //  "My Object"
(object.getName)();     //  "My Object"
(object.getName = object.getName)();    //"The Window"
```

**`内存泄漏`**  

关于闭包在`IE`浏览器中的一些问题

##### 模仿块级作用域

`JavaScript`中没有块级作用域，在块中或者`if for`条件表达式中定义的变量实际也是包含在函数中而非语句中创建的；即使在块语句结束后重新声明同名的变量，仍然不能改变它的值

匿名函数可以用来模仿块级作用域：

```javascript
(function() {
    //这里就是块级作用域
})();
```
上面的代码中定义并且立即调用这个匿名函数，将匿名函数包含在一对圆括号中，表示它是一个函数表达式，紧随其后的一个圆括号则会立即调用这个函数

> 注意：要在匿名函数定义之后立即调用必须给匿名函数定义加上小括号，否则会导致语法错误

> 这种方式同样可以减少闭包占用内存的问题，因为没有指向匿名函数的引用。所以只要函数执行完毕就可以立即销毁其作用域链了

##### 私有变量

`JavaScript`中没有私有成员的概念，所有对象的属性都是共有的；但是我们可以私有变量（函数中的变量）和闭包的特性来创建私有变量和方法：

```javascript
function MyObject() {、
    //私有成员
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    
    //特权成员
    this.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
}
```

上例中，`MyObject`是一个构造函数，当使用`new`关键字调用构造函数返回一个对象之后，构造函数中的两个“私有”成员将不能被直接访问，只能通过绑定到返回的对象上的`publicMethod`属性（方法）访问，这样就实现了实例的私有成员

利用私有成员和特权成员的方法还可以隐藏那些不应该被直接修改的数据；但是使用构造函数方法的问题是每一个实例都会创建同样一组新方法

**`静态私有变量`**  

通过在私有作用域中定义私有变量或者函数，同样也可以创建特权方法：

```javascript
(function() {
    //私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    
    //构造函数
    MyObject = function() {};
    
    //共有/特权方法
    MyObject.prototype.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
})();
```

MyObject定义为全局的让构造函数可以在全局中访问到，同时使用函数表达式的方法为其赋值

??????????????

**`模块模式`**  

?????????????

**`增强的模块模式`**  

?????????????

#### BOM

`BOM`提供了很多功能用于访问浏览器的功能，这些功能与网页内容无关。目前W3C已经将`BOM`的主要方法纳入到`HTML5`规范中

##### window对象

`window`是`BOM`的核心对象，它表示一个浏览器实例。在浏览器中`window`对象有双重的角色：一方面是`JavaScript`访问浏览器的一个重要接口；另外一方面它又是`ECMAScript`中的`Global`对象，所以在程序中定义的全局变量、函数、对象都属于`window`对象的

> 注意：全局的变量和直接定义在`window`对象上的属性还是有区别的：后者可以使用`delete`操作符删除，但是前者不能，因为使用`var`关键字添加的全局变量的`[[Configurable]]`属性被设置为`false`；另外尝试访问未经声明的变量会返回错误，但是通过查询`window`对象属性的方式就可以知道这个可能未声明的变量是否存在

后面要讲的几个全局的对象：`location` `navigator` `screen` `history`对象都是`window`对象的属性

**`窗口关系及框架`**

如果页面中包含多个`<frame>`，则每一个`<frame>`都有自己的`window`对象，并且保存在`frames`集合中，我们可以通过数值索引或者框架名称来访问相应的`window`对象：

```html
<html>
    <head>
        <title>FrameSet Example</title>
    </head>
    <frameset rows="160, *">
        <frame src="frame.html" name="topFrame">
        <frameset cols="50%, 50%">
            <frame src="anotherframe.html" name="leftFrame">
            <frame src="yetanotherframe.html" name="rightFrame">
        </frameset>
    </frameset>
</html>
```

上面的例子可以使用`window.frame[0]`或者`window.frame["topFrame"]`来引用上方的框架，不过最好使用`top`来代替`window`（否则和返回的`window`对象有冲突），`top`总是表示最外层的框架（浏览器窗口）

`parent`对象始终指向当前框架的上层框架；如果在没有框架的情况下`top`等于`parent`

`top`和`parent`都是`window`的属性；而这些属性通常也是指向另外一个`window`对象，所以可以通过`window.parent.parent.frames[0]`这样来访问

**`窗口位置`**  

`screenLeft`和`screenTop`属性用来确定和修改`window`对象（浏览器窗口）的位置（firefox使用`screenX`和`screenY`）；确定位置的属性在各个浏览器的实现上有些差异，请参考《JavaScript高级程序设计》

各个浏览器都提供了统一的方法`moveTo()` `moveBy()`方法将浏览器窗口移动到一个新的额位置；这两个方法都接受两个参数，第一个方法的两个参数表示要移动到的坐标位置，第二个方法的两个参数表示要移动的相对像素数

**`窗口大小`**  

`innerWidth` `innerHeight` `outerWidth` `outerHeight`属性用来确定窗口的大小，但是各个浏览器实现还是有很大的不同

`resizeTo()` `resizeBy()`方法可以将窗口大小进行调整；这两个方法都接收两个参数，第一个方法的两个参数表示要调整到的绝对大小的宽度和高度，第二个方法的两个参数表示要增加的宽度和高度

**`导航和打开窗口`**  

`window.open()`可以导航到一个特定的URL。这个方法接受四个参数：要加载的URL、窗口目标、一个特性字符串、一个布尔值表示新页面是否在历史记录中取代当前加载页面；如果传递第二个参数，则这个参数表示一个窗口或者框架名称，即，如果页面中有这个框架名，则在那个框架中打开，如果没有则打开一个新的窗口来加载指定的URL；第二个参数也可以是这些特殊的窗口名：`_self` `_parent` `_top` `_blank`

如果根据第一个参数的特定设置打开了一个新的窗口，并且没有指定第三个参数则新的窗口将包含所有工具栏、地址栏状态栏等；使用第三个参数可以控制这个新窗口的组件：

```javascript
fullscreen  yes/no  浏览器窗口是否最大化（IE）
height      number  新窗口的高度（不能小于100）
left        number  新窗口的左坐标（不能是负值）
location    yes/no  是否在浏览器中显式地址栏；如果设置为no则地址栏可能隐藏也可能禁用取决于浏览器实现
menubar     yes/no  是否显示菜单栏，默认为no
resizable   yes/no  是否可以缩放，默认no
scrollbars  yes/no  是否可以滚动显式，默认no
status      yes/no  是否显示状态栏，默认no
toolbar     yes/no  是否显示工具栏，默认no
top         yes/no  新窗口的上坐标（不能是负值）
width       yes/no  新窗口的宽度（不能小于100）
```

这些参数名称和值使用`=`连接，并且可以使用`,`分隔多个：

```javascript
window.open("http://www.wrox.com","wroxWindow","height=400,width=400,top=10,left=10,resizable=yes");
```

`window.open`返回这个新窗口的引用（`window`对象），这个对象除了前面介绍的方法之外还有一些额外的方法：

```javascript
window.close()      //这个方法可以在不经用户同意的情况下关闭这个新窗口
window.closed       //这个属性可以判断这个窗口是否已经关闭
window.opener       //这个属性保存着打开这个新窗口的窗口对象
```

弹出窗口在不同的浏览器可能会受到不同的限制；另外浏览器内置和一些第三方扩展程序也可能屏蔽弹出窗口，我们可以使用如下代码来检测并且提示用户：

```javascript
var blocked = false;
try {
    var wroxWin = window.open("http://www.wrox.com","_blank");
    if(wroxWin == null) {
        blocked = true;
    }
} catch (ex) {
    blocked = true;
}

if(blocked) {
    alert("The popup was blocked!");
}
```

> 窗口屏蔽并不能阻止程序对弹出窗口的操作????????

**`间歇调用和超时调用`**  

`超时调用`使用`window.setTimeout()`方法，它接受两个参数：要执行的代码和以毫秒表示的等待时间；第一个参数可以是包含`JavaScript`代码的字符串（不建议），也可以是一个函数；这个方法返回一个`ID`表示这个超时调用的任务id，后面可以使用`window.clearTimeout()`方法将这个id作为参数来取消这个超时调用任务

> 因为`JavaScript`是单线程的程序，经过第二个参数指定的时间之后，指定的任务是否会执行要看当前任务队列里的任务数量（实际上这个参数指定的是经过多长时间后把任务添加到要执行的任务队列）

> 超时调用的代码都是在全局作用域下执行的，因此代码中的`this`对象指向`window`，在严格模式下是`undeined`

`间歇调用`使用`window.setInterval()`方法与超时调用类似，不过它会根据第二个参数指定的时间间隔重复的执行任务；参数和返回值也一样；同样可以使用`window.clearInterval()`方法清除间歇调用任务

> 注意：在大多数情况下，超时调用没必要跟踪任务ID，但是间歇调用应该总是保存返回的任务ID值

**`系统对话框`**  

`JavaScript`中有三种类型的系统对话框：`alert()` `confirm()` `prompt()`

`alert()`通常用于显示一个警告信息，对话框中有一个`OK`按钮可以关闭这个对话框

`comfirm()`通常也用于显示一个警告信息，不过这个对话框中有两个按钮：当我们点击`OK`时这个方法返回`true`；当我们点击`cancel`或者右上角的关闭按钮时这个方法返回`false`

`prompt()`这个对话框除了可以给用于显示一些信息之外还可以接收用户的输入；方法接受两个参数，第一个参数表示显示给用户的信息，第二个参数表示输入文本框的默认值；如果用户点击了`OK`按钮这个方法返回用户的输入信息，否则返回`null`

##### location对象

这个对象主要提供了与当前窗口加载的文档的有关信息；它既是`window`对象中的属性，也是`document`对象的属性，即`window.location`和`document.location`引用的是同一个对象

这个对象包括如下属性（这些属性的值都是一个字符串）：

```javascript
hash    返回URL中的hash（#号后跟的0个或者多个字符），如果不包含则返回空字符串
host    返回服务器名称和端口号
hostname    返回不带端口号的服务器名称
href    返回当前加载页面的完整URL。location.toString()也返回这个值
path    返回URL中的目录或者文件名   "/wilecda/"
port    返回URL中指定的端口号，如果没有明确的指定端口号则返回空字符串
protocol    返回页面使用的协议，通常是"http:" "https:"
search  返回URL中的查询字符串，这个字符以"?"开始
```
这些属性不仅是可以获取，而且可以更改；更改会导致按照更改后的URL重新请求，并且会在浏览器中添加一条重新请求前URL的历史记录

下面这段代码可以用来获取查询字符串并且返回一个包含所有查询参数的对象：

```javascript
function getQueryStringArgs() {
    var qs = (location.search.length > 0 ? location.search.substring(1) : ""),
    args = {},
    items = qs.length ? qs.split("&") : [],
    item = null, name = null, value = null,
    i = 0, len = items.length;
    
    for(i = 0; i < len; i++) {
        item = items[i].split("=");
        name = decodeURIComponent(item[0]);
        value = decodeURIComponent(item[1]);
        
        if(name.length) {
            args[name] = value;
        }
    }
    
    return args;
}
```

`location.assign()`方法可以打开参数指定的URL地址，并且在浏览器的历史记录中增加一条记录；将`location.href`或者`window.location`属性设置为一个URL实际上也是调用这个方法

`location.replace()`方法也可以打开参数指定的URL地址，但是并不会生成历史记录，即点击后退按钮不会回到之前的页面

`location.reload()`方法会导致当前页面重新被加载，如果不传递任何参数，则浏览器会根据自上次请求以来资源内容是否变动来确定是从缓存加载还是重新请求（相当于`F5`刷新），如果传递一个`true`参数则总是重新发送请求（相当于`Ctrl+F5`）

##### navigator对象

`navigator`对象提供了一些属性和方法用来识别客户端浏览器，详细信息参考`《JavaScript高级程序设计》`

##### screen history对象

`screen`对象和`history`对象在实际开发中使用的比较少，详细信息可以参考`《JavaScript高级程序设计》`

#### DOM

`DOM`是针对HTML和XML文档的一个API。`DOM`描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的一部分

##### 节点层次

`DOM`将任何HTML和XML文档描绘成一个由多层次节点构成的结构。节点分为几种不同的类型，每种类型分别表示文档中不同的信息和标记。每个节点都拥有自己的特点、数据和方法，另外也与其他节点存在某种关系

```html
<html>
    <head>
        <title>Sample Page</title>
    </head>
    <body>
        <p>Hello World!</p>
    </body>
</html>
```

上面的文档中，`document`节点只有一个子节点`<html>`元素，称为**文档元素**。文档元素是最外层的元素，文档中的其他元素都应该包含在文档元素中，一个页面只能有一个文档元素。在HTML页面中，文档元素就是`<html>`元素；在XML中没有预定义的文档元素

HTML中总共有12种节点类型，这些类型都继承自一个基类：

**`Node类型`**  

`DOM1`中定义了一个`Node`接口，`DOM`中的所有节点类型都实现了这个接口。这个接口在`JavaScript`中是作为`Node`类型实现的，并且所有节点类型都继承自`Node`类型（后面的所有类型节点理论上都有这些属性和方法）。每一个节点都有一个`nodeType`属性，表示节点类型，节点类型可以是下面这些常量：

```javascript
Node.ELEMENT_NODE(1)
Node.ATTRIBUTE_NODE(2)
Node.TEXT_NODE(3)
Node.CDATA_SECTION_NODE(4)
Node.ENTITY_REFERENCE_NODE(5)
Node.ENTITY_NODE(6)
Node.PROCESSING_INSTRUCTION_NODE(7)
Node.COMMENT_NODE(8)
Node.DOCUMENT_NODE(9)
Node.DOCUMENT_TYPE_NODE(10)
Node.DOCUMENT_FRAGMENT_NODE(11)
Node.NOTATION_NODE(12)
```

因为IE浏览器中并没有提供这些常量，所以要判断一个节点的类型时最好使用后面的数字来保持兼容性。开发中最常用的节点类型是1和3

可以使用`nodeName`和`nodeValue`属性获取节点名称和值，根据节点类型这两个属性并不是总是有意义：如果是一个元素节点，则`nodeName`的值为标签名，`nodeValue`的值为`null`

每一个节点都有一个`childNodes`属性，这个属性的值为一个`NodeList`对象。可以通过索引或者`item()`方法传递一个数值参数获取其中值，并且这个对象还有一个`length`属性表示元素个数，但它并不是`Array`对象的实例。`NodeList`总是实时的反应出当前DOM结构的变化

下面的代码可以将一个`NodeList`对象实例转换为一个数组：

```javascript
function convertToArray(nodes) {
    var array = null;
    try {
        array = Array.prototype.slice.call(nodes,0);    //非IE浏览器
    } catch(ex) {
        array = new Array();
        for(var i = 0, len = nodes.length; i < len; i++) {
            array.push(nodes[i]);
        }
    }
    return array;
}
```

每个节点都有一个`parentNode`属性，这个属性指向文档树中的父节点，包含在`childNodes`列表中的所有节点都指向同一个父节点。包含在`childNodes`列表中的所有节点之间都是同胞节点，可以使用每一个节点的`previousSibling`和`nextSibling`属性来访问，第一个节点的前一个和最后一个节点的后一个节点值都为`null`。`firstChild`和`lastChild`属性分别表示一个节点的第一个子节点和最后一个子节点，这两个节点也可以使用`someNode.childNodes[0]`和`someNode.childNodes[someNode.childNodes.length-1]`

`hasChildNodes()`方法在一个节点有子节点时返回`true`，否则返回`false`；通过`someNode.childNodes.length`属性的检查也可以进行判断

每一个节点都有一个`ownerDocument`属性，该属性指向表示整个文档的文档节点，任何节点都只属于一个文档节点；子节点不必层层的返回到文档节点，通过这属性可以直接获取

上述的属性都是只读的，所以不能通过更改属性值的方式添加节点。所以`DOM`提供了一些操作节点的方法：

```javascript
appendChild()
//向childNodes列表末尾添加一个节点，并返回这个节点
//添加节点后childNodes列表中的相应属性会立即调整为最新状态
//如果作为参数传入的节点已经是文档的一部分，则表示移动这个节点
//要注意的是，尽管DOM数是由一系列指针连接起来的，但是任何节点都不能同时出现在一个文档的两个地方

insertBefore()
//向childNodes列表的某一个位置插入一个节点
//第一个参数表示要插入的节点，第二个参数表示参照节点；参照节点也可以是null效果同appendChild()

replaceChild()
//将childNodes列表的某一节点替换为另外一个节点
//第一个参数表示替换后的节点，第二个参数表示要替换的节点
//替换后原来节点的关系被复制到了新的节点中

removeChild()
//移除指定的节点，返回值为已经移除的节点
```

> 上述方法都要求在父节点上进行操作；`DOM`中不是所有节点都有子节点，如果在没有子节点的节点上调用这些方法会返回错误

`cloneNode()`方法可以复制一个节点，使用`true`参数表示复制整个子节点数（指定的节点和所有子节点），使用`fasle`作为参数表示只复制这个节点；复制返回的节点只能存在于本文档对象，可以使用上面提供的方法给其加上文档关系（否则的话是一个“孤儿”节点）

> 这个方法只复制文档特性，其他的都不会复制，也不会复制添加到`DOM`节点中的`JavaScript`属性

**`Document类型`**  

`Document`表示文档类型。浏览器中`document`对象是`HTMLDocument`（继承自`Document`类型）类型的一个实例，表示整个HTML页面。`document`同时是`window`对象的一个属性，因此可以当做一个全局对象来访问；这个对象还有下面特征：

*   `nodeType`的值为9
*   `nodeName`的值为`"#doucment"`
*   `nodeValue`的值为`null`
*   `parentNode`的值为`null`
*   `ownerDocuemnt`的值为`null`
*   其子节点可能是一个`DocumentType`、 一个`Element`、`ProcessingInstruction`、`Comment` 

`Document`类型可以表示HTML页面或者XML页面。最常见的应用还是作为`HTMLDocument`类型实例的`document`对象。通过这个文档对象可以取得页面有关信息、操作页面外观及底层结构

> 非IE浏览器中可以访问`Docuemnt`类型的构造函数和原型；所有浏览器中都可以访问`HTMLDocument`类型的构造函数和原型

访问`document`子节点时可以使用`document.docuemntElement`属性直接取得`<html>`元素节点（`document`对象唯一的元素节点）；另外还有一个`document.body`属性直接指向`<body>`元素节点；其他的子节点元素需要使用`childNodes`列表来访问

`Document`类型通常存在的另外一个子节点类型是`DocumentType`，`<!DOCTYPE>`被看成是与文档其他部分不同的实体，可以通过`document.doctype`属性获取文档类型信息

> 这个子节点在各个浏览器上的访问方式有很大不同详细信息参考`《JavaScript高级程序设计》`

> 注释节点类型在各个浏览器上的实现解析方式也有很大不同，因为使用比较少这里不详述

> 通常我们不需要在`document`上调用更改子节点的各种方法，因为这个节点类型是只读的，并且其唯一的元素节点通常已经存在

作为`HTMLDocument`类型的一个实例，`document`对象还有一些标准的`Document`类型所没有的属性：

*   `document.title`包含着`<title>`元素中的文本，这个属性可以读取，但是写无效
*   `document.URL`包含当前页面完整的URL
*   `document.domain`只包含页面的域名（包括主机名）
*   `document.referrer`保存着连接到本页面的那个页面的URL，如果没有则返回空字符串

> 这些信息其实是从HTTP请求头中获取到的

> 上面的属性中只有`domain`是可更改的，通常在`JavaScript`跨页面通信时需要将这个两个页面的`domain`都改为相同的域名才可以通信

`JavaScript`中提供了几个用于在页面中查找元素的方法（属于`Document`类的方法）：

```javascript
document.getElementById()
//接受一个参数表示页面中元素的id，并返回这个元素节点，id区分大小写，如果指定的id不存在则返回null
//如果页面中有多个元素的id相同，则使用这个方法只返回第一个元素
//在IE7中表单元素的name属性也会被检查是否匹配，其他浏览器则不会

document.getElementsByTagName()
//接受一个参数表示元素标签名，返回包含零个或者多个元素的`NodeList`对象
//在HTML中这个方法返回一个HTMLCollection对象，这个对象和NodeList对象类似，可以使用[index]或者item(index)方法获取元素，使用length属性获取元素数量
//当使用下标获取元素时，可以指定一个字符串的下标，这时会和所有元素的name属性值进行匹配，匹配到则返回

HTMLCollection.namedItem()
//这个方法接受一个字符串作为参数，返回上一个方法返回的HTMLCollection对象中有name属性并且和参数匹配的元素
//这个方法也接受"*"作为参数表示所有元素

document.getElementsByName()
//这方法是HTMLDoucment对象类型的方法
//接受一个参数表示name属性值，返回一个HTMLCollection包含所有具有name属性值并且和参数匹配的元素
//这个方法通常用来获取单选按钮，一组单选按钮应该总是具有相同的name属性
```

部分元素类型都有一个专门的属性指向这样的元素类型集合（HTMLCollection对象），这些属性是`HTMLDocument`对象类型特有的：`document.anchors` `document.forms` `document.images` `document.links`

`JavaScript`提供了将输出流写入到网页中的方法：`write()` `writeln()` `open()` `close()`，这些方法在开发中使用比较少，详细信息参考`《JavaScript高级程序设计》`

**`Element类型`**  

`Element`类型用于表现`XML`和`HTML`元素，提供了对元素标签名，子节点和特定的访问：

*   `nodeType`的值为1
*   `nodeName`的值为元素的标签名
*   `nodeValue`的值为null
*   `parentNode`可能是`Document`或者`Element`
*   其子节点可能是`Element` `Text` `Comment` `ProcessingInstruction` `CDATASection` `EntityReference`

在获取到元素节点实例之后可以使用这个对象的`nodeName`或者`tagName`属性来访问元素的标签名；要注意的是返回的标签名可能是大写的，并且不带尖括号，如果要和字符串进行比较的话最好显式的将其转换为小写再进行比较

`Element`类型在`HTML`文档中使用`HTMLElement`对象类型（及其子类型）来表示，`HTMLElement`类型继承自`Element`并且添加了一些属性。每一个`HTML`元素都有下面的特性：

*   `id`：元素在文档中的唯一标识符
*   `className`：与元素的`class`属性对应，即为元素指定的css类（因为class是`ECMAScript`的保留字，所以使用这个名称）
*   `title`：有关元素的附加说明，一般通过工具提示条显示出来
*   `lang`：元素内容的语言代码
*   `dir`：语言的方向，即从左到右或者从右到左

上面几个属性都是可读写的，但是只有`className`会实时的反应当前的值

`《JavaScript高级程序设计》 p263`列出了`HTML`中所有元素和类型的对应

在`JavaScript`中使用下面三个方法操作这些元素的属性：`getAttribute()` `setAttribute()` `removeAttribute()`；比如：

```javascript
<div id="myDiv" my_special_attribute="hello!"></div>

var value = div.getAttribute("my_special_attribute");   //hello
```
> 注意：属性名称是不区分大小写的，并且`HTML5`规范要求自定义的属性应该加上`data-`前缀

`HTML`元素的自有属性（非自定义），也可以通过这个对象类型实例的属性来访问（在IE浏览器中所有属性都可以使用`.` `[]`的方式访问）

以下两种属性，通过对象属性方式和通过方法访问到的值不同：

*   `style`：属性通过`getAttribute()`方法返回的值是对应的`css`样式字符串；而通过对象属性方式返回的是另外一个对象
*   `onclick`：如果通过`getAttribute()`方法访问返回值是对应的`JavaScript`代码字符串；而通过对象属性方式返回的是一个函数对象引用

> 由于以上差别的存在，所以在实际开发当中应该总是使用对象属性的方式来访问属性；只有在访问自定义属性时才使用`getAttribute()`方法

`setAttribute()`方法接受两参数：要设置的属性名和属性值；`removeAttribute()`方法用来彻底的删除一个元素的属性

`Element`类型还有一个`attributes`属性，这个属性是一个`NamedNodeMap`类型（类似于`NodeList`的集合类型）；当前元素的所有属性在`NamedNodeMap`中是以`attr`节点类型保存的；我们可以通过`NamedNodeMap`的下列方法来访问和操作这些属性：

*   `getNamedItem(name)`：返回`nodeName`为`name`的节点
*   `removeNamedItem(name)`：从列表中移除`nodeName`为`name`的节点
*   `setNamedItem(node)`：向列表中添加节点，以节点的`nodeName`属性为索引
*   `item(pos)`：返回位于数字`pos`位置处的节点

> `attributes`属性中包含一系列的节点，每个节点的`nodeName`是属性名称，节点的`nodeValue`是属性的值

`attributes`属性在遍历一个元素的属性时非常有用：

```javascript
function outputAttributes(element) {
    var pairs = new Array(),
        attrName,
        attrValue,
        i, len;
    for(i = 0, len = element.attribute.length; i < len; i++) {
        attrName = element.attributes[i].nodeName;
        attrValue = element.attributes[i].nodeValue;
        if(element.attributes[i].specified) {   //针对IE浏览器
            pairs.push(attrName + "=\"" + attrValue + "\"");
        )
    }
    return pairs.join(" ");
}
```

> 不同浏览器的`attributes`返回属性的顺序不同；另外在IE浏览器中应该只接收已经被设置的属性

`创建元素节点`：使用`document.createElement()`方法可以创建新的元素节点，这个方法接受一个参数：要创建的元素标签名称（在HTML中不区分大小写），这个方法返回新创建的元素，这时就可以给元素添加属性、子节点及其他一些操作，但是新创建的元素并未被添加到当前文档中，因此在浏览器中不会有任何的效果；我们可以使用上节的一些方法将这个节点元素添加到`document`中

> 在IE浏览器中`cereateElement()`方法可以接受一个完整的元素字符串（包括属性）来创建这个元素节点

在遍历元素子节点时需要注意：IE浏览器中有效的元素才被认为是子节点；而其他浏览器中，如果文档中元素与元素之间有空白，这些空白被认为是一个文本节点；所以在取有效子节点时需要进行节点类型的判断：

```javascript
for(var i = 0, len = element.childNodes.length; i < len; i++) {
    if(element.childNodes[i].nodeType == 1) {   //元素节点
        //some action
    }
}
```

`Element`也有`getElementsByTagName()`方法，用来在某一个元素节点的后代节点中进行元素选取

**`Text类型`**  

`Text`类型表示文本节点，文本节点是可以按照字面解释的纯文本内容；文本中可以包含转义后的`HTML`字符，但是不能包含`HTML`代码；这类节点具有以下特征：

*   `nodeType`值为3
*   `nodeName`值为`"#text"`
*   `nodeValue`的值为节点所包含的文本
*   `parentNode`是一个`Element`
*   没有子节点

可以通过`Text`对象类型实例的`nodeVlaue`或者`data`属性取得节点中包含的文本，这两个属性的值相同，并且通过一个属性赋值可以通过另外一个属性获取；可以通过`length`属性获取文本节点的字符个数，`nodeValue`和`data`是一个字符串类型所以也有一个`length`属性并且和实例的`length`属性值相同

可以使用下面方法操作节点中的文本：

```javascript
appendData(text)    //将text添加到节点末尾
deleteData(offset, count)   //从offset开始删除count个字符
insertDate(offset, text)    //在offset的地方插入文本text
replaceData(offset, count, text)    //将从offset开始的count个字符替换为text
splitText(offset)   //从offset指定的位置将当前文本节点分成两个文本节点
substringData(offset, count)    //提取从offset开始到offset+count位置的子串
```

> 默认情况下，可以包含文本节点的元素最多只能有一个文本节点，而且这个节点必须有内容（可以是空格，但不能是空字符串）

> 如果这个文本节点存在于文档树中，那么对文本节点内容的修改会立刻反应出来；另外字符串中包含的`HTML`特殊字符会被编码

使用`document.createTextNode()`方法可以创建一个文本节点，这个方法接受一个参数：创建的文本节点的内容；返回新的文本节点实例，并且设置`ownerDocument`属性。同样除非手动的将这个节点加入到文档树中否则在浏览器中看不到这个节点：

```javascript
var element = document.createElement("div");
element.className = "message";

var textNode = document.createTextNode("hello world!");
element.appendChild(textNode);

document.body.appendChild(element);
```

> 也可以为一个元素节点添加多个文本子节点，这样显示时所有的文本子节点的文本会连接在一起，中间没有空格

如果文档中存在相邻的同胞文本节点或者相邻的文本子节点时，可以使用`Node`对象类型实例的`normalize()`方法来合并相邻的文本节点，这是合并后的文本节点内容`nodeValue`等于合并前所有文本节点内容连接起来的值

> 浏览器在解析文档时不会创建两个相邻的文本节点

`Text`对象类型实例提供了一个`splitText()`方法，这个方法按照参数指定的位置将文本节点分割成两个文本节点；原来的文本节点内容变为分割后的前一部分，返回的新的文本节点包含后一部分；这两个文本节点的`parentNode`相同

**`Comment类型`**  

注释在`DOM`中是使用`Comment`类型来表示的；`Comment`具有以下特征：

*   `nodeType`的值为8
*   `nodeName`的值为`"#comment"`
*   `nodeValue`的值为注释的内容
*   `parentNode`可能是`Document`或者`Element`
*   没有子节点

`Comment`类型继承自`Text`类型，除了`splitText()`方法之外，`Comment`类型大多数的属性和方法和`Text`类型相同；注释节点可以通过其父节点来访问；可以使用`docuemnt.createComment()`来创建一个注释节点类型实例

> 浏览器不会识别`</html>`元素之后的注释

**`CDATASection类型`**  

这个类型只存在于`XML`文档中，表示`CDATA`区域；这个类型有以下特征：

*   `nodeType`值为4
*   `nodeName`值为`"#cdata-section"`
*   `nodeVlaue`值为`CDATA`区域中的内容
*   `parentNode`可能是`Document`或者`Element`
*   没有子节点

这个类型同样继承自`Text`类型，因此拥有除了`splitText()`方法之外的所有字符操作方法

**`DocumentType类型`**  

`DocumentType`类型实例保存着与文档的`doctype`有关的信息，有以下特征：

*   `nodeType`的值为10
*   `nodeName`的值为`doctype`的名称
*   `nodeValue`的值为null
*   `parentNode`的值为Document
*   没有子节点

`IE`浏览器不支持这种类型（在`IE9`之后会给`document.doctyp`赋正确的值），`Chrome4`以后才支持这种类型；在`DOM1`中，这种类型对象不能被动态创建，只能在解析文档代码时创建；支持它的浏览器会`DocumentType`类型对象实例保存在`document.doctype`属性中

`DOM1`中描述了`DocmentType`类型对象实例的三个属性：`name` `entities` `notations `；这三个属性中目前只有`name`是有用的值为当前的文档类型，即`<!DOCTYPE>`元素中的内容；后面两个属性都是`NamedNodeMap`对象，都为空对象

**`DocumentFragment类型`**  

**`Attr类型`**  

元素的属性在`DOM`中是以`Attr`类型来表示的。在所有浏览器中都可以访问`Attr`类型的构造函数和原型。本质上，`Attr`类型对象实例就是存在于`Element`对象实例的`attributes`属性中的节点。属性节点有以下特征：

*   `nodeType`的值为2
*   `nodeName`的值为属性的名称
*   `nodeValue`属性的值
*   `parentNode`的值为null
*   在`HTML`没有子节点，在`XML`中子节点可以是`Text`或者`EntityReference`

尽管`Attr`也是一种节点类型，但却不被认为是`DOM`文档树的一部分。开发中通常使用`Element`的三个操作属性的方法来获取属性值，而不是使用这种节点类型实例

`Attr`类型对象实例有三个属性：`name` `value` `specified`分别用来表示属性名、属性值和是否是默认属性

使用`document.createAttribute()`方法可以创建一个属性节点类型实例：

```javascript
var attr = document.createAttribute("align");   //创建属性对象实例
attr.value = "left";    //为属性赋值
element.setAttribute(attr);     //为元素添加属性
alert(element.attributes["align"].value);   //"left" attributes所指向的NamedNodeMap中保存的是一个一个的属性节点
alert(element.getAttributeNode("align").value);     //"left"
alert(element.getAttribute("align"));   //"left"
```
> `getAttributeNode()`方法返回属性节点类型实例；而`getAttribute()`则返回指定的属性的值

##### DOM操作技术

**`动态脚本`**  

动态脚本是指在页面加载时不存在，但是在将来某一时刻通过修改`DOM`动态添加的脚本；创建动态脚本也有两种方式：插入外部文件和直接插入`JavaScript`代码

可以使用这样的方式创建一个`<script>`元素：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";

script.src = "client.js";
document.body.appendChild(script);
```
> 在最后一行执行之前，相应的js文件还没有被加载到页面中

> 使用上面方式加载js因为不确定何时js文件会加载完毕，所以需要配合某些事件进行处理

直接插入`JavaScript`代码的方式也可以动态的进行：

```javascript
function loadScriptString(code) {
    var script = document.createElement("script");
    script.type = "text/javascript";
    try {
        script.appendChild(document.createTextNode(code));
    } catch(ex) {
        script.text = code;     //IE浏览器将<script>视为特殊元素不允许访问其子节点，但是可以使用text属性访问
    }
    document.body.appendChild(script);
}

loadScriptString("function sayHi() { alert("hi"); }");
```
> 上面方式加载的代码会在全局作用域中执行；效果和在全局作用域中调用`eval()`函数时相同的

**`动态样式`**  

把`CSS`样式包含到页面中也有两种方式：使用`<link>`元素链接外部样式文件；使用`<style>`元素添加内部样式

使用代码创建`<link>`元素并添加到页面中的过程和动态脚本类似，但是要注意这个元素必须要添加到`<head>`元素中才能保证所有浏览器的行为一致

> 同样，加载外部样式与执行`JavaScript`代码的过程没有固定的次序，我们可以利用事件来对过程进行检测（通常是不必要的）

使用代码创建`<style>`元素并添加到页面中的方式和动态脚本也是类似的：

```javascript
var style = document.createElement("style");
style.style = "text/css";
try {
    style.appendChild(document.createTextNode("body {background-color:red}"));
} catch(ex) {
    style.styleSheet.cssText = "body {background-color:red}";
    //IE浏览器将<style>视为特殊元素不允许直接访问其子节点，但是可以通过styleSheet属性的cssText属性访问
}
var head = document.getElementsByTagName("head")[0];
head.appendChild(style);
```

**`操作表格`**  

如果使用原始的创建并添加节点的方式创建表格会比较麻烦；`HTMLDOM`为`<table>` `<tbody>` `<tr>`元素添加了一些属性和方法来方便表格的构建：

```javascript
<table>元素相关的属性和方法：

caption ：保存着对<caption>元素的指针
tBodies ：是一个<tbody>元素的HTMLCollection
tFoot ：保存着对<tfoot>元素的指针
tHead ：保存着对<thead>元素的指针
rows ：是一个表格中所有行的HTMLCollection
createTHead() ：创建<thead>元素，将其放入表格中，返回引用
createTFoot() ：创建<tfoot>元素，将其放入表格中，返回引用
createCaption() ：创建<caption>元素，将其放入表格中，返回引用
deleteTHead() ：删除<thead>元素
deleteTFoot() ：删除<tfoot>元素
deleteCaption() ：删除<caption>元素
deleteRow(pos) ：删除指定位置的行
insertRow(pos) ：向rows集合中指定位置插入一行

<tbody>元素相关的属性和方法：

rows ：保存着<tbody>元素中行的HTMLCollection
deleteRow(pos) ：删除指定位置的行
insertRow(pos) ：向rows集合中指定位置插入一行，返回对新插入行的引用

<tr>元素相关的属性和方法：

cells ：保存着<tr>元素中单元格的HTMLCollection
deleteCell(pos) ：删除指定位置的单元格
insertCell(pos) ：向cells集合中指定位置插入一个单元格，返回对新插入单元格的引用
```
使用上面提供的属性和方法创建表格的代码示例如下：

```javascript
//创建table
var table = document.createElement("table");
table.border = 1;
table.width = "100%";

//创建tbody
var tbody = document.createElement("tbody");
table.appendChild(tbody);

//创建第一行
tbody.insertRow(0);
tbody.rows[0].insertCell(0);
tbody.rows[0].cells[0].appendChild(document.createTextNode("Cell 1,1"));
tbody.rows[0].insertCell(1);
tbody.rows[0].cells[1].appendChild(document.createTextNode("Cell 2,1"));

//创建第二行
tbody.insertRow(1);
tbody.rows[1].insertCell(0);
tbody.rows[1].cells[0].appendChild(document.createTextNode("Cell 1,2"));
tbody.rows[1].insertCell(1);
tbody.rows[1].cells[1].appendChild(document.createTextNode("Cell 2,2"));
```

**`使用NodeList`**  

`NodeList`及相似的`NamedNodeMap` `HTMLCollection`，这三个集合都是动态的，每当文件结构发生变化时，这些数据结构总是实时得到更新

要迭代一个`NodeList`时最好使用length属性初始化第一个变量：

```javascript
var divs = document.getElementsByTagName("div"),
    i, len, div;
    
//注意虽然divs是在循环外面获取的，但是这个divs是动态更新的，所以如果将i变量直接和divs.length属性进行对比，则i永远小于后者，会导致死循环
for(i = 0, len = divs.length; i < len; i++) {
    div = document.createElement("div");
    document.body.appendChild(div);
}
```
