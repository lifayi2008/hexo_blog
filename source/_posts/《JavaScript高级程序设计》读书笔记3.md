---
title: 《JavaScript高级程序设计》读书笔记3
date: 2016-12-07 13:45:19
tags: JavaScript
categories: JavaScript
---
### 事件

`JavaScript`和`HTML`之间的交互是以事件的方式进行的；事件就是文档或者浏览器窗口中发生的一些特定的交互瞬间。`DOM2`开始对浏览器中的事件进行规范，几乎所有的浏览器都实现了`DOM2`级事件的核心模块部分（IE8除外）
<!-- more -->
下面是事件中的一些需要理解的核心部分：

#### 事件流

`IE`和`Netscape`使用的分别是`事件冒泡流`和`事件捕获流`

**`事件冒泡`**

即事件开始由最具体的元素接收，然后逐级向上传播到较为不具体的节点；比如在一个`<body>`元素中有一个`<div>`元素上发生了点击事件，则接收到事件的节点顺序依次是：`<div>` `<body>` `<html>` `Document`

> 目前几乎所有浏览器都支持事件冒泡，在实际开发中建议总是使用`事件冒泡`，在需要的时候才使用`事件捕获`

> `IE9`以后的浏览器及其他主流浏览器会将事件一直冒泡到`window`对象

**`事件捕获`**

即不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件

目前`IE9+`及其他主流浏览器都支持事件捕获；虽然`DOM2`级事件规范要求从`document`对象开始传播，但是这些浏览器实现都是从`window`对象开始捕获事件的

**`DOM事件流`**

`DOM2`级事件规定事件流包括三个阶段：`事件捕获阶段` `处于目标阶段` `事件冒泡阶段`；在`DOM`事件流中，实际的目标节点在捕获阶段不会收到事件，事件在目标节点的父节点一级就停止了；然后就是`处于目标`阶段，这时事件在节点（元素）上发生（这个阶段被看做是事件冒泡阶段的一部分）；最后事件冒泡发生，事件又传播回文档

> `DOM2`级规范明确指出捕获阶段不会涉及事件目标，但是最新版本的所有浏览器都会在捕获阶段触发事件对象上的事件，这样就会有两个机会在目标对象上操作事件

> `IE8`不支持`DOM事件流`

#### 事件处理程序

事件处理程序就是为事件绑定的用来响应事件的函数；事件处理程序的名字通常为`on + 事件名`的形式；可以使用几种方法来指定事件处理程序

**`HTML事件处理程序`**

某一个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的`HTML`属性来指定，属性的值应该是能够执行的`JS`代码：

```javascript
<input type="button" value="Click Me" onclick="alert('Clicked')" />
```
> 不能在属性值中使用未经转义的`HTML`语法字符：`&` `"` `<` `>`（可以使用`HTML`实体来代替）

也可以将元素事件属性的值指定为一个在别处定义的 **函数名的调用** （这实际上还是`JS`代码调用）：

```javascript
<script type="text/javascript">
    function showMessage() {
        alert("Hello world!");
    }
</script>
<input type="button" value="Click Me" onclick="showMessage()" />
```
> 在属性值中执行的代码可以访问全局作用域中的任何代码（调用任意函数）

在事件属性值中调用的函数可以访问如下属性：`event`表示事件对象本身；`this`等于事件目标元素（因为事件处理程序是在元素的作用域范围内执行的）

> 实际测试中这里的`this`表示`window`对象

> 可以通过扩展作用域的方式来直接在事件属性值中访问其他作用域中的属性；但是要注意这可能在不同的浏览器会导致不同的结果

使用`HTML`事件处理程序有以下缺点：

*   用户点击按钮触发事件时相应的`JS`代码可能还未加载完成
*   扩展事件处理程序作用域链的行为在不同的浏览器中不一致
*   `HTML`代码和`JS`代码耦合过紧

**`DOM0级事件处理程序`**

