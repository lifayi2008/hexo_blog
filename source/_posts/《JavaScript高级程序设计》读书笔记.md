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

#### DOM扩展

本章介绍了一些已经被标准化的`DOM`扩展：`Selectors API（选择符API）` `HTML5` `Element Traversal（元素遍历）`

##### 选择符API

选择符API提供了根据`CSS`选择符选择与某一个模式匹配的`DOM`元素的原生API；与以往程序员自己用`JavaScript`实现的库相比，这极大的改善了性能

`Selectors API Level 1`的核心是两个方法：`querySelector()` `querySelectorAll()`

**`querySelector()`**

这个方法接受一个`CSS`选择符，返回与该模式匹配的第一个元素，如果没有找到相应的元素则返回`null`：

```javascript
var body = document.querySelector("body");
var myDiv = document.querySelector("#myDiv");
var selected = document.querySelector(".selected");
var img = document.body.querySelector("img.button");
```

**`querySelectorAll()`**

这个方法接受的参数和`querySelector()`相同，但是返回值是一个`NodeList`实例，表示所有匹配到的元素节点

```javascript
var ems = document.getElementById("myDiv").querySelectorAll("em");
var selecteds = document.querySelectorAll(".selected");
var strongs = document.querySelectorAll("p strong");

//使用for循环遍历返回的NodeList对象
var i, len, strong;
for(i = 0, len = strongs.length; i < len; i++) {
    strong = strong[i];     //或者strong.item(i)
    strong.className = "important";
}
```

**`上面两个方法的共同点`**

可以调用这两个方法的元素类型包括：`Document` `DocumentFragment` `Element`；通过`Document`对象类型实例调用这个方法时，会在文档元素的范围内查找；通过`Element`对象类型实例调用这个方法时，只会在该元素后代的元素中查找

如果传入的参数是`CSS`不支持的选择符，则抛出异常

**`matchesSelector()`**

这个是`Selectors API Level 2`为`Element`元素对象添加的方法；这个方法同样接收一个`CSS`选择符，如果选择符对应的元素存在则返回`true`，否则返回`false`

这个方法可能还没有被大多数浏览器实现，但是都提供了一个相同功能的另外一个方法：`msMatchesSelector()` `mozMatchesSelector()` `webkitMatchesSelector()`；在使用中可以用一个函数包装一下

##### 元素遍历

IE浏览器和其他浏览器对待元素之间空格不同，前者会忽略空格，后者会认为是一个文本节点，所以导致了`ChildNodes`和`firstChild`属性的值在两种浏览器中不同；`DOM`扩展规范提供了几个访问这些元素的一致的属性：

*   `childElementCount`：返回子元素的个数（`Element`类型的节点）
*   `firstElementChild`：指向第一个元素
*   `lastElementChild`：指向最后一个元素
*   `previousElementSibling`：指向前一个同辈元素
*   `nextElementSibling`：指向后一个同辈元素

使用上面这些属性，在取一个元素的最后一个子元素时就不必判断返回的节点是注释或者文本节点了

##### HTML5

**`与类相关的扩充`**

`HTML5`增加了一些`class`相关的API，致力于简化`CSS`类的用法：

*   `getElementsByClassName()`：可以在`Document`和`Element`对象实例上调用这个方法，这个方法是通过既有`DOM`实现的；这个方法接受一个字符串表示的一个或者多个类名，返回节点或者子节点中带有指定类名的所有元素的`NodeList`

*   `classList`：这个属性在操作元素的类名时有用，如果类名中包含空格（组合类），并且想要删除其中一个类名时需要使用`className`属性获取然后分割然后再组合字符串；在`HTML5`中所有元素都有一个`classList`属性，这个属性是一个新的集合类型`DOMTokenList`的实例

    `DOMTokenList`和其他集合类型类似，有一个`length`属性表示集合元素的数量，要取得每一个元素可以使用`item()`方法或者使用`[]`语法；此外这个新类型还有额外的几个方法：`add(value)`将给定的字符串值添加到列表中，`contains(value)`如果给定的字符串值存在则返回`true`，`remove(value)`从列表中删除指定的字符串值，`toggle(value)`如果列表中已经存在则删除，如果不存在则添加
    
**`焦点管理`**

*   `document.activeElement`：这个属性始终指向当前获得焦点的元素；文档加载期间这个属性的值为`null`；文档加载完成后这个属性的值是`document.body`

*   `document.hasFocus()`：这个方法用于检测一个元素是否获取到了焦点

**`HTMLDocument`**

`HTML5`为这个对象类型添加了一些属性和方法，虽然是刚刚添加进标准，但是这些属性在一些浏览器中已经存在了很长时间了

*   `document.readyState`：这个属性有两个可能的值`loading` `complete`分表表示正在加载文档和文档已经加载完成

*   `document.compatMode`：这个属性有两个可能的值`CSS1Compat` `BackCompat`分别表示浏览器处于标准模式和兼容模式

*   `document.head`：这个属性引用文档的`<head>`元素；不过在开发中最好这样写：

        var head = document.head || document.getElementByTagName("head")[0];
    
**`字符集属性`**

可以使用`document.charset`获取或者设置的当前页面字符集；这个和在页面中使用`<meta>`元素设置有一样的效果

另外还有一个`document.defaultCharset`属性表示根据默认浏览器及操作系统的设置

**`自定义数据属性`**

`HTML5`规定可以为元素添加非标准的属性，自定义的属性应该以`data-`开始；添加了自定义属性之后，可以通过元素的`dataset`属性来访问自定义的属性，这个属性是一个`DOMStringMap`对象类型的一个实例；`DOMStringMap`对象中的属性表示这个元素的自定义属性，但是这里面的属性没有`data-`前缀

**`插入标记`**

插入标记可以在不使用`DOM`接口的情况下创建元素（使用`html`字符串），这在需要大量的插入元素时很高效

*   `innerHTML`属性：在读模式下，这个属性返回调用元素的所有子节点（包括元素、注释和文本）的`HTML`字符串（IE和Opera会将标记转换为大写）；在写模式下，浏览器会根据为这个属性赋的值创建一个新的`DOM`树，然后使用这个数替换调用这个属性的元素的所有子节点

    > 为这个属性设置的字符串值中包括的`HTML`标记会被正确的解析，并且创建一个`DOM`树
    
    > 使用这个属性为`<script>` `<style>`元素设置值时会有一些问题，详细信息参考`《JavaScript高级程序设计》p295`
    

*   `outerHTML`属性：这个和`innerHTML`属性的区别是包括调用这个属性的元素

*   `insertAdjacentHTML()`：这个方法接受两个参数：要插入的位置和要插入的`HTML`元素字符串；第一个参数可以是下面的值：

        beforebegin     //在当前元素之前插入一个紧邻的同辈元素
        afterend        //在当前元素之后插入一个紧邻的同辈元素
        afterbegin      //插入到当前元素第一个子元素的位置
        beforeend       //插入到当前元素最后一个子元素的位置

    > 如果浏览器无法解析第二参数则会抛出错误

> 插入标记可能会占用大量的内存，所以多次的调用相关属性和方法（比如在循环中使用，最好是在循环中组件字符串，然后一次性的赋值给对应的属性）

**`scrollIntoView()`**

这个方法可以在所有元素上调用，这个方法通过滚动浏览器窗口或者某个容器元素，使调用的元素可以出现在视口中；如果传入一个`true`参数或者不传入参数则调用这个方法的元素的顶部会和视口顶部对齐；如果传入一个`false`参数则会进行底部对齐

##### 专有扩展

这些特性目前还未纳入`HTML5`规范，详细信息参考`《JavaScript高级程序设计》p298`

#### DOM2和DOM3

`DOM2`包括如下模块：

*   `DOM Level 2 Core`：在1级核心的基础上构建，为节点添加了更多的属性和方法
*   `DOM Level 2 Views`：为文档定义了基于样式信息的不同视图
*   `DOM Level 2 Events`：说明了如何使用事件与`DOM`文档交互
*   `DOM Level 2 Style`：定义了如何以编程方式来访问和改变`CSS`样式信息
*   `DOM Level 2 Traversal and Range`：遍历`DOM`文档和选择其特定部分的新接口
*   `DOM Level 2 HTML`：在1级`HTML`基础上构建，添加了更多的属性方法和接口

> `DOM 3`增加了`XPATH`模块和`Load and Save`模块

#### 事件

`JavaScript`和`HTML`之间的交互是以事件的方式进行的；事件就是文档或者浏览器窗口中发生的一些特定的交互瞬间。`DOM2`开始对浏览器中的事件进行规范，几乎所有的浏览器都实现了`DOM2`级事件的核心模块部分（IE8除外）

