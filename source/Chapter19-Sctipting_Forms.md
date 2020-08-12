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
假设有一个文本框，我们只允许用户输入数值。
此时，可以利用focus事件修改文本框的背景颜色，以便更清楚地表明这个字段获得了焦点。
可以利用blur事件恢复文本框的背景颜色，利用change事件在用户输入了非数值字符时再次修改背景颜色。
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

在HTML中，有两种方式来表现文本框：

- **使用`<input>`元素的单行文本框** ：必须将`<input>`元素的`type`特性设置为`"text"`。而通过设置`size`特性，可以指定文本框中能够显示的字符数。通过`value`特性，可以设置文本框的初始值，而`maxlength`特性则用于指定文本框可以接受的最大字符数。
- **使用`<textarea>`的多行文本框** ：与`<input>`元素不同，`<textarea>`的初始值必须要放在`<textarea>`和`</textarea>`之间，并且不能在HTML中给`<textarea>`指定最大字符数。

我们建议读者像上面这样使用`value`属性读取或设置文本框的值，不建议使用标准的DOM方法。换句话说，不要使用`setAttribute()`设置`<input>`元素的`value`特性，也不要去修改`<textarea>`元素的第一个子节点。

### 19.2.1 选择文本

`<input type='text'>`和`<textarea>`两种文本框都支持`select()`方法，这个方法用于选择文本框中的所有文本。在调用`select()`方法时，大多数浏览器（Opera除外）都会将焦点设置到文本框中。这个方法不接受参数，可以在任何时候被调用。

下面代码在获得文本框焦点时选中所有文本：

```js
let textbox = document.forms[0].elements["textbox1"];

textbox.addEventListener("focus", (event) => {
    event.target.select();
});
```

#### `select`事件

与`select()`方法对应的，是一个`select`事件。在选择了文本框中的文本时，就会触发`select`事件。不过，到底什么时候触发`select`事件，还会因浏览器而异。在IE9+、Opera、Firefox、Chrome和Safari中，只有用户选择了文本（而且要释放鼠标），才会触发`select`事件。另外，在调用`select()`方法时也会触发`select`事件。

```js
let textbox = document.forms[0].elements["textbox1"];

textbox.addEventListener("select", (event) => {
    console.log('Text selected: ${textbox.value}');
});
```

#### 取得选择的文本

HTML5添加两个属性以便取得选择的文本：

- **`selectionStart`属性** ：被选择文本开头索引；
- **`selectionEnd`属性** ：被选择文本的结尾索引；

这两个属性中保存的是基于0的索引数值，表示所选择文本的范围（即文本选区开头和结尾的偏移量）。

要取得用户在文本框中选择的文本，可以使用如下代码：

```js
function getSelectedText(textbox){
    return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
}
```

下面代码在选择事件被触发时返回被选择的文本：
```js
let textbox = document.forms[0].elements["textbox1"];
textbox.addEventListener("select", (event) => {
    selectedText = event.target.value.substring(event.target.selectionStart, event.target.selectionEnd);
    console.log(selecetedText);
});
```

#### 选择部分文本

**`setSelectionRange()`方法** ：为了选择文本框中的部分文本，HTML5为文本框引入了`setSelectionRange()`方法。这个方法接收2个参数：要选择的第一个字符的索引和要选择的最后一个字符之后的字符的索引（类似于`substring()`方法的2个参数）。要看到选择的文本，必须在调用`setSelectionRange()`之前或之后立即将焦点设置到文本框。

```js
textbox.value = "Hello world!"

// select all text
textbox.setSelectionRange(0, textbox.value.length); // "Hello world!"

// select first three characters
textbox.setSelectionRange(0, 3); // "Hel"

// select characters 4 through 6
textbox.setSelectionRange(4, 7); // "o w"
```

### 19.2.2 过滤输入

由于文本框在默认情况下没有提供多少验证数据的手段，因此必须使用JavaScript来完成此类过滤输入的操作。而综合运用事件和DOM手段，就可以将普通的文本框转换成能够理解用户输入数据的功能型控件。

#### 屏蔽字符

有时候，我们需要用户输入的文本中包含或不包含某些字符。如前所述，响应向文本框中插入字符操作的是`keypress`事件。因此，可以通过阻止这个事件的默认行为来屏蔽此类字符。

在极端的情况下，可以通过下列代码屏蔽所有按键操作：

```js
textbox.addEventListener("keypress", (event) => {
    event.preventDefault(); // 所有按键操作都将被屏蔽，导致文本框变成只读的.
});
```

如果只想屏蔽特定的字符，则需要检测`keypress`事件对应的字符编码，然后再决定如何响应。例如，下列代码只允许用户输入数值：

```js
/*
以下代码只能拦截英文字符，不能拦截输入法输入的中文字符
*/
textbox.addEventListener("keypress", (event) => {
    if (!/\d/.test(String.fromCharCode(event.charCode))){
        event.preventDefault();
    }
});
```

以下代码屏蔽非数值字符，但不屏蔽那些也会触发`keypress`事件的基本按键（包括Ctrl键）：

```js
textbox.addEventListener("keypress", (event) => {
    if (!/\d/.test(String.fromCharCode(event.charCode)) && event.charCode > 9 && !event.ctrlKey){
        event.preventDefault();
    }
});
```

#### 操作剪贴板

HTML 5也把剪贴板事件纳入了规范，包含下列6个剪贴板事件：

- `beforecopy`：在发生复制操作前触发。
- `copy`：在发生复制操作时触发。
- `beforecut`：在发生剪切操作前触发。
- `cut`：在发生剪切操作时触发。
- `beforepaste`：在发生粘贴操作前触发。
- `paste`：在发生粘贴操作时触发。

由于没有针对剪贴板操作的标准，这些事件及相关对象会因浏览器而异。

在实际的事件发生之前，通过`beforecopy`、`beforecut`和`beforepaste`事件可以在向剪贴板发送数据，或者从剪贴板取得数据之前修改数据。不过，取消这些事件并不会取消对剪贴板的操作——只有取消`copy`、`cut`和`paste`事件，才能阻止相应操作发生。

**`clipboardData`对象** ：使用`clipboardData`对象可以访问剪贴板中的数据。在IE中，这个对象是`window`对象的属性；而在Firefox、Safari和Chrome中，这个对象是相应`event`对象的属性。但是，在Firefox、Safari和Chorme中，只有在处理剪贴板事件期间`clipboardData`对象才有效，这是为了防止对剪贴板的未授权访问；在IE中，则可以随时访问`clipboardData`对象。为了确保跨浏览器兼容性，最好只在发生剪贴板事件期间使用这个对象。

**`clipboardData`对象**有三个方法：

- **`getData()`方法** ：`getData()`用于从剪贴板中取得数据，它接受一个参数，即要取得的数据的格式。在IE中，有两种数据格式：`"text"`和`"URL"`。在Firefox、Safari和Chrome中，这个参数是一种MIME类型；不过，可以用`"text"`代表`"text/plain"`。
- **`setData()`方法** ：
- **`clearData()`方法** ：