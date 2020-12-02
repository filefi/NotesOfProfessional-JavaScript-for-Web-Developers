# 第2章 在HTML中使用JavaScript

## 2.1 `<script>`元素

向HTML页面中插入JavaScript的主要方法，就是使用`<script>`元素。

**`<script>`有下列8个属性：**

- `async`：可选。表示应该立即开始下载脚本，但不能阻止其他页面动作，比如下载资源或等待其他脚本加载。只对外部脚本文件有效。
- `charset`：可选。表示通过`src`属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少有人用。
- `crossorigin`：可选。配置相关请求的CORS（跨源资源共享）设置。默认不使用CORS。`crossorigin="anonymous"`设置文件请求不必设置凭据标志。`crossorigin="use-credentials"`设置凭据标志，意味着出站请求会包含凭据。
- `defer`：可选。表示在文档解析和显示完成后再执行脚本是没有问题的。只对外部脚本文件有效。在IE7及更早的版本中，对行内脚本也可以指定这个属性。
- `integrity`：可选。允许对接收到的资源和指定的加密签名进行比对，以验证子资源完整性（SRI，Subresource Intergrity）。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于确保内容分发网络（CDN，Content Delivery Network）不会提供恶意内容。这个属性不是所有浏览器都支持。
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

包含在`<script>`元素内部的JavaScript代码将被从上至下依次解释。在解释器对`<script>`元素内部的所有代码求值完毕以前，页面中的其余内容都不会被浏览器加载，也不会被显示。

如果要通过`<script>`元素来包含外部JavaScript文件，那么`src`属性就是必需的。这个属性的值是一个指向外部JavaScript文件的URL，例如：

```html
<script src="example.js"></script>
```

与解析嵌入式JavaScript代码一样，在解释外部JavaScript文件时，页面也会阻塞（阻塞时间也包含下载该文件的时间）。

> 按照惯例，外部JavaScript文件带有.js扩展名。如果不使用.js扩展名，请确保服务器能返回正确的MIME类型。

注意，带有`src`属性的`<script>`元素不应该在其`<script>`和`</script>`标签之间再包含额外的JavaScript代码。如果包含了嵌入的代码，则只会下载并执行外部脚本文件，嵌入的代码会被忽略。

另外，通过`<script>`元素的`src`属性还可以包含来自外部域的JavaScript文件。在这一点上，`<script>`与`<img>`元素非常相似，即它的`src`属性可以是指向当前HTML页面所在域之外的某个域中的完整URL。

无论如何包含代码，只要不存在`defer`和`async`属性，浏览器都会按照`<script>`元素在页面中出现的先后顺序对它们**依次**进行解析。

### 2.1.1 标签占位符

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
		<!-- 这里是页面内容 -->
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
        <!-- 这里是页面内容 -->
        <script src="example1.js"></script>
        <script src="example2.js"></script>
    </body>
</html>
```

这样，在解析包含的JavaScript代码之前，页面的内容将完全呈现在浏览器中。

### 2.1.2 推迟执行脚本
`<script>`标签的`defer`属性告诉浏览器立即下载脚本，但脚本会被延迟到整个页面都解析完毕后再运行。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script defer src="example1.js"></script>
        <script defer src="example2.js"></script>
    </head>
    <body>
        <!-- 这里是页面内容 -->
    </body>
</html>
```

这个例子中，`<head>`中包含的`<script>`脚本将延迟到浏览器遇到`</html>`标签后再执行。HTML5规范要求 *延迟脚本* 按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本都会在`DOMContentLoaded`事件之前执行。不过在实际当中，推迟执行的脚本不一定总会按顺序执行或者在`DOMContentLoaded`事件之前执行，因此最好只包含一个这样的脚本。

### 2.1.3 异步脚本
`<script>`元素的`async`属性与`defer`属性类似，都用于改变处理脚本的行为。`async`与`defer`都只适用于外部脚本文件，并告诉浏览器立即下载文件。但与`defer`不同的是，标记为`async`的脚本并不保证按照它们在页面中出现的先后顺序执行。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Example HTML Page</title>
        <script async src="example1.js"></script>
        <script async src="example2.js"></script>
    </head>
    <body>
        <!-- 这里是页面内容 -->
    </body>