下面是事件中的一些需要理解的核心部分：

##### 事件流

`IE`和`Netscape`使用的分别是`事件冒泡流`和`事件捕获流`

**`事件冒泡`**

即事件开始由最具体的元素接收，然后逐级向上传播到较为不具体的节点；比如在一个`<body>`元素中有一个`<div>`元素上发生了点击事件，则接收到事件的节点顺序依次是：`<div>` `<body>` `<html>` `Document`

> 目前几乎所有浏览器都支持事件冒泡，在实际开发中建议总是使用`事件冒泡`，在需要的时候才使用`事件捕获`

**`DOM事件流`**

`DOM2`级事件规定事件流包括三个阶段：`事件捕获阶段` `处于目标阶段` `事件冒泡阶段`；在`DOM`事件流中，实际的目标节点在捕获阶段不会收到事件，事件在目标节点的父节点一级就停止了；然后就是`处于目标`阶段，这时事件在节点（元素）上发生（这个阶段被看做是事件冒泡阶段的一部分）；最后事件冒泡发生，事件又传播会文档

> `DOM2`级规范明确指出捕获阶段不会涉及事件目标，但是最新版本的所有浏览器都会在捕获阶段触发事件对象上的事件，这样就会有两个机会在目标对象上操作事件

> `IE8`不支持`DOM事件流`

##### 事件处理程序

事件处理程序就是为事件绑定的用来响应事件的函数；事件处理程序的名字通常为`on + 事件名`的形式；可以使用几种方法来指定事件处理程序

**`HTML事件处理程序`**

某一个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的`HTML`属性来指定，属性的值应该是能够执行的`JS`代码：

    <input type="button" value="Click Me" onclick="alert('Clicked')" />

> 不能在属性值中指定未经转义的`HTML`语法字符：`&` `"` `<` `>`（可以使用`HTML`实体来代替）

*   也可以将元素事件属性的值指定为一个在别处定义的**函数名的调用**（这实际上还是`JS`代码调用）：

        <script type="text/javascript">
            function showMessage() {
                alert("Hello world!");
            }
        </script>
        <input type="button" value="Click Me" onclick="showMessage()" />
        
> 在属性值中执行的代码可以访问全局作用域中的属性和方法

在事件属性值中调用的函数可以访问如下属性：`event`表示事件对象本身；`this`等于事件目标元素（因为事件处理程序是在元素的作用域范围内执行的）

> 可以通过扩展作用域的方式来直接在事件属性值中访问其他作用域中的属性

*   使用`HTML`事件处理程序有以下缺点：用户点击按钮触发事件时相应的`JS`代码可能还未加载完成；扩展事件处理程序作用域链的行为在不同的浏览器中不一致；`HTML`代码和`JS`代码耦合过紧

**`DOM0级事件处理程序`**

这种事件处理程序要先获取要绑定事件处理程序的元素，然后通过为其相应的事件属性赋值：

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function () {
    alert("Clicked");
};
```
> 这种方式指定的事件处理程序同样在元素的作用域范围内执行，可以在程序中使用`this`访问元素本身的属性和方法

> 这种方式指定的事件处理程序同样存在`HTML`事件处理程序中的第一个问题；另外`DOM0`只能添加一个事件处理程序

使用`btn.onclick = null`可以删除事件处理程序；`HTML`事件处理程序也可以使用这种方式删除

**`DOM2级事件处理程序`**

`DOM2`增加了两个方法用于添加和删除事件处理程序：`addEventListener()` `removeEventListener()`；所有的`DOM`节点都包含这两个方法，他们接收单个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值（如果是`true`则表示在捕获阶段调用，如果是`false`则表示在冒泡阶段调用事件处理程序）：

```javascript
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function() {  //使用这种方法时事件名前面不用加on
    alert(this.id);
}, false);
```
> 这样添加的事件处理程序也是在元素的作用域中执行

使用`DOM2`事件处理程序可以为一个元素添加多个事件处理程序，事件被触发时这些事件处理程序依次执行

使用`addEventListener()`方法添加的事件处理程序只能通过`removeEventListener()`移除，并且传入的参数要一致（所以通过匿名函数创建的事件处理程序无法移除）

> 通常将事件处理程序添加在事件流冒泡阶段，这样可以最大限度兼容所有浏览器；最好在只需要事件到达目标之前截获它的时候才将事件添加到捕获阶段

> `IE9`和其他浏览器的较新的版本都支持`DOM2`级

**`IE事件处理程序`**

`IE`使用另外两个方法来添加事件处理程序：`attachEvent()` `detachEvent()`；这两个方法也接受相同的两个参数：事件处理程序名称和事件处理程序函数；使用这个方法添加的事件处理程序会添加到冒泡阶段

使用这个方法添加的事件处理程序和`DOM0` `DOM2`两种的主要区别是，事件处理程序被添加到全局作用域中（处理函数中的`this`对象等同于`window`）

> `IE`事件处理程序在添加事件时要在事件名前面加上`on`

使用`attacheEvent()`方法也可以为一个元素添加多个事件处理程序；与`DOM2`不同的是添加的多个事件处理程序按照加入的顺序反向执行

使用`attacheEvent()`方法添加的事件处理程序也可以使用`detachEvent()`方法移除，后者要传入的参数和创建事件处理程序时一致

**`跨浏览器的事件处理程序`**

可以使用如下代码在跨浏览器中处理事件添加和移除问题：

```javascript
var EventUtil = {
    addHandler : function(element, type, handler) {
        if(element.addEventListener) {  //dom2事件
            element.addEventListener(type, handler, false);
        } else if(element.attachEvent) {    //ie事件
            element.attachEvent("on" + type, handler);
        } else {                        //dom0事件
            element["on" + type] = handler;
        }
    },
    
    removeHandler : function(element, type, handler) {
        if(element.removeEventListener) {
            element.removeEventListener(type, handler, false);
        } else if(element.detachEvent) {
            element.detachEvent("on" + type, handler);
        } else {
            element["on" + type] = null;
        }
    }
};
```

##### 事件对象

在触发`DOM`上的某一个事件时，会产生一个事件对象，这个对象中包含着所有与事件相关的信息，比如：和事件相关的元素、事件类型及和特定事件相关的信息

**`DOM中的事件对象`**

不论建立事件处理程序时使用哪种方法，`DOM`都会将一个`event`对象传入到事件处理程序中：

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function(event) {
    alert(event.type);      //"click"
};

btn.addEventListener("click", function(event) {
    alert(event.type);      //"click"
}, false);
```

不同的事件处理程序，可用的属性和方法也不同。下面是所有事件类型都有的成员：

属性/方法 | 类型 | 读/写 | 说明
---|---|---|---
`bubbles` | Boolean | 只读 | 表明事件是否冒泡
`cancelable` | Boolean | 只读 | 表明是否可以取消事件的默认行为
`target` | Element | 只读 | 事件的目标
`currentTarget` | Element | 只读 | 其事件处理程序当前正在处理事件的那个元素
`defaultPrevented` | Boolean | 只读 | 为`true`表示已经调用了`preventDefault()`（DOM3新增）
`detail` | Integer | 只读 | 与事件相关的细节信息
`eventPhase` | Integer | 只读 | 调用事件处理程序的阶段：1（捕获）2（处于目标）3（冒泡）
`preventDefault()` | Function | 只读 | 取消事件的默认行为（需要`cancelable`为`true`）
`stopImmediatePropagation()` | Function | 只读 | 取消事件的进一步捕获或者冒泡，同时阻止任何事件处理程序被调用（DOM3新增）
`stopPropagation()` | Function | 只读 | 取消事件的进一步捕获或者冒泡（需要`bubbles`为`true`）
`trusted` | Boolean | 只读 | 为`true`表示事件是浏览器生成的，否则表示通过`JS`创建的（DOM3新增）
`view` | AbstractView | 只读 | 与事件关联的抽象视图。等同于发生事件的`window`对象

如果将事件处理程序指定给了目标元素，则`this` `currentTarge` `target`包含相同的值；因为事件流的存在所以这三个值可能不同，比如如果给`<body>`元素绑定了事件，这时如果我们点击`<body>`中的一个按钮时，`target`为这个按钮元素，而`this`和`currentTarget`为`<body>`元素

> 只有在事件处理程序执行期间，`event`对象才会存在，一旦事件处理程序执行完成，对象就会被销毁

**`IE中的事件对象`**

要访问`IE`中的`event`对象有几种不同的方式，取决于指定事件处理程序的方法：

