# 第16章　DOM2和DOM3

DOM1级主要定义的是HTML和XML文档的底层结构。DOM2和DOM3级则在这个结构的基础上引入了更多的交互能力，也支持了更高级的XML特性。为此，DOM2和DOM3级分为许多模块（模块之间具有某种关联），分别描述了DOM的某个非常具体的子集。这些模块 如下。

- DOM2级核心（DOM Level 2 Core）：在1级核心基础上构建，为节点添加了更多方法和属性。
- DOM2级视图（DOM Level 2 Views）：为文档定义了基于样式信息的不同视图。
- DOM2级事件（DOM Level 2 Events）：说明了如何使用事件与DOM文档交互。
- DOM2级样式（DOM Level 2 Style）：定义了如何以编程方式来访问和改变CSS样式信息。
- DOM2级遍历和范围（DOM Level 2 Traversal and Range）：引入了遍历DOM文档和选择其特定部分的新接口。
- DOM2级 HTML（DOM Level 2 HTML）：在1级HTML基础上构建，添加了更多属性、方法和新接口。
- DOM Mutation Observers: Allows for definition of callbacks upon changes to the DOM. Mutation Observers were defined in the DOM4 specification to replace Mutation Events.



## 16.1 DOM变化

### 16.1.1　针对XML命名空间的变化

有了XML命名空间，不同XML文档的元素就可以混合在一起，共同构成格式良好的文档，而不必担心发生命名冲突。从技术上说，HTML不支持XML命名空间，但XHTML支持XML命名空间。

#### `Node`类型的变化

在DOM2级中，`Node`类型包含下列特定于命名空间的属性：

- `localName`：不带命名空间前缀的节点名称。当节点使用了命名空间前缀时，其`nodeName`等于`prefix+":"+ localName`。
- `namespaceURI`：命名空间URI或者（在未指定的情况下是）`null`。
- `prefix`：命名空间前缀或者（在未指定的情况下是）`null`。

DOM3级在此基础上更进一步，又引入了下列与命名空间有关的方法：

- `isDefaultNamespace(namespaceURI)`：在指定的`namespaceURI`是当前节点的默认命名空间的情况下返回`true`。
- `lookupNamespaceURI(prefix)`：返回给定`prefix`的命名空间。
- `lookupPrefix(namespaceURI)`：返回给定`namespaceURI`的前缀。

#### `Document`类型的变化

DOM2级中的`Document`类型也发生了变化，包含了下列与命名空间有关的方法：

- `createElementNS(namespaceURI, tagName)`：使用给定的`tagName`创建一个属于命名空间`namespaceURI`的新元素。
- `createAttributeNS(namespaceURI, attributeName)`：使用给定的`attributeName`创建一个属于命名空间`namespaceURI`的新特性。
- `getElementsByTagNameNS(namespaceURI, tagName)`：返回属于命名空间`namespaceURI`的`tagName`元素的`NodeList`。

只有在文档中存在两个或多个命名空间时，这些与命名空间有关的方法才是必需的。

####  `Element`类型的变化

DOM2级核心”中有关`Element`的变化，主要涉及操作特性。新增的方法如下：

- `getAttributeNS(namespaceURI,localName)`：取得属于命名空间`namespaceURI`且名为`localName`的特性。
- `getAttributeNodeNS(namespaceURI,localName)`：取得属于命名空间`namespaceURI`且名为`localName`的特性节点。
- `getElementsByTagNameNS(namespaceURI, tagName)`：返回属于命名空间`namespaceURI`的`tagName`元素的`NodeList`。
- `hasAttributeNS(namespaceURI,localName)`：确定当前元素是否有一个名为`localName`的特性，而且该特性的命名空间是`namespaceURI`。注意，“DOM2级核心”也增加了一个`hasAttribute()`方法，用于不考虑命名空间的情况。
- `removeAttriubteNS(namespaceURI,localName)`：删除属于命名空间`namespaceURI`且名为`localName`的特性。
- `setAttributeNS(namespaceURI,qualifiedName,value)`：设置属于命名空间`namespaceURI`且名为`qualifiedName`的特性的值为`value`。
- `setAttributeNodeNS(attNode)`：设置属于命名空间`namespaceURI`的特性节点。

除了第一个参数之外，这些方法与DOM1级中相关方法的作用相同；第一个参数始终都是一个命名空间URI。

#### `NamedNodeMap`类型的变化

`NamedNodeMap`类型也新增了下列与命名空间有关的方法。由于特性是通过`NamedNodeMap`表示的，因此这些方法多数情况下只针对特性使用：

- `getNamedItemNS(namespaceURI,localName)`：取得属于命名空间`namespaceURI`且名为`localName`的项。
- `removeNamedItemNS(namespaceURI,localName)`：移除属于命名空间`namespaceURI`且名为`localName`的项。
- `setNamedItemNS(node)`：添加`node`，这个节点已经事先指定了命名空间信息。

