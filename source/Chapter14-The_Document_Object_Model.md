# 第14章 DOM

DOM（文档对象模型）是针对HTML和XML文档的一个API（应用程序编程接口）。DOM描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。

1998年10月DOM１级规范成为W3C的推荐标准，为基本的文档结构及查询提供了接口。

## 14.1 节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点为根节点的树形结构。

**节点分为几种不同的类型，每种类型分别表示文档中不同的信息及（或）标记。** 每个节点都拥有各自的特点、数据和方法，另外也与其他节点存在某种关系。

**文档节点**是每个文档的根节点。在HTML页面中，**文档元素**始终都是`<html>`元素。在XML中，没有预定义的元素，因此任何元素都可能成为**文档元素**。

每一对标签都可以通过树中的一个节点来表示：HTML元素通过元素节点表示，特性（attribute）通过特性节点表示，文档类型通过文档类型节点表示，而注释则通过注释节点表示。

### 14.1.1 `Node`类型

DOM1级定义了一个`Node`接口，该接口将由DOM中的所有节点类型实现。这个`Node`接口在JavaScript中是作为`Node`类型实现的；除了IE之外，在其他所有浏览器中都可以访问到这个类型。

**JavaScript中的所有节点类型都继承自`Node`类型，因此所有节点类型都共享着相同的基本属性和方法。**

每个节点都有一个`nodeType`属性，用于表明**节点的类型** 。**节点类型**由在`Node`类型中定义的下列12个数值常量来表示，任何节点类型必居其一：

- `Node.ELEMENT_NODE`(1)；
- `Node.ATTRIBUTE_NODE`(2)；
- `Node.TEXT_NODE`(3)；
- `Node.CDATA_SECTION_NODE`(4)；
- `Node.ENTITY_REFERENCE_NODE`(5)；
- `Node.ENTITY_NODE`(6)；
- `Node.PROCESSING_INSTRUCTION_NODE`(7)；
- `Node.COMMENT_NODE`(8)；
- `Node.DOCUMENT_NODE`(9)；
- `Node.DOCUMENT_TYPE_NODE`(10)；
- `Node.DOCUMENT_FRAGMENT_NODE`(11)；
- `Node.NOTATION_NODE`(12)。

通过比较上面这些常量，可以很容易地确定节点的类型，例如：

```js
if (someNode.nodeType == Node.ELEMENT_NODE){   //在IE中无效
    alert("Node is an element.");
}
```

#### `nodeName`和`nodeValue`属性

要了解节点的具体信息，可以使用`nodeName`和`nodeValue`这两个属性。对于元素节点，`nodeName`中保存的始终都是元素的标签名，而`nodeValue`的值则始终为`null`。

#### 节点关系

文档中所有的节点之间都存在这样或那样的关系。节点间的各种关系可以用传统的家族关系来描述，相当于把文档树比喻成家谱。下图形象地展示了节点的关系：

![](_static/images/Chapter14-The_Document_Object_Model..assets/13.d10z.02.png)

**每个节点都有以下属性和方法：**

- **`childNodes`属性**：其中保存着一个`NodeList`对象。`NodeList`是一种类数组对象，用于保存一组有序的节点，可以通过位置来访问这些节点。`NodeList`对象实际上是基于DOM结构动态执行查询的结果，因此DOM结构的变化能够自动反映在`NodeList`对象中。
- **`parentNode`属性**：该属性指向文档树中的父节点。包含在`childNodes`列表中的所有节点都具有相同的父节点，因此它们的`parentNode`属性都指向同一个节点。父节点与其第一个和最后一个子节点之间也存在特殊关系。
- **`previousSibling`和`nextSibling`属性**：此外，包含在`childNodes`列表中的每个节点相互之间都是同胞节点。通过使用列表中每个节点的`previousSibling`和`nextSibling`属性，可以访问同一列表中的其他节点。列表中第一个节点的`previousSibling`属性值为`null`，而列表中最后一个节点的`nextSibling`属性的值同样也为`null`。
- **`firstChild`和`lastChild`属性**：父节点的`firstChild`和`lastChild`属性分别指向其`childNodes`列表中的第一个和最后一个节点。其中，`someNode.firstChild`的值始终等于`someNode.childNodes[0]`，而`someNode.lastChild`的值始终等于`someNode.childNodes [someNode.childNodes.length-1]`。如果没有子节点，那么`firstChild`和`lastChild`的值均为`null`。在只有一个子节点的情况下，`firstChild`和`lastChild`指向同一个节点。
- **`ownerDocument`属性**：该属性指向表示整个文档的文档节点。这种关系表示的是任何节点都属于它所在的文档，任何节点都不能同时存在于两个或更多个文档中。通过这个属性，我们可以不必在节点层次中通过层层回溯到达顶端，而是可以直接访问文档节点。
- **`hasChildNodes()`方法**：这个方法在节点包含一或多个子节点的情况下返回`true`。

#### 操作节点

**以下4个方法操作的都是某个节点的子节点，也就是说，要使用这几个方法必须先取得父节点（使用`parentNode`属性）：**

- **`appendChild()`方法**：用于向`childNodes`列表的末尾添加一个节点。添加节点后，`childNodes`的新增节点、父节点及以前的最后一个子节点的关系指针都会相应地得到更新。更新完成后，`appendChild()`返回新增的节点。*注意: 任何DOM节点不能同时出现在文档中的多个位置上。*