*   使用`DOM0`级事件方法添加的处理程序中，`event`对象作为`window`对象的一个属性存在：`window.event`
*   使用`attachEvent()`方法添加的事件处理程序中，`event`被作为参数传入到事件处理函数中
*   通过`HTML`属性指定的事件处理程序，可以通过名为`event`的变量访问`event`对象

根据事件类型不同，`event`对象中有不同的属性和方法，但是所有类型的事件都有如下属性：

属性/方法 | 类型 | 读/写 | 说明
---|---|---|---
`cancelbubble` | Boolean | 读/写 | 默认值为`false`，将其设置为`true`可以取消事件冒泡（和`stopPropagation()`作用相同）
`returnValue` | Boolean | 读/写 | 默认为`true`，将其设置为`false`可以取消事件的默认行为（和`preventDefault()`作用相同）
`srcElement` | Element | 只读 | 事件的目标（和`src`相同）
`type` | String | 只读 | 被触发的事件类型

事件属性在使用不同事件注册方法时有不同的值，详细参考`<<JavaScript高级程序设计>>p351`

**`跨浏览器的事件对象`**

可以在实际开发中编写如下代码来解决跨浏览器事件对象的访问：

```javascript
var EventUtil = {
    addHandler : function() {},     //前面的章节中已经实现
    removeHandler : function() {},  //前面的章节中已经实现
    
    getEvent : function(event) {
        return event ? event : window.event;
    },
    
    getTarget : function(event) {
        return event.target || event.srcElement;
    },
    
    preventDefault : function(event) {
        if(event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },
    
    stopPropagation : function(event) {
        if(event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```

##### 事件类型

`DOM3`级规定了如下几种事件类型：

*   `UI事件`：当用户与界面上的元素交互时触发
*   `焦点事件`：当元素获得或者失去焦点时触发
*   `鼠标事件`：当用于通过鼠标在页面上执行操作时触发
*   `滚轮事件`：当使用鼠标滚轮时触发
*   `文本事件`：当在文档中输入文本时触发
*   `键盘事件`：当用户通过键盘在页面上执行操作时触发
*   `合成事件`：当`IME`输入字符时触发
*   `变动事件`：当底层`DOM`结构发生变化时触发
*   `变动名称事件`：当元素或者属性名变动时触发（已废弃）

**`UI事件`**

`UI事件`是指那些不一定与用户操作有关的事件，在`DOM`中保留是为了向后的兼容；这些事件除了`DOMActive`在`DOM2`级事件中都归为`HTML`事件

要确定浏览器是否支持`DOM2`级事件中的这些`HTML`事件可以使用如下代码：

```javascript
var isSupproted = document.implementation.hasFeature("HTMLEvents", "2.0");
```

要确定浏览器是否支持`DOM3`级事件中的`UI`事件可以使用如下代码：

```javascript
var isSupproted = document.implementation.hasFeature("UIEvent", "3.0");
```

*   `DOMActive`：表示元素已经被用户操作激活；不建议使用这个事件

*   `load`：当页面完全（包括图像、`Javascript`、`CSS`等外部资源）加载后在`window`上触发，当所有框架都加载完毕时在框架集上面触发，当图像加载完毕时在`<img>`元素上触发，或者当嵌入的内容加载完毕时在`<object>`元素上面触发

    推荐的定义`onload`事件处理程序的方式如下：
    
    ```javascript
    EventUtil.addHandler(window, "load", function(event) {
        alert("Loaded!");
    });
    //这里的event不会包含事件相关的信息，但是兼容dom的浏览器中，event.target会被设置为document
    ```
    
    另外一种方式是直接在相关元素上面加入`onload`属性，属性的值为事件处理程序函数：
    ```javascript
    <img src="smile.gif" onload="alert('Image loaded.')">
    ```
    在创建一个`<img>`元素时就可以为其指定一个事件处理程序：
    
    ```javascript
    EventUtil.addHandler(window, "load", function() {   //在向一个页面中加入元素时，必须等待页面加载完成，否则会出错
        var image = document.createElement("img");
        EventUtil.addHandler(image, "load", function() {
            event = EventUtil.getEvent(event);
            alert(EventUtil.getTarget(event).src);
        });
        document.body.appendChild(image);
        image.src = "smile.gif";
        //新元素在指定了src属性之后就会开始下载资源，而不是在加入到文档之后才开始
    });
    ```
    `firefox`浏览器支持在`<script>`元素上指定`load`事件；`IE` `Opera`浏览器支持在`<link>`元素上指定`load`事件

*   `unload`：当页面完全卸载后在`window`上触发，当所有框架都卸载后在框架上面触发，或者当嵌入的内容卸载完毕后在`<object>`元素上面触发

    指定`onunload`事件处理程序也可以使用如下两种方式：
    
    ```javascript
    EventUtil.addHandler(window, "unload", function(event) {
        alert("Unloaded");
    });
    //这里的event不会包含事件相关的信息，但是兼容dom的浏览器中，event.target会被设置为document
    
    <body onunload="alert('Unloaded!')">
    ```
    > 一旦页面卸载之后，再对页面执行`dom`操作就会出现错误

*   `abort`：在用户停止下载过程时，如果嵌入的内容没有加载完，则在`<object>`元素上面触发

*   `error`：当发生`JavaScript`错误时在`window`上面触发，当无法加载图像时在`<img>`元素上面触发，当无法加载嵌入的内容时`<object>`元素上面触发，或者当有一个或者多个框架无法加载时在框架上面触发

*   `select`：当用户选择文本框（`<input>` `textarea`）中的一或者多个字符时被触发

*   `resize`：当窗口或者框架的大小变化时在`window`或该框架上触发

    可以使用在`window`上面添加事件处理程序或者在`<body>`元素添加`onresize`属性的方式来添加
    
    另外`firefox`浏览器只有在用户停止调整窗口大小时才会触发这个事件；其他浏览器只要用户调整窗口大小一个像素就会触发，然后在用户调整的过程中会不断的触发（所以应该尽可能的保持这个事件处理程序的简单）
    
    > 窗口的最大化或者最小化也会触发这个事件

*   `scroll`：当用户滚动带滚动条的元素中的内容时，在该元素上面触发；`<body>`元素中包含加载页面的滚动条

    在混杂模式下可以通过`<body>`元素的`scrollLeft`和`scrollTop`来监控到这一变化；在标准模式下除了`Safari`浏览器，则需要`<html>`元素的上述属性来取得窗口滚动变化：
    
    ```javascript
    EventUtil.addHandler(window, "scroll", function() {
        if(document.compatMode == "CSS1Compat") {
            alert(document.documentElement.scrollTop);
        } else {
            alert(document.body.scrollTop);
        }
    });
    ```
    > 这个事件也会在文档滚动期间重复触发，所以应该尽量保持事件处理程序的简单
    
**焦点事件**

利用这些事件和`document.hasFocus()`方法以及`document.acitveElement`属性配合，可以跟踪用户的行为

*   `blur`：在元素失去焦点时触发，这个事件不会冒泡；所有浏览器都支持
*   `focus`：在元素获得焦点时触发，这个事件不会冒泡；所有浏览器都支持

*   `focusin`：在元素获得焦点时触发，这个事件与`HTML`事件`focus`等价，但它会有冒泡行为，`DOM3`级使用这个事件
*   `focusout`：在元素失去焦点时触发，和`HTML`事件`blur`功能相同，`DOM3`级使用这个事件

*   `DOMFocusIn`：在元素获得焦点时触发。这个事件与`HTML`事件`focus`等价，但它会有冒泡行为，只有`Opera`支持
*   `DOMFocusOut`：在元素失去焦点时触发，和`HTML`事件`blur`功能相同，只有`Opera`支持

当焦点从页面的一个元素移动到另外一个元素时，会依次触发如下事件：

1.  `focusout`在失去的焦点的元素上触发
2.  `focusin`在获得焦点的元素上触发
3.  `blur`在失去焦点的元素上触发
4.  `DOMFocusOut`在失去焦点的元素上触发
5.  `focus`在获得焦点的元素上触发
6.  `DOMFocusin`在获得焦点的元素上触发

要确定浏览器是否支持这些事件，可以使用如下代码：

```javascript
var isSupported = document.implementation.hasFeature("FocusEvent", "3.0");
```

**`鼠标与滚轮事件`**

`DOM3`级事件中定义了9个鼠标事件：