这种事件处理程序要先获取要绑定事件处理程序的元素，然后通过为其相应的事件属性赋值（包括`window`和`document`元素也有相关事件处理程序属性）：

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function () {
    alert("Clicked");
};
```
> 这种方式指定的事件处理程序同样在元素的作用域范围内执行，可以在程序中使用`this`访问元素本身的属性和方法

> 这种方式指定的事件处理程序同样存在`HTML`事件处理程序中的第一个问题；另外`DOM0`只能添加一个事件处理程序

使用`btn.onclick = null`可以删除事件处理程序；如果通过`HTML`事件处理程序为一个元素绑定`onclick`事件，那么这个元素的`onclick`属性也就是绑定的事件函数代码；所以通过`HTML`事件处理程序绑定的事件也可以通过将对应属性赋值为`null`的方式来删除事件

**`DOM2级事件处理程序`**

`DOM2`级事件增加了两个方法用于添加和删除事件处理程序：`addEventListener()` `removeEventListener()`；所有的`DOM`节点都包含这两个方法，他们接收单个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值（如果是`true`则表示在捕获阶段调用，如果是`false`则表示在冒泡阶段调用事件处理程序）：

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

> `IE9`和其他主流浏览器的较新的版本都支持`DOM2`级事件处理程序

**`IE事件处理程序`**

`IE`使用另外两个方法来添加事件处理程序：`attachEvent()` `detachEvent()`；这两个方法也接受相同的两个参数：事件处理程序名称和事件处理程序函数；使用这个方法添加的事件处理程序会添加到冒泡阶段

使用这个方法添加的事件处理程序和`DOM0` `DOM2`两种的主要区别是，事件处理程序被添加到全局作用域中（处理函数中的`this`对象等同于`window`）

> `IE`事件处理程序在添加事件时要在事件名前面加上`on`

使用`attacheEvent()`方法也可以为一个元素添加多个事件处理程序；与`DOM2`不同的是添加的多个事件处理程序按照加入的顺序反向执行

使用`attacheEvent()`方法添加的事件处理程序也可以使用`detachEvent()`方法移除，后者要传入的参数和创建事件处理程序时一致

**`跨浏览器的事件处理程序`**

可以使用如下代码在跨浏览器中处理事件的添加和移除：

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

#### 事件对象

在触发`DOM`上的某一个事件时，会产生一个事件对象，这个对象中包含着所有与事件相关的信息，比如：和事件相关的元素、事件类型及和特定事件相关的信息

**`DOM中的事件对象`**

使用`DOM0`或者`DOM2`级事件处理程序，`DOM`都会将一个`event`对象传入到事件处理程序中；而使用`HTML`事件处理程序则直接可以访问`event`变量：

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

属性/方法 | 类型 | 读写 | 说明
---|---|---|---
`bubbles` | Boolean | 只读 | 表明事件是否冒泡
`cancelable` | Boolean | 只读 | 表明是否可以取消事件的默认行为
`target` | Element | 只读 | 实际触发事件的元素
`currentTarget` | Element | 只读 | 事件绑定的元素 
`defaultPrevented` | Boolean | 只读 | 为`true`表示已经调用了`preventDefault()`（DOM3新增）
`detail` | Integer | 只读 | 与事件相关的细节信息
`eventPhase` | Integer | 只读 | 调用事件处理程序的阶段：1（捕获）2（处于目标）3（冒泡）
`preventDefault()` | Function | 只读 | 取消事件的默认行为（需要`cancelable`为`true`）
`stopImmediatePropagation()` | Function | 只读 | 取消事件的进一步捕获或者冒泡，同时阻止任何事件处理程序被调用（DOM3新增）
`stopPropagation()` | Function | 只读 | 取消事件的进一步捕获或者冒泡（需要`bubbles`为`true`）
`trusted` | Boolean | 只读 | 为`true`表示事件是浏览器生成的，否则表示通过`JS`创建的（DOM3新增）
`view` | AbstractView | 只读 | 与事件关联的抽象视图。等同于发生事件的`window`对象

> `trusted`在大多数浏览器中都未实现

如果将事件处理程序指定给了目标元素（即绑定事件处理程序的元素和触发事件的元素相同），则`this` `currentTarge` `target`包含相同的值；因为事件流的存在所以这三个值可能不同，比如如果给`<body>`元素绑定了事件，这时如果我们点击`<body>`中的一个按钮时，`target`为这个按钮元素，而`this`和`currentTarget`为`<body>`元素

在一个事件处理程序中调用事件对象`event`的`stopPropagation()`方法可以阻止事件的进一步捕获或者冒泡

> 只有在事件处理程序执行期间，`event`对象才会存在，一旦事件处理程序执行完成，对象就会被销毁

**`IE中的事件对象`**

要访问`IE`中的`event`对象有几种不同的方式，取决于指定事件处理程序的方法：

*   使用`DOM0`级事件方法添加的处理程序中，`event`对象作为`window`对象的一个属性存在：`window.event`
*   使用`attachEvent()`方法添加的事件处理程序中，`event`被作为参数传入到事件处理函数中
*   通过`HTML`属性指定的事件处理程序，可以通过名为`event`的变量访问`event`对象

根据事件类型不同，`event`对象中有不同的属性和方法，但是所有类型的事件都有如下属性：

属性/方法 | 类型 | 读写 | 说明
---|---|---|---
`cancelbubble` | Boolean | 读写 | 默认值为`false`，将其设置为`true`可以取消事件冒泡（和`stopPropagation()`作用相同）
`returnValue` | Boolean | 读写 | 默认为`true`，将其设置为`false`可以取消事件的默认行为（和`preventDefault()`作用相同）
`srcElement` | Element | 只读 | 事件的目标（和`src`相同）
`type` | String | 只读 | 被触发的事件类型

根据设置事件处理程序的方式不同，事件处理程序运行的环境也不同；所以`this`不总是等于绑定事件处理程序的元素，通常应该使用`event.srcElement`

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

#### 事件类型

`DOM3`级事件规定了如下几种事件类型：

*   `UI事件`：当用户与界面上的元素交互时触发
*   `焦点事件`：当元素获得或者失去焦点时触发
*   `鼠标事件`：当用于通过鼠标在页面上执行操作时触发
*   `滚轮事件`：当使用鼠标滚轮时触发
*   `文本事件`：当在文档中输入文本时触发
*   `键盘事件`：当用户通过键盘在页面上执行操作时触发
*   `合成事件`：当`IME`输入字符时触发
*   `变动事件`：当底层`DOM`结构发生变化时触发
*   `变动名称事件`：当元素或者属性名变动时触发（已废弃）

> `IE9+`及其他主流浏览器都支持`DOM2`级和`DOM3`级事件

##### UI事件

`UI事件`是指那些不一定与用户操作有关的事件，在`DOM`中保留是为了向后的兼容；这些事件除了`DOMActive`在`DOM2`级事件中都归为`HTML`事件

要确定浏览器是否支持`DOM2`级事件中的这些`HTML`事件可以使用如下代码：

```javascript
var isSupproted = document.implementation.hasFeature("HTMLEvents", "2.0");
```

要确定浏览器是否支持`DOM3`级事件中的`UI`事件可以使用如下代码：

```javascript
var isSupproted = document.implementation.hasFeature("UIEvent", "3.0");
```

**`DOMActive`**

表示元素已经被用户操作激活；不建议使用这个事件

**`load事件`**

页面完全（包括图像、`Javascript`、`CSS`等外部资源）加载后在`window`上触发；当所有框架都加载完毕时在框架集上面触发；当图像加载完毕时在`<img>`元素上触发；或者当嵌入的内容加载完毕时在`<object>`元素上面触发

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
EventUtil.addHandler(window, "load", function() {
//在向一个页面中加入元素时，必须等待页面加载完成，否则会出错
    var image = document.createElement("img");
    EventUtil.addHandler(image, "load", function() {
        event = EventUtil.getEvent(event);
        alert(EventUtil.getTarget(event).src);
    });
    document.body.appendChild(image);
    image.src = "smile.gif";
    //新图像元素在指定了src属性之后就会开始下载资源，而不是在加入到文档之后才开始
});
```
`firefox`浏览器支持在`<script>`元素上指定`load`事件；`IE` `Opera`浏览器支持在`<link>`元素上指定`load`事件

**`unload事件`**

当页面完全卸载后在`window`上触发；当所有框架都卸载后在框架上面触发；或者当嵌入的内容卸载完毕后在`<object>`元素上面触发

指定`onunload`事件处理程序也可以使用如下两种方式：
    
```javascript
EventUtil.addHandler(window, "unload", function(event) {
    alert("Unloaded");
});
//这里的event同样不会包含事件相关的信息，但是兼容dom的浏览器中，event.target会被设置为document
    
<body onunload="alert('Unloaded!')">
```
> 一旦页面卸载之后，再对页面执行`dom`操作就会出现错误

**`abort`**

在用户停止下载过程时，如果嵌入的内容没有加载完，则在`<object>`元素上面触发

**`error`**

当发生`JavaScript`错误时在`window`上面触发；当无法加载图像时在`<img>`元素上面触发；当无法加载嵌入的内容时`<object>`元素上面触发；或者当有一个或者多个框架无法加载时在框架上面触发

**`select`**

当用户选择文本框（`<input>` `<textarea>`）中的一或者多个字符时被触发

**`resize`**

当窗口或者框架的大小变化时在`window`或该框架上触发

可以使用在`window`上面添加事件处理程序或者在`<body>`元素添加`onresize`属性的方式来添加；更推荐使用下面的方法：

```javascript
EventUtil.addHandler(window, "resize", function(event){
    alert("Resized");
});
```
> 同其他在`window`上触发的事件一样，在兼容`DOM`的浏览器上`event`对象只有`target`属性被赋值，而`IE`事件对象则没有任何属性
    
另外`firefox`浏览器只有在用户停止调整窗口大小时才会触发这个事件；其他浏览器只要用户调整窗口大小一个像素就会触发，然后在用户调整的过程中会不断的触发（所以应该尽可能的保持这个事件处理程序的简单）
    
> 窗口的最大化或者最小化也会触发这个事件

**`scroll`**

当用户滚动带滚动条的元素中的内容时，在该元素上面触发；`<body>`元素中包含加载页面的滚动条

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
    
##### 焦点事件

利用这些事件和`document.hasFocus()`方法以及`document.acitveElement`属性配合，可以跟踪用户的行为；下面是六个焦点事件：

*   `blur`：在元素失去焦点时触发，这个事件不会冒泡；所有浏览器都支持
*   `focus`：在元素获得焦点时触发，这个事件不会冒泡；所有浏览器都支持

> 以上两个为首选使用的事件

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

##### 鼠标与滚轮事件

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

可以使用如下代码检查浏览器是否支持支持以上`DOM2`级相关的事件（除`dbclick` `mouseenter` `mouseleave`）:

```javascript
var isSupproted = document.implementation.hasFeature("MouseEvents", "2.0");
```

要检查浏览器是否支持上面的所有事件可以使用如下代码：

```javascript
var isSupported = document.implementation.hasFeature("MouseEvent", "3.0");
//dom3级中事件的名称是MouseEvent
```

获取鼠标事件发生时，鼠标指针在 **视口** 中的位置可以使用事件对象的`clientX` `clientY`属性（所有浏览器都支持）：

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

**`滚轮事件`**

这个事件可以在任意元素上面触发，最终会冒泡到`window`（在`IE8`中是`document`）；这个事件的事件对象除了包含鼠标事件的信息之外还包括`wheelDelta`属性，当用户向前滚动滚轮时，值是120的倍数，当用户向后滚动滚轮时，值是-120的倍数

`Firefox`使用了一个与众不同的`DOMMouseScroll`事件，来在鼠标滚轮滚动时触发，这个事件会冒泡到`window`对象；事件对象的`detail`属性使用3的倍数表示向后滚动，-3的倍数表示向前滚动

