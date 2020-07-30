# 第17章 事件

JavaScript与HTML之间的交互是通过**事件**实现的。事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间。可以使用**侦听器**（或处理程序）来预订事件，以便事件发生时执行相应的代码。这种在传统软件工程中被称为观察员模式的模型，支持页面的行为（JavaScript代码）与页面的外观（HTML和CSS代码）之间的松散耦合。

## 17.1 事件流

**事件流**描述的是从页面中接收事件的顺序。但有意思的是，IE和Netscape开发团队居然提出了差不多是完全相反的事件流的概念。IE的事件流是事件冒泡流，而Netscape Communicator的事件流是事件捕获流。

### 17.1.1 事件冒泡

IE的事件流叫做**事件冒泡**（event bubbling），即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。

以下面的HTML页面为例：

```html
<!DOCTYPE html>
<html>
<head>
    <title>Event Bubbling Example</title>
</head>
<body>
    <div id="myDiv">Click Me</div>
</body>
</html>
```

如果你单击了页面中的`<div>`元素，那么这个`click`事件会按照如下顺序传播：

![](_static/images/Chapter17-Events.assets/16.d13z.01.png)

所有现代浏览器都支持事件冒泡，但在具体实现上还是有一些差别。IE5.5及更早版本中的事件冒泡会跳过`<html>`元素（从`<body>`直接跳到`document`）。IE9、Firefox、Chrome和Safari则将事件一直冒泡到`window`对象。

### 17.1.2 事件捕获

Netscape Communicator团队提出的另一种事件流叫做**事件捕获**（event capturing）。事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在事件到达预定目标之前捕获它。

如果仍以前面的HTML页面作为演示事件捕获的例子，那么单击`<div>`元素就会以下列顺序触发`click`事件：

![](_static/images/Chapter17-Events.assets/16.d13z.02.png)

尽管“DOM2级事件”规范要求事件应该从`document`对象开始传播，但**大多数浏览器都是从`window`对象开始捕获事件的。**

由于老版本的浏览器不支持，因此很少有人使用事件捕获。**建议读者放心地使用事件冒泡，在有特殊需要时再使用事件捕获。**

### 17.1.3 DOM事件流

“DOM2级事件”规定的事件流包括3个阶段：

- **事件捕获阶段** ：首先发生的是事件捕获，为截获事件提供了机会。
- **处于目标阶段** ：然后是实际的目标接收到事件。
- **事件冒泡阶段** ：最后一个阶段是冒泡阶段，可以在这个阶段对事件做出响应。

以前面简单的HTML页面为例，单击`<div>`元素会按照下图所示顺序触发事件：

![](_static/images/Chapter17-Events.assets/16.d13z.03.png)

在DOM事件流中，实际的目标（`<div>`元素）在捕获阶段不会接收到事件。这意味着在捕获阶段，事件从`document`到`<html>`再到`<body>`后就停止了。下一个阶段是“处于目标”阶段，于是事件在`<div>`上发生，并在事件处理中被看成冒泡阶段的一部分。然后，冒泡阶段发生，事件又传播回文档。

多数支持DOM事件流的浏览器都实现了一种特定的行为。即使“DOM2级事件”规范明确要求捕获阶段不会涉及事件目标，但所有现代浏览器都会在捕获阶段触发事件对象上的事件。结果，就是有两个机会在目标对象上面操作事件。



## 17.2 事件处理程序 (Event Handlers)