*   `click`：在用户单击主鼠标按钮（默认左键）或者按下回车键时触发；这意味着`onclick`事件处理程序可以通过键盘或者鼠标触发执行
*   `dbclick`：在用户双击主鼠标按钮时触发
*   `mousedown`：用户按下任意鼠标按钮时触发。不能通过键盘触发这个事件
*   `mouseup`：用户释放鼠标按钮时触发。不能通过键盘触发这个事件
*   `mouseenter`：在鼠标光标从元素外部首次移动到元素范围之内时触发。这个事件不冒泡，而且光标移动到后代元素时不触发（`DOM2`不支持）
*   `mouseleave`：在位于元素范围内的光标移动到元素范围之外时触发。这个事件不冒泡，而且光标移动到后代元素时不触发（`DOM2`不支持）
*   `mousemove`：当鼠标在元素范围内移动时重复触发。不能通过键盘触发这个事件
*   `mouseout`：在鼠标位于一个元素上方，然后将其移入另外一个元素时触发；另外一个元素可能是这个元素的子元素，也可能位于这个元素的外部。不能通过键盘触发这个事件
*   `mouseover`：在鼠标指针位于一个元素外部，然后将其首次移入另外一个元素的边界之内时触发。不能通过键盘触发这个事件

页面上的所有元素支持鼠标事件；除了`mouseenter`和`mouseleave`所有鼠标时间都会冒泡，也可以被取消，取消这些事件会影响浏览器（中其他事件）的默认行为，比如取消`mouseup` `mousedown`其中的一个事件，则就不会触发`click`事件；如果有代码阻止连续两次触发`click`事件，那就不会触发`dbclick`事件；这四个事件的触发顺序始终如下：`mousedown` `mouseup` `click` `mousedown` `mouseup` `click` `dbclick`

可以使用如下代码检查浏览器是否支持支持以上`DOM2`级相关的事件（出`dbclick` `mouseenter` `mouseleave`）:

```javascript
var isSupproted = document.implementation.hasFeature("MouseEvents", "2.0");
```

要检查浏览器是否支持上面的所有事件可以使用如下代码：

```javascript
var isSupported = document.implementation.hasFeature("MouseEvent", "3.0");
//dom3级中事件的名称是MouseEvent
```

获取鼠标事件发生时，鼠标指针在**视口**中的位置可以使用事件对象的`clientX` `clientY`属性（所有浏览器都支持）：

```javascript
var div = document.getElementById("myDiv");
EventUtil.addHandler(div, "click", function(event) {
    event = EventUtil.getEvent(event);
    alert("Client coordinates: " + event.clientX + "," + client.clientY);
});
```

获取鼠标事件发生时，鼠标指针在**页面**中的位置可以使用事件对象的`pageX` `pageY`属性；`IE8`不支持这两个属性，但是可以通过`document.body`或者`document.documentElement`的`scrollLeft`和`scrollTop`两个属性加上`clientX`和`clientY`计算出来

获取鼠标事件发生时，鼠标指针相对于电脑屏幕的位置使用事件对象的`screenX` `screenY`属性

如果用户在点击鼠标按钮的同时按下了某个键盘控制键（`Shift` `Ctrl` `Alt` `Win/Cmd`）键时，可以更改鼠标事件原本的行为；`DOM`添加了四个事件对象属性用来判断和控制：`shiftKey` `ctrlKey` `altKey` `metaKey`，这四个属性都是布尔值，当相应的按键被按下了则返回`true`否则返回`false`

> `IE8`及之前的版本不支持`metaKey`

在发生`mouseover` `mouseout`事件时，会有相关元素的概念，这两个事件会涉及把鼠标指针从一个元素的边界之内移动到另外一个元素的边界之内；对于`mouseover`而言，事件的主要目标是获得光标的元素，相关元素就是失去光标的那个元素；对于`mouseout`而言，事件的主要目标是失去光标的元素，而相关元素就是获得光标的那个元素；这些相关元素可以使用`event`对象的`relatedTarget`属性来获取到

```javascript
var EventUtil = {
    getRelatedTarget : function(event) {
        if(event.relatedTarget) {
            return event.relatedTarget;
        } else if(event.toElement) {
            return event.toElement;
        } else if(event.fromElement) {
            return event.fromElement;
        } else {
            return null;
        }
    },
};
```

> 只有`mouseover` `mouseout`事件才有这个属性，其他事件的这个属性值为`null`；`IE8`使用`fromElement` `toElement`属性获取相应的值

鼠标事件`event`对象中还存在一个`button`属性用来表示按下的鼠标按钮：0表示主按钮，1表示中间按钮，2表示次按钮

`DOM2`级事件还提供了`event.detail`属性，这个属性表示在给定位置上发生了多少次单击，这个属性从1开始每次单击都会递增；如果在`mousedown`和`mouseup`之间移动了位置，则`detail`重置为0

**`滚轮事件`**这个事件可以在任意元素上面触发，最终会冒泡到`document` `window`；这个事件的事件对象除了包含鼠标事件的信息之外还包括`wheelDelta`属性，当用户向前滚动滚轮时，值是120的倍数，当用户向后滚动滚轮时，值是-120的倍数

**`键盘与文本事件`**

`DOM3`级事件为键盘事件制定了规范，之前主要是是`DOM0`级相关的规范；`DOM`键盘事件有以下三个：

*   `keydown`当用户按下键盘上的任意键时触发，如果按住不放的话会重复触发此事件
*   `keypress`当用户按下键盘上的字符键时触发，如果管按住不放的话会重复触发此事件
*   `keyup`当用户释放按键时触发

这三个事件的触发顺序如下：`keydown` --- `keypress`（如果用户按下字符键） --- `keyup`；如果用户一直按着不放，则会重复触发前两个事件，知道松开按键时会触发最后一个事件

在发生`keydown`和`keyup`事件时，`event`对象的`keyCode`属性会包含一个与键盘上特定按键对应的代码；如果是数字和字符键则和`ASCII`码对应（shift+字符也被识别为小写）

> 其他非字符数字按键的编码参考`《JavaScript高级程序设计》`

在所有浏览器中，按下能够输入和被删除的字符按键都会触发`keypress`事件，这个事件对象中包含一个`charCode`属性（`IE8`不支持），属性值为按下的那个按键所代表的字符的`ASCII`码，而`keyCode`属性的值根据浏览器而不同（`IE8`和`Opera`中为对应的`ASCII`码，其他的为0），所以可以编写如下跨浏览器代码：

```javascript
var EventUtil = {
    getCharCode : function(event) {
        if(typeof event.charCode == "number") {
            return event.charCode;
        } else {
            return event.keyCode;
        }
    },
};
```

`DOM3`中为键盘事件对象添加了`key` `char` `keyIdentifier` `location`属性和`getModifierState()`方法，但是现在大多数浏览器未实现，所以不建议使用

还有一个在文本被插入文本框之前触发的文本事件：`textInput`；这个事件和`keypress`的不同之处在于只有可编辑区域才会触发前者，而任何可以获取焦点的元素都可以触发后者；另外，前者在只有在按下数字字符按键时才会触发，而后者在按下那些能影响文本显示的按键时也会触发（退格键）

`textInput`事件对象包含一个`data`属性，这个属性值就是按下的字符按键，这个事件可以识别输入的大写字符；另外还有一个`inputMethod`属性表示把文本输入到文本框中的方式（只有`IE`支持，详细信息参考`《JavaScript高级程序设计》`）

**`复合事件`**

这是`DOM3`中新增的一类事件用于处理`IME`的输入序列，`IME`表示输入法编辑器，可以让用户输入在键盘上没有的字符

**`变动事件`**

`DOM2`级变动事件能在`DOM`文档结构发生变化时给出通知，`DOM2`级包含如下変动事件：

*   `DOMSubtreeModified`在`DOM`结构发生任何变化时触发，这个事件在其他事件发生后都会触发
*   `DOMNodeInserted`在节点被作为子节点插入到另外一个节点时触发
*   `DOMNodeRemoved`在节点从其父节点上移除时触发
*   `DOMNodeInsertedIntoDocument`在一个节点被直接插入文档或者通过子树间接插入文档时被触发，这个事件在`DOMNodeInserted`被触发之后一定会被触发
*   `DOMNodeRemovedFromDocument`在一个节点被直接移除或者通过子树间接移除时触发，这个事件在`DOMNodeRemoved`被触发之后一定会被触发
*   `DOMAttrModified`在文档属性被改变时触发
*   `DOMCharacterDataModified`在文本节点值发生变化时触发

检测浏览器是否支持变动事件可以使用如下代码：

```javascript
var isSupproted = document.implementation.hasFeature("MutationEvents", "2.0");
```
............................................

............................................

**`HTML5事件`**

#### 表单脚本

##### 表单的基础知识

在`JavaScript`中表单对应的是`HTMLForm-Element`类型，这个类型继承了`HTMLElement`，因此和其他元素类型具有相同的属性和方法；除此之外还有如下属性和方法：