如下是跨浏览器获取滚轮事件信息的示例代码：

```javascript
var EventUtil = {
    ...
    
    getWheelDelta: function(event) {
        if(event.wheelDelta) {
            return (client.engine.opera && client.engine.opera < 9.5 ?
                -event.wheelDelta : event.wheelDelta);
        } else {
            return -event.detail * 40;
        }
    },
};

(function () {
    function handleMouseWheel(event) {
        event = EventUtil.getEvent(event);
        var delta = EventUtil.getWheelDelta(event);
        alert(delta);
    }
    
    EventUtil.addHandler(document, "mousewheel", handleMouseWheel);
    EventUtil.addHandler(document, "DOMMouseScroll", handleMouseWheel);
    
})();
```

##### 键盘与文本事件

`DOM3`级事件为键盘事件制定了规范，之前主要是是`DOM0`级相关的规范；`DOM0`键盘事件有以下三个：

*   `keydown`当用户按下键盘上的任意键时触发，如果按住不放的话会重复触发此事件
*   `keypress`当用户按下键盘上的字符键时触发，如果管按住不放的话会重复触发此事件
*   `keyup`当用户释放按键时触发

这三个事件的触发顺序如下：`keydown` --- `keypress`（如果用户按下字符键） --- `keyup`；如果用户一直按着不放，则会重复触发前两个事件，直到松开按键时会触发最后一个事件

在发生`keydown`和`keyup`事件时，`event`对象的`keyCode`属性会包含一个与键盘上特定按键对应的代码；如果是数字和字符键则和`ASCII`码对应（`shift`+字符也被识别为小写）

> 其他非字符数字按键的编码参考`《JavaScript高级程序设计》`

在所有浏览器中，按下能够输入和被删除的字符按键都会触发`keypress`事件，这个事件对象中包含一个`charCode`属性（`IE8`不支持），属性值为按下的那个按键所代表的字符的`ASCII`码，而`keyCode`属性的值根据浏览器而不同（`IE8`和`Opera`中为对应的`ASCII`码，其他的为0），所以可以编写如下跨浏览器获取用户当前按下按键的`ASCII`编码的代码：

```javascript
var EventUtil = {
    ...
    
    getCharCode : function(event) {
        if(typeof event.charCode == "number") {
            return event.charCode;
        } else {
            return event.keyCode;
        }
    },
};
```

`DOM3`中为键盘事件对象添加了`key` `char` `location`属性和`getModifierState()`方法。`key`和`char`属性是为了取代`keyCode` `charCode`属性，它的值是一个字符串：在按下某个字符键时，`key`和`char`就是相应的字符；在按下某一个非字符按键时，`key`的值是相应的键名，`char`的值是`null`

> `firefox` `chrome`的最新版浏览器已经支持这些方法和属性，其他浏览器支持情况请在使用前测试

`DOM3`新增一个在文本被插入文本框之前触发的文本事件：`textInput`；这个事件和`keypress`的不同之处在于只有可编辑区域才会触发前者，而任何可以获取焦点的元素都可以触发后者；另外，前者在只有在按下能够输入的实际字符按键时才会触发，而后者在按下那些能影响文本显示的按键时也会触发（如退格键）

`textInput`事件对象包含一个`data`属性，这个属性值就是按下的字符（而非编码），这个事件可以识别输入的大写字符；另外还有一个`inputMethod`属性表示把文本输入到文本框中的方式（只有`IE`支持，详细信息参考`《JavaScript高级程序设计》`）

##### 复合事件

这是`DOM3`中新增的一类事件用于处理`IME`的输入序列，`IME`表示输入法编辑器，可以让用户输入在键盘上没有的字符

##### 变动事件

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
> `IE9`及其他浏览器较新版本都支持前五个变动事件；建议在开发中不要使用后两个变动事件

**`删除节点时`**

在使用`removeChild()` `replaceChild()`方法从`DOM`节点中移除节点时，会首先触发`DOMNodeRemoved`事件，这个事件的`event.target`属性是被删除的节点，而`event.relatedNode`属性包含着对目标节点父节点的引用；这个事件触发时，节点尚未从其父节点中移除，因此`parentNode`等同于`event.relatedNode`；另外这个事件会冒泡，因此可以在`DOM`的任何层次上处理它

如果被移除的节点包含子节点，那么在其所有子节点以及这个被删除的节点上会相继触发`DOMNodeRemovedFromDocument`事件，但是这个事件不会冒泡；这个事件的`event.target`属性是相应的元素。除此之外事件对象不包含其他信息

最后会在要移除节点的父节点上触发`DOMSubtreeModified`事件（事件的目标是被移除节点的父节点）

**`插入节点时`**

对应的使用添加节点的方法时，首先会触发`DOMNodeInserted`事件；这个事件的目标是被插入的节点，而`event.relatedNode`属性中包含对父节点的引用；在这个事件触发时，节点已经被插入到新的父节点中；这个事件是冒泡的

然后会触发`DOMNodeInsertedIntoDocument`事件，这个事件的目标是被插入的节点；这个事件不会冒泡

最后会触发`DOMSubtreeModified`事件，这个事件的目标是新插入节点的父节点

##### HTML5事件

`HTML5`详细的列出了浏览器应该支持的所有事件。本节只讨论其中得到浏览器完善支持的事件

**`contentmenu事件`**

这个事件用以表示何时应该显示上下文菜单，以便开发人员取消默认的上下文菜单而提供自定义菜单；因为这个事件是冒泡的，所以可以为`document`对象指定给一个事件处理程序来处理页面中任何元素产生的这个事件

在所有浏览器中都可以使用`event.preventDefault()`或者将`event.returnValue`设置为`false`来阻止默认的行为

因为这个事件属于鼠标事件，所以其事件对象中包含与光标位置有关的所有属性；通常会使用`contentmenu`事件来显示自定义的上下文菜单，使用`onclick`事件处理程序来隐藏该菜单

> 目前所有主流浏览器都支持这个事件

**`beforeunload事件`**

这一事件可以让开发人员在页面卸载之前做一些操作，通常是提示用户是否关闭本页面；要达成上述操作（弹出提示对话框），必须将这个事件对象`event.returnValue`值设置为要显示给用户的字符串（为`IE` `Firefox`），同时将这个字符串作为绑定事件处理函数的返回值（为`Safari` `Chrome`）

如果用户点击弹出对话框中的`OK`则继续卸载网页，如果用户点击`Cancel`则终止卸载网页

> 目前所有主流浏览器都支持这个事件；`Opera11`之前不支持这个事件

**`DOMContentLoaded事件`**

`window`的`load`事件会在页面中的所有资源都加载完毕后触发；而`DOMContentLoaded`事件则在形成完整的`DOM`树之后就会触发，而不管图像、JS文件和CSS文件等资源文件是否已经加载完毕

可以为`document`或者`window`添加相应的事件处理程序；这个事件会冒泡到`window`，但是事件实际的目标是`document`；这个事件处理程序的对象不包含其他有效信息

> `IE9+`及其他主流浏览器的最新版本都支持这个事件；在不支持这个事件的浏览器上可以在`JS`代码开始设置一个超时事件为0的定时器来模拟这个事件，但是这样仍不能保证，这段代码会在`load`事件触发之前执行

**`readyStatechange事件`**

这个事件是`IE`浏览器为`DOM`文档的某些部分提供的事件，用于提供与文档或者元素加载状态有关的信息，但是这个事件的行为有时很难预料

**`pageshow事件和pagehide事件`**

这个事件是`firefox`和`opera`提供的在被缓存的页面显示或者隐藏时触发的事件

**`hashchange事件`**

这个事件是`HTML5`新增的事件，可以在`URL`参数列表（及`URL`中`#`后面的字符串）发生变化时通知开发人员

必须把这个事件绑定到`window`对象；这个事件的事件处理程序会包含两个属性：`oldURL` `newURL`

> 因为浏览器对这个事件支持的不好，所以在开发中通常使用`location`对象来获取`URL`