```js
let returnedNode = someNode.appendChild(newNode);
alert(returnedNode == newNode); // true
alert(someNode.lastChild == newNode); // true
```

- **`insertBefore()`方法**：该方法把节点放在`childNodes`列表中某个特定的位置上，而不是放在末尾。插入节点后，被插入的节点会变成参照节点的前一个同胞节点（`previousSibling`），同时被方法返回。这个方法接受两个参数：要插入的节点和作为参照的节点。如果参照节点是`null`，则`insertBefore()`与`appendChild()`执行相同的操作。

```js
// insert as last child
returnedNode = someNode.insertBefore(newNode, null);
alert(newNode == someNode.lastChild); // true

// insert as the new first child
returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
alert(returnedNode == newNode); // true
alert(newNode == someNode.firstChild); // true

// insert before last child
returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
alert(newNode == someNode.childNodes[someNode.childNodes.length - 2]); // true
```

- **`replaceChild()`方法** ：这个方法返回要替换的节点并将其从文档树中移除，同时由要插入的节点占据其位置。该接受的两个参数是：要插入的节点和要替换的节点。

```js
// replace first child
let returnedNode = someNode.replaceChild(newNode, someNode.firstChild);

// replace last child
returnedNode = someNode.replaceChild(newNode, someNode.lastChild);
```

- **`removeChild()`方法** ：这个方法移除而非替换节点，被移除的节点将成为方法的返回值。该方法接受一个参数，即要移除的节点。

```js
// remove first child
let formerFirstChild = someNode.removeChild(someNode.firstChild);

// remove last child
let formerLastChild = someNode.removeChild(someNode.lastChild);
```

#### 其他方法

- **`cloneNode()`方法** ：用于创建调用这个方法的节点的一个完全相同的副本。`cloneNode()`方法接受一个布尔值参数，表示是否执行深复制。在参数为`true`的情况下，执行深复制，也就是复制节点及其整个子节点树；在参数为`false`的情况下，执行浅复制，即只复制节点本身。
- **`normalize()`方法** ：这个方法唯一的作用就是处理文档树中的文本节点。由于解析器的实现或DOM操作等原因，可能会出现文本节点不包含文本，或者接连出现两个文本节点的情况。当在某个节点上调用这个方法时，就会在该节点的后代节点中查找上述两种情况。如果找到了空文本节点，则删除它；如果找到相邻的文本节点，则将它们合并为一个文本节点。

> `cloneNode()`方法不会复制添加到DOM节点中的JavaScript属性，例如事件处理程序等。这个方法只复制特性、（在明确指定的情况下也复制）子节点，其他一切都不会复制。IE在此存在一个bug，即它会复制事件处理程序，所以我们建议在复制之前最好先移除事件处理程序。



### 14.1.2 `Document`类型

JavaScript通过`Document`类型表示文档，可以表示HTML页面或者其他基于XML的文档。

在浏览器中，`document`对象是`HTMLDocument`（继承自`Document`类型）的一个实例，表示整个HTML页面。而且，`document`对象是`window`对象的一个属性，因此可以将其作为全局对象来访问。

`Document`节点具有下列特征：

- `nodeType`的值为9；
- `nodeName`的值为`"#document"`；
- `nodeValue`的值为`null`；
- `parentNode`的值为`null`；
- `ownerDocument`的值为`null`；
- 其子节点可能是一个`DocumentType`（最多一个）、`Element`（最多一个）、`ProcessingInstruction`或`Comment`。

**通过`document`这个文档对象，不仅可以取得与页面有关的信息，而且还能操作页面的外观及其底层结构。**

#### 文档子节点

- **`documentElement`属性** ：该属性始终指向HTML页面中的`<html>`元素。
- **`childNodes`属性** ：该类数组属性可以访问文档元素的子元素。
- **`body`属性** ：作为`HTMLDocument`的实例，`document`对象还有一个`body`属性，直接指向`<body>`元素。
- **`doctype`属性** ：`DocumentType`可以作为`Document`的子节点。通常将`<!DOCTYPE>`标签看成一个与文档其他部分不同的实体，可以通过`doctype`属性（在浏览器中是`document.doctype`）来访问它的信息。

```js
// documentElement、firstChild和childNodes[0]的值相同，都指向<html>元素。
let html = document.documentElement;        //取得对<html>的引用
alert(html === document.childNodes[0]);     //true
alert(html === document.firstChild);        //true
```

```js
let body = document.body;    // 取得对<body>的引用
```

```js
let doctype = document.doctype;     //取得对<!DOCTYPE>的引用
```

从技术上说，出现在`<html>`元素外部的注释应该算是文档的子节点。然而，不同的浏览器在是否解析这些注释以及能否正确处理它们等方面，存在很大差异。

#### 文档信息

作为`HTMLDocument`的一个实例，`document`对象还有一些标准的`Document`对象所没有的属性。这些属性提供了`document`对象所表现的网页的一些信息：