```javascript
acceptCharset   服务器能够处理的字符集；等价于HTML中的accept-charset属性
action  表单提交的URL；等价于HTML中的action属性
elements    表单中所有控件集合（HTMLCollection类型）
enctype     请求的编码类型；等价于HTML中的enctype属性
length      表单中控件的数量
method      要发送的请求类型；等价于HTML中的method属性
name        表单的名称；等价于HTML中的name属性
reset()     将表单所有域重置为默认值的方法
submit()    提交表单的方法
target      用于发送请求和接收相应的窗口名称；等价于HTML的target属性
```

> 下文中的字段是指`HTML`元素在`HTMLForm-Element`对象类型中的表示，所以元素和字段混用

*   要取得页面中的表单元素，除了可以使用`getElementById()`方法之外，还可以使用`document.forms`属性，这个属性包含页面中的所有表单的集合，在这个集合中可以通过索引或者表单的`name`属性来访问特定的表单：

    ```javascript
    var firstForm = document.forms[0];
    var myForm = document.forms["form1"];
    //不推荐使用document.form2的方式取得表单对象
    ```

*   在HTML中定义一个提交按钮可以使用下面的三种方式：

    ```javascript
    <input type="submit" value="Submit Form" />
    <button type="submit">Submit Form</button>
    <input type="image" src="test.gif" />
    ```

    只要表单中存在上面一种提交按钮，那么当表单在获取到焦点时按下回车键则会提交这个表单，如果表单里面没有提交按钮则按下回车键不会提交；这种方式提交表单时，浏览器会在向服务器发送请求之前触发`submit`事件，我们可以在这个事件处理程序中做一些控制

    在`JavaScript`中也可以调用`submit()`方法提交表单，这种方式无需表单的提交按钮，并且不会触发表单的`submit`事件

    提交表单时可能出现的问题是重复提交，解决这种问题可以使用两种方式：禁用提交按钮，或者利用`onsubmit`事件处理程序取消后续的提交请求

*   在HTML中重置表单可以使用如下按钮：

    ```javascript
    <input type="reset" value="Reset Form" />
    <button type="reset">Reset From</button>
    ```
    重置表单会将表单字段都恢复到页面刚刚加载完时的状态；当用户点击重置按钮时，会触发`reset`事件，在这个事件处理程序中可以做一些控制
    
    重置表单也可以使用`form`元素的`reset()`方法，但是这个方法同样会触发表单的`reset`事件
    
*   表单对象有一个`elements`属性，这个属性是表单中元素（字段）的有序列表，元素顺序和他们出现在表单中的顺序相同，可以按照索引或者元素（字段）的`name`属性来访问这个元素：

    ```javascript
    var form = document.getElementById("from1");
    
    var field1 = form.elements[0];
    var field2 = form.elements["textbox1"];
    var fieldCount = form.elements.length;
    
    //如果表单元素需要公用一个name，则使用这个name值会返回一个以该name命名的NodeList，如果使用索引则只返回第一个name匹配的元素
    <input type="radio" name="color" value="red" />
    <input type="radio" name="color" value="green" />
    <input type="radio" name="color" value="blue" />
    
    var form = document.getElementById("myForm");
    
    var colorFields = form.elements["color"];
    var firstColorField = colorFileds[0];
    
    var firstFiled = form.elements[0];
    
    alert(firstColorField == firstField);   //true
    ```
    > 也可以通过表单对象的属性的方式来访问元素`form[0]` `from["color"]`；但是在实际开发中最好不要使用这种方式
    
*   除了`<fieldset>`元素（字段）之外，所有表单字段都有相同的一组属性；由于`<input>`元素可以表示多种表单字段，所有有些属性只适合特定字段类型，有些属性适用所有类型：

    ```javascript
    disabled    布尔值  表示当前字段是否被禁用
    from    指向当前字段（元素）所属表单的指针；只读
    name    当前字段的名称
    readOnly    布尔值  表示当前字段是否只读
    tabIndex    表示当前字段使用tab键切换的顺序
    type    当前字段的类型
    value   当前字段将会提交给服务器的值。对文件字段来说这个值是只读的，表示文件在计算机中的路径
    ```
    对于可读写的表单字段的值我们可以通过脚本进行控制，比如在用户提交完表单后将提交按钮置为不可用：
    
    ```javascript
    EventUtil.addHandler(form, "submit", function() {
        event = EventUtil.getEvent(event);
        var target = EventUtil.getTarget(event);
        
        var button = target.getelements["submit-btn"];
        
        btn.disable = true;
    });
    
    //注意不要在提交按钮的click事件上进行处理，因为不同浏览器对于点击按钮和触发click事件的顺序不同
    ```
    
    表单字段的`type`属性值的具体值如下：
    
    ```javascript
    对于<input>元素，type值就是HTML元素type属性的值
    
    单选列表    <select>    "select-one"
    多选列表    <select multiple>   "select-multiple"
    自定义按钮  <button>        "submit"
    自定义非提交按钮    <button type="button">  "button"
    自定义重置按钮  <button type="reset">   "reset"
    自定义提交按钮  <button type="submit">  "submit"
    ```
    
*   每个表单字段都有两个方法：`focus()` `blur()`；`focus()`方法用于将浏览器的焦点设置到表单字段，使其可以响应键盘事件

    > 注意对隐藏元素调用这个方法会导致错误

    `HTML5`增加一个`autofocus`属性，只要为某个表单元素设置这个属性，则当页面加载完成后那个元素会自动获得焦点
    
    `blur()`方法是将焦点从当前元素上移走，在早期元素还没有`readOnly`属性时，这个方法有用

*   所有表单字段还有如下特有的事件：

    ```javascript
    blur    当前字段失去焦点时触发
    change  对于<input>和<textarea>元素，在他们失去焦点并且value值改变时触发；对于<select>元素，在其选项值改变时触发
    focus   当前字段获得焦点时触发
    ```
    
    当用户改变了焦点时，或者调用了`focus()` `blur()`方法时都会触发`focus` `blur`事件，这两个事件在所有表单字段中都是一样的；但是`change`事件则根据元素的不同而有不同的触发方式
    
    > 注意：在不同的浏览器中`focus` `blur`事件的触发顺序不同，应用中不能依赖着两个事件的特定顺序
    
##### 文本框脚本

文本框有两种形式：`<input type="text">`和`<textarea>`两者的属性有一定的区别，详细可以参考`HTML`文档；使用脚本给文本框赋值时推荐使用下面的方法：

```javascript
var textbox = document.forms[0].elements["textbox1"];
textbox.value = "some new value";
//注意不要使用setattribute方法给value属性添加值，也不要使用给<textarea>添加子节点的方式
```

**`选择文本的方法`**

*   两种文本框都支持`select()`方法，这个方法可以选择文本框中的所有文本，调用这个方法之后浏览器会将焦点设置为这个文本框（Opera）除外

    下面的代码在文本框获取焦点时选定文本框中的所有文本：
    
    ```javascript
    EventUtil.addHandler(textbox, "focus", function(event) {
        event = EventUtil.getEvent(event);
        var target = EventUtil.getTarget(event)
        target.select();
    });
    ```
*   文本框中有一个选择事件，在用户选择了文本框中的文本时就会触发选择事件；`IE8`在用户选择了一个字母时（还未释放鼠标）就会触发这个事件，而`IE9`和其他浏览器则在用户释放鼠标按钮时才会触发；调用`select()`方法时也会触发这个事件

*   `HTML5`中添加了两个属性用来获取选择的文本内容：`selectionStart` `selectionEnd`，这两个属性保存的是基于0的文本的范围，因此可以使用如下代码获取用户选择的文本（IE8使用另外的两个属性）：

    ```javascript
    function getSelectedText(textbox) {
        if(typeof textbox.selectionStart == "number") {
            return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
        } else if (document.selection) {
            return document.selection.createRange().text;
        }
    }
    ```
*   `HTML5`添加了一个用来选择文本框中部分文本的方法：`setSelectionRange()`，这个方法接收两个参数：要选择的第一个字符的索引和要选择的最后一个字符的后一个字符的索引

    `IE8`之前的浏览器不能使用这个方法，详细信息参考`《JavaScript高级程序设计》`
    
**`过滤输入`**

*   可以使用如下代码屏蔽用户输入非数字字符：

    ```javascript
    EventUtil.addHandler(textbox, "keypress", function(event) {
        event = EventUtil.getEvent(event);
        var target = EventUtil.getTarget(event);
        var charCode = EventUtil.getCharCode(event);
        
        if(!/\d/.test(String.fromCharCode(charCode)) && charCode > 9 && !event.ctrlkey) {
            EventUtil.preventDefault(event);
        }
    });
    ```
    因为`Firefox`和`Safari3.1`之前的版本，按下上下退格删除键也会触发`keypress`事件，并且这时`charCode`的值分别为0和8，所以判断大于9即可；另外还要注意用户可能使用复制粘贴的方式填写文本
    