### 16.1.2　其他方面的变化

#### `DocumentType`类型的变化

`DocumentType`类型新增了3个属性：

- `publicId`属性 ：表示的是文档类型声明中的两个信息段中的第一个；
- `systemId`属性 ：表示的是文档类型声明中的两个信息段中的第二个；
- `internalSubset`属性 ：用于访问包含在文档类型声明中的额外定义。

```js
console.log(document.doctype.publicId);
console.log(document.doctype.systemId);
console.log(document.doctype.internalSubset);
```

#### `Document`类型的变化

- **`importNode()`方法** ：的用途是从一个文档中取得一个节点，然后将其导入到另一个文档，使其成为这个文档结构的一部分。需要注意的是，每个节点都有一个`ownerDocument`属性，表示所属的文档。如果调用`appendChild()`时传入的节点属于不同的文档（`ownerDocument`属性的值不一样），则会导致错误。但在调用`importNode()`时传入不同文档的节点则会返回一个新节点，这个新节点的所有权归当前文档所有。
- **`defaultView`属性** ：其中保存着一个指针，指向拥有给定文档的窗口（或框架）。
- **`document.implementation.createDocumentType()`方法** ：用于创建一个新的`DocumentType`节点，接受3个参数：文档类型名称、`publicId`、`systemId`。
- **`document.implementation.createDocument()`方法** ：这个方法接受3个参数：针对文档中元素的`namespaceURI`、文档元素的标签名、新文档的文档类型。
- **`document.implementation.createHTMLDocument()`方法** ：创建一个完整的HTML文档，包括`<html>`、`<head>`、`<title>`和`<body>`元素。这个方法只接受一个参数，即新创建文档的标题（放在`<title>`元素中的字符串），返回新的HTML文档。这个新返回的文档，是`HTMLDocument`类型的实例，因而具有该类型的所有属性和方法，包括`title`和`body`属性。

```js
document.defaultView === window // true
```

```js
let htmldoc = document.implementation.createHTMLDocument("New Doc");
console.log(htmldoc.title); // "New Doc"
console.log(typeof htmldoc.body); // "object"
```

#### `Node`类型的变化

- **`isSupported()`方法** ：与DOM1级为`document.implementation`引入的`hasFeature()`方法类似，`isSupported()`方法用于确定当前节点具有什么能力。这个方法也接受相同的两个参数：特性名和特性版本号。如果浏览器实现了相应特性，而且能够基于给定节点执行该特性，`isSupported()`就返回`true`。

- **`isSameNode()`方法** ：这个方法都接受一个节点参数，并在传入节点与引用的节点相同时返回`true`。所谓相同，指的是两个节点引用的是同一个对象。
- **`isEqualNode()`方法** ：这个方法都接受一个节点参数，并在传入节点与引用的节点相等时返回`true`。所谓相等，指的是两个节点是相同的类型，具有相等的属性（`nodeName`、`nodeValue`，等等），而且它们的`attributes`和`childNodes`属性也相等（相同位置包含相同的值）。
- **`setUserData()`方法** ：这个方法会将数据指定给节点，它接受3个参数：要设置的键、实际的数据（可以是任何数据类型）和处理函数。以下代码可以将数据指定给一个节点。传入`setUserData()`中的处理函数会在带有数据的节点被复制、删除、重命名或引入一个文档时调用，因而你可以事先决定在上述操作发生时如何处理用户数据。处理函数接受5个参数：表示操作类型的数值（1表示复制，2表示导入，3表示删除，4表示重命名）、数据键、数据值、源节点和目标节点。在删除节点时，源节点是`null`；除在复制节点时，目标节点均为`null`。
- **`getUserData()`方法** ：使用`getUserData()`并传入相同的键，就可以取得`setUserData()`方法设置的数据。

```js
let div1 = document.createElement("div");
div1.setAttribute("class", "box");

let div2 = document.createElement("div");
div2.setAttribute("class", "box");

console.log(div1.isSameNode(div1)); // true
console.log(div1.isEqualNode(div2)); // true
console.log(div1.isSameNode(div2)); // false
```

```js
// 将数据指定给一个节点
document.body.setUserData("name", "Nicholas", function() {});

// 使用getUserData()并传入相同的键，就可以取得该数据
let value = document.body.getUserData("name");


let div = document.createElement("div");
div.setUserData("name", "Nicholas", function(operation, key, value, src, dest) {
if (operation == 1) {
dest.setUserData(key, value, function() {}); }
});
let newDiv = div.cloneNode(true);
console.log(newDiv.getUserData("name")); // "Nicholas"
```

#### iframes的变化

- **`contentDocument`属性** ：这个属性包含一个指针，指向表示框架内容的文档对象。

```js
let iframe = document.getElementById("myIframe");
let iframeDoc = iframe.contentDocument;
```

由于`contentDocument`属性是`Document`类型的实例，因此可以像使用其他HTML文档一样使用它，包括所有属性和方法。

