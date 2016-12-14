---
title: 《JavaScript高级程序设计》读书笔记2
date: 2016-12-01 15:01:56
tags: JavaScript
categories: JavaScript
---
### BOM

`BOM`提供了很多功能用于访问浏览器的功能，这些功能与网页内容无关。目前W3C已经将`BOM`的主要方法纳入到`HTML5`规范中

#### window对象
<!-- more -->
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

> 在使用框架的情况下，浏览器中会存在多个`Global`对象；在每个框架中定义的全局变量会自动成为框架中`window`对象的属性

**`窗口位置`**  

`screenLeft`和`screenTop`属性用来确定和修改`window`对象（浏览器窗口）的位置（firefox使用`screenX`和`screenY`）；确定位置的属性在各个浏览器的实现上有些差异，下面的代码可以跨浏览器的获取窗口左边和上边的位置：

```javascript
var leftPos = (typeof window.screenLeft == "number") ? window.screenLeft : window.screenX
var topPos = (typeof window.screenTop == "number") ? window.screenTop : window.screenY
```
> 在`Chrome` `Firefox` `Safari`浏览器中上面的代码获取到的是整个浏览器窗口相对于屏幕的坐标值，而`IE` `Opera`浏览器则返回的是，浏览器可视窗口相对于屏幕的坐标值

各个浏览器都提供了统一的方法`moveTo()` `moveBy()`方法将浏览器窗口移动到一个新的额位置；这两个方法都接受两个参数，第一个方法的两个参数表示要移动到的坐标位置，第二个方法的两个参数表示要移动的相对像素数

**`窗口大小`**  

`innerWidth` `innerHeight` `outerWidth` `outerHeight`属性用来确定窗口的大小，但是各个浏览器实现还是有很大的不同；下面的代码可以跨浏览器获取浏览器视口的大小：

```javascript
var pageWidth = window.innerWidth;
var pageHeight = window.innerHeight;

if(typeof pageWidth != "number") {
    if(document.compatMode == "CSS1Compat") {
        pageWidth = document.documentElement.clientWidth;
        pageHeight = document.documentElement.clientHeight;
    } else {
        pageWidth = document.body.clientWidth;
        pageHeight = document.body.clientHeight;
    }
}
```

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

#### location对象

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

#### navigator对象

`navigator`对象提供了一些属性和方法用来识别客户端浏览器，详细信息参考`《JavaScript高级程序设计》`

#### screen history对象

`screen`对象和`history`对象在实际开发中使用的比较少，详细信息可以参考`《JavaScript高级程序设计》`

### DOM

`DOM`是针对HTML和XML文档的一个API。`DOM`描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的一部分

#### 节点层次

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
`document`节点是每个文档的根节点

上面的文档中，`document`节点只有一个子节点`<html>`元素，称为**文档元素**。文档元素是最外层的元素，文档中的其他元素都应该包含在文档元素中，一个页面只能有一个文档元素。在HTML页面中，文档元素就是`<html>`元素；在XML中没有预定义的文档元素

HTML中总共有12种节点类型，这些类型都继承自一个基类--`Node`：

##### Node类型

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

**`节点操作方法`**

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

`cloneNode()`方法可以复制一个节点，使用`true`参数表示复制整个子节点数（指定的节点和所有子节点），使用`fasle`作为参数表示只复制这个节点；复制返回的节点只能存在于本文档对象，可以使用上面提供的方法给其加上文档关系（否则的话是一个 *孤儿* 节点）

> 这个方法只复制文档特性，其他的都不会复制，也不会复制添加到`DOM`节点中的`JavaScript`属性

##### Document类型

`Document`表示文档节点类型。浏览器中`document`对象是`HTMLDocument`（继承自`Document`类型）类型的一个实例，表示整个HTML页面。`document`同时是`window`对象的一个属性，因此可以当做一个全局对象来访问；这个对象还有下面特征：

*   `nodeType`的值为9
*   `nodeName`的值为`"#doucment"`
*   `nodeValue`的值为`null`
*   `parentNode`的值为`null`
*   `ownerDocuemnt`的值为`null`
*   其子节点可能是一个`DocumentType`、 一个`Element`、`ProcessingInstruction`、`Comment` 

`Document`类型可以表示HTML页面或者XML页面。最常见的应用还是作为`HTMLDocument`类型实例的`document`对象。通过这个文档对象可以取得页面有关信息、操作页面外观及底层结构

> 非IE浏览器中可以访问`Docuemnt`类型的构造函数和原型；所有浏览器中都可以访问`HTMLDocument`类型的构造函数和原型

访问`document`子节点时可以使用`document.docuemntElement`属性直接取得`<html>`元素节点（`document`对象唯一的元素节点）；另外还有一个`document.body`属性直接指向`<body>`元素节点

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

`document.implementation`属性提供了一个`hasFeature()`方法，这个方法接收两个参数：要检查的DOM功能的名称字符串和版本号字符串；可以用来检查一个浏览器是否支持这个DOM功能，如果是返回`true`；可以作为参数传入的值参考`《JavaScript高级程序设计》p259`

