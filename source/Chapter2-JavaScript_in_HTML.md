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