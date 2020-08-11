# 第19章 表单脚本

## 19.1 表单的基础知识

在HTML中，表单是由`<form>`元素来表示的，而在JavaScript中，表单对应的则是`HTMLFormElement`类型。`HTMLFormElement`继承了`HTMLElement`，因而与其他HTML元素具有相同的默认属性。不过，`HTMLFormElement`也有它自己下列独有的属性和方法。

- `acceptCharset`：服务器能够处理的字符集；等价于HTML中的`accept-charset`特性。
- `action`：接受请求的URL；等价于HTML中的`action`特性。
- `elements`：表单中所有控件的集合（`HTMLCollection`）。
- `enctype`：请求的编码类型；等价于HTML中的`enctype`特性。
- `length`：表单中控件的数量。
- `method`：要发送的HTTP请求类型，通常是`"get"`或`"post"；`等价于HTML的`method`特性。
- `name`：表单的名称；等价于HTML的`name`特性。
- `reset()`：将所有表单域重置为默认值。
- `submit()`：提交表单。
- `target`：用于发送请求和接收响应的窗口名称；等价于HTML的`target`特性。

### 19.1.1 提交表单

用户单击提交按钮或图像按钮时，就会提交表单。使用`<input>`或`<button>`都可以定义提交按钮，只要将其`type`特性的值设置为`"submit"`即可，而图像按钮则是通过将`<input>`的`type`特性值设置为`"image"`来定义的。因此，只要我们单击以下代码生成的按钮，就可以提交表单。

```html
<!-- 通用提交按钮 -->
<input type="submit" value="Submit Form">

<!-- 自定义提交按钮 -->
<button type="submit">Submit Form</button>

<!-- 图像按钮 -->
<input type="image" src="graphic.gif">
```

以这种方式提交表单时，浏览器会在将请求发送给服务器之前触发`submit`事件。这样，我们就有机会验证表单数据，并据以决定是否允许表单提交。阻止这个事件的默认行为就可以取消表单提交。例如，下列代码会阻止表单提交。

```js
let form = document.getElementById("myForm");

form.addEventListener("submit", (event) => {
    // prevent form submission
    event.preventDefault();
});
```

在JavaScript中，以编程方式调用`submit()`方法也可以提交表单。而且，这种方式无需表单包含提交按钮，任何时候都可以正常提交表单。**在以调用`submit()`方法的形式提交表单时，不会触发`submit`事件** ，因此要记得在调用此方法之前先验证表单数据。

```js
let form = document.getElementById("myForm");

// submit the form
form.submit();
```

提交表单时可能出现的最大问题，就是重复提交表单。解决这一问题的办法有两个：在第一次提交表单后就禁用提交按钮，或者利用`onsubmit`事件处理程序取消后续的表单提交操作。

### 19.1.2 重置表单

在用户单击重置按钮时，表单会被重置。使用`type`特性值为`"reset"`的`<input>`或`<button>`都可以创建重置按钮，如下面的例子所示。

```html
<!-- 通用重置按钮 -->
<input type="reset" value="Reset Form">

<!-- 自定义重置按钮 -->
<button type="reset">Reset Form</button>
```

用户单击重置按钮重置表单时，会触发`reset`事件。利用这个机会，我们可以在必要时取消重置操作。例如，下面展示了阻止重置表单的代码。

```js
let form = document.getElementById("myForm");

form.addEventListener("reset", (event) => {
    event.preventDefault();
});
```

与提交表单一样，也可以通过JavaScript来重置表单。**调用`reset()`方法会像单击重置按钮一样触发`reset`事件。**如下面的例子所示：

```js
let form = document.getElementById("myForm");

//重置表单
form.reset();
```

### 19.1.3 表单字段

每个表单都有`elements`属性，该属性是表单中所有表单元素(字段)的集合。这个`elements`集合是一个有序列表，其中包含着表单中的所有字段，例如`<input>`、`<textarea>`、`<button>`和`<fieldset>`。每个表单字段在`elements`集合中的顺序，与它们出现在标记中的顺序相同，可以按照位置和`name`特性来访问它们。如果有多个表单控件都在使用一个`name`（如单选按钮），那么就会返回以该`name`命名的一个`NodeList`。

```js
let form = document.getElementById("form1");

//取得表单中的第一个字段
let field1 = form.elements[0];

//取得名为"textbox1"的字段
let field2 = form.elements["textbox1"];

//取得表单中包含的字段的数量
let fieldCount = form.elements.length;
```

#### 共有的表单字段属性

除了`<fieldset>`元素之外，所有表单字段都拥有相同的一组属性。由于`<input>`类型可以表示多种表单字段，因此有些属性只适用于某些字段，但还有一些属性是所有字段所共有的。表单字段共有的属性如下。

- `disabled`：布尔值，表示当前字段是否被禁用。
- `form`：指向当前字段所属表单的指针；只读。
- `name`：当前字段的名称。
- `readOnly`：布尔值，表示当前字段是否只读。
- `tabIndex`：表示当前字段的切换（tab）序号。
- `type`：当前字段的类型，如`"checkbox"`、`"radio"`，等等。
- `value`：当前字段将被提交给服务器的值。对文件字段来说，这个属性是只读的，包含着文件在计算机中的路径。