`JavaScript`提供了将输出流写入到网页中的方法：`write()` `writeln()` `open()` `close()`，这些方法在开发中使用比较少，详细信息参考`《JavaScript高级程序设计》`

##### Element类型

`Element`类型用于表现`XML`和`HTML`元素，提供了对元素标签名，子节点和属性的访问：

*   `nodeType`的值为1
*   `nodeName`的值为元素的标签名
*   `nodeValue`的值为null
*   `parentNode`可能是`Document`或者`Element`
*   其子节点可能是`Element` `Text` `Comment` `ProcessingInstruction` `CDATASection` `EntityReference`

在获取到元素节点实例之后可以使用这个对象的`nodeName`或者`tagName`属性来访问元素的标签名

> 要注意的是返回的标签名可能是大写的，并且不带尖括号，如果要和字符串进行比较的话最好显式的将其转换为小写再进行比较

`Element`类型在`HTML`文档中使用`HTMLElement`对象类型（及其子类型）来表示，`HTMLElement`类型继承自`Element`并且添加了一些属性。每一个`HTML`元素都有下面的特性：

*   `id`：元素在文档中的唯一标识符
*   `className`：与元素的`class`属性对应，即为元素指定的css类（因为class是`ECMAScript`的保留字，所以使用这个名称）
*   `title`：有关元素的附加说明，一般通过工具提示条显示出来
*   `lang`：元素内容的语言代码
*   `dir`：语言的方向，即从左到右或者从右到左

上面几个属性都是可读写的，但是只有`className`会实时的反应当前的值

`《JavaScript高级程序设计》 p263`列出了`HTML`中所有元素和类型的对应

**`元素属性操作`**

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

**`创建元素节点`**

使用`document.createElement()`方法可以创建新的元素节点，这个方法接受一个参数：要创建的元素标签名称（在HTML中不区分大小写），这个方法返回新创建的元素，这时就可以给元素添加属性、子节点及其他一些操作，但是新创建的元素并未被添加到当前文档中，因此在浏览器中不会有任何的效果；我们可以使用上节的一些方法将这个节点元素添加到`document`中

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

##### Text类型

`Text`类型表示文本节点，文本节点是可以按照字面解释的纯文本内容；文本中可以包含转义后的`HTML`字符，但是不能包含`HTML`代码；这类节点具有以下特征：

*   `nodeType`值为3
*   `nodeName`值为`"#text"`
*   `nodeValue`的值为节点所包含的文本
*   `parentNode`是一个`Element`
*   没有子节点

可以通过`Text`对象类型实例的`nodeVlaue`或者`data`属性取得节点中包含的文本，这两个属性的值相同，并且通过一个属性赋值可以通过另外一个属性获取；可以通过`length`属性获取文本节点的字符个数，`nodeValue`和`data`是一个字符串类型所以也有一个`length`属性并且和实例的`length`属性值相同

可以使用下面文本节点对象的方法操作节点中的文本：

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

##### Comment类型

注释在`DOM`中是使用`Comment`类型来表示的；`Comment`具有以下特征：

*   `nodeType`的值为8
*   `nodeName`的值为`"#comment"`
*   `nodeValue`的值为注释的内容
*   `parentNode`可能是`Document`或者`Element`
*   没有子节点

`Comment`类型继承自`Text`类型，除了`splitText()`方法之外，`Comment`类型大多数的属性和方法和`Text`类型相同；注释节点可以通过其父节点来访问；可以使用`docuemnt.createComment()`来创建一个注释节点类型实例

> 浏览器不会识别`</html>`元素之后的注释

##### CDATASection类型

这个类型只存在于`XML`文档中，表示`CDATA`区域；这个类型有以下特征：

*   `nodeType`值为4
*   `nodeName`值为`"#cdata-section"`
*   `nodeVlaue`值为`CDATA`区域中的内容
*   `parentNode`可能是`Document`或者`Element`
*   没有子节点

这个类型同样继承自`Text`类型，因此拥有除了`splitText()`方法之外的所有字符操作方法

##### DocumentType类型

`DocumentType`类型实例保存着与文档的`doctype`有关的信息，有以下特征：

*   `nodeType`的值为10
*   `nodeName`的值为`doctype`的名称
*   `nodeValue`的值为null
*   `parentNode`的值为Document
*   没有子节点

`IE`浏览器不支持这种类型（在`IE9`之后会给`document.doctyp`赋正确的值），`Chrome4`以后才支持这种类型；在`DOM1`中，这种类型对象不能被动态创建，只能在解析文档代码时创建；支持它的浏览器会`DocumentType`类型对象实例保存在`document.doctype`属性中

`DOM1`中描述了`DocmentType`类型对象实例的三个属性：`name` `entities` `notations `；这三个属性中目前只有`name`是有用的值为当前的文档类型，即`<!DOCTYPE>`元素中的内容；后面两个属性都是`NamedNodeMap`对象，都为空对象

##### DocumentFragment类型

`DocumentFragment`类型没有对应的标记，这是一种轻型的文档，可以包含和控制节点，但是不能被添加到文档中：

