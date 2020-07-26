# 第15章　DOM扩展

## 15.1 选择器API

### 15.1.1 `querySelector()`方法

`querySelector()`方法接收一个CSS选择符，返回与该模式匹配的第一个元素，如果没有找到匹配的元素，返回`null`。如果传入了不被支持的选择符，`querySelector()`会抛出错误。

通过`Document`类型调用`querySelector()`方法时，会在文档元素的范围内查找匹配的元素。而通过`Element`类型调用`querySelector()`方法时，只会在该元素后代元素的范围内查找匹配的元素。

```js
// Get the body element
let body = document.querySelector("body");

// Get the element with the ID "myDiv"
let myDiv = document.querySelector("#myDiv");

// Get first element with a class of "selected"
let selected = document.querySelector(".selected");

// Get first image with class of "button"
let img = document.body.querySelector("img.button");
```

### 15.1.2 `querySelectorAll()`方法

`querySelectorAll()`方法接收一个CSS选择符，返回的一个`NodeList`的实例，其中包含所有匹配的元素。

通过`Document`类型调用`querySelectorAll()`方法时，会在文档元素的范围内查找匹配的元素。而通过`Element`类型调用`querySelector()`方法时，只会在该元素后代元素的范围内查找匹配的元素。

```js
// Get all <em> elements in a <div> (similar to getElementsByTagName("em"))
let ems = document.getElementById("myDiv").querySelectorAll("em");

// Get all elements wthat have "selected" as a class
let selecteds = document.querySelectorAll(".selected");

// Get all <strong> elements inside of <p> elements
let strongs = document.querySelectorAll("p strong");
```

要取得返回的`NodeList`中的每一个元素，可以使用`item()`方法，也可以使用方括号语法：

```js
let strongElements = document.querySelectorAll("p strong");

// All three of the following loops will have the same effect:
for (let strong of strongElements) {
    strong.className = "important";
}

for (let i = 0; i < strongElements.length; ++i) {
    strongElements.item(i).className = "important";
}

for (let i = 0; i < strongElements.length; ++i) {
    strongElements [i].className = "important";
}
```

### 15.1.3 `matches()`方法

`Element`类型新增了一个方法`matches()`，这个方法接收一个参数，即CSS选择器，如果调用元素与该选择符匹配，返回`true`；否则，返回`false`。

在取得某个元素引用的情况下，使用这个方法能够方便地检测它是否会被`querySelector()`或`querySelectorAll()`方法返回。

```js
if (document.body.matches ("body.page1")){
    // true
}
```



## 15.2 元素遍历

Element Traversal API为DOM元素添加了以下5个属性：

- `childElementCount`：返回子元素（不包括文本节点和注释）的个数。
- `firstElementChild`：指向第一个子元素；`firstChild`的元素版。
- `lastElementChild`：指向最后一个子元素；`lastChild`的元素版。
- `previousElementSibling`：指向前一个同辈元素；`previousSibling`的元素版。
- `nextElementSibling`：指向后一个同辈元素；`nextSibling`的元素版。

使用Element Traversal属性实现跨浏览器遍历某元素的所有子元素：

```js
let parentElement = document.getElementById('parent');
let currentChildElement = parentElement.firstElementChild;

// For zero children, firstElementChild returns null and the loop is skipped
while (currentChildElement) {
    // You already know this is an ELEMENT_NODE, do whatever work is needed here
    processChild(currentChildElement);
    if (currentChildElement === parentElement.lastElementChild) {
        break;
    }
    currentChildElement = currentChildElement.nextElementSibling;
}
```

使用传统方式实现以上相同功能：

```js
let parentElement = document.getElementById('parent');
let currentChildNode = parentElement.firstChild;

// For zero children, firstChild returns null and the loop is skipped
while (currentChildNode) {
    if (currentChildNode.nodeType === 1) {
    	// If this is an ELEMENT_NODE, do whatever work is needed in here
        processChild(currentChildNode);
    }
    if (currentChildNode === parentElement.lastChild) {
        break;
    }
    currentChildNode = currentChildNode.nextSibling;
}
```



## 15.3 HTML5

### 15.3.1 与`class`属性相关的扩充

**`getElementsByClassName()`方法**可以通过`document`对象及所有HTML元素调用该方法。这个方法接收一个参数，即一个包含一或多个类名的字符串，返回带有指定类的所有元素的`NodeList`。传入多个类名时，类名的先后顺序不重要。

```js
// Get all elements with a class containing "username" and "current"
// It does not matter if one is declared before the other
let allCurrentUsernames = document.getElementsByClassName("username current");

// Get all elements with a class of "selected" that exist in myDiv's subtree
let selected = document.getElementById("myDiv").getElementsByClassName("selected");
```

**`classList`属性**是新集合类型`DOMTokenList`的实例。当`class`属性中包含多个类名时，使用它可以方便地修改`class`属性中的类名。与其他DOM集合类似，`DOMTokenList`有一个表示自己包含多少元素的`length`属性，而要取得每个元素可以使用`item()`方法，也可以使用方括号语法。这个新类型还定义如下方法：

- `add(value)`：将给定的字符串值添加到列表中。如果值已经存在，就不添加了。
- `contains(value)`：表示列表中是否存在给定的值，如果存在则返回`true`，否则返回`false`。
- `remove(value)`：从列表中删除给定的字符串。
- `toggle(value)`：如果列表中已经存在给定的值，删除它；如果列表中没有给定的值，添加它。

### 15.3.2 焦点管理

HTML5也添加了辅助管理DOM焦点的功能：

- `document.activeElement`属性，这个属性始终会引用DOM中当前获得了焦点的元素。元素获得焦点的方式有页面加载、用户输入（通常是通过按Tab键）和在代码中调用`focus()`方法。
- `document.hasFocus()`方法用于确定文档是否获得了焦点。通过检测文档是否获得了焦点，可以知道用户是不是正在与页面交互。

