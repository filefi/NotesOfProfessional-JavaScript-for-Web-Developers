# 第2章　在HTML中使用JavaScript

## 2.1 `<script>`元素

向HTML页面中插入JavaScript的主要方法，就是使用`<script>`元素。

**HTML 5为`<script>`定义了下列6个属性：**

- `async`：可选。表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或等待加载其他脚本。只对外部脚本文件有效。
- `charset`：可选。表示通过`src`属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少有人用。
- `defer`：可选。表示脚本可以延迟到文档完全被解析和显示之后再执行。只对外部脚本文件有效。IE7及更早版本对嵌入脚本也支持这个属性。
- `language`： ***已废弃。*** 原来用于表示编写代码使用的脚本语言（如`JavaScript`、`JavaScript1.2`或`VBScript`）。大多数浏览器会忽略这个属性，因此也没有必要再用了。
- `src`：可选。表示包含要执行代码的外部文件。
- `type`：必选，如果没有指定这个属性，则其默认值为`text/javascript`。可以看成是`language`的替代属性；表示编写代码使用的脚本语言的内容类型（也称为MIME类型）。虽然`text/javascript`和`text/ecmascript`都已经不被推荐使用，但人们一直以来使用的都还是`text/javascript`。实际上，服务器在传送JavaScript文件时使用的MIME类型通常是`application/x–javascript`，但在`type`中设置这个值却可能导致脚本被忽略。另外，在非IE浏览器中还可以使用以下值：`application/javascript`和`application/ ecmascript`。考虑到约定俗成和最大限度的浏览器兼容性，目前`type`属性的值依旧还是`text/javascript`。如果值为`module`，代码将被视为ES6 模块，然后才可以使用`import`和`export`关键字。

**使用`<script>`元素的方式有2种：**

- 直接在页面中嵌入JavaScript代码
- 包含外部JavaScript文件

要包含内联JavaScript代码，直接将JavaScript代码放入`<script>`元素内部即可：

```html
<script>
    function sayHi() {
        console.log("Hi!");
    }
</script>
```

包含在`<script>`元素内部的JavaScript代码将被从上至下依次解释。在解释器对`<script>`元素内部的所有代码求值完毕以前，页面中的其余内容都不会被浏览器加载或显示。

如果要通过`<script>`元素来包含外部JavaScript文件，那么`src`属性就是必需的。这个属性的值是一个指向外部JavaScript文件的URL，例如：

```html
<script src="example.js"></script>
```

与解析嵌入式JavaScript代码一样，在解析外部JavaScript文件（包括下载该文件）时，页面的处理也会暂时停止。

> 按照惯例，外部JavaScript文件带有.js扩展名。如果不使用.js扩展名，请确保服务器能返回正确的MIME类型。

注意，带有`src`属性的`<script>`元素不应该在其`<script>`和`</script>`标签之间再包含额外的JavaScript代码。如果包含了嵌入的代码，则只会下载并执行外部脚本文件，嵌入的代码会被忽略。

另外，通过`<script>`元素的`src`属性还可以包含来自外部域的JavaScript文件。在这一点上，`<script>`与`<img>`元素非常相似，即它的`src`属性可以是指向当前HTML页面所在域之外的某个域中的完整URL。

无论如何包含代码，只要不存在`defer`和`async`属性，浏览器都会按照`<script>`元素在页面中出现的先后顺序对它们**依次**进行解析。

### 2.1.1 标签的位置

`<script>`标签的放置位置通常有2个：

- 所有`<script>`元素都应该放在页面的`<head>`元素中；
- 把全部JavaScript引用放在`<body>`元素中页面内容的后面；

按照传统的做法，所有`<script>`元素都应该放在页面的`<head>`元素中，例如：

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example HTML Page</title>
        <script src="example1.js"></script>
        <script src="example2.js"></script>
    </head>
    <body>
		<!-- 这里放内容 -->
    </body>
</html>
```

这种做法的缺点是，必须等到全部JavaScript代码都被下载、解析和执行完成以后，才能开始呈现页面的内容（浏览器在遇到`<body>`标签时才开始呈现内容）。如果页面中有很多JavaScript代码，会导致浏览器在呈现页面时出现明显的延迟，而延迟期间的浏览器窗口中将是一片空白。

为了避免这个问题，现代Web应用程序一般都把全部JavaScript引用放在`<body>`元素中页面内容的后面，如下例所示：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
    </head>
    <body>
        <!-- 这里放内容 -->
        <script src="example1.js"></script>
        <script src="example2.js"></script>
    </body>
</html>
```

这样，在解析包含的JavaScript代码之前，页面的内容将完全呈现在浏览器中。

### 2.1.2 延迟脚本
`<script>`标签的`defer`属性的用途是表明脚本在执行时不会影响页面的构造。也就是说，脚本会被延迟到整个页面都解析完毕后再运行。相当于告诉浏览器立即下载，但延迟执行。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script defer src="example1.js"></script>
        <script defer src="example2.js"></script>
    </head>
    <body>
        <!-- content here -->
    </body>
</html>
```

这个例子中，`<head>`中包含的`<script>`脚本将延迟到浏览器遇到`</html>`标签后再执行。HTML5规范要求 *延迟脚本* 按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于`DOMContentLoaded`事件执行。

### 2.1.3 异步脚本
`<script>`元素的`async`属性与`defer`属性类似，都用于改变处理脚本的行为。`async`只适用于外部脚本文件，并告诉浏览器立即下载文件。但与`defer`不同的是，标记为`async`的脚本并不保证按照指定它们的先后顺序执行。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script async src="example1.js"></script>
        <script async src="example2.js"></script>
    </head>
    <body>
        <!-- 这里放内容 -->
    </body>
</html>
```

异步脚本一定会在页面的`load`事件前执行，但可能会在`DOMContentLoaded`事件触发之前或之后执行。

### 2.1.4 动态脚本加载

并不是只能使用静态`<script>`标签来获取脚本资源。JavaScript可以使用DOM API来动态添加`<script>`元素，并加载`<script>`元素所指定的资源。

```js
let script = document.createElement('script');
script.src = 'gibberish.js';
document.head.appendChild(script);
```

直到将`HTMLElement`被附加到DOM，此脚本本身运行时才会生成此资源请求。默认情况下，使用这种方式创建的脚本是异步的。然而，这可能会带来问题，因为不是所有浏览器都支持异步脚本请求。因此，要统一动态脚本加载行为，可以显式地将标签标记为同步：

```js
let script = document.createElement('script');
script.src = 'gibberish.js';
script.async = false;
document.head.appendChild(script);
```



## 2.2 内联代码VS外部文件

并不存在必须使用外部文件的硬性规定，但使用外部文件具有如下优点：

- **可维护性**：遍及不同HTML页面的JavaScript会造成维护问题。但把所有JavaScript文件都放在一个文件夹中，开发人员因此也能够在不触及HTML标记的情况下，集中精力编辑JavaScript代码。
- **可缓存**：浏览器能够根据具体的设置，缓存链接的所有外部JavaScript文件。也就是说，如果有两个页面都使用同一个文件，那么这个文件只需下载一次。
- **适应未来**：通过外部文件来包含JavaScript无须使用前面提到XHTML或注释hack。HTML和XHTML包含外部文件的语法是相同的。



## 2.3 文档模式