*   `nodeType`的值为11
*   `nodeName`的值为`"#document-fragment"`
*   `nodeValue`的值为`null`
*   `parentNode`的值为`null`
*   子节点可以是`Element` `ProcessingInstruction` `Comment` `Text` `CDATASection` `EntityReference`

使用`document.createDocumentFragment()`方法可以创建一个文档片段节点类型，这种类型继承了`Node`类型的所有方法可以用于文档内容的操作

将文档中的节点添加到文档片段节点中会从文档树中移除该节点；添加到文档片段中的新节点默认不会成为文档树的一部分；通过`appendChild()` `insertBefore()`方法将文档片段添加到文档中时，实际上只会讲文档片段的所有子节点添加到文档树中，文档片段节点不会成为文档树的一部分

##### Attr类型

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
element.setAttributeNode(attr);     //为元素添加属性
alert(element.attributes["align"].value);   //"left" attributes所指向的NamedNodeMap中保存的是一个一个的属性节点
alert(element.getAttributeNode("align").value);     //"left"
alert(element.getAttribute("align"));   //"left"
```
要将创建的属性节点添加到元素节点中，需要使用元素节点对象的`setAttributeNode()`方法；而访问元素节点的属性可以使用`attributes`属性、`getAttributeNode()`方法、`getAttribute()`方法

> `getAttributeNode()`方法返回属性节点类型实例，而`getAttribute()`则返回指定的属性的值；通常使用后者比操作属性节点更方便

#### DOM操作技术

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

要迭代一个`NodeList`时最好使用`length`属性初始化一个变量：

```javascript
var divs = document.getElementsByTagName("div"),
    i, len, div;
    