#### 内存和性能

#### 模拟事件

模拟事件即主动的触发某个浏览器事件，模拟事件的效果和常规事件一样。`IE9` `Opera` `Firefox` `Chrome` `Safari`浏览器都支持模拟事件，`IE`的模拟事件略有不同

##### DOM中的事件模拟

可以调用`document.createEvent()`方法来创建一个`event`对象，这个方法接受一个参数：要创建的事件类型的字符串；在`DOM2`中这些字符串都使用英文复数形式，`DOM3`中都变成了单数形式；事件类型字符串可以是下面几种：

```javascript
UIEvents    一般化的UI事件
MouseEvents     一般化的鼠标事件
MutationEvents      一般化的DOM变动事件
HTMLEvents      一般化的HTML事件
```

创建一个模拟事件对象之后，还需要调用事件特定的初始化方法来初始化这个对象

然后就可以使用节点的`dispatchEvent()`方法来在这个节点上触发这个事件（所有支持事件`DOM`节点都支持这个方法）；事件触发之后就和常规事件无异

**`模拟鼠标事件`**

将`"MouseEvents"`作为参数传递给`document.createEvent()`方法，这个方法返回的对象有一个`initMouseEvent()`方法，这个方法接受以下15个参数用来设置该鼠标事件的信息：

```javascript
type(字符串)  表示要触发的事件类型，如"click"
bubbles(布尔值)     表示事件是否应该冒泡。为了精确的模拟鼠标事件应该将这个参数设置为true
cancelable(布尔值)  表示事件是否可以取消。同样应该设置为true
view(AbstractView)  与事件关联的视图。这个参数应该设置为document.defaultView
detail(整数)    与事件有关的详细信息。通常为0
screenX(整数)   事件相对于屏幕的X坐标
screenY(整数)   事件相对于屏幕的Y坐标
clientX(整数)   事件相对于视口的X坐标
clientY(整数)   事件相对于视口的Y坐标
ctrlKey altKey shiftKey metaKey(布尔值)  表示是否按下相应的键。默认是false
button(整数)    表示按下哪一个鼠标键。默认是0
relatedTarget(对象)  表示与事件相关的对象 这个参数只在模拟mouseover或者mouseout时使用
```
> 这些参数与鼠标事件的`event`对象的属性一一对应；再触发事件时，浏览器要用到前四个参数（因此需要正确设置），后几个参数属于事件对象相关的

将这个初始化过的`event`对象传给节点的`dispatchEvent()`方法时，浏览器会自动设置这个`event`的`target`属性

**`模拟键盘事件`**

`DOM2`中没有模拟键盘事件的方法；`DOM3`中给`document.createEvent()`方法传入`"KeyboardEvent"`就可以创建一个键盘事件，返回对象的`initKeyEvent()`方法接受如下参数：

```javascript
前4个参数和模拟鼠标事件一样，但是第一个参数应该是键盘相关的事件：keydown keyup
key(整数)   表示按下键的键码
location(整数)  0表示主键盘，1表示左，2表示右，3表示数字键盘，4表示移动设备，5表示手柄
modifiers(字符串)   空格分隔的修改键列表，比如"Shift Ctrl"
repeat(整数)    在一行中按了这个键多少次
```
> `DOM3`中不提倡使用`keypress`事件

在`Firefox`中调用`document.createEvent()`方法传入一个`"KeyEvents"`字符串就可以创建一个键盘事件，返回对象的`initKeyEvent()`方法接受如下参数：

```javascript
前四个参数和鼠标模拟事件一样
ctrlKey altKey shiftKey metaKey(布尔值)  表示是否按下相应的键。默认是false
keyCode(整数)   被按下或者释放键的键码。这个参数对`keydown` `keyup`事件有用，默认是0
charCode(整数)  通过按键生成的字符的ASCII编码。这个参数对`keypress`事件有用，默认为0
```

在其他浏览器中需要使用`"Events"`作为参数创建一个通用的事件，然后再向事件对象中添加键盘特有的信息：

```javasscript
var textbox = document.getElementById("myTextbox");
var event = document.createEvent("Events");

event.initEvent(type, bubbles, cancelable);
event.view = document.defaultView;
event.altKey = false;
event.ctrlKey = false;
event.shiftKey = false;
event.metaKey = false;
event.keyCode = 65;
event.charCode = 65;

textbox.dispatchEvent(event);
```
> 这种方式的模式事件方法不会向文本框输入数据（无法真正的模拟键盘事件）

**`模拟其他事件和自定义事件`**

##### IE8之前的模拟事件

### 表单脚本

#### 表单的基础知识

在`JavaScript`中表单对应的是`HTMLFormElement`类型，这个类型继承了`HTMLElement`，因此和其他元素类型具有相同的属性和方法；除此之外还有如下属性和方法：

```javascript
acceptCharset   服务器能够处理的字符集；等价于form元素中的accept-charset属性
action  表单提交的URL；等价于form元素中的action属性
elements    表单中所有控件集合（HTMLCollection类型）
enctype     请求的编码类型；等价于form元素中的enctype属性
length      表单中控件的数量
method      要发送的请求类型名称字符串；等价于form元素中的method属性
name        表单的名称；等价于form元素中的name属性
reset()     将表单所有域重置为默认值的方法
submit()    提交表单的方法
target      用于发送请求和接收相应的窗口名称；等价于form元素的target属性
```

> 下文中的字段是指`HTML`元素在`HTMLFormElement`对象类型中的表示，所以元素和字段混用

要取得页面中的表单元素，除了可以使用`getElementById()`方法之外，还可以使用`document.forms`属性，这个属性包含页面中的所有表单的集合，在这个集合中可以通过索引或者表单的`name`属性来访问特定的表单：

```javascript
var firstForm = document.forms[0];
var myForm = document.forms["form1"];

var anotherForm = document.form2;
//不推荐使用document.form2的方式取得表单对象
```

**`提交表单`**

在HTML中定义一个提交按钮可以使用下面的三种方式：

```javascript
<input type="submit" value="Submit Form" />
<button type="submit">Submit Form</button>
<input type="image" src="test.gif" />
```

只要表单中存在上面一种提交按钮，那么当表单在获取到焦点时按下回车键则会提交这个表单，如果表单里面没有提交按钮则按下回车键不会提交；这种方式提交表单时，浏览器会在向服务器发送请求之前触发`submit`事件，我们可以在这个事件处理程序中做一些控制

在`JavaScript`中也可以调用表单对象的`submit()`方法提交表单，这种方式无需表单的提交按钮，并且不会触发表单的`submit`事件

提交表单时可能出现的问题是重复提交，解决这种问题可以使用两种方式：禁用提交按钮，或者利用`onsubmit`事件处理程序取消后续的提交请求

**`重置表单`**

在HTML中重置表单可以使用如下按钮：

```javascript
<input type="reset" value="Reset Form" />
<button type="reset">Reset From</button>
```
重置表单会将表单字段都恢复到页面刚刚加载完时的状态；当用户点击重置按钮时，会触发`reset`事件，在这个事件处理程序中可以做一些控制
    
重置表单也可以使用`form`元素的`reset()`方法，但是这个方法同样会触发表单的`reset`事件

##### 表单字段

表单对象有一个`elements`属性，这个属性是表单中元素（字段）的有序列表，元素顺序和他们出现在表单中的顺序相同，可以按照索引或者元素（字段）的`name`属性来访问这个元素：

```javascript
var form = document.getElementById("from1");

var field1 = form.elements[0];
var field2 = form.elements["textbox1"];
var fieldCount = form.elements.length;
    
<input type="radio" name="color" value="red" />
<input type="radio" name="color" value="green" />
<input type="radio" name="color" value="blue" />

var form = document.getElementById("myForm");

//如果表单元素需要公用一个name，则使用这个name值会返回一个以该name命名的NodeLis
var colorFields = form.elements["color"];
var firstColorField = colorFileds[0];

//如果使用索引则只返回第一个name匹配的元素
var firstFiled = form.elements[0];
    
alert(firstColorField == firstField);   //true
```
> 也可以通过表单对象的属性的方式来访问元素，比如：`form[0]` `from["color"]`；但是在实际开发中最好不要使用这种方式