- **`title`属性** ：包含着`<title>`元素中的文本，修改`title`属性的值会改变`<title>`元素。
- **`URL`属性** ：中包含页面完整的URL（即地址栏中显示的URL）。
- **`domain`属性** ：中只包含页面的域名。
- **`referrer`属性** ：中则保存着链接到当前页面的那个页面的URL。在没有来源页面的情况下，`referrer`属性中可能会包含空字符串。

#### 查找元素

最常见的DOM应用就是取得特定的某个或某组元素的引用，然后再执行一些操作了。

可以使用`document`对象的几个方法来取得元素。

其中，`Document`类型为此提供了2个方法：

- **`getElementById()`方法** ：接收一个参数：要取得的元素的`id`。如果找到相应的元素则返回该元素，如果不存在带有相应ID的元素，则返回`null`。如果页面中多个元素的ID值相同，`getElementById()`只返回文档中第一次出现的元素。
- **`getElementsByTagName()`方法** ：这个方法接受一个参数，即要取得元素的标签名，而返回的是包含零或多个元素的`NodeList`。在HTML文档中，这个方法会返回一个`HTMLCollection`对象，作为一个“动态”集合，该对象与`NodeList`类似，可以使用方括号语法或`item()`方法来访问`HTMLCollection`对象中的项。

`HTMLDocument`类型提供的方法：

- **`getElementsByName()`方法** ：这个方法会返回带有给定`name`特性的所有元素。



```js
let div = document.getElementById("myDiv"); // retrieve reference to the <div>
let div = document.getElementById("mydiv"); // null
```

```js
let images = document.getElementsByTagName("img");

alert(images.length); // output the number of images
alert(images[0].src); // output the src attribute of the first image
alert(images.item(0).src); // output the src attribute of the first image

// namedItem()方法可以通过元素的name特性取得集合中的项。
let myImage = images.namedItem("myImage");

// HTMLCollection还支持按名称访问项
let myImage = images["myImage"];

// 要想取得文档中的所有元素，可以向getElementsByTagName()中传入"*"
let allElements = document.getElementsByTagName("*");
```

#### 特殊集合

除了属性和方法，`document`对象还有一些特殊的集合。这些集合都是`HTMLCollection`对象，都可以通过`HTMLDocument`对象访问到，而且，与`HTMLCollection`对象类似，集合中的项也会随着当前文档内容的更新而更新：

- `document.anchors`，包含文档中所有带`name`特性的`<a>`元素；
- `document.applets`，包含文档中所有的`<applet>`元素，因为不再推荐使用`<applet>`元素，所以这个集合已经不建议使用了；
- `document.forms`，包含文档中所有的`<form>`元素，与`document.getElementsByTagName("form")`得到的结果相同；
- `document.images`，包含文档中所有的`<img>`元素，与`document.getElementsByTagName("img")`得到的结果相同；
- `document.links`，包含文档中所有带`href`特性的`<a>`元素。

#### DOM一致性检测

由于DOM分为多个级别，也包含多个部分，因此检测浏览器实现了DOM的哪些部分就十分必要了。

`document.implementation`属性就是为此提供相应信息和功能的对象，与浏览器对DOM的实现直接对应。DOM1级只为`document.implementation`规定了一个方法，即`hasFeature()`。这个方法接受两个参数：要检测的DOM功能的名称及版本号。如果浏览器支持给定名称和版本的功能，则该方法返回`true`：

```js
hasXmlDom = document.implementation.hasFeature("XML", "1.0");
```

**建议多数情况下，在使用DOM的某些特殊的功能之前，最好除了检测`hasFeature()`之外，还同时使用能力检测。**

#### 文档写入

`document`对象具备将输出流写入到网页中的能力。这个能力体现在下列4个方法中：

- **`write()`方法** ：接受一个字符串参数，即要写入到输出流中的文本。`write()`会原样写入。
- **`writeln()`方法** ：接受一个字符串参数，即要写入到输出流中的文本。`writeln()`则会在字符串的末尾添加一个换行符（`\n`）。
- **`open()`方法** ：用于打开网页的输出流。如果是在页面加载期间使用`write()`或`writeln()`方法，则不需要用到这两个方法。
- **`close()`方法** ：用于关闭网页的输出流。如果是在页面加载期间使用`write()`或`writeln()`方法，则不需要用到这两个方法。



### 14.1.3 `Element`类型

`Element`类型用于表现XML或HTML元素，提供了对元素标签名、子节点及特性的访问。`Element`节点具有以下特征：

- `nodeType`的值为1；
- `nodeName`的值为元素的标签名；
- `nodeValue`的值为`null`；
- `parentNode`可能是`Document`或`Element`；
- 其子节点可能是`Element`、`Text`、`Comment`、`ProcessingInstruction`、`CDATASection`或`EntityReference`。

#### HTML元素

**所有HTML元素都是由`HTMLElement`或者其更具体的子类型来表示的。** `HTMLElement`类型直接继承自`Element`并添加了一些属性。添加的这些属性分别对应于每个HTML元素中都存在的下列标准特性：

- `id`，元素在文档中的唯一标识符。
- `title`，有关元素的附加说明信息，一般通过工具提示条显示出来。
- `lang`，元素内容的语言代码，很少使用。
- `dir`，语言的方向，值为`"ltr"`（left-to-right，从左至右）或`"rtl"`（right-to-left，从右至左），也很少使用。
- `className`，与元素的`class`特性对应，即为元素指定的CSS类。没有将这个属性命名为`class`，是因为`class`是ECMAScript的保留字。

