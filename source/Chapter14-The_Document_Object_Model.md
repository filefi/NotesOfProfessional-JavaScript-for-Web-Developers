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