**`共有属性`**    

除了`<fieldset>`元素（字段）之外，所有表单字段都有相同的一组属性；由于`<input>`元素可以表示多种表单字段，所有有些属性只适合特定字段类型，有些属性适用所有类型：

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
```
> 注意不要在提交按钮的`click`事件上进行处理，因为不同浏览器触发`click`事件和`submit`事件的顺序不同；如果先触发了`click`事件导致提交按钮被禁用，则永远不会再触发`submit`事件了
    
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

**`共有方法和事件`**
    
每个表单字段都有两个方法：`focus()` `blur()`；`focus()`方法用于将浏览器的焦点设置到表单字段，使其可以响应键盘事件

> 注意对隐藏元素调用这个方法会导致错误

`HTML5`增加一个`autofocus`属性，只要为某个表单元素设置这个属性，则当页面加载完成后那个元素会自动获得焦点
    
`blur()`方法是将焦点从当前元素上移走，在早期元素还没有`readOnly`属性时，这个方法有用

所有表单字段还有如下特有的事件：

```javascript
blur    当前字段失去焦点时触发
change  对于<input>和<textarea>元素，在他们失去焦点并且value值改变时触发；对于<select>元素，在其选项值改变时触发
focus   当前字段获得焦点时触发
```
    
当用户改变了焦点时，或者调用了`focus()` `blur()`方法时都会触发`focus` `blur`事件，这两个事件在所有表单字段中都是一样的；但是`change`事件则根据元素的不同而有不同的触发方式
    
> 注意：在不同的浏览器中`focus` `blur`事件的触发顺序不同，应用中不能依赖着两个事件被触发的特定顺序
    
#### 文本框脚本

文本框有两种形式：`<input type="text">`和`<textarea>`两者的属性有一定的区别，详细可以参考`HTML`文档；使用脚本给文本框赋值时推荐使用下面的方法：

```javascript
var textbox = document.forms[0].elements["textbox1"];
textbox.value = "some new value";
```
> 注意不要使用setattribute方法给value属性添加值，也不要使用给<textarea>添加子节点的方式

##### 选择文本的方法

两种文本框都支持`select()`方法，这个方法可以选择文本框中的所有文本，调用这个方法之后浏览器会将焦点设置为这个文本框（Opera除外）

下面的代码在文本框获取焦点时选定文本框中的所有文本：
    
```javascript
EventUtil.addHandler(textbox, "focus", function(event) {
    event = EventUtil.getEvent(event);
    var target = EventUtil.getTarget(event)
    target.select();
});
```

文本框中有一个`select`事件，在用户选择了文本框中的文本时就会触发`select`事件；`IE8`在用户选择了一个字母时（还未释放鼠标）就会触发这个事件，而`IE9`和其他浏览器则在用户释放鼠标按钮时才会触发；调用`select()`方法时也会触发这个事件

`HTML5`中添加了两个属性用来获取选择的文本内容：`selectionStart` `selectionEnd`，这两个属性保存的是基于0的文本的范围，因此可以使用如下代码获取用户选择的文本（IE8使用另外的两个属性）：

```javascript
function getSelectedText(textbox) {
    if(typeof textbox.selectionStart == "number") {
        return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
    } else if (document.selection) {
        return document.selection.createRange().text;
    }
}
```

`HTML5`添加了一个用来选择文本框中部分文本的方法：`setSelectionRange()`，这个方法接收两个参数：要选择的第一个字符的索引和要选择的最后一个字符的后一个字符的索引

> 要看的选择的文本，还必须在调用`setSelectionRange()`方法之前或者之后立即将焦点设置到文本框

> `IE8`之前的浏览器不能使用这个方法，详细信息参考`《JavaScript高级程序设计》`
    
##### 过滤输入

可以使用如下代码屏蔽用户输入非数字字符：

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
    
除了`Opera`浏览器之外，其他的浏览器都支持访问和操作剪切板的操作和事件；`HTML5`规定了如下六个剪贴板事件：

```javascript
beforecopy  在发生复制操作前触发
copy    在发生复制操作时触发
beforecut   在发生剪切操作前触发
cut     在发生剪切操作时触发
beforepaste     在发生粘贴操作之前触发
paste   在发生粘贴操作时触发
```
因为缺少标准各个浏览器在实现时有较大差异，详细信息参考`《JavaScript高级程序设计》`
    
##### 自动切换焦点

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

##### HTML5约束验证API

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

#### 选择框脚本

选择框元素可以通过`<select>` `<option>`创建；`JavaScript`中使用`HTMLSelectElement`类型对象表示；这对象除了表单字段的属性和方法外，还包括如下属性和方法：

```javascript
add(newOption, relOption)     向控件中插入新的<option>元素，位置在relOption之前
multiple    表示控件中所有<option>元素的一个HTMLCollection
remove(index)   移除给定位置的选项
selectedIndex   基于0的选中项的索引，如果没有选中项则值为-1；如果是多选框则只保存选中项的第一项
size    选择框中可见的行数；等价于HTML中的size属性
```

选择框的`type`属性是`select-one`或者`select-multiple`取决于选择框中是否有`multiple`属性；选择框的`value`属性由当前选中的项决定，有如下规则：

*   如果没有选中的项，则选择框的value属性保存空字符
*   如果有一个选中的项，且该项的value已经在HTML中指定，则选择框的value属性等于选中项的value值（即使value值是空字符串）
*   如果有一个选中项，但是该项的value未在HTML中指定，则选择框的value属性等于该项的文本
*   如果有多个选中项，则选择框的value属性值将根据前两条规则取得第一个选中的项

在`DOM`中每一个`<option>`元素都有一个`HTMLOptionElement`类型对象表示；这对象除了表单字段的属性和方法外，还包括如下属性和方法：

```javascript
index   当前选项在options集合中的索引
label   当前选项的标签；等价于HTML中的label属性
selected    布尔值表示当前选项是否被选中。将这个属性设置为true可以选中当前项
text    选项的文本
value   选项的值；等价于HTML中的value属性
```

在取得选择框的文本和值时可以使用如下两种方式：

```javascript
var selectbox = document.forms[0].elements["location"];

var text = selectbox.options[0].firstChild.nodeValue;
var value = selectbox.options[0].getAttribute("value");

var text = selectbox.options[0].text;
var value = selectbox.options[0].value;
```
在使用`DOM`访问`<option>`元素时推荐使用上面第二种方式进行访问；通常特定于元素的属性和方法总是比较高效，而且所有浏览器都支持这些属性

> 选择框的`change`事件在选项被改变时立即被触发；而其他表单元素的`change`事件一般在值被修改，并且焦点离开时才会触发

**`选择选项`**

对于只允许选择一项的选择框，访问选中项最简单的方式就是使用选择框的`selectedIndex`属性：

```javascript
var selectedOption = selectbox.options[selectbox.selectedIndex];
```
可以设置`selectedIndex`属性的值来选中选择框中的某一项；但是对于多选选择框来说，设置这个属性的值会导致其他选项被取消选中，所以可以通过给每一个选项对象的`selected`属性赋`true`的方式来设置选中的项

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

除了可以使用创建`<option>`元素然后添加子节点的方法添加选项外，还可以使用选择框对象的`add()`方法来添加；这个方法接受两个参数：要添加的新选项和将位于新选项之后的项（将这个参数置为`null`可以添加到最后一项）；IE中第二个参数是可选的，但是`DOM`规范则要求必须传递一个参数所以可以将第二个参数设置为`undefined`

```javascript
var newOption = new Option("Option text", "Option value");
selectbox.add(newOption, undefined);
//这里使用了一个遗留的option构造函数创建了一个选项对象
```
> 上面的代码在选项框的最后添加一个选项，如果要在其他地方插入选项，并且要兼容`IE`和其他浏览器就只能使用标准的`DOM`技术和`insertBefore()`方法

**`移除选项`**

移除一个选项可以使用如下方法：

```javascript
//使用dom方法
selectbox.removeChild(selectbox.options[0]);