每个元素都有一或多个特性（attribute），这些特性（attribute）的用途是给出相应元素或其内容的附加信息。操作特性的DOM方法主要有：

- **`getAttribute()`方法** ：这个方法可以取得元素特性（包括自定义特性）的值。如果给定名称的特性不存在，则返回`null`。
- **`setAttribute()`方法** ：这个方法既可以操作HTML特性也可以操作自定义特性。这个方法接受2个参数：要设置的特性名和值。如果特性已经存在，`setAttribute()`会以指定的值替换现有的值；如果特性不存在，`setAttribute()`则创建该属性并设置相应的值。
- **`removeAttribute()`方法** ：这个方法用于彻底删除元素的特性。调用这个方法不仅会清除特性的值，而且也会从元素中完全删除特性。

#### 取得特性 (Getting Attributes)

**任何元素的所有特性（attributes），可以通过`getAttribute()`方法取得（包括自定义属性）的值，也都可以通过DOM元素本身的属性（properties）来访问。**

有2类特殊的特性，它们虽然有对应的特性名，但特性的值与通过`getAttribute()`返回的值并不相同：

- 第一类特性就是`style`，用于通过CSS为元素指定样式。在通过`getAttribute()`访问时，返回的`style`特性值中包含的是CSS文本，而通过属性来访问它则会返回一个对象。由于`style`属性是用于以编程方式访问元素样式的，因此并没有直接映射到`style`特性。

- 第二类与众不同的特性是`onclick`这样的事件处理程序。当在元素上使用时，`onclick`特性中包含的是JavaScript代码，如果通过`getAttribute()`访问，则会返回相应代码的字符串。而在访问`onclick`属性时，则会返回一个JavaScript函数（如果未在元素中指定相应特性，则返回null）。这是因为`onclick`及其他事件处理程序属性本身就应该被赋予函数值。

由于存在这些差别，在通过JavaScript以编程方式操作DOM时，开发人员经常不使用`getAttribute()`，而是只使用对象的属性。

#### 设置特性 (Setting Attributes)

**因为所有特性都是属性，所以除了使用`setAttribute()`方法，还可以通过直接给属性赋值来设置特性的值。**

```js
// 使用setAttribute方法设置元素的特性值
div.setAttribute("id", "someOtherId");
div.setAttribute("class", "ft");
div.setAttribute("title", "Some other text");
div.setAttribute("lang","fr");
div.setAttribute("dir", "rtl");

// 通过直接给属性赋值来设置特性的值
div.id = "someOtherId";
div.align = "left";
```

不过，通过直接给属性赋值来添加自定义的属性，该属性不会自动成为元素的特性：

```js
div.mycolor = "red";
alert(div.getAttribute("mycolor")); // null (except in Internet Explorer)
```

#### `attributes`属性

`Element`类型有一个`attributes`属性，其他DOM节点类型都没有的这个`attributes`属性。`attributes`属性中包含一个`NamedNodeMap`，与`NodeList`类似，也是一个“动态”的集合。元素的每一个特性（attribute）都由一个`Attr`节点表示，每个节点都保存在`NamedNodeMap`对象中。`NamedNodeMap`对象拥有下列方法：

- **`getNamedItem(name)`方法** ：返回`nodeName`属性等于`name`的节点；
- **`removeNamedItem(name)`方法** ：从列表中移除`nodeName`属性等于`name`的节点，返回表示被删除特性的`Attr`节点。
- **`setNamedItem(node)`方法** ：向列表中添加节点，以节点的`nodeName`属性为索引；
- **`item(pos)`方法** ：返回位于数字`pos`位置处的节点。

`attributes`属性中包含一系列节点，每个节点的`nodeName`就是特性的名称，而节点的`nodeValue`就是特性的值。

```js
// 取得元素的`id`特性
let id = element.attributes.getNamedItem("id").nodeValue;

// 以下是使用方括号语法通过特性名称访问节点的简写方式。
let id = element.attributes["id"].nodeValue;

// 也可以使用这种语法来设置特性的值，即先取得特性节点，然后再将其`nodeValue`设置为新值。
element.attributes["id"].nodeValue = "someOtherId";
```

调用`removeNamedItem()`方法与在元素上调用`removeAttribute()`方法的效果相同，唯一的区别是`removeNamedItem()`返回表示被删除特性的`Attr`节点。

```js
let oldAttr = element.attributes.removeNamedItem("id");
```

如果想要遍历元素的特性，`attributes`属性可以派上用场。在需要将DOM结构序列化为XML或HTML字符串时，多数都会涉及遍历元素特性。

```js
// 迭代元素的每一个特性，然后将它们构造成name="value" name="value"这样的字符串格式
function outputAttributes(element) {
    let pairs = [];
    for (let i = 0, len = element.attributes.length; i < len; ++i) {
        const attribute = element.attributes[i];
        pairs.push(`${attribute.nodeName}="${attribute.nodeValue}"`);
    }
    return pairs.join(" ");
}
```

#### 创建元素