</html>
```

在这个例子中，第二个脚本可能先于第一个脚本执行。因此，重点在于它们之间没有依赖关系。给脚本添加`async`属性的目的是告诉浏览器，不必等脚本下载和执行完后再加载页面，同样也不必等到该异步脚本下载和执行后再加载其他脚本。正因为如此，异步脚本不应该在加载期间修改DOM。

异步脚本一定会在页面的`load`事件前执行，但可能会在`DOMContentLoaded`事件触发之前或之后执行。

### 2.1.4 动态脚本加载

并不是只能使用静态`<script>`标签来获取脚本资源。JavaScript可以使用DOM API来动态添加`<script>`元素，并加载`<script>`元素所指定的资源。

```js
let script = document.createElement('script');
script.src = 'gibberish.js';
document.head.appendChild(script);
```

在把`HTMLElement`元素添加到DOM，并且运行到这段代码之前，都不会发送资源请求。默认情况下，使用这种方式创建的`<script>`元素是异步的，相当于添加了`async`属性。然而，这可能会带来问题，因为不是所有浏览器都支持`async`属性。因此，要统一动态脚本加载行为，可以显式地将标签标记为同步：

```js
let script = document.createElement('script');
script.src = 'gibberish.js';
script.async = false;
document.head.appendChild(script);
```

以这种方式获取的资源对浏览器预加载器是不可见的。这会严重影响它们在资源获取队列中的优先级。甚至可能会严重影响性能。要想让预加载器知道这些动态请求文件的存在，可以在文档头部显式声明它们：

```html
<link rel="preload" href="gibberish.js">
```



## 2.2 内联代码VS外部文件

并不存在必须使用外部文件的硬性规定，但使用外部文件具有如下优点：

- **可维护性**：JavaScript代码如果分散到很多HTML页面，会导致维护困难。但把所有JavaScript文件都放在一个文件夹中，开发人员因此也能够在不触及HTML标记的情况下，集中精力编辑JavaScript代码。
- **可缓存**：浏览器会根据特定的设置，缓存所有外部链接的JavaScript文件。也就是说，如果有两个页面都使用同一个文件，那么这个文件只需下载一次。
- **适应未来**：通过把JavaScript放到外部文件中，就无须使用前面提到XHTML或注释hack。HTML和XHTML包含外部文件的语法是相同的。

在配置浏览器请求外部文件时，要重点考虑的一点是它们会占用多少带宽。在SPDY/HTTP2中，预请求的消耗已显著降低，以轻量、独立JavaScript组件形式向客户端送达脚本更具优势。

比如，第一个页面包含如下脚本：

```html
<script src="mainA.js"></script>
<script src="component1.js"></script>
<script src="component2.js"></script>
<script src="component3.js"></script>
...
```

后续页面可能包含如下脚本：

```html
<script src="mainB.js"></script>
<script src="component3.js"></script>
<script src="component4.js"></script>
<script src="component5.js"></script>
...
```

在初次请求时，如果浏览器支持SPDY/HTTP2，就可以从同一个地方取得一批文件，并将它们逐个放到浏览器缓存中。从浏览器角度看，通过SPDY/HTTP2获取所有这些独立的资源与获取一个大JavaScript文件的延迟差不多。

在第二个页面请求时，由于你已经把应用程序切割成了轻量可缓存的文件，第二个页面也依赖的某些组件此时已经存在于浏览器缓存中了。

当然，这里假设浏览器支持SPDY/HTTP2，只有比较新的浏览器才满足。如果你还想支持那些比较老的浏览器，可能还是用一个大文件更合适。



## 2.3 文档模式

- **混杂模式** (quirks mode) 会让IE的行为与（包含非标准特性的）IE5相同；

- **标准模式** (standards mode) 则让IE的行为更接近标准行为。虽然这两种模式主要影响CSS内容的呈现，但在某些情况下也会影响到JavaScript的解释执行。

- **准标准模式** (almost standards mode) 与标准模式之间的差异几乎可以忽略不计。不标准的地方主要体现在处理图片间隙的时候（在表格中使用图片时问题最明显）。

对于**标准模式**，可以通过使用下面任何一种文档类型来开启：

```html
<!-- HTML 4.01 严格型 -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
"http://www.w3.org/TR/html4/strict.dtd">

<!-- XHTML 1.0 严格型 -->
<!DOCTYPE html PUBLIC
"-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

<!-- HTML 5 -->
<!DOCTYPE html>
```

准标准模式与标准模式非常接近，很少需要区分。人们在说到“标准模式”时，可能指其中任何一个。



## 2.4 `<noscript>`元素

包含在`<noscript>`元素中的内容只有在下列任一条件被满足时，才会显示出来：

- 浏览器不支持脚本；
- 浏览器支持脚本，但脚本被禁用。

```html
<html> 
  <head>
    <title>Example HTML Page</title>
    <script type="text/javascript" defer="defer" src="example1.js"></script>
    <script type="text/javascript" defer="defer" src="example2.js"></script>
  </head>
  <body>
    <noscript>
      <p>本页面需要浏览器支持（启用）JavaScript。</p>
    </noscript>
  </body>
</html>
```