//使用选择框对象的方法
selectbox.remove(0);

//遗留方法
selectbox.options[0] = null;
```
> 移除一个选项后，其他的选项会自动向上移动一个位置

**`移动和重排选项`**

使用`DOM`相关的方法进行选项的移动时非常方便的，使用`appendChild()` `insertBefore()`方法将一个已经在一个选择框中的选项添加到另外一个选项框时，会自动从前一个选择框移除；代码如下：

```javascript
var selectbox1 = document.getElementById("selLocation1");
var selectbox2 = document.getElementById("selLocation2");

selectbox2.appendChild(selectbox1.options[0]);
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

#### 表单序列化

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

#### 富文本编辑

### HTML脚本编程

#### 跨文档消息传递

跨文档消息传递（`cross-document messaging`），是指来自不同域的页面间传递消息；在`XDM`出现之前因为安全原因跨文档消息传递是不直接允许的。`XDM`现在已经作为一个单独的规范，并且更名为`Web Messaging`

`XDM`的核心是`postMessage()`方法，这个方法在`HTML5`中被用来向另外一个地方传递数据；对于`XDM`而言，是指当前页面包含的一个`iframe`元素，或者由当前页面弹出的窗口

`postMessage()`方法接收两个参数：一条消息和一个表示消息接收方来自哪个域的字符串；第二个参数对保障安全通信比较重要，可以防止把消息发送到不安全的地方

```javascript
var iframeWindow = document.getElementById("myframe").contentWindow;
iframeWindow.postMessage("A secret", "http://www.wrox.com");
```
上面的代码尝试向内嵌框架发送一条消息，并且指定框架中的文档必须来源于`http://www.wrox.com`域；如果匹配则消息发送，否则什么也不做；可以将`postMessage()`方法的第二个参数设置为`*`，则表示可以把消息发送给来自任何域的文档

接收到`XDM`消息时，会触发`window`对象的`message`事件；这个事件是以异步形式触发的（因此发送的消息可能不会立刻接收到）；触发这个事件后，传递给事件处理程序的事件对象包含以下三个方面的信息：

```javascript
data:   表示传递给postMessage()函数的第一个参数
origin：  发送消息的文档所在的域
source：    发送消息的文档的window对象代理
```

通常接收到消息事件后应该立即验证消息来源：

```javascript
EventUtil.addHandler(window, "message", function(event) {
    if(event.origin == "http://www.wrox.com") {
        processMessage(event.data);
        
        //可选的向来源窗口发送消息
        event.source.postMessage("Received!", "http://p2p.wrox.com");
    }
});
```
> 上面最后一行代码使用`event.source`来表示发送消息的`window`对象的 **代理** 这个代理只能用来调用`postMessage()`方法发送消息，并且这个方法永远存在（可以调用）

> 虽然规范中允许`postMessage()`方法第一个参数可以是任意类型数据，但是通常应该总是使用字符串

#### 原生拖放

`HTML5`以`IE`的实例为基础制定了拖放的规范；`Firefox3.5` `Safari3` `Chrome`页根据`HTML5`规范实现了原生拖放功能；拖放可以在框架间、窗口间甚至在应用间进行

##### 拖放事件

通过拖放事件，可以控制拖放相关的各个方面：确定哪里发生了拖放事件、是在被拖动元素上触发还是在放置目标上触发；拖动元素时将依次触发如下事件：`dragstart` `drag` `dragend`

当按下鼠标键并开始移动鼠标时，会在被拖放元素上触发`dragstart`事件；随后会触发`drag`事件，而且在元素被拖动期间会持续触发该事件（与`mousemove`事件相似）；当停止拖动时会触发`dragend`事件（不管是将被拖动对象放置在有效的放置目标还是无效放置目标）

> 上述三个事件的目标（`target`）都是被拖动对象

当某个元素被拖动到一个有效的放置目标时，会依次发生下列事件：`dragenter` `dragover` `dragleave/drop`；只要有元素被拖动到放置目标上时就会触发`dragenter`事件，然后是`dragover`事件，而且在被拖动的元素还在放置目标范围内移动时会持续触发该事件（类似于`mouseout`事件）；如果元素被拖出了放置目标，则`dragover`事件不再发生，但会触发`dragleave`事件；如果元素被放置到了放置目标中，则会触发`drop`事件

> 上述三个事件的目标都是作为放置目标的元素

##### 自定义放置目标

虽然所有元素都支持放置目标事件，但是这些元素默认是不允许放置的；如果拖动元素经过不允许放置的元素，则不会发生`drop`事件；不过可以把任何元素变成有效的放置目标，方法是重写`drogenter`和`dragover`事件的默认行为：

```javascript
var droptarget = document.getElementById("droptarget");

EventUtil.addHandler(droptarget, "dragover", function() {
    EventUtil.preventDefault(event);
});

EventUtil.addHandler(droptarget, "dragenter", function() {
    EventUtil.preventDefault(event);
});
```

在`firefox3.5+`中`drop`事件的默认行为是打开被拖放目标的`URL`；因此为了让`firefox`支持正常的拖放行为需要取消`drop`事件的默认行为：

```javascript
EventUtil.addHandler(droptarget, "drop", function() {
    EventUtil.preventDefautl(event);
});
```

##### dataTransfer对象

这个对象用来从被拖动元素向放置目标传递字符串格式的数据；因为它是事件对象的属性，所以只能在拖放事件的事件处理程序中访问到这个对象，通过这个对象的属性和方法来完善拖放功能

**`getData和setData方法`**

这个对象的两个主要方法是：`getData()` `setData()`；前者用来获取后者设置的值，这两个函数的第一个参数是一个字符串，值可以是`"text"` `"URL"`表示保存的数据类型，`getData()`还有第二参数用来指定实际的数据值；`HTML5`标准允许设置各种`MIME`类型，同时也兼容`text`和`URL`类型（分别被映射为`text/plain` `text/uri-list`）

> `dataTransfer`对象可以为每种`MIME`类型都保存一个值，因此可以同时在这个对象中保存一段文本和一个`URL`

> 保存在`dataTransfer`对象中的数据只能在`drop`的时间处理程序中读取；如果未读取到数据那可能是这个对象已经被销毁

在拖动文本框中的文本或者图像链接时，浏览器会调用`setData()`方法将拖动的文本或者图像的`URL`以相应的类型保存在`dataTransfer`对象中，当这些元素被拖放到放置目标时就可以通过`getDate()`方法获取数据；同时我们也可以在`dragstart`事件处理程序中调用`setData()`方法手动保存要传输的数据

> 将数据保存为文本和`URL`是有区别的：文本类型的数据不会得到任何处理，而保存为`URL`类型的被拖动的元素在被放置到另外一个浏览器窗口时会打开这个`URL`

因为`Firefox`浏览器不能正确的映射`url`和`text`，所以跨浏览器的使用这个对象，需要进行如下检查：

```javascript
var dataTransfer = event.dataTransfer;

//读取URL
var url = dataTransfer.getData("url") || dataTransfer.getData("text/uri-list");

//读取文本时使用首字母大写的Text
var text = dataTransfer.getData("Text");
```
> `IE10`之前的浏览器不支持`MIME`类型名称

**`dropEffect和effectAllowed属性`**

这两个属性用来确定被拖动的元素及作为放置目标的元素能够接收什么操作

通过`dropEffect`属性可以知道被拖动的元素能够执行那种放置行为，这个属性的值可以是：