使用`document.createElement()`方法可以创建新元素，并为新元素设置`ownerDocument`属性。这个方法只接受一个参数，即要创建元素的标签名。这个标签名在HTML文档中不区分大小写，而在XML（包括XHTML）文档中，则是区分大小写的。

```js
// 创建一个<div>元素
let div = document.createElement("div");

// 给新元素设置新属性
div.id = "myNewDiv";
div.className = "box";
```

要把新元素添加到文档树，可以使用`appendChild()`、`insertBefore()`、`replaceChild()`或`Element.insertAdjacentElement()`方法。

```js
// 把新创建的元素div添加到文档的<body>元素中
document.body.appendChild(div);
```

一旦将元素添加到文档树中，浏览器就会立即呈现该元素。此后，对这个元素所作的任何修改都会实时反映在浏览器中。

#### 元素的子节点

元素可以有任意数目的子节点和后代节点，因为元素可以是其他元素的子节点。元素的`childNodes`属性中包含了它的所有子节点，这些子节点有可能是元素、文本节点、注释或处理指令。

不同浏览器在看待这些节点方面存在显著的不同，以下面的代码为例。

```html
<ul id="myList">
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
```

如果是IE来解析这些代码，那么`<ul>`元素会有3个子节点，分别是3个`<li>`元素。但如果是在其他浏览器中，`<ul>`元素都会有7个元素，包括3个`<li>`元素和4个文本节点（表示`<li>`元素之间的空白符）。如果像下面这样将元素间的空白符删除，那么所有浏览器都会返回相同数目的子节点。

```html
<ul id="myList"><li>Item 1</li><li>Item 2</li><li>Item 3</li></ul>
```

元素也支持`getElementsByTagName()`方法。在通过元素调用这个方法时，除了搜索起点是当前元素之外，其他方面都跟通过`document`调用这个方法相同，因此结果只会返回当前元素的后代。

```js
// 取得前面<ul>元素中包含的所有<li>元素
let ul = document.getElementById("myList");
let items = ul.getElementsByTagName("li");
```



### 14.1.4 `Text`类型

文本节点由`Text`类型表示，包含的是可以照字面解释的纯文本内容。纯文本中可以包含转义后的HTML字符，但不能包含HTML代码。`Text`节点具有以下特征：

- `nodeType`的值为3；
- `nodeName`的值为`"#text"`；
- `nodeValue`的值为节点所包含的文本；
- `parentNode`是一个`Element`；
- 不支持子节点。

`Text`节点具有以下属性与方法：

- `nodeValue`属性：访问和修改`Text`节点中包含的文本，与`data`属性中包含的值相同。对`nodeValue`的修改也会通过`data`反映出来，反之亦然。
- `data`属性：访问和修改`Text`节点中包含的文本，与`nodeValue`属性中包含的值相同。对`data`的修改也会通过`nodeValue`反映出来，反之亦然。
- `length`属性：保存着节点中字符的数目。而且，`nodeValue.length`和`data.length`中也保存着同样的值。
- `appendData(text)`方法：将`text`添加到节点的末尾。
- `deleteData(offset, count)`方法：从`offset`指定的位置开始删除`count`个字符。
- `insertData(offset, text)`方法：在`offset`指定的位置插入`text`。
- `replaceData(offset, count, text)`方法：用`text`替换从`offset`指定的位置开始到`offset+count`为止处的文本。
- `splitText(offset)`方法：从`offset`指定的位置将当前文本节点分成两个文本节点。
- `substringData(offset, count)`方法：提取从`offset`指定的位置开始到`offset+count`为止处的字符串。

在默认情况下，每个可以包含内容的元素最多只能有一个文本节点，而且必须确实有内容存在。

```html
<!-- 没有内容，也就没有文本节点 -->
<div></div>

<!-- 有空格，因而有一个文本节点 -->
<div> </div>

<!-- 有内容，因而有一个文本节点 -->
<div>Hello World!</div>
```

#### 创建文本节点

使用`document.createTextNode()`创建新文本节点，并为其设置`ownerDocument`属性。这个方法接受一个参数——要插入节点中的文本。与设置已有文本节点的值一样，作为参数的文本也将按照HTML或XML的格式进行编码。

```js
let textNode = document.createTextNode("<strong>Hello</strong> world!");
```

除非把新节点添加到文档树中已经存在的节点中，否则我们不会在浏览器窗口中看到新节点。

```js
// 创建一个<div>元素节点
let element = document.createElement("div");
element.className = "message";

// 创建文本节点
let textNode = document.createTextNode("Hello world!");
// 向div元素节点中添加文本节点
element.appendChild(textNode);
// 向body节点中添加div元素节点
document.body.appendChild(element);
```

一般情况下，每个元素只有一个文本子节点。不过，在某些情况下也可能包含多个文本子节点：

```js
let element = document.createElement("div");
element.className = "message";

let textNode = document.createTextNode("Hello world!");
element.appendChild(textNode);

let anotherTextNode = document.createTextNode("Yippee!");
element.appendChild(anotherTextNode);

document.body.appendChild(element);
```

如果两个文本节点是相邻的同胞节点，那么这两个节点中的文本就会连起来显示，中间不会有空格。

#### 规范化文本节点 (Normalizing Text Nodes)

