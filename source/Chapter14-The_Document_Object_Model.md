# 第14章 DOM

DOM（文档对象模型）是针对HTML和XML文档的一个API（应用程序编程接口）。DOM描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。

1998年10月DOM１级规范成为W3C的推荐标准，为基本的文档结构及查询提供了接口。

## 14.1 节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点为根节点的树形结构。

**节点分为几种不同的类型，每种类型分别表示文档中不同的信息及（或）标记。** 每个节点都拥有各自的特点、数据和方法，另外也与其他节点存在某种关系。

**文档节点**是每个文档的根节点。在HTML页面中，**文档元素**始终都是`<html>`元素。在XML中，没有预定义的元素，因此任何元素都可能成为**文档元素**。

每一对标签都可以通过树中的一个节点来表示：HTML元素通过元素节点表示，特性（attribute）通过特性节点表示，文档类型通过文档类型节点表示，而注释则通过注释节点表示。

### 14.1.1 `Node`类型

DOM1级定义了一个`Node`接口，该接口将由DOM中的所有节点类型实现。这个`Node`接口在JavaScript中是作为`Node`类型实现的；除了IE之外，在其他所有浏览器中都可以访问到这个类型。JavaScript中的所有节点类型都继承自`Node`类型，因此所有节点类型都共享着相同的基本属性和方法。

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



### 10.1.3 `Element`类型