```javascript
"none"  不能把拖动的元素放在这里。这是除文本框元素之外其他元素的默认值
"move"  应该把拖动的元素移动到放置目标
"copy"  应该把拖动元素复制到放置目标
"link"  表示放置目标会打开拖动的元素（拖动元素必须是一个链接）
```
将元素拖动到放置目标时，上面的每一个值都会导致光标显示为不同的符号；但是如何实现光标指示的动作则是可自定义的；如果未做设置则不会有任何行为；要使用这个属性，则必须在`dragenter`事件处理程序中针对放置目标来设置

`dropEffect`只有在搭配`effectAllowed`属性时才有用；这个属性表示允许拖动元素的哪种`dropEffect`；这个属性值可以是：

```javascript
"uninitialized"     没有给被拖动元素设置任何放置行为
"none"              被拖动的元素不能有任何行为
"copy"              只允许值为"copy"的 dropEffect
"link"              只允许值为"link"的 dropEffect
"move"              只允许值为"move"的 dropEffect
"copyLink"          允许值为"copy" "link"的 dropEffect
"copyMove"          允许值为"copy" "move"的 dropEffect
"linkMove"          允许值为"link" "move"的 dropEffect
"all"               允许所有的 dropEffect
```
> 必须在`dragstart`事件处理程序中设置`effectAllowed`属性

**`其他属性和方法`**

`HTML5`规定的其他属性和方法包括：`addElement(element)` `clearData(format)` `setDragImage(element, x, y)` `types`；因为这些属性和方法只在部分浏览器上有实现所以这里不详述

##### 可拖动

默认情况下，不需要编写任何代码，图像和链接时可拖动的，文本在被选中的情况下也是可拖动的；我们也可以使用`HTML5`为其他元素添加的`draggable`属性来设置元素是否可以被拖动：

```javascript
<img src="smile.gif" draggable="false" alt="Smiley face">
<div draggable="true">...</div>
```
> `IE10+` `Firefox4+` `Safari5+` `Chrome`支持`draggable`属性；另外为了让`Firefox`支持可拖动属性，还必须添加一个`ondragstart`事件处理程序，并在`dataTransfer`对象中保存一些信息

#### 媒体元素

#### 历史状态管理

`HTML5`通过更新`history`对象为管理历史状态提供了方便。通过`hashchange`事件可以知道`URL`在什么时候发生了变化（即页面中该有所反应）；通过状态管理`API`能够在不加载新页面的情况下更改浏览器的`URL`

`history.pushState()`方法可以将新的状态信息加入历史状态栈，这个方法接收三个参数：状态对象、新的状态标题和可选的相对`URL`；调用这个方法之后，浏览器地址栏也会变成新的`URL`(`location.href`也会被更改)，但是并没有发送新的请求

> 第二个参数目前所有浏览器都未实现，可以传空字符串

> 第一个参数应该尽可能提供初始化页面状态所需的各种信息

调用这个方法创建一个新的历史状态之后，浏览器上的后退按钮可以使用；当按下后退按钮时会触发`window`对象的`popstate`事件，这个 **事件对象** 的`state`属性包含着当初以第一个参数传递给`pushState()`方法的状态对象

拿到这个状态对象之后我们必须手动将当前页面重置为状态对象中数据表示的状态（即状态对象中通常保存的是当前页面所需的参数）

要更新当前状态可以调用`replaceState()`方法，所需的两个参数和`pushState()`前两个相同；调用这个方法不会在历史状态中创建新的状态，只会重写当前状态

> 在使用`HTML5`状态管理机制时，需要确保使用`pushState()`创造的每一个`URL`，在web服务器上都有一个实际的`URL`与之相对应，否则用户单击刷新按钮时会显示404错误


### JSON

`JSON`(JavaScript Object Notation)是一种数据格式；而且很多语言都实现了`JSON`格式解析

#### 语法

`JSON`可以表示如下三种类型的值：

*   `简单值`：使用与`JavaScript`相同的语法，可以在`JSON`中表示`字符串` `数值` `布尔值` `null`（`JSON`不支持`undefined`）
*   `对象`：对象是一种复杂数类型，在`JSON`中也是一组无序的键值对；每个键值对中的值可以是任意类型（简单值、对象、数组）
*   `数组`：数组也是一种复杂数据类型，在`JSON`中也是一组有序的值的列表，可以通过数值索引访问其中的值；数组的值也可以是任意类型

`JSON`中的字符串必须使用`""`（双引号）引用

`JSON`中的对象和`ECMAScript`中对象字面值相同，但是属性名必须加`""`（双引号）引用，属性值应该符合数组和简单值的规则

`JSON`中的数组和`ECMAScript`中数组字面量相同，但是元素值应该符合`JSON`中简单值和对象的规则

#### 解析与序列化

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

### Ajax与Comet

`Ajax`（Asynchronous JavaScript + XML）技术的核心是`XMLHttpRequest`对象（简称`XHR`），可以在无需卸载页面的情况下向服务器请求更多的数据；`Ajax`虽然名字中包含`XML`，但是与实际通信使用的数据格式无关

在`XHR`对象出现之前，开发人员需要使用`java applet`或者`Flash`相关技术向服务器发送请求；而`XHR`对象则将浏览器原生的`HTTP`通信能力提供给了开发人员

#### XMLHttpRequest对象

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

#### XMLHttpRequest 2 级

目前几乎所有浏览器都实现了`XMLHttpRequest2`的部分内容

**`FormData`**

`XMLHttpRequest2`为表单序列化定义了一个`FormData`类型对象，这个对象提供了序列化表单和创建表单格式数据（用于`XHR`请求）提供了便利；下面的代码创建了一个对象并且添加一些数据：

```javascript
var data = new FormData();
data.append("name", "Nicholas");

//也可以在创建对象时向构造函数传入表单对象
var data = new FormData(document.forms[0]);
```
创建了一个`FormData`对象类型实例之后就可以将它传给`send()`方法；使用这个对象的方便之处是不必明确的在`XHR`对象上设置请求头部，`XHR`能够识别传入的数据类型是`FormData`并且自动添加合适的头部信息

**`超时设定`**

`XMLHttpRequest2`为`XHR`对象添加了一个`timeout`属性，可以为请求设置一个超时时间，如果在超时时间内未收到请求就会触发`timeout`事件，我们可以设置`ontimeout`事件处理程序来处理这种情况（目前只有IE支持这个属性）

**`overrideMimeType()方法`**

这个方法可以用来覆盖响应数据中的`MIME`类型设置；比如`reponseXML`属性会根据服务器响应头信息的`MIME`类型来进行设置，如果头信息指定内容类型为不是`xml`则不会设置这个属性，这时我们可以使用这个方法覆盖内容类型强制设置`responseXML`属性

目前除`IE`浏览器外，其他浏览器都支持这个方法

#### 进度事件

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
#### 跨源资源共享

使用`XHR`来进行`Ajax`请求的一点限制是：只能访问和当前页在同一个域中的资源；`CORS`（Cross-Origin Resource Sharing）用来进行跨源资源的请求

`CORS`使用自定义的`HTTP`头部让浏览器与服务器进行通信；比如发送一个请求时，没有自定义的头部，并且主体内容是`text/plain`，在发送该请求时附加一个额外的 **`Origin`** 头部，包含了请求页面的源信息（协议、域名、端口），例如：`Origin: http://www.nc.net`；如果服务器认为这个请求可以接收，就在`Access-Control-Allow-Origin`响应头中加上和请求相同的信息

> 这个请求和响应都不包含cookie信息

**`IE对CORS的实现`**

`IE8`中使用`XDomainRequest`对象类型，这个类型和`XMLHttpRequest`类型相似，但是可以实现安全的跨域通信。下面是两者的不同之处：

```javascript
cookie不会随请求发送，也不会被添加到响应中
只能设置请求头部信息中的Content-Type字段
不能访问响应头部信息
只支持GET和POST请求
```
使用这个对象创建的请求头中包含`Origin`信息，服务器可以根据这个头字段值来决定是否相应请求