**事件**就是用户或浏览器自身执行的某种动作。诸如`click`、`load`和`mouseover`，都是事件的名字。**事件处理程序** (Event Handlers) 或**事件侦听器** (Event Listener）是响应某个事件的函数。事件处理程序的名字以`"on"`开头，因此`click`事件的事件处理程序就是`onclick`，`load`事件的事件处理程序就是`onload`。

### 17.2.1 HTML事件处理程序

某个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的HTML特性来指定。这个特性的值应该是能够执行的JavaScript代码。

例如，要在按钮被单击时执行一些JavaScript，可以像下面这样编写代码：

```html
<input type="button" value="Click Me" onclick="console.log('Clicked')"/>
```

当单击这个按钮时，就会显示一个控制台日志。这个操作是通过指定`onclick`特性并将一些JavaScript代码作为它的值来定义的。 **由于这个值是JavaScript代码，因此不能在其中使用未经转义的HTML语法字符，例如和号（&）、双引号（""）、小于号（<）或大于号（>）。**

在HTML中定义的事件处理程序可以包含要执行具体动作的JavaScript代码，也可以调用在页面其他地方定义的脚本，还可以调用外部文件中的代码：

```html
<script>
function showMessage() {
    console.log("Hello world!");
}
</script>
<input type="button" value="Click Me" onclick="showMessage()"/>
```

事件处理程序中的代码在执行时，有权访问全局作用域中的任何代码。

这样指定事件处理程序会创建一个封装着元素属性值的函数。这个函数中有一个局部变量**`event`事件对象**。可以直接访问`event`事件对象，你不用自己定义它，也不用从函数的参数列表中读取。

```html
<!-- 输出 "click" -->
<input type="button" value="Click Me" onclick="alert(event.type)">
```

在这个函数内部，`this`值等于事件的目标元素，例如：

```html
<!-- 输出 "Click Me" -->
<input type="button" value="Click Me" onclick="alert(this.value)">
```

**在HTML中指定事件处理程序的缺点：**

- 首先，存在一个时差问题。因为用户可能会在HTML元素一出现在页面上就触发相应的事件，但当时的事件处理程序有可能尚不具备执行条件。假设`showMessage()`函数是在按钮下方、页面的最底部定义的。如果用户在页面解析`showMessage()`函数之前就单击了按钮，就会引发错误。为此，很多HTML事件处理程序都会被封装在一个`try-catch`块中，以便错误不会浮出水面。

- 这样扩展事件处理程序的作用域链在不同浏览器中会导致不同结果。不同JavaScript引擎遵循的标识符解析规则略有差异，很可能会在访问非限定对象成员时出错。

- HTML与JavaScript代码紧密耦合。如果要更换事件处理程序，就要改动两个地方：HTML代码和JavaScript代码。

### 17.2.2 DOM0级事件处理程序

通过JavaScript指定事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。要使用JavaScript指定事件处理程序，首先必须取得一个要操作的对象的引用。

每个元素（包括`window`和`document`）都有自己的事件处理程序属性，这些属性通常全部小写，例如`onclick`。将这种属性的值设置为一个函数，就可以指定事件处理程序，如下所示：

```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
	console.log("Clicked");
};
```

注意，在这些代码运行以前不会指定事件处理程序，因此如果这些代码在页面中位于按钮后面，就有可能在一段时间内怎么单击都没有反应。

使用DOM0级方法指定的事件处理程序被认为是元素的方法。因此，这时候的事件处理程序是在元素的作用域中运行；换句话说，程序中的`this`引用当前元素。

```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
    console.log(this.id); // "myBtn"
};
```

可以在事件处理程序中通过`this`访问元素的任何属性和方法。**以这种方式添加的事件处理程序会在事件流的冒泡阶段被处理。**

也可以删除通过DOM0级方法指定的事件处理程序，只要像下面这样将事件处理程序属性的值设置为`null`即可：

```js
btn.onclick = null;     //删除事件处理程序
```

**注意，DOM0级对每个事件只支持一个事件处理程序。**

### 17.2.3 DOM2级事件处理程序

“DOM2级事件”定义了两个方法，用于处理指定和删除事件处理程序的操作：

- **`addEventListener()`方法** ：添加事件处理程序。

- **`removeEventListener()`方法** ：移除通过`addEventListener()`添加的事件处理程序。移除时传入的参数与添加处理程序时使用的参数相同。这也意味着通过`addEventListener()` **添加的匿名函数将无法移除**。

所有DOM节点中都包含这两个方法，并且它们都接受3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是`true`，表示**在捕获阶段调用**事件处理程序；如果是`false`，表示**在冒泡阶段调用**事件处理程序。

要在按钮上为`click`事件添加事件处理程序，可以使用下列代码：

```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {   // 为一个按钮添加了onclick事件处理程序
    console.log(this.id);
}, false);
```

与DOM0级方法一样，这里添加的事件处理程序也是在其依附的元素的作用域中运行。

**使用DOM2级方法添加事件处理程序可以为每个事件添加多个事件处理程序。**

```js
/* 
这里为按钮添加了两个事件处理程序。
这两个事件处理程序会按照添加它们的顺序触发，因此首先会显示元素的ID，其次会显示`"Hello world!"`消息。
*/

let btn = document.getElementById("myBtn");

btn.addEventListener("click", () => {
console.log(this.id);
}, false);

btn.addEventListener("click", () => {
console.log("Hello world!");
}, false);
```

通过`addEventListener()` 添加的匿名函数将无法移除：

```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
    console.log(this.id);
}, false);

// other code here
btn.removeEventListener("click", function() { // won't work!
    console.log(this.id);
}, false);
```

```js
let btn = document.getElementById("myBtn");
let handler = function() {
    console.log(this.id);
};

btn.addEventListener("click", handler, false);
// other code here
btn.removeEventListener("click", handler, false); // works!
```

**大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段，这样可以最大限度地兼容各种浏览器。最好只在需要在事件到达目标之前截获它的时候将事件处理程序添加到捕获阶段。如果不是特别需要，我们不建议在事件捕获阶段注册事件处理程序。**

### 17.2.4 IE事件处理程序

略

### 17.2.5 跨浏览器的事件处理程序

以下代码实现了一个事件处理对象`EventUtil`，它有两个方法，分别用来实现添加和移除跨浏览器的事件处理程序：

```js
var EventUtil = {
    // addHandler()方法接受3个参数：要操作的元素、事件名称和事件处理程序函数。
    addHandler: function(element, type, handler) {  
        if (element.addEventListener) { // 使用DOM0方法
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent) { // 使用IE方法
            element.attachEvent("on" + type, handler);
        } else { // 默认使用DOM0分发
            element["on" + type] = handler;
        }
    },
    // removeHandler()方法接受3个参数：要操作的元素、事件名称和事件处理程序函数。
    removeHandler: function(element, type, handler) {
        if (element.removeEventListener) { // 使用DOM0方法
            element.removeEventListener(type, handler, false);
        } else if (element.detachEvent) { // 使用IE方法
            element.detachEvent("on" + type, handler);
        } else { // 默认使用DOM0分发
            element["on" + type] = null;
        }
    }
};
```

可以像下面这样使用`EventUtil`对象：

```js
let btn = document.getElementById("myBtn");
let handler = function() {
    console.log("Clicked");
};

EventUtil.addHandler(btn, "click", handler);
// other code here
EventUtil.removeHandler(btn, "click", handler);
```



## 17.3 事件对象

在触发DOM上的某个事件时，会产生一个事件对象`event`，这个对象中包含着所有与事件有关的信息。包括导致事件的元素、事件的类型以及其他与特定事件相关的信息。例如，鼠标操作导致的事件对象中，会包含鼠标位置的信息，而键盘操作导致的事件对象中，会包含与按下的键有关的信息。所有浏览器都支持`event`对象，但支持方式不同。

### 17.3.1 DOM中的事件对象

兼容DOM的浏览器会将一个`event`对象传入到事件处理程序中。无论指定事件处理程序时使用什么方法（DOM0级或DOM2级），都会传入`event`对象。

```js
// 这个例子中的两个事件处理程序都会弹出一个警告框，显示由event.type属性表示的事件类型。

let btn = document.getElementById("myBtn");

btn.onclick = function(event) {
    console.log(event.type); // "click"
};

btn.addEventListener("click", (event) => {
    console.log(event.type); // "click"
}, false);
```

在通过HTML特性指定事件处理程序时，变量`event`中保存着`event`对象。以这种方式提供`event`对象，可以让HTML特性事件处理程序与JavaScript函数执行相同的操作。请看下面的例子：

```html
<input type="button" value="Click Me" onclick="alert(event.type)"/>
```

`event`对象包含与创建它的特定事件有关的属性和方法。触发的事件类型不一样，可用的属性和方法也不一样。下表列出了所有事件都会有的成员：

| 属性/方法                    | 类　　型       | 读/写 | 说　　明                                                     |
| :--------------------------- | :------------- | :---- | :----------------------------------------------------------- |
| `bubbles`                    | `Boolean`      | 只读  | 表明事件是否冒泡                                             |
| `cancelable`                 | `Boolean`      | 只读  | 表明是否可以取消事件的默认行为                               |
| `currentTarget`              | `Element`      | 只读  | 其事件处理程序当前正在处理事件的那个元素。                   |
| `defaultPrevented`           | `Boolean`      | 只读  | 为`true`表示已经调用了`preventDefault()`（DOM3级事件中新增） |
| `detail`                     | `Integer`      | 只读  | 与事件相关的细节信息                                         |
| `eventPhase`                 | `Integer`      | 只读  | 调用事件处理程序的阶段：1表示捕获阶段，2表示“处于目标”，3表示冒泡阶段 |
| `preventDefault()`           | `Function`     | 只读  | 取消事件的默认行为。如果`cancelable`是`true`，则可以使用这个方法 |
| `stopImmediatePropagation()` | `Function`     | 只读  | 取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用（DOM3级事件中新增） |
| `stopPropagation()`          | `Function`     | 只读  | 取消事件的进一步捕获或冒泡。如果`bubbles`为`true`，则可以使用这个方法 |
| `target`                     | `Element`      | 只读  | 事件的实际目标                                               |
| `trusted`                    | `Boolean`      | 只读  | 为`true`表示事件是浏览器生成的。为`false`表示事件是由开发人员通过JavaScript创建的（DOM3级事件中新增） |
| `type`                       | `String`       | 只读  | 被触发的事件的类型                                           |
| `view`                       | `AbstractView` | 只读  | 与事件关联的抽象视图。等同于发生事件的`window`对象           |

在事件处理程序内部，对象`this`始终等于`currentTarget`的值，而`target`则只包含事件的实际目标。如果直接将事件处理程序指定给了目标元素，则`this`、`currentTarget`和`target`包含相同的值。