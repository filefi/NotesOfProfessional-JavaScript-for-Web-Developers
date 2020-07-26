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