`XDR`对象的使用方法和`XHR`对象类似：创建一个对象实例，调用对象实例的`open()`方法，再调用对象的`send()`方法；但是这个对象的`send()`方法只接受两个参数：请求的类型和`URL`；请求返回后也会触发`load`事件，响应的数据也保存在`responseText`属性中：

```javascript
var xdr = new XDomainRequest();
xdr.onload = function() {
    alert(xdr.responseText);
};
xdr.open("get", "http://www.somewhere.com/page/");
xdr.send(null);
```
使用这个对象只能发送异步的请求；在接收到响应之后，只能访问响应的原始文本，没有办法确定相应的状态码；而且只要响应有效就会触发`load`事件，如果失败（包括缺少`Access-Control-Allow-Origin`头部）就会触发`error`事件，但是`error`事件对象不包含任何信息

> 通常需要对`error`事件建立事件处理程序，来检测请求失败

在请求返回之前可以调用`abort()`方法来中止请求；与`XHR`在`IE`中的实现类似，也可以为`XDR`对象的`timeout`属性赋一个值来指定请求超时时间，并且超时时会触发`ontimeout`事件：

```javascript
xdr.timeout = 1000;
xdr.ontimeout = function() {
    alert("Request took too long");
};
```

对于`post`请求类型，`XDR`对象也支持`contentType`属性，可以用来指定发送请求数据的类型，这个属性是通过`XDR`对象影响头部信息的唯一方式:

```javascript
var xdr = new XDomainRequest();
xdr.open("post", "http://www.somewhere.com/page/");
xdr.contentType = "application/x-www-form-urlencoded";
xdr.send("name1=value1&name2=value2");
```

**`其他浏览器的实现`**

其他大多数浏览器都直接通过`XMLHttpRequest`对象实现了对`CORS`的原生支持；只需要在调用`open()`方法时将`URL`设置为绝对路径即可

相应返回后通过`XHR`对象可以访问`status`和`statusTest`属性，并且还支持同步异步请求；但是也有一些限制：

```javascript
不能使用setRequestHeader()设置头部

不能接收和发送cookie

调用getAllResponseHeaders()方法返回空字符串
```
> 所以在使用`XHR`对象时，访问本域内资源最好使用相对路径，访问其他域资源使用绝对路径

**`Preflighted Requests`**

使用这种技术的`CORS`支持开发人员自定义头部、使用除`GET` `POST`之外的请求方法、以及不同类型的主体内容。实现方式是首先发送一个请求方法为`OPTIONS`的`Preflight`请求，请求包括下面头部信息：

```javascript
Origin      与简单请求相同
Access-Control-Request-Method       请求自身使用的方法
Access-Control-Request-Header       (可选)自定义的头部信息，多个头部以逗号分隔
```
服务器可以决定是否允许这种类型的请求。服务器通过在响应中发送如下头部信息与浏览器进行沟通：

```javascript
Access-Control-Allow-Origin     与简单请求相同
Access-Control-Allow-Methods    允许的请求方法，多个方法以逗号分隔
Access-Control-Allow-Headers    允许的头部，多个头部以逗号分隔
Access-Control-Max-Age          应该将这个Preflight请求缓存多长时间（秒）
```
> 请求结束后，结果将按照指定的缓存时间进行缓存；最新版本的其他浏览器都支持这种请求，`IE10`之前的版本不支持

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
send()      用于发送请求
onerror     用于替代onreadystatechange检测错误
onload      用于替代onreadystatechange检测成功
responseText属性    用于取得相应内容
```

#### 其他的跨域技术

**`图像ping`**

**`JSONP`**

`JSONP`是`JSON width padding`（填充式`JSON`）的简写，是应用`JSON`的一种新方法；`JSONP`是被包含在函数调用中的`JSON`:

```javascript
callback({"name":"Nicholas"});
```

`JSONP`由两部分组成：回调函数和数据。回调函数时当相应到来时应该在页面中调用的函数，回调函数的名字一般在请求中指定；而数据是传入回调函数中的`JSON`数据。下面是`JSONP`通常请求的格式：

```javascript
http://freegeoip.net/json/?callback=handleResponse
```

`JSONP`是通过动态的`<script>`元素来使用的，使用时可以为`src`属性指定一个跨域的`URL`。这里的`<script>`元素和`<img>`元素类似，都可以从其他域加载资源。被加载的资源在请求完成`JSONP`相应加载到页面中以后就会立即执行：

```javascript
function handlerResponse(response) {
    alert("Your at IP address " + response.ip + ", which is in " + response.city + ", " + response.region_name);
}

var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```

`JSONP`技术可以直接访问相应文本，支持在浏览器和服务器之间双向通信；不过也有两点不足：如果请求的域不是你能控制的则安全性是一个问题，另外很难确定`JSONP`请求是否失败；`HTML5`中为`<script>`元素新增了一个`onerror`事件处理程序，但是目前还未得到任何浏览器支持

**`Comet`**

**`服务器发送事件`**

**`Web Sockets`**

`Web Sockets`的目标是在一个单独的持久连接上提供全双工、双向通信。在`JS`中创建了`Web Sockets`之后，会有一个`HTTP`发送到浏览器以发起连接；取得服务器响应之后，建立的连接从`HTTP`协议转换为`Web Socket`协议。所以需要服务器端也支持`Web Sockets`协议才可以

> `Web Sockets`协议的`URL`使用`ws://`和`wss://`（加密连接）

要创建一个`Web Socket`首先实例一个`WebSocket`对象并传入要连接的`URL`:

```javascript
var socket = new WebSocket("ws://www.example.com/server.php");
```
> 传入的`URL`必须使用绝对路径，因此可以打开到任何域的连接，但是是否可以通信要看服务器的处理

实例化一个`WebSocket`对象之后，浏览器马上就会尝试创建连接；`WebSocket`也有一个表示当前状态的`readyState`属性，属性的值如下：

```javascript
WebSocket.OPENING   (0)     正在建立连接
WebSocket.OPEN      (1)     已经建立连接
WebSocket.CLOSING   (2)     正在关闭连接
WebSocket.CLOSE     (3)     已经关闭连接
```
> `WebSocket`没有`readystatechange`事件，不过有其他事件来表示不同的状态；`readyState`属性的值从0开始

要关闭`Web Sockets`连接可以在任何时候调用`close()`方法；调用这个方法之后`readyState`属性值变为2（正在关闭），关闭连接之后就变成3

要向服务器发送数据使用`send()`方法并传入任意字符串：

```javascript
var socket = new WebSocket("ws://www.example.com/server.php");
socket.send("Hello world");
```
`Web Sockets`只能发送纯文本数据，所以对于复杂类型的数据结构，需要在发送之前进行序列化：

```javascript
var message = {
    time: new Date(),
    text: "Hello world!",
    clientId: "ssdfadffads"
};
socket.send(JSON.stringify(message));
```
服务器要读取其中的数据，就要解析接收到的`JSON`字符串。

当服务器向客户端发来消息时，`WebSocket`对象就会触发`message`事件；返回的数据字符串会保存在这个事件对象的`data`属性中：

```javascript
socket.onmessage = {
    var data = event.data;
    //其他处理
};
```

`WebSocket`对象还有另外三个事件，在连接生命周期的不同阶段触发：

```javascript
open        在成功建立连接时触发
error       在发生错误时触发，连接不能持续
close       在连接关闭时触发
这个事件的事件对象有额外的信息属性：`wasClean`是一个布尔值，表示连接是否已经明确地关闭；`code`是服务器返回的数值状态码；`reason`是一个字符串，包含服务器发送的信息
```
> `WebSocket`对象不支持`DOM2`级事件处理程序，所以必须使用`DOM0`级语法分别定义每个事件的处理程序

#### 安全

为了确保通过`XHR`访问`URL`的安全，同行的作为就是验证发送请求者是否有权访问相应的资源：

*   要求以`SSL`连接来访问可以通过`XHR`请求的资源
*   要求每一次请求都要附带经过相应算法计算得到的验证码
