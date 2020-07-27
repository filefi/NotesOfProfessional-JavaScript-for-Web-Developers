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

DOM2级核心”中有关`Element`的变化，主要涉及操作特性。新增的方法如下。

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