`Node`类型定义了（因而在所有节点类型中都存在）`normalize()`方法。如果在一个包含两个或多个文本节点的父元素上调用`normalize()`方法，则会将所有文本节点合并成一个节点，结果节点的`nodeValue`等于将合并前每个文本节点的`nodeValue`值拼接起来的值。

```js
let element = document.createElement("div");
element.className = "message";

let textNode = document.createTextNode("Hello world!");
element.appendChild(textNode);

let anotherTextNode = document.createTextNode("Yippee!");
element.appendChild(anotherTextNode);

document.body.appendChild(element);
alert(element.childNodes.length); // 2

element.normalize();
alert(element.childNodes.length); // 1
alert(element.firstChild.nodeValue); // "Hello world!Yippee!"
```

#### 分割文本节点

`Text`类型提供了一个作用与`normalize()`相反的方法：`splitText()`。这个方法会将一个文本节点分成两个文本节点，即按照指定的位置分割`nodeValue`值。原来的文本节点将包含从开始到指定位置之前的内容，新文本节点将包含剩下的文本。这个方法会返回一个新文本节点，该节点与原节点的`parentNode`相同。

```js
let element = document.createElement("div");
element.className = "message";

let textNode = document.createTextNode("Hello world!");
element.appendChild(textNode);

document.body.appendChild(element);

let newNode = element.firstChild.splitText(5);
alert(element.firstChild.nodeValue); // "Hello"
alert(newNode.nodeValue); // " world!"
alert(element.childNodes.length); // 2
```



### 14.1.5 `Comment`类型

注释在DOM中是通过`Comment`类型来表示的。`Comment`节点具有下列特征：

- `nodeType`的值为8；
- `nodeName`的值为`"#comment"`；
- `nodeValue`的值是注释的内容；
- `parentNode`可能是`Document`或`Element`；
- 不支持子节点。

`Comment`类型与`Text`类型继承自相同的基类，因此它拥有除`splitText()`之外的所有字符串操作方法。与`Text`类型相似，也可以通过`nodeValue`或`data`属性来取得注释的内容。

使用`document.createComment()`并为其传递注释文本也可以创建注释节点：

```js
let comment = document.createComment("A comment");
```

### 14.1.6 `CDATASection`类型

`CDATASection`类型只针对基于XML的文档，表示的是CDATA区域。`CDATASection`类型继承自`Text`类型，因此拥有除`splitText()`之外的所有字符串操作方法。`CDATASection`节点具有下列特征：

- `nodeType`的值为4；
- `nodeName`的值为`"#cdata-section"`；
- `nodeValue`的值是CDATA区域中的内容；
- `parentNode`可能是`Document`或`Element`；
- 不支持子节点。

在真正的XML文档中，可以使用`document.createCDataSection()`来创建CDATA区域，只需为其传入节点的内容即可。

### 14.1.7 `DocumentType`类型

`DocumentType`包含着与文档的doctype有关的所有信息，它具有下列特征：

- `nodeType`的值为10；
- `nodeName`的值为doctype的名称；
- `nodeValue`的值为`null`；
- `parentNode`是`Document`；
- 不支持子节点。

在DOM1级中，`DocumentType`对象不能动态创建，而只能通过解析文档代码的方式来创建。支持它的浏览器会把`DocumentType`对象保存在`document.doctype`中。

DOM1级描述了`DocumentType`对象的3个属性：

- `name`表示文档类型的名称；

- `entities`是由文档类型描述的实体的`NamedNodeMap`对象；

- `notations`是由文档类型描述的符号的`NamedNodeMap`对象。

通常，浏览器中的文档使用的都是HTML或XHTML文档类型，因而`entities`和`notations`都是空列表（列表中的项来自行内文档类型声明）。`name`属性中保存的是文档类型的名称，也就是出现在`<!DOCTYPE`之后的文本。

### 14.1.8 `DocumentFragment`类型

在所有节点类型中，只有`DocumentFragment`在文档中没有对应的标记。可以将文档片段（document fragment）作为一个“仓库”来使用，即可以在里面保存将来可能会添加到文档中的节点，但不能把文档片段直接添加到文档中。如果将文档中的节点添加到文档片段中，就会从文档树中移除该节点，也不会从浏览器中再看到该节点。添加到文档片段中的新节点同样也不属于文档树。

`DocumentFragment`节点具有下列特征：

- `nodeType`的值为11；
- `nodeName`的值为`"#document-fragment"`；
- `nodeValue`的值为`null`；
- `parentNode`的值为`null`；
- 子节点可以是`Element`、`ProcessingInstruction`、`Comment`、`Text`、`CDATASection`或`EntityReference`。

使用`document.createDocumentFragment()`方法创建文档片段：

```js
let fragment = document.createDocumentFragment();
```

文档片段继承了`Node`的所有方法，通常用于执行那些针对文档的DOM操作。

在将文档片段作为参数传递给`appendChild()`或`insertBefore()`方法时，实际上只会将文档片段的所有子节点添加到相应位置上；文档片段本身永远不会成为文档树的一部分。

假设我们想为这个`<ul>`元素添加3个列表项：

```js
<ul id="myList"></ul>
```

如果逐个地添加列表项，将会导致浏览器反复渲染（呈现）新信息。为避免这个问题，可以像下面这样使用一个文档片段来保存创建的列表项，然后再一次性将它们添加到文档中。