//注意虽然divs是在循环外面获取的，但是这个divs是动态更新的，所以如果将i变量直接和divs.length属性进行对比，则i永远小于后者，会导致死循环
for(i = 0, len = divs.length; i < len; i++) {
    div = document.createElement("div");
    document.body.appendChild(div);
}
```
> 一般来说应该尽量减少访问`NodeList`的次数，因为每次访问`NodeList`都会运行一次基于文档的查询

### DOM扩展

本章介绍了一些已经被标准化的`DOM`扩展：`Selectors API（选择符API）` `HTML5` `Element Traversal（元素遍历）`；以及一些专有扩展

#### 选择符API

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

#### 元素遍历

IE浏览器和其他浏览器对待元素之间空格不同，前者会忽略空格，后者会认为是一个文本节点，所以导致了`ChildNodes`和`firstChild`属性的值在两种浏览器中不同；`DOM`扩展规范提供了几个访问这些元素的一致的属性：

*   `childElementCount`：返回子元素的个数（`Element`类型的节点）
*   `firstElementChild`：指向第一个元素
*   `lastElementChild`：指向最后一个元素
*   `previousElementSibling`：指向前一个同辈元素
*   `nextElementSibling`：指向后一个同辈元素

使用上面这些属性，在取一个元素的最后一个子元素时就不必判断返回的节点是注释或者文本节点了：

```javascript
var i, len, child = element.firstElementChild;
while(child != element.lastElementChild) {
    processChild(child);
    child  = child.nextElementSibling;
}
```
> 支持这些属性的浏览器有`IE9+` `Firefox3.5+` `Safari4+` `Chrome` `Opera10+`

#### HTML5

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

*   `document.compatMode`：这个属性有两个可能的值`CSS1Compat` `BackCompat`分别表示浏览器处于标准模式和混杂模式

*   `document.head`：这个属性引用文档的`<head>`元素；不过在开发中最好这样写：

        var head = document.head || document.getElementByTagName("head")[0];
    
**`字符集属性`**

可以使用`document.charset`获取或者设置的当前页面字符集；这个和在页面中使用`<meta>`元素设置有一样的效果

另外还有一个`document.defaultCharset`属性表示根据默认浏览器及操作系统的设置

> 除了`forefox`不支持第二个属性之外，其他的浏览器都支持这两个属性

**`自定义数据属性`**

`HTML5`规定可以为元素添加非标准的属性，自定义的属性应该以`data-`开始；添加了自定义属性之后，可以通过元素的`dataset`属性来访问自定义的属性，这个属性是一个`DOMStringMap`对象类型的一个实例；`DOMStringMap`对象中的属性表示这个元素的自定义属性，但是这里面的属性没有`data-`前缀

**`插入标记`**

插入标记可以在不使用`DOM`接口的情况下创建元素（使用包含`html`标签的字符串），这在需要大量的插入元素时很高效

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

> 插入标记可能会占用大量的内存，所以多次的调用相关属性和方法（比如在循环中使用，最好是在循环中组建字符串，然后一次性的赋值给对应的属性）

**`scrollIntoView()`**

这个方法可以在所有元素上调用，这个方法通过滚动浏览器窗口或者某个容器元素，使调用的元素可以出现在视口中；如果传入一个`true`参数或者不传入参数则调用这个方法的元素的顶部会和视口顶部对齐；如果传入一个`false`参数则会进行底部对齐

> 通过让某一个元素获取焦点也会让这个元素滚动到视口中

#### 专有扩展

**`children属性`**

这个属性是`HTMLCollection`类型的实例，表示只包含元素中同样是元素的节点，除此之外和`childNodes`属性一致；因为在`IE8`及之前的版本中，后者会返回包括注释节点的所有类型节点

**`contains()方法`**

这个方法用于检查作为参数的节点是否是调用节点的后代节点，如果是返回`true`

`DOM Level 3`中的`compareDocumentPosition()`方法可以用来确定调用节点和参数节点之间的关系，这个方法返回一个表示关系的位掩码：

```javascript
1       无关（给定的节点不在当前文档树中）
2       居前（给定的节点在`DOM`数中位于参考节点之前）
4       居后（给定的节点在`DOM`数中位于参考节点之后）
8       包含（给定的节点是参考节点的祖先）
16      被包含（给定的节点是参考节点的后代）
```
下面是一个跨平台的实现`contains()`功能的代码：

```javascript
function contains(refNode, otherNode) {
    if(typeof refNode.contains == "function" && 
        (!client.engine.webkit || client.engine.webkit >= 522)) {
        return refNode.contains(otherNode);    
    } else if(typeof refNode.compareDocumentPosition == "function") {
        return !!(refNode.compareDocumentPosition(otherNode) & 16);
    } else {
        var node = otherNode.parentNode;
        do {
            if(node === refNode) {
                return true;
            } else {
                node = node.parentNode;
            }
        } while(node !== null)
        
        return false;
    }
}
```

**`插入文本`**

`IE`浏览器的专有属性`innerText` `outerText`属性用于获取和设置元素及子元素中的所有文本

获取文本内容时会将调用元素及子元素中包含的所有文本提取出来连接成一个字符串（根据不同的浏览器，可能会含有换行符保留原来的样式）；而设置文本值时会移除原来的所有子节点，将子节点替换为一个文本节点（为这个属性赋值的字符串）

> `outerText`同`outerHTML`，由于改变`outerText`的值会导致调用这个属性的元素不存在所以在开发中较少使用这个

> 同`innerHTML`属性，为这个属性赋值时同样会对字符串中包含的`HTML`语法字符进行编码

> `IE` `Safari 3+` `Opera 8+` `Chrome`支持这个属性，`firefox`支持另外一个属于`DOM Level 3`规范的功能相同的属性`textContent`

**`滚动相关的方法`**

还有另外几个和滚动页面相关的方法：`scrollIntoViewIfNeeded(true)` `scrollByLines(linecount)` `scrollByPages(pageCount)`，目前只有`Safari`和`Chrome`实现了这些方法

### DOM2和DOM3

`DOM2`包括如下模块：

*   `DOM Level 2 Core`：在1级核心的基础上构建，为节点添加了更多的属性和方法
*   `DOM Level 2 Views`：为文档定义了基于样式信息的不同视图
*   `DOM Level 2 Events`：说明了如何使用事件与`DOM`文档交互
*   `DOM Level 2 Style`：定义了如何以编程方式来访问和改变`CSS`样式信息
*   `DOM Level 2 Traversal and Range`：遍历`DOM`文档和选择其特定部分的新接口
*   `DOM Level 2 HTML`：在1级`HTML`基础上构建，添加了更多的属性方法和接口

> `DOM 3`增加了`XPATH`模块和`Load and Save`模块

#### DOM变化

可以通过如下代码来确定浏览器是否支持这些`DOM`模块：

```javascript
var supportDOM2Core = document.implementation.hasFeature("Core", "2.0");
var supportDOM3Core = document.implementation.hasFeature("Core", "3.0");
var supportDOM2HTML = document.implementation.hasFeature("HTML", "2.0");
var supportDOM2Views = document.implementation.hasFeature("Views", "2.0");
var supportDOM2XML = document.implementation.hasFeature("XML", "2.0");
```

**`针对XML命名空间的变化`**

有了`XML`命名空间的支持，不同`XML`文档的元素就可以混合在一起；`HTML`不支持`XML`命名空间，但是`XHTML`支持`XML`命名空间；命名空间使用`xmlns`属性来指定

**`DocumentType类型的变化`**

`DocumentType`类型新增了三个属性：`publicId` `systemId` `internalSubset`；前两个属性表示的是文档声明中的两个信息段，这两个信息段在`DOM1`中无法访问到，例如：

```xml
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
    "http://www.w3.org/TR/html4/strict.dtd">
    