*   除了`Opera`浏览器之外，其他的浏览器都支持访问和操作剪切板的操作和事件；`HTML5`规定了如下六个剪贴板事件：

    ```javascript
    beforecopy  在发生复制操作前触发
    copy    在发生复制操作时触发
    beforecut   在发生剪切操作前触发
    cut     在发生剪切操作时触发
    beforepaste     在发生粘贴操作之前触发
    paste   在发生粘贴操作时触发
    ```
    
    因为缺少标准各个浏览器在实现时有较大差异，详细信息参考`《JavaScript高级程序设计》`
    
**`自动切换焦点`**

下面的代码实现了在输入电话号码的三个部分时焦点的自动切换：

```javascript
<input type="text" name="tel1" id="txtTel1" maxlength="3" />
<input type="text" name="tel2" id="txtTel2" maxlength="3" />
<input type="text" name="tel3" id="txtTel3" maxlength="4" />

(function() {
    function tabForward(event) {
        event = EventUtil.getEvent(event);
        var target = EventUtil.getTarget(event);
        
        if(target.value.length == target.maxLength) {
            var form = target.form;
            for(var i = 0, len = form.elements.length; i < len; i++) {
                if(form.elements[i] == target) {
                    if(form.elements[i+1]) {
                        form.elements[i+1].focus();
                    }
                    return;
                }
            }
        }
    }
    
    var textbox1 = document.getElementById("txtTel1");
    var textbox2 = document.getElementById("txtTel2");
    var textbox3 = document.getElementById("txtTel3");
    
    EventUtil.addHandler(textbox1, "keyup", tabForward);
    EventUtil.addHandler(textbox2, "keyup", tabForward);
    EventUtil.addHandler(textbox3, "keyup", tabForward);
})();
```

**`HTML5约束验证API`**

`HTML5`中添加了验证表单的一些属性，浏览器会根据属性的设置对表单输入进行验证并且可以显示一些错误

*   `required`属性指定了`<input>` `<textarea>` `<select>`元素；虽然验证操作与`JavaScript DOM`无关，但是仍然可以使用`DOM`获取这个属性的值：

    ```javascript
    var isUsernameRequired = document.forms[0].elements["username"].required;
    
    //使用下面的代码可以检测浏览器是否支持这个属性
    var isSupported = "required" in document.createElement("input");
    ```

*   可以将`<input>`元素的`type`属性设置为`email` `url`来限制文本框中只能输入的格式

    可以使用的类型还有`number` `range` `datetime` `datetime-local` `date` `month` `week` `time`根据不同的类型可以设置相应的属性
    
*   文本框可以使用`pattern`属性来指定一个正则表达式模式，用于匹配文本框输入的值：

    ```javascript
    //限制文本框只能输入数字
    <input type="text" pattern="\d+" name="count" />
    
    //获取给定的模式
    var pattern = document.forms[0].elements["count"].pattern;
    
    //使用以下代码可以检测浏览器是否支持这个属性
    var isPatternSupported = "pattern" in document.createElement("input");
    ```
    
*   `checkValidity()`方法可以用来检查表单中的某个字段是否满足约束的值；这个方法可以在整个表单时上调用，也可以在单个元素上调用

    `validity`属性指向一个对象，对象中的一系列属性用来描述上面一个方法返回`false`的原因，包含如下可能的属性：
    
    ```javascript
    customError     如果设置了setCustomValidity()则为true
    patternMismatch     如果输入值和给定模式不匹配返回true
    rangeOverflow       如果值比给定max大返回true
    rangeUnderflow      如果值比给定min值小返回true
    stepMismatch        如果min和max直接的步长值不合理返回true
    tooLong         如果输入长度超过maxlength属性设置的长度返回true
    typeMismatch    如果输入内如不符合要求的格式返回true
    vaild           如果这里的其他属性都是false则返回true
    valueMissing    如果标注为required的元素没有值返回true
    ```
    
*   给表单添加`novalidate`属性，或者给提交按钮添加`formnovalidate`属性可以禁止验证

##### 选择框脚本

选择框元素可以通过`<select>` `<option>`创建；`JavaScript`中使用`HTMLSelectElement`类型对象表示；这对象除了表单字段的属性和方法外，还包括如下属性和方法：

```javascrpt
add(newOption, relOption)     向控件中插入新的<option>元素，位置在relOption之前
multiple    表示控件中所有<option>元素的一个HTMLCollection
remove(index)   移除给定位置的选项
selectedIndex   基于0的选中项的索引，如果没有选中项则值为-1；如果是多选框则只保存选中项的第一项
size    选择框中可见的行数；等价于HTML中的size属性

选择框的type属性是select-one或者select-multiple取决于选择框中是否有multiple属性；选择框的value属性由当前选中的项决定，有如下规则：

如果没有选中的项，则选择框的value属性保存空字符
如果有一个选中的项，且该项的value已经在HTML中指定，则选择框的value属性等于选中项的value值（即使value值是空字符串）
如果有一个选中项，但是该项的value未在HTML中指定，则选择框的value属性等于该项的文本
如果有多个选中项，则选择框的value属性值将根据前两条规则取得第一个选中的项
```

在`DOM`中每一个`<option>`元素都有一个`HTMLOptionElement`类型对象表示；这对象除了表单字段的属性和方法外，还包括如下属性和方法：

```javascript
index   当前选项在options集合中的索引
label   当前选项的标签；等价于HTML中的label属性
selected    布尔值表示当前选项是否被选中。将这个属性设置为true可以选中当前项
text    选项的文本
value   选项的值；等价于HTML中的value属性
```
> 在使用`DOM`访问`<option>`元素时推荐使用上面的属性访问

> 选择框的`change`事件在选项被改变时即被触发；而其他表单元素的`change`事件一般在值被修改，并且焦点离开时才会触发

**`选择选项`**

对于只允许选择一项的选择框，访问选中项最简单的方式就是使用选择框的`selectedIndex`属性：

```javascript
var selectedOption = selectbox.options[selectbox.selectedIndex];
```

要在允许多选的选择框中获取所有选中的项可以遍历每个选项的`selected`属性，进行如下编码：

```javascript
function getSelectedOptions(selectbox) {
    var result = new Array();
    var option = null;
    
    for(var i = 0, len = selectbox.options.length; i < len; i++) {
        option = selectbox.options[i];
        if(option.selected) {
            result.push(option);
        }
    }
    return result;
}
```

**`添加选项`**

除了可以使用创建`<option>`元素然后添加子节点的方法添加选项外，还可以使用选择框对象的`add()`方法来添加；这个方法接受两个参数：要添加的新选项和将位于新选项之后的项（将这个参数置为`null`可以添加到最后一项，IE中将第二个参数设置为`undefined`）

```javascript
var newOption = new Option("Option text", "Option value");
selectbox.add(newOption, undefined);
//这里使用了一个遗留的option构造函数创建了一个选项对象
```

**`移除选项`**

移除一个选项可以使用如下方法：

```javascript
//使用dom方法
selectbox.removeChild(selectbox.options[0]);

//使用选项对象的方法
selectbox.remove(0);

//遗留方法
selectbox.options[0] = null;
```
> 移除一个选项后，其他的选项会自动向上移动一个位置

**`移动和重拍选项`**

使用`DOM`相关的方法进行选项的移动时非常方便的，使用`appendChild()` `insertBefore()`方法将一个已经在一个选择框中的选项添加到另外一个选项框时，会自动从前一个选择框移除；代码如下：

```javascript
var selectbox1 = document.getElementById("selLocation1");
var selectbox2 = document.getElementById("selLocation2");

selectbox2 = appendChild(selectbox1.options[0]);
```

重新排序一个选择框我们可以使用`DOM`的`insertBefore()`方法：

```javascript
//将特定选项向前移动一个位置
var optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove, selectbox.options[optionToMove.index - 1]);

//向后移动一个位置
var optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove, selectbox.options[optionToMove.index + 2]);
```

##### 表单序列化

在提交表单时，浏览器按如下步骤对表单进行操作并发送数据：

1.  对表单字段的名称和值进行`URL`编码，并使用`&`连接
2.  不发送禁用的表单字段
3.  只发送勾选的复选框和单选框
4.  不发送`type`为`reset`和`button`的按钮
5.  多选框中的每个选中的值单独一个条目
6.  在单击提交按钮（包括`<input type="iamge">`）提交表单的情况下，也会发送提交按钮；否则不发送提交按钮
7.  `<select>`元素的值，就是选中的`<option>`元素的`value`属性；如果没有指定`value`则是相应的文本