除了`form`属性之外，可以通过JavaScript动态修改其他任何属性：

```js
let form = document.getElementById("myForm");
let field = form.elements[0];

// change the value
field.value = "Another value";

// check the value of form
console.log(field.form === form); // true

// set focus to the field
field.focus();

// disable the field
field.disabled = true;

// change the type of field (not recommended, but possible for <input>)
field.type = "checkbox";
```

很多用户可能会重复单击表单的提交按钮。为此，最常见的解决方案，就是侦听`submit`事件，并在该事件第一次发生时禁用提交按钮即可。以下就是这样一个例子：

```js
// Code to prevent multiple form submissions
let form = document.getElementById("myForm");

form.addEventListener("submit", (event) => {
    let target = event.target;
    
    // get the submit button
    let btn = target.elements["submit-btn"];
    
    // disable the submit button
    btn.disabled = true;
});
```

注意，不能通过`onclick`事件处理程序来实现这个功能，原因是不同浏览器之间存在“时差”：有的浏览器会在触发表单的`submit`事件之前触发`click`事件，而有的浏览器则相反。对于先触发`click`事件的浏览器，意味着会在提交发生之前禁用按钮，结果永远都不会提交表单。

除了`<fieldset>`之外，所有表单字段都有`type`属性。对于`<input>`元素，这个值等于HTML特性`type`的值。对于其他元素，这个`type`属性的值如下表所列。

| 说　　明         | HTML示例                             | `type`属性的值      |
| :--------------- | :----------------------------------- | :------------------ |
| 单选列表         | `<select>...</select>`               | `"select-one"`      |
| 多选列表         | `<select multiple>...</select>`      | `"select-multiple"` |
| 自定义按钮       | `<button>...</button>`               | `"submit"`          |
| 自定义非提交按钮 | `<button type="button">...</button>` | `"button"`          |
| 自定义重置按钮   | `<button type="reset">...</buton>`   | `"reset"`           |
| 自定义提交按钮   | `<button type="submit">...</buton>`  | `"submit"`          |

此外，`<input>`和`<button>`元素的`type`属性是可以动态修改的，而`<select>`元素的`type`属性则是只读的。

#### 共有的表单字段方法

每个表单字段都有2个方法：

- **`focus()`方法** ：`focus()`方法用于将浏览器的焦点设置到表单字段，即激活表单字段，使其可以响应键盘事件。接收到焦点的文本框会显示插入符号，随时可以接收输入。
- **`blur()`方法** ：与`focus()`方法相对，`blur()`方法的作用是从元素中移走焦点。

```js
/*
使用`focus()`方法，可以将用户的注意力吸引到页面中的某个部位。
例如，在页面加载完毕后，将焦点转移到表单中的第一个字段。为此，可以侦听页面的`load`事件，并在该事件发生时在表单的第一个字段上调用`focus()`方法，如下面的例子所示。
*/
window.addEventListener("load", (event) => {
    document.forms[0].elements[0].focus();
});
```

HTML5为表单字段新增了一个`autofocus`属性。在支持这个属性的浏览器中，只要设置这个属性，不用JavaScript就能自动把焦点移动到相应字段。例如：

```html
<input type="text" autofocus>
```

```js
/*
在调用blur()方法时，并不会把焦点转移到某个特定的元素上；仅仅是将焦点从调用这个方法的元素上面移走而已。
*/
document.forms[0].elements[0].blur();
```

#### 共有的表单字段事件

除了支持鼠标、键盘、更改和HTML事件之外，所有表单字段都支持下列3个事件：

- `blur`：当前字段失去焦点时触发。
- `change`：对于`<input>`和`<textarea>`元素，在它们失去焦点且`value`值改变时触发；对于`<select>`元素，在其选项改变时触发。
- `focus`：当前字段获得焦点时触发。

这3个事件的使用实例：

```js
/*
假设有一个文本框，我们只允许用户输入数值。此时，可以利用focus事件修改文本框的背景颜色，以便更清楚地表明这个字段获得了焦点。可以利用blur事件恢复文本框的背景颜色，利用change事件在用户输入了非数值字符时再次修改背景颜色。
*/

let textbox = document.forms[0].elements[0];

// onfocus事件处理程序将文本框的背景颜色修改为黄色，以清楚地表明当前字段已经激活。
textbox.addEventListener("focus", (event) => {
    let target = event.target;
    if (target.style.backgroundColor != "red") {
        target.style.backgroundColor = "yellow";
    }
});

// onblur事件处理程序则会在发现非数值字符时，将文本框背景颜色修改为红色。
textbox.addEventListener("blur", (event) => {
    let target = event.target;
    target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
});

// onchange事件处理程序则会在发现非数值字符时，将文本框背景颜色修改为红色。
textbox.addEventListener("change", (event) => {
    let target = event.target;
    target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
});
```

> **注意：关于`blur`和`change`事件的关系，并没有严格的规定。在某些浏览器中，`blur`事件会先于`change`事件发生；而在其他浏览器中，则恰好相反。**



## 19.2 文本框脚本