```js
let button = document.getElementById("myButton");
button.focus();
console.log(document.activeElement === button); // true
```

```js
let button = document.getElementById("myButton");
button.focus();
console.log(document.hasFocus()); // true
```

### 15.3.3 `HTMLDocument`的变化

HTML5扩展了`HTMLDocument`，增加了新的功能。

- **`document.readyState`属性** ：可以使用它来确认文档是否已经加载完成。它有2个可能的值，`loading`表示正在加载文档；`complete`表示已经加载完文档。

`document.readyState`属性的基本用法如下：

```js

if (document.readyState == "complete"){ 
    //执行操作
}
```

- **`document.compatMode`属性** ：这个属性就是为了告诉开发人员浏览器采用了哪种渲染模式。在标准模式下，`document.compatMode`的值等于`"CSS1Compat"`，而在混杂模式下，`document.compatMode`的值等于`"BackCompat"`。

- **`document.head`属性** ：引用文档的`<head>`元素。

### 15.3.4 字符集属性

- **`document.characterSet`属性** ：表示文档中实际使用的字符集，也可以用来指定新字符集。默认情况下，这个属性的值为`"UTF-16"`，但可以通过`<meta>`元素、响应头部或直接设置`charset`属性修改这个值。

```js
console.log(document.characterSet); // "UTF-16"
document.characterSet = "UTF-8";
```

### 15.3.5 自定义数据属性

HTML5规定可以为元素添加非标准的属性，但要添加前缀`data-`，目的是为元素提供与渲染无关的信息，或者提供语义信息。

添加了自定义属性之后，可以通过元素的`dataset`属性来访问自定义属性的值。`dataset`属性的值是`DOMStringMap`的一个实例，也就是一个名值对儿的映射。在这个映射中，每个`data-name`形式的属性都会有一个对应的属性，只不过属性名没有`data-`前缀（比如，自定义属性是`data-myname`，那映射中对应的属性就是`myname`）

```js
// Methods used in this example are for illustrative purposes only

let div = document.getElementById("myDiv");

// Get the values
let appId = div.dataset.appId;
let myName = div.dataset.myname;

// Set the value
div.dataset.appId = 23456;
div.dataset.myname = "Michael";

// Is there a "myname" value?
if (div.dataset.myname){
    console.log('Hello, ${div.dataset.myname}');
}
```

如果需要给元素添加一些不可见的数据以便进行其他处理，那就要用到自定义数据属性。在跟踪链接或混搭应用中，通过自定义数据属性能方便地知道点击来自页面中的哪个部分。It is also extensively utilized in numerous single-page application frameworks.

### 15.3.6 插入标记

- **`innerHTML`属性** ：在读模式下，`innerHTML`属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应的HTML标记。在写模式下，`innerHTML`会根据指定的值创建新的DOM树，然后用这个DOM树完全替换调用元素原先的所有子节点。
- **`outerHTML`属性** ：在读模式下，`outerHTML`返回调用它的元素及所有子节点的HTML标签。在写模式下，`outerHTML`会根据指定的HTML字符串创建新的DOM子树，然后用这个DOM子树完全替换调用元素。
- **`insertAdjacentHTML()`方法和`insertAdjacentText()`方法** ：它们接收两个参数：插入位置和要插入的HTML文本。第一个参数必须是下列值之一：
    - `"beforebegin"`，在当前元素之前插入一个紧邻的同辈元素；
    - `"afterbegin"`，在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素；
    - `"beforeend"`，在当前元素之下插入一个新的子元素或在最后一个子元素之后再插入新的子元素；
    - `"afterend"`，在当前元素之后插入一个紧邻的同辈元素。

```js
// Insert as previous sibling
element.insertAdjacentHTML("beforebegin", "<p>Hello world!</p>");
element.insertAdjacentText("beforebegin", "Hello world!");

// Insert as first child
element.insertAdjacentHTML("afterbegin", "<p>Hello world!</p>");
element.insertAdjacentText("afterbegin", "Hello world!");

// Insert as last child
element.insertAdjacentHTML("beforeend", "<p>Hello world!</p>");
element.insertAdjacentText("beforeend", "Hello world!");

// Insert as next sibling
element.insertAdjacentHTML("afterend", "<p>Hello world!</p>"); element.
insertAdjacentText("afterend", "Hello world!");
```

**使用本节介绍的方法替换子节点可能会导致浏览器的内存占用问题。在使用`innerHTML`、`outerHTML`属性和`insertAdjacentHTML()`方法时，最好先手工删除要被替换的元素的所有事件处理程序和JavaScript对象属性。**

### 15.3.7 `scrollIntoView()`方法

`scrollIntoView()`可以在所有HTML元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。

- If an argument of `true` is supplied, it specifies `alignToTop`: The window scrolls so that the top of the element is at the top of the viewport.
- If an argument of `false` is supplied, it specifies `alignToTop`: The window scrolls so that the bottom of the element is at the top of the viewport.
- If an object argument is supplied, the user can provide values for the behavior property, which specifies how the scroll should occur: auto, instant, or smooth (limited support outside of Firefox), and the block property is the same as `alignToTop`.
- If no argument is supplied, the element is scrolled so that it is fully visible in the viewport but may not be aligned at the top.

```js
// Ensures this element is visible
document.forms[0].scrollIntoView();

// These behave identically
document.forms[0].scrollIntoView(true);
document.forms[0].scrollIntoView({block: true});

// This attempts to scroll the element smoothly into view:
document.forms[0].scrollIntoView({behavior: 'smooth', block: true});
```



## 15.4 专有扩展 (PROPRIETARY EXTENSIONS)