```js
// 创建一个文档片段
let fragment = document.createDocumentFragment();
// 取得了对<ul>元素的引用
let ul = document.getElementById("myList");

for (let i = 0; i < 3; ++i) {
    // 分别创建<li>元素
    let li = document.createElement("li");
    // 创建文本节点
    li.appendChild(document.createTextNode(`Item ${i + 1}`));
    // 将<li>元素添加到文档片段中
    fragment.appendChild(li);
}

// 文档片段的所有子节点都被删除并转移到了<ul>元素中
ul.appendChild(fragment);
```



### 14.1.9 `Attr`类型

元素的特性在DOM中以`Attr`类型来表示。在所有浏览器中（包括IE8），都可以访问`Attr`类型的构造函数和原型。从技术角度讲，特性就是存在于元素的`attributes`属性中的节点。特性节点具有下列特征：

- `nodeType`的值为2；
- `nodeName`的值是特性的名称；
- `nodeValue`的值是特性的值；
- `parentNode`的值为`null`；
- 在HTML中不支持（没有）子节点；
- 在XML中子节点可以是`Text`或`EntityReference`。

尽管它们也是节点，但特性却不被认为是DOM文档树的一部分。开发人员最常使用的是`getAttribute()`、`setAttribute()`和`removeAttribute()`方法，很少直接引用特性节点。

`Attr`对象有3个属性：

- `name`是特性名称（与`nodeName`的值相同）
- `value`是特性的值（与`nodeValue`的值相同）
- `specified`是一个布尔值，用以区别特性是在代码中指定的，还是默认的。

使用`document.createAttribute()`并传入特性的名称可以创建新的特性节点。

```js
// 创建一个新的特性节点
let attr = document.createAttribute("align");\
// 把value属性的值设置为"left"
attr.value = "left";
// 为元素添加align特性
element.setAttributeNode(attr);

alert(element.attributes["align"].value); // "left"
alert(element.getAttributeNode("align").value); // "left"
alert(element.getAttribute("align")); // "left"
```



## 14.2 操作DOM

### 14.2.1 动态脚本

**动态脚本**指的是在页面加载时不存在，但将来的某一时刻通过修改DOM动态添加的脚本。跟操作HTML元素一样，创建动态脚本也有两种方式：插入外部文件和直接插入JavaScript代码。

```js
let script = document.createElement("script");
script.src = "foo.js";
document.body.appendChild(script);   // 把<script>元素添加到页面中之前，是不会下载外部文件的。
```

以上代码创建了以下`<script>`元素：

```html
<script src="foo.js"></script>
```

另一种指定JavaScript代码的方式是行内方式，如下面的例子所示：

```html
<script>
    function sayHi() {
        alert("hi");
    }
</script>
```

从逻辑上讲，下面的DOM代码是等效的：

```js
let script = document.createElement("script");
script.appendChild(document.createTextNode("function sayHi(){alert('hi');}"));
document.body.appendChild(script);
```

### 14.2.2 动态样式

能够把CSS样式包含到HTML页面中的元素有两个。其中，`<link>`元素用于包含来自外部的文件，而`<style>`元素用于指定嵌入的样式。

与动态脚本类似，所谓**动态样式**是指在页面刚加载时不存在的样式；**动态样式**是在页面加载完成后动态添加到页面中的。**加载外部样式文件的过程是异步的。**

以下面这个典型的`<link>`元素为例：

```html
<link rel="stylesheet" type="text/css" href="styles.css">
```

使用DOM代码可以很容易地动态创建出这个元素：

```js
let link = document.createElement("link");
link.rel = "stylesheet";
link.type = "text/css";
link.href = "styles.css";
let head = document.getElementsByTagName("head")[0];
head.appendChild(link);   // 必须将<link>元素添加到<head>而不是<body>元素
```

另一种定义样式的方式是使用`<style>`元素来包含嵌入式CSS，如下所示：

```html
<style type="text/css">
body {
    background-color: red;
}
</style>
```

按照相同的逻辑，下列DOM代码应该是有效的：

```js
let style = document.createElement("style");
style.type = "text/css";
style.appendChild(document.createTextNode("body{background-color:red}"));
let head = document.getElementsByTagName("head")[0];
head.appendChild(style);
```

### 14.2.3 操作表格

```html
<table border="1" width="100%">
    <tbody>
        <tr>
            <td>Cell 1,1</td>
            <td>Cell 2,1</td>
        </tr>
        <tr>
            <td>Cell 1,2</td>
            <td>Cell 2,2</td>
        </tr>
    </tbody>
</table>
```

```js
// create the table
let table = document.createElement("table");
table.border = 1;
table.width = "100%";

// create the tbody
let tbody = document.createElement("tbody");
table.appendChild(tbody);

// create the first row
let row1 = document.createElement("tr");
tbody.appendChild(row1);
let cell1_1 = document.createElement("td");
cell1_1.appendChild(document.createTextNode("Cell 1,1"));
row1.appendChild(cell1_1);
let cell2_1 = document.createElement("td");
cell2_1.appendChild(document.createTextNode("Cell 2,1"));
row1.appendChild(cell2_1);

// create the second row
let row2 = document.createElement("tr");
tbody.appendChild(row2);
let cell1_2 = document.createElement("td");
cell1_2.appendChild(document.createTextNode("Cell 1,2"));
row2.appendChild(cell1_2);
let cell2_2= document.createElement("td");
cell2_2.appendChild(document.createTextNode("Cell 2,2"));
row2.appendChild(cell2_2);

// add the table to the document body
document.body.appendChild(table);
```