下面的代码实现了表单序列化：

```javascript
function serialize(form) {
    var parts = [],
        field = null,
        i, len, j, optLen, option, optValue;
        
    for(i = 0, len = form.elements.length; i < len; i++) {
        field = form.elements[i];
        
        switch(field.type) {
            case "select-one":
            case "select-multiple":
            
            if(field.name.length) {
                for(j = 0, optLen = field.options.length; j < optLen; j++) {
                    option = field.options[j];
                    if(option.selected) {
                        optValue = "";
                        if(option.hasAttribute) {
                            optValue = (option.hasAttribute("value") ? option.value : option.text);
                        } else {
                            optionValue = (option.attributes["value"].specified ? option.value : option.text);
                        }
                        parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(optValue));
                    }
                }
            }
            break;
            
            case undefined:
            case "file":
            case "submit":
            case "reset":
            case "button":
            break;
            
            case "radio":
            case "checkbox":
                if(!field.checked) {
                    break;
                }
                
            default:
                if(field.name.length) {
                    parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(field.value));
                }
        }
    }
    
    return parts.join("&");
}
```

##### 富文本编辑

#### JSON

`JSON`(JavaScript Object Notation)是一种数据格式；而且很多语言都实现了`JSON`格式解析

##### 语法

`JSON`可以表示如下三种类型的值：

*   `简单值`：使用与`JavaScript`相同的语法，可以在`JSON`中表示`字符串` `数值` `布尔值` `null`（`JSON`不支持`undefined`）
*   `对象`：对象是一种复杂数类型，在`JSON`中也是一组无序的键值对；每个键值对中的值可以是任意类型（简单值、对象、数组）
*   `数组`：数组也是一种复杂数据类型，在`JSON`中也是一组有序的值的列表，可以通过数值索引访问其中的值；数组的值也可以是任意类型

`JSON`中的字符串必须使用`""`（双引号）引用

`JSON`中的对象和`ECMAScript`中对象字面值相同，但是属性名必须加`""`（双引号）引用，属性值应该符合数组和简单值的规则

`JSON`中的数组和`ECMAScript`中数组字面量相同，但是元素值应该符合`JSON`中简单值和对象的规则

##### 解析与序列化

`ECMAScript5`对解析`JSON`数据进行了规范化，定义了一个全局对象`JSON`；这个对象有两个方法：`stringify()` `parse()`，这两个方法分别用于把`ECMAScript`对象序列化成`JSON`字符串和把`JSON`字符串解析为`ECMAScript`数据，比如：

```javascript
var book = {
    title: "Professional JavaScrpt",
    authors: ["Nicholas"],
    edition: 3,
    year: 2011
};

var jsonText = JSON.stringify(book);

var bookCopy = JSON.parse(jsonText)
```

默认情况下不带参数的`stringify()`方法生成的`JSON`字符串不包括任何缩进和空格

在序列化`JavaScript`对象时，所有函数及原型成员都被忽略；此外只为`undefined`的属性也被忽略

反序列化之后的对象虽然和原对象有相同的属性但是并不是同一个对象

**`序列化选项`**

`JSON.stringify()`方法还可以接受另外两个参数：第一个参数是一个过滤器（数组、函数），第二个参数是一个选项用于指定是否在`JSON`字符串中保留缩进

*   当方法第二个参数是一个数组时表示序列化作用的属性名称集合。当第二个参数是一个函数时，这个函数会接收到两个参数：当前处理的属性名和属性值；函数的返回值是实际序列化后的属性值

*   第三个参数可以指定为一个数值，表示每一个级别缩进的空格数（最大为10）；也可以指定一个字符串，这样将使用这个字符串作为缩进字符（最长为10）而不是空格

*   如果给一个对象添加了`toJSON()`方法，那么在序列化这个对象时，会调用这个方法，并且将这个方法的返回值作为实际将要序列化的对象，函数的返回值可以是任意类型

加上`toJSON()`方法之后，调用`stringify()`序列化对象的实际过程如下：

1.  如果存在`toJSON()`方法并且能通过它取得有效值，则调用这个方法；否则返回对象本身
2.  如果提供了第二个参数则应用这个函数过滤器；传入过滤器的值是第1步返回的值
3.  对第2步返回的每个值进行相应的序列化
4.  如果提供第三个参数，则执行相应的格式化

**`解析选项`**

`JSON.parse()`也可以接受一个函数作为第二个参数，这个函数将在每个键值对上调用；这个函数也会接收到两个参数，即当前处理的键和值；返回值作为实际处理的对象

#### Ajax与Comet

`Ajax`（Asynchronous JavaScript + XML）技术的核心是`XMLHttpRequest`对象（简称`XHR`）；虽然名字中包含`XML`，但是与实际通信使用的数据格式无关

##### XMLHttpRequest对象

`IE7`及其他的浏览器都支持原生的`XHR`对象，所以创建这样一个对象只需要使用这样的方式：`var xhr = new XMLHttpRequest()`；在不支持原生对象的`IE7`之前的浏览器中还需要使用别的方法

**`XHR用法`**

`XHR`对象常用的第一个方法是`open()`，它接受三个参数：要发送的请求的类型、请求的`URL`、是否异步发送请求：

```javascript
var xhr = new XMLHttpRequest();
xhr.open("get", "example.php", false);
xhr.send(null);
```
如果`URL`未指定一个完整的`URL`路径则表示相对于本页面；另外调用这个方法并不会发送请求，这仅仅创建一个请求实体；后面的`send()`方法才会发送请求

`send()`方法接受一个参数表示要作为请求主题发送的数据

> 注意这个方法只能向同一个域中使用相同端口和协议的`URL`发送请求；否则会引发安全错误

*   同步的请求会等待请求被响应之后，代码才会继续执行。收到响应后，相应的数据会自动填充`XHR`对象的下列属性：

    ```javascript
    responseText    作为响应主体被返回的文本
    responseXML     如果响应内容类型是`text/xml`或者`application/xml`，这个属性将保存着包含响应数据的`XML` `DOM`文档
    status          响应的`HTTP`状态码
    statusText      响应状态说明
    ```
    通常响应返回之后应该首先检查`status`状态，如果是`2xx`则表示成功，如果是`304`则表示资源未改动，可以使用缓存