<!DOCTYPE HTML PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/xhtml1-strict.dtd"
[<!ELEMENT name (#PCDATA)>]>
```
上面的代码中，`"-//W3C//DTD HTML 4.01//EN"`表示`publicId` `"http://www.w3.org/TR/html4/strict.dtd"`表示`systemId` `[<!ELEMENT name (#PCDATA)>]`表示`internalSubset`

**`Document类型的变化`**

**`Node类型的变化`**

**`框架的变化`**

框架和内嵌框架分别用`HTMLFrameElement`和`HTMLIFrameElement`表示，他们在`DOM2`中有一个新的属性`contentDocument`；这个属性是一个指向框架内容文档对象的指针（在此之前只能使用`frames`集合访问框架）：

```javascript
var iframe = document.getElementById("myIframe");
var iframeDoc = iframe.contentDocument;
//如果要兼容IE8之前浏览器需要使用下面的代码
var iframeDoc = iframe.contentDocument || iframe.contentWindow.document;
```
`contentDocument`对象是`Document`对象类型的一个实例，因此可以使用它的所有属性和方法；`IE8`之前的版本不支持这个属性，但是可以使用`contentWindow`属性所表示的`window`对象的`document`属性来访问内嵌框架文档对象

> 所有浏览器都支持`contentWindow`属性；访问框架或者内嵌框架的文档对象要受到跨域安全策略的限制

#### 样式

可以通过如下代码来确定浏览器是否支持`DOM2`级定义的`CSS`能力：

```javascript
var supportDOM2CSS = document.implementation.hasFeature("CSS", "2.0");
var supportDOM2CSS2 = document.implementation.hasFeature("CSS2", "2.0");
```

##### 访问元素的样式

任何支持`style`属性的元素在`JavaScript`中的元素对象中都有一个`style`属性，这个属性的值是`CSSStyleDeclaration`类型对象的实例，对象实例的每个属性对应`HTML`元素的`CSS`样式属性（使用连字符连接的两个单词属性名，需要转换为驼峰式的属性名），比如：

```javascript
background-image        style.backgroundImage
color                   style.color
display                 style.display
font-family             style.fontFamily
float                   style.cssFloat      //因为float是js的保留字，所以这是唯一一个不符合规则的名称
```
> 通过属性的方式不能获取外部样式表和嵌入样式表层叠而来的样式信息

> 在标准模式下，如果属性需要一个带单位的值就必须给出单位，在混杂模式下可以不指定单位但是，在实践中应该总是给出单位

> 如果没有为元素设置`style`属性，那么`style`对象中也可能包含一些默认的值，但这些值并不能准确的反应当前的样式信息

**`DOM2样式的属性和方法`**

`DOM2`级规范为`style`对象（元素的`style`属性值）定义了一些方法和属性：

```javascript
cssText         可以通过这个属性访问到元素`style`属性的`CSS`代码
length          应用给元素的`CSS`属性的数量
parentRule      表示`CSS`信息的`CSSRule`对象
getPropertyCSSValue(propertyName)       返回包含给定值的`CSSValue`对象
getPropertyPriority(propertyName)       如果给定的属性使用了`!important`设置，则返回`"important"`，否则返回空字符串
getPropertyValue(propertyName)      返回给定属性的字符串值
item(index)     返回给定位置的`CSS`属性名称
removeProperty(proertyName)     从样式表中删除给定的属性
setProperty(propertyName, value, priority)  将给定的属性设置为相应的值，并加上优先权标志（`"important"`或者`""`）
```
给`cssText`属性赋值会覆盖之前的所有属性
    
`length`经常和`item()`方法配合遍历所有`style`的属性，还可以使用`style[index]`的方法来访问；这两种方式取得的都是`style`对象的属性名（`"backgrond-color"`而非`backgroundColor`）；然后使用`getPropertyValue()`方法获取属性的值的字符串形式
    
`CSSValue`对象包含两个属性：`cssText` `cssValueType`；前者等同于`getPropertyValue()`方法返回的值，后者是一个数值常量：0表示继承的值，1表示基本的值，2表示值列表，3表示自定义的值
    
**`计算的样式`**

`DOM2`级样式增强了`document.defaultView`对象，提供了`getComputedStyle()`方法；这个方法接受两个参数：要取得计算样式的元素和一个伪元素字符串（例如`":after"`，如果不需要伪元素信息也可以为`null`）；这个方法返回一个`CSSStyleDeclaration`对象（和`style`属性值的类型相同），其中包含了所有计算出的样式

`IE`浏览器不支持`getComputedStyle()`，但是`style`对象中有一个`currentStyle`属性，这个属性是也是`CSSStyleDeclaration`类型的实例，包含当前元素所有计算出的样式
    
##### 操作样式表

在`HTML`中样式的两种元素是由`HTMLLinkElement` `HTMLStyleElement`类型表示的；但是另外一种类型`CSSStyleSheet`类型则更加通用，它只表示样式表，而不管这些样式表是如何定义的；另外`CSSStyleSheet`对象提供的是一套只读的接口（`disabled`属性除外）；使用下面的代码可以确定浏览器是否支持这个类型：

```javascript
var supportsDOM2StyleSheets = document.implementation.hasFeature("StyleSheets", "2.0")
```

`CSSStyleSheet`继承自`StyleSheet`，继承的属性如下：

```javascript
disabled        表示样式表是否被禁用的布尔值。这个属性是可写的
href            如果样式表是通过`<link>`包含的，则是样式表的`URL`；否则是`null`
media           当前样式表支持的所有媒体类型的集合，这个集合也有一个`length`属性和一个`item()`方法，也可以使用方括号取得集合中的特定项；如果为空集合则表示支持所有媒体类型，在`IE`中这个属性获得是样式表中`media`属性值的字符串

ownerNode       指向拥有当前样式表的节点的指针；如果样式表是其他样式表通过`@import`导入的，则这个属性值为`null`；`IE`不支持这个属性

parentStyleSheet    在当前样式表是通过`@import`导入的情况下，这个属性是一个指向导入它的样式表的指针

title       ownerNode中title属性的值
type        表示样式表类型的字符串。对于`CSS`样式表而言，这个字符串是`"type/css"`
```

除了上面继承的一些属性外，还有如下属性和方法：

```javascript
cssRules        样式表中包含的规则（样式）的集合，`IE`不支持这个属性但是有一个类似的`rules`属性
ownerRule       如果样式表是通过`@import`导入的，这个属性就是一个指针，指向表示导入的规则；否则为`null`，`IE`不支持这个属性
deleteRule(index)   删除`cssRules`集合中指定位置的规则（样式）；`IE`不支持这个方法，但是支持一个类似的`removeRule()`方法
insertRule(rule, index)     向`cssRules`集合中指定的位置插入`rule`（样式）字符串；`IE`不支持这个方法，但是支持一个类似的`addRule()`方法
```

应用于文档的所有样式表是通过`document.styleSheets`属性所表示的集合来包含的，通过这个集合的`length`属性可以获知文档中样式表的数量，通过方括号或者`item()`方法可以访问每一个样式表（`CSSStyleSheet`类型）

> 这个属性只包含`rel`属性值为`stylesheet`的`<link>`元素和`type`属性值为`text/css`的`<style>`元素

也可以直接通过`<link>`或者`<style>`元素的`sheet`属性取得`CSSStyleSheet`对象（在`IE`浏览器中是`stylesheet`属性）

**`CSS规则（样式）`**

`CSSRule`对象表示样式表中的每一条规则，这个对象是供其他多种类型继承的基类，其中最常用的是`CSSStyleRule`类型，表示样式信息，这种对象类型包含如下属性：

```javascript
cssText     返回整条规则对应的文本；浏览器对样式表内部处理方式不同，返回的文本可能与样式表中实际的文本不同；safari总是将文本转换为全部小写
parentRule  如果当期规则是导入规则，则这个属性引用的就是导入规则；否则这个值为null
parentStyleSheet    当前规则所属的样式表
type        表示规则类型的常量值。对于样式规则，这个值为1
    
selectorText    返回当前规则的选择符文本；这个文本可能和样式表中的实际文本不一样；safari会转换为小写，除opera外这个属性是只读的
style       表示一个`CSSStyleDeclaration`对象，可以通过它设置和取得规则中特定的样式值
```
> `IE`浏览器不支持前四个属性
    
上面比较常用的三个属性是：`cssText` `selectorText` `style`
    
`cssText`属性和`style.cssText`属性类似，但是前者包含选择符文本和围绕样式的花括号，后者只包含样式信息（和元素的`style.cssText`相同）；另外`cssText`是只读的
    
`style`属性可以用于获取和设置规则中的样式信息：
    
```javascript
var sheet = document.styleSheet[0];
var rules = sheet.cssRules || sheet.rules;
var rule = rules[0];
alert(rule.selectorText);
alert(rule.style.cssText);
alert(rule.style.backgroundColor);
```
    
**`创建规则（样式）`**

要向现有的样式表中添加新的规则，需要使用`insertRule()`方法；这个方法接收两个参数：规则文本和表示在哪里插入规则的索引；在`IE`中则需要使用`addRule()`方法

下面是插入规则的跨平台代码：
    
```javascript
function insertRule(sheet, selectorText, cssText, position) {
    if(sheet.insertRule) {
        sheet.insertRule(selectorText + "{" + cssText + "}", position);
    } else if(sheet.addRule) {
        sheet.addRule(selectorText, cssText, position);
    }
}
```
    
**`删除规则（样式）`**

要删除现有样式表中的规则可以使用`deleteRule(position)`方法；在`IE`中使用`removeRule(position)`方法

下面是删除规则的跨平台代码：
    
```javascript
function deleteRule(sheet, index) {
    if(sheet.deleteRule) {
        sheet.deleteRule(index);
    } else if(sheet.removeRule) {
        sheet.removeRule(index);
    }
}
```
    
##### 元素大小

下面的这些确定页面中元素大小的属性和方法并不是`DOM2`级规范，而是由`IE`浏览器首先使用，现在所有主流浏览器都已经支持这些（元素对象的）属性和方法

**`偏移量`**

偏移量定义的是元素的可见空间大小相对于其父元素偏移值；可见空间为除外边距的元素大小；通过下面四个属性可以获取元素的偏移量：

```javascript
offsetHeight        元素在垂直方向上占用的空间大小，以像素计
offsetWidth         元素在水平方向上占用的空间大小，以像素计
offsetLeft          元素左外边框至包含元素的左内边框之间的像素距离
offsetTop           元素上外边框至包含元素的上内边框之间的像素距离
```
> 包含元素的引用保存在`offsetParent`属性中；这个属性不一定与`parentNode`的值相等；比如，`<td>`元素的`offsetParent`是`<table>`元素，因为`<tr>`元素没有大小

要想知道某一个元素在页面上的偏移量，需要层层获取`offsetParent`至根元素，然后将所有`offsetLeft`或者`offsetTop`相加即可：

```javascript
function getElementLeft(element) {
    var actualLeft = element.offsetLeft;
    var current = element.offsetParent;
    
    while(current != null) {
        actualLeft += current.offsetLeft;
        current = current.offsetParent;
    }
    
    return actualLeft;
}

function getElementTop(element) {
    var actualTop = element.offsetTop;
    var current = element.offsetParent;
    
    while(current != null) {
        actualTop += current.offsetTop;
        current = current.offsetParent;
    }
    return actualTop;
}
```
> 对于使用表格和内嵌框架布局的页面，可能得到得到值不是很准确

> 所有这些属性都是只读的，并且每一次访问它们都需要重新计算；所以在需要重复访问这些属性的代码中，应该将他们保存在本地变量中

**`客户区大小`**

元素的客户区大小（client dimension）是指元素的内容及其内边距所占空间的大小；有两个属性可以用来获取这些值：`clientWidth` `clientHeight`，这两个属性分别表示，内容宽高加上分别的两个内边距的宽度

> 这两个属性的值不包括滚动条的大小；并且同样是只读的，而且每次访问都需要重新计算

这两个属性的一个应用是要确定浏览器视口的大小，可以使用如下代码：

```javascript
function getViewport() {
    if(document.compatMode == "BackCompat") {
        return {
            width: document.body.clientWidth,
            height: document.body.clientHeight
        };
    } else {
        return {
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        };
    }
}
```

**`滚动大小`**

滚动大小（scroll dimension）是指包含滚动内容的元素的大小。有些元素（比如`<html>`），会自动添加滚动条；但是另外一些元素则需要手动设置`CSS`的`overflow`样式才能滚动；下面是与滚动相关的属性：

```javascript
scrollHeight        没有滚动条的情况下，元素内容的总高度（元素的实际高度）
scrollWidth         没有滚动条的情况下，元素内容的总宽度（元素的实际宽度）
scrollLeft          被隐藏在内容区域左侧的像素数；为这个属性赋值可以改变元素滚动位置
scrollTop           被隐藏在内容区域上方的像素数；为这个属性赋值可以改变元素的滚动位置
```

后面两个属性即可以获取当前元素的滚动状态，也可以设置元素的滚动位置。在元素尚未滚动时这两个属性的值为0，如果元素被滚动了这两个指标是滚动后隐藏的部分的大小

下面是一个回到顶部的代码：

```javascript
function scrollToTop(element) {
    if(element.scrollTop != 0) {
        element.scrollTop = 0;
    }
}
```

**`确定元素大小`**

#### 遍历

`DOM2`级遍历和范围模块定义了两个用于辅助完成顺序遍历`DOM`结构的类型：`NodeIterator` `TreeWalker`；这两个类型能够基于给定的任意起点对子`DOM`结构进行深度优先的遍历；除`IE`浏览器之外其他的浏览器都支持这两个类型

##### NodeIterator类型

可以使用`document.createNodeIterator()`方法创建这种类型的一个实例，这个方法接受四个参数：

*   `root`    想要作为搜索起点的树中的节点
*   `whatToShow`      表示想要访问的节点类型的数字代码的位掩码
*   `filter`    是一个`NodeFilter`对象，或者一个表示应该接受或者拒绝某种特定节点的函数，如果不指定过滤器则是`null`
*   `entityReferenceExpansion`  布尔值，表示是否要扩展实体引用（这个参数在`HTML`中没有用）

`whatToShow`通常使用下面的常量的 **按位或** 操作：

```javascript
NodeFilter.SHOW_ALL         所有类型节点
NodeFilter.SHOW_ELEMENT     元素节点
NodeFilter.SHOW_COMMENT     注释节点
NodeFilter.SHOW_TEXT        文本节点
NodeFilter.SHOW_DOCUMENT    文档节点
NodeFilter.SHOW_DOCUMENT_TYPE       文档类型节点
NodeFilter.SHOW_ATTRIUBTE           属性节点（实际上不能遍历这个值）
NodeFilter.SHOW_CDATA_SECTION       CDATA节点（在HTML中无意义）
NodeFilter.SHOW_ENTITY_REFERENCE    实体引用节点（在HTML中无意义）
NodeFilter.SHOW_ENTITYE             实体节点（在HTML中无意义）
NodeFilter.SHOW_PROCESSING_INSTRUCTION  处理指令节点（在HTML中无意义）
NodeFilter.SHOW_DOCUMENT_FRAGMENT   文档片段节点（在HTML中无意义）
NodeFilter.SHOW_NOTATION            符号节点（在HTML中无意义）
```

`filter`参数可以是一个自定义的对象，这个对象只有一个方法`acceptNode()`，如果应该访问某个节点则这个方法返回`NodeFilter.FILTER_ACCEPT`，否则返回`NodeFIlter.FILTER_SKIP`：

```javascript
var filter = {
    acceptNode : function(node) {
        return node.tagName.toLowerCase() == "p" ? NodeFilter.FILTER_ACCEPT : NodeFIlter.FILTER_SKIP;
    }
};

var iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
```
> 这个参数也可以直接是一个功能类似的函数：接受`node`参数，返回`FILTER_ACCEPT`或者`FILTER_SKIP`

`NodeIterator`类型的两个主要方法是：`nextNode()` `previousNode()`；在刚刚创建的`NodeIterator`对象中，第一次调用`nextNode()`会返回创建这个对象类型所基于的根节点，当遍历到子树的最后一个节点时再调用`nextNode()`会返回`null`；`previousNode()`方法工作机制类似

> 因为这两个方法都是基于`NodeIterator`在`DOM`结构中的内部指针工作，所以`DOM`结构的变化会反应在遍历结果中

##### TreeWalker类型

这种类型是`NodeIterator`类型更高级的版本，这种类型的对象除了支持`nextNode()` `previousNode()`方法之外，还提供了下列用于在不同方向遍历`DOM`的方法：

```javascript
parentNode()    遍历到当前节点的父节点
firstChild()    遍历到当前节点的第一个子节点
lastChild()     遍历到当前节点的最后一个子节点
nextSibling()   遍历到当前节点的下一个同辈节点
previousSibling()   遍历到当前节点的上一个同辈节点
```

创建`TreeWalker`对象需要使用`document.createTreeWalker()`方法，这个方法接收的四个参数和`createNodeIterator()`方法相同

`filter`参数除了可以返回上节所述的两种值之外还可以返回：`NodeFilter.FILTER_REJECT`，这个返回值表示跳过这个节点及这个节点所有的子节点

`TreeWalker`类型对象还有一个属性`currentNode`，表示上一次调用遍历方法时返回的节点；通过改变这个属性的值可以修改遍历继续进行的起点

#### 范围

范围用来选取文档中的一个区域，而不必考虑节点的界限；这种选择在后台完成，对用户来说是不可见的

当前几乎所有浏览器都实现了`DOM`中的范围；而`IE8`之前的浏览器则以自己的方式实现了范围（SB）

##### `DOM`范围

如果浏览器支持范围，就可以使用`document.createRange()`方法来创建一个`DOM`范围，然后就可以使用它在后台选择文档中的特定部分；如果对创建的返回设置位置之后，还可以针对范围的内容执行更多种操作

> 新创建的范围和创建它的文档对象`document`关联在一起，不能用于别的文档

每一个范围由一个`Range`类型的实例表示，这个类型对象有很多的属性和方法

下面的属性提供了当前范围在文档中的位置信息（将范围放置在文档中特定位置时，这些值就被赋值）：

```javascript
startContainer      选区中第一个节点的父节点
startOffset         范围在`startContainer`中的偏移量；如果`startContainer`是文本节点、注释节点或者CDATA节点，那么这个属性是范围起点之前跳过的字节数；否则就是范围中第一个子节点在父节点中的索引
endContainer        选区中最后一个节点的父节点
endOffset           范围在`endContainer`中的偏移量，规则和`startOffset`相同
commonAncestorContainer: `startContainer`和`endContainer`共同的祖先节点在文档树中最深的那一个节点
```

**`DOM范围的简单选择`**

创建一个范围（`Range`）对象之后，就需要选择文档的一部分；最简单的可以使用这个对象的`selectNode()`和`selectNodeContents()`方法，这两个方法都接受一个参数：一个`DOM`节点，然后使用该节点的信息来填充范围；第一个方法会选取整个节点，第二个方法只选择节点的子节点

为了更精细的控制将哪些节点包含在范围中，还可以使用下面的方法：

```javascript
setStartBefore(refNode)     将范围的起点设置在`refNode`之前，因此`refNode`也就是范围选区的第一个子节点
setStartAfter(refNode)  将范围的起点设置在`refNode`之后，因此其下一个同辈节点是范围选区的第一个子节点
setEndBefore(refNode)   将范围的终点设置在`refNode`之前，其上一个同辈节点才是范围选区中最后一个子节点
setEndAfter(refNode)    将范围终点设置在`refNode`之后，因此`refNode`也就是范围选区中的最后一个子节点
```
> 调用上面的方法时，前面提到的那些属性都会自动设置好；当然也可以直接指定这些属性的值

**`DOM范围的复杂选择`**

使用`setStart()`和`setEnd()`方法可以创建复杂的范围；这两个方法都接受两个参数：参照节点和一个偏移量值；对`setStart()`方法来说参照节点会变成`startContainer`，而偏移量值会变成`startOffset`；对`setEnd()`方法来说参照节点会变成`endContainer`，而偏移量值会变成`endOffset`

这一部分内容在本次学习过程中并未详细深入的了解，详细信息参考`《JavaScript高级程序设计》p333`

**`操作DOM范围中的内容`**

**`插入DOM范围中的内容`**

**`折叠DOM范围`**

**`比较DOM范围`**

**`复制DOM范围`**

**`清理DOM范围`**

##### IE8及之前的范围