为了方便构建表格，HTML DOM还为`<table>`、`<tbody>`和`<tr>`元素添加了一些属性和方法。

为`<table>`元素添加的属性和方法如下：

- `caption`：保存着对`<caption>`元素（如果有）的指针。
- `tBodies`：是一个`<tbody>`元素的`HTMLCollection`。
- `tFoot`：保存着对`<tfoot>`元素（如果有）的指针。
- `tHead`：保存着对`<thead>`元素（如果有）的指针。
- `rows`：是一个表格中所有行的`HTMLCollection`。
- `createTHead()`：创建`<thead>`元素，将其放到表格中，返回引用。
- `createTFoot()`：创建`<tfoot>`元素，将其放到表格中，返回引用。
- `createCaption()`：创建`<caption>`元素，将其放到表格中，返回引用。
- `deleteTHead()`：删除`<thead>`元素。
- `deleteTFoot()`：删除`<tfoot>`元素。
- `deleteCaption()`：删除`<caption>`元素。
- `deleteRow(pos)`：删除指定位置的行。
- `insertRow(pos)`：向`rows`集合中的指定位置插入一行。

为`<tbody>`元素添加的属性和方法如下：

- `rows`：保存着`<tbody>`元素中行的`HTMLCollection`。
- `deleteRow(pos)`：删除指定位置的行。
- `insertRow(pos)`：向`rows`集合中的指定位置插入一行，返回对新插入行的引用。

为`<tr>`元素添加的属性和方法如下：

- `cells`：保存着`<tr>`元素中单元格的`HTMLCollection`。
- `deleteCell(pos)`：删除指定位置的单元格。
- `insertCell(pos)`：向`cells`集合中的指定位置插入一个单元格，返回对新插入单元格的引用。

使用这些属性和方法，可以极大地减少创建表格所需的代码数量。例如，使用这些属性和方法可以将前面的代码重写如下：

```js
// create the table
let table = document.createElement("table");
table.border = 1;
table.width = "100%";

// create the tbody
let tbody = document.createElement("tbody");
table.appendChild(tbody);

// create the first row
tbody.insertRow(0);
tbody.rows[0].insertCell(0);
tbody.rows[0].cells[0].appendChild(document.createTextNode("Cell 1,1"));
tbody.rows[0].insertCell(1);
tbody.rows[0].cells[1].appendChild(document.createTextNode("Cell 2,1"));

// create the second row
tbody.insertRow(1);
tbody.rows[1].insertCell(0);
tbody.rows[1].cells[0].appendChild(document.createTextNode("Cell 1,2"));
tbody.rows[1].insertCell(1);
tbody.rows[1].cells[1].appendChild(document.createTextNode("Cell 2,2"));

// add the table to the document body
document.body.appendChild(table);
```



### 14.2.4 使用`NodeList`

`NodeList`及其“近亲”`NamedNodeMap`和`HTMLCollection`都是“动态的”；换句话说，每当文档结构发生变化时，它们都会得到更新。因此，它们始终都会保存着最新、最准确的信息。从本质上说，所有`NodeList`对象都是在访问DOM文档时实时运行的查询。

下列代码会导致无限循环：

```js
// 取得文档中所有<div>元素的HTMLCollection。
let divs = document.getElementsByTagName("div");
for (let i = 0; i < divs.length; ++i){  // 对条件i < divs.length求值，意味着会运行取得所有<div>元素的查询
    // 创建一个新<div>元素
    let div = document.createElement("div");
    // 将div元素添加到文档中
    document.body.appendChild(div);
}
```

考虑到循环体每次都会创建一个新`<div>`元素并将其添加到文档中，因此`divs.length`的值在每次循环后都会递增。既然`i`和`divs.length`每次都会同时递增，结果它们的值永远也不会相等。

使用ES6的迭代器并不能解决这个问题：

```js
for (let div of document.getElementsByTagName("div")){
    let newDiv = document.createElement("div");
    document.body.appendChild(newDiv);
}
```

**如果想要迭代一个`NodeList`，最好是使用`length`属性初始化第二个变量，然后将迭代器与该变量进行比较：**

```js
let divs = document.getElementsByTagName("div");
for (let i = 0, len = divs.length; i < len; ++i) {
    let div = document.createElement("div");
    document.body.appendChild(div);
}
```

作为另一种替代方法，如果想避免使用第二个变量，也可以反向迭代列表：

```js
let divs = document.getElementsByTagName("div");
for (let i = divs.length - 1; i >= 0; --i) {
    let div = document.createElement("div");
    document.body.appendChild(div);
}
```



## 14.3 MUTATION OBSERVERS

### 14.3.1 Basic usage

### 14.3.2 Controlling the Observer scope with MutationObserverInit

### 14.3.3 Async Callbacks and the Record Queue

### 14.3.4 Performance, Memory, and Garbage Collection