*   如果使用异步请求的方式，则需要检查`XHR`对象的`readyState`属性，这个属性表示请求/响应当前的状态，可以有如下值：

    ```javascript
    0   未初始化    尚未调用open()方法
    1   启动        已经调用open()方法，但尚未调用send()方法
    2   发送        已经调用send()方法，但尚未接收到响应
    3   接收        已经接收到部分数据响应
    4   完成        已经接收到全部数据响应，而且用户已经可以在客户端使用
    ```
    另外只要`readyState`属性的值有改变就会触发一次`readystatechange`事件，可以利用这个事件来检测`readyState`当前的值然后进行各种操作；注意如果要保证跨浏览器的可用性需要在调用`open()`方法之前注册`onreadystatechange`事件处理程序：
    
    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechagne = function() {
        if(xhr.readyState ==4) {
            //这里使用xhr而非this，因为在有的浏览器可能执行失败
            if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                alert(xhr.responseText);
            } else {
                alert("Request was unsuccessful:" + xhr.status);
            }
        }
    };
    
    xhr.open("get", "example.php", true);
    xhr.send(null);
    ```
    异步发送的请求在收到响应之前还可以使用`abort()`方法来取消请求；注意调用这个方法之后当前`XHR`对象会停止触发事件，而且也不能再访问这个对象的任何属性和方法；最好解引用这个对象
    
**`HTTP头部信息`**

`XHR`对象也提供了操作请求头部的数据的方法；默认情况下发送的请求会包括如下头部信息：

```javascript
Accept      浏览器能够处理的内容类型
Accept-Charset      浏览器能够显示的字符集
Accept-Encoding     浏览器能够处理的压缩编码
Accept-Language     浏览器当前设置的语言
Connection      浏览器与服务器之间的连接类型
Cookie      当前页面设置的任何Cookie
Host        发出请求的页面所在的域
Referer     发出请求的页面的URI。（正确的单词应该是referrer，但是HTTP规范中使用错误的单词）
User-Agent      表示当前使用的浏览器
```

使用`setRequestHeader()`方法可以设置自定义的请求头部信息；这个方法接收两个参数：头部字段名称、响应名称的值；这个方法必须在`open()`方法之后`send()`方法之前调用

> 有些浏览器不允许更改默认的头部信息

使用`getResponseHeader()`方法并传入头部字段名称字符串作为参数就可以获取到响应的头部信息；而调用`getAllResponseHeaders()`方法则会获取到所有头部信息组成的一个长字符串（包括换行）：

```javascript
Date: Sun, 14 Nov 2004 18:04:03 GMT
Server: Apache/1.3.29 (Unix)
Vary: Accept
X-Powered-By: PHP/4.3.8
Connection: close
Content-Type: text/html; charset=iso-8859-1
```
**`GET请求`**

使用`GET`请求发送请求数据时，请求参数（`key``value`）需要使用`encodeURIComponent()`函数进行编码，并且请求参数和`URI`之间使用`?`分隔，请求参数和值之间使用`=`分隔，多个请求参数之间使用`&`分隔

可以使用如下函数来方便的添加参数：

```javascript
function addURLParam(url, name, value) {
    url += (url.indexOf("?") == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}
```

**`POST请求`**

`POST`请求的数据被放在`HTTP`协议请求体中，所有默认情况下需要在服务器端对请求的数据进行提取处理；但是我们可以用`XHR`来模仿表单提交，需要将`Content-Type`头信息设置为`application/x-www-form-urlencoded`（表单提交时的内容类型），然后创建适当格式的请求体字符串：

```javascript
function submitData() {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechagne = function() {
        if(xhr.readyState ==4) {
            if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                alert(xhr.responseText);
            } else {
                alert("Request was unsuccessful:" + xhr.status);
            }
        }
    };
    
    xhr.open("post", "postexample.php", true);
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    var form = document.getElementById("user-info");
    xhr.send(serialize(form));
    //这里引用了14章的serialize函数
}
```
> 与`GET`请求相比，`POST`要消耗更多的处理器资源和时间

##### XMLHttpRequest 2 级

目前几乎所有浏览器都实现了`XMLHttpRequest2`的部分内容

**`FormData`**

`XMLHttpRequest2`为表单序列化定义了一个`FormData`类型对象，这个对象提供了序列化表单和创建表单格式数据（用于`XHR`请求）提供了便利；下面的代码创建了一个对象并且添加一些数据：

```javascript
var data = new FormData();
data.append("name", "Nicholas");

//也可以在创建对象时向构造函数传入表单对象
var data = new FormData(document.forms[0]);
```
创建了一个`FormData`对象类型实例之后就可以将它传给`send()`方法

**`超时设定`**

`XMLHttpRequest2`为`XHR`对象添加了一个`timeout`属性，可以为请求设置一个超时时间，如果在超时时间内未收到请求就会触发`timeout`事件，我们可以设置`ontimeout`事件处理程序来处理这种情况（目前只有IE支持这个属性）

**`overrideMimeType()方法`**

这个方法可以用来覆盖响应数据中的`MIME`类型设置；比如`reponseXML`属性会根据服务器响应头信息的`MIME`类型来进行设置，如果头信息指定内容类型为不是`xml`则不会设置这个属性，这时我们可以使用这个方法覆盖内容类型强制设置`responseXML`属性

目前除`IE`浏览器外，其他浏览器都支持这个方法

##### 进度事件

这个事件定义了客户端与服务器通信有关的事件，包含如下六个进度事件：

```javascript
loadstart       在接收到响应数据的第一个字节时触发
progress        在接收相应期间不断的触发
error           在请求发生错误时触发
abort           因为调用abort()方法而终止时触发
load            接收到完整的响应数据时触发
loadend         在通信完成或者触发 error、abort、load事件后触发
```
目前`IE`只支持`load`事件，其他的浏览器都支持前五个事件，`loadend`事件还没有浏览器支持

**`load事件`**

使用`load`事件后就没必要再处理`readystatechange`事件和`readyState`属性了。这个事件的处理程序会接收到一个`event`对象，对象的`target`属性指向`XHR`对象实例，但是并非所有浏览器都实现这一点，所以在实际开发中还要使用`XHR`对象实例来访问一些属性（响应状态码）

> 只要浏览器接收到响应就会触发`load`事件，不会管返回的状态

**`progress事件`**

这个事件会在浏览器接收到数据的期间周期性的触发。`onprogress`事件处理程序接收到的`event`对象的`target`属性指向`XHR`对象实例，并且有额外的三个属性：`lengthComputable` `position` ` totalSize`，分别表示信息是否可用、已经接收到的字节数、根据`Content-Length`头信息确定的预期字节数；使用这些信息可以构建一个信息接收进度条：

```javascript
var xhr = new XMLHttpRequest();
xhr.onload = function(event) {
    if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        alert(xhr.responseText);
    } else {
        alert("Request was unsuccessful:" + xhr.status);
    }
};

xhr.onprogress = function(event) {
    var divStats = document.getElementById("status");
    if(event.lengthComputable) {
        disStatus.innerHTML = "Received " + event.positioin + " of" + event.totalSize + " bytes";
    }
};

xhr.open("get", "example.php", true);
xhr.send(null);
```
##### 跨源资源共享

使用`XHR`来进行`Ajax`请求的一点限制是：只能访问和当前页在同一个域中的资源；`CORS`（Cross-Origin Resource Sharing）用来进行跨源资源的请求

`CORS`使用自定义的`HTTP`头部让浏览器与服务器进行通信；比如发送一个请求时，没有自定义的头部，并且主体内容是`text/plain`，在发送该请求时附加一个额外的`Origin`头部，包含了请求页面的源信息（协议、域名、端口），比如：`Origin: http://www.nc.net`；如果服务器认为这个请求可以接收，就在`Access-Control-Allow-Origin`响应头中加上和请求相同的信息

> 这个请求和响应都不包含cookie信息

**`IE对CORS的实现`**

`IE8`中使用`XDomainRequest`类型，这个类型和`XMLHttpRequest`类型相似，但是可以实现安全的跨域通信。下面是两者的不同之处：

```javascript
cookie不会随请求发送，也不会被添加到响应中
只能设置请求头部信息中的Content-Type字段
不能访问响应头部信息
只支持GET和POST请求
```
详细的信息参考`《JavaScript高级程序设计》p583`

**`其他浏览器的实现`**

其他大多数浏览器都通过`XMLHttpRequest`对象实现了对`CORS`的原生支持；只需要在调用`open()`方法时将`URL`设置为绝对路径即可

相应返回后通过`XHR`对象可以访问`status`和`statusTest`属性，并且还支持同步异步请求；但是也有一些限制：

```javascript
不能使用setRequestHeader()设置头部
不能接收和发送cookie
调用getAllResponseHeaders()方法返回空字符串
```
> 所以在使用`XHR`对象时，访问本域内资源最好使用相对路径，访问其他域资源使用绝对路径

**`Preflighted Requests`**

**`带凭据的请求`**

默认情况下跨源请求不能携带凭据信息（cookie、HTTP认证、客户端SSL）。通过将`withCredentials`属性设置为`true`，可以指定某个请求将要发送凭据信息；如果服务器接受，则会在响应头中添加如下头信息：`Access-Control-Allow-Credentials: true`；如果服务器的响应中不包含这个头部，则浏览器不会将响应信息交给`JavaScipt`，这样`responseText`将为空，`status`为0，而且会调用`onerror`事件处理程序

> `IE10`之前的版本不支持带凭据的请求

**`跨浏览器的CORS`**

下面实现了一个跨浏览器的`CORS`：

```javascript
function createCORSRequest(method, url) {
    var xhr = new XMLHttpRequest();
    if("withCredentials" in xhr) {
        xhr.open(method, url, true);
    } else if(typeof XDomainRequest != "undefined") {
        vxhr = new XDomainRequest();
        xhr.open(method, url);
    } else {
        xhr = null;
    }
    return xhr;
}

var request = createCORSRequest("get", "http://www.test.com/page/");
if(request) {
    request.onload = function() {
        //handler
    };
    request.send();
}

//IE和其他类型CORS都可以使用的方法有：
abort()     用于停止正在进行的请求
onerror     用于替代onreadystatechange检测错误
onload      用于替代onreadystatechange检测成功
responseText属性    用于取得相应内容
send()      用于发送请求
```

##### 其他的跨域技术

**`图像ping`**

**`JSONP`**

**`Comet`**

**`服务器发送事件`**

**`Web Sockets`**

##### 安全

为了确保通过`XHR`访问`URL`的安全，同行的作为就是验证发送请求者是否有权访问相应的资源：

*   要求以`SSL`连接来访问可以通过`XHR`请求的资源
*   要求每一次请求都要附带经过相应算法计算得到的验证码
