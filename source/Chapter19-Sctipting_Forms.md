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

- **`getData()`方法** ：`getData()`用于从剪贴板中取得数据，它接受一个参数，即要取得的数据的格式。在IE中，有两种数据格式：`"text"`和`"URL"`。在Firefox、Safari和Chrome中，这个参数是一种MIME类型。
- **`setData()`方法** ：`setData()`方法的第一个参数也是数据类型，第二个参数是要放在剪贴板中的文本。对于第一个参数，IE照样支持`"text"`和`"URL"`，而Safari和Chrome仍然只支持MIME类型。这两个浏览器在成功将文本放到剪贴板中后，都会返回`true`；否则，返回`false`。
- **`clearData()`方法**

以下代码对`getData()`和`setData()`实现了跨浏览器的封装：

```js
function getClipboardText(event){
    var clipboardData = (event.clipboardData || window.clipboardData);
    return clipboardData.getData("text");
}

function setClipboardText (event, value){
    if (event.clipboardData){
        return event.clipboardData.setData("text/plain", value);
    } else if (window.clipboardData){
        return window.clipboardData.setData("text", value);
    }
}
```

在`paste`事件中，可以确定剪贴板中的值是否有效，如果无效，就可以像下面示例中那样，取消默认的行为：

```js
textbox.addEventListener("paste", (event) => {
    let text = getClipboardText(event);
    if (!/^\d*$/.test(text)){
        event.preventDefault();
    }
});
```

### 19.2.3 自动切换焦点

为增强易用性，同时加快数据输入，可以在前一个文本框中的字符达到最大数量后，自动将焦点切换到下一个文本框。换句话说，用户在第一个文本框中输入了3个数字之后，焦点就会切换到第二个文本框，再输入3个数字，焦点又会切换到第三个文本框。这种“自动切换焦点”的功能，可以通过下列代码实现：

```html
<input type="text" name="tel1" id="txtTel1" maxlength="3">
<input type="text" name="tel2" id="txtTel2" maxlength="3">
<input type="text" name="tel3" id="txtTel3" maxlength="4">

<script>
function tabForward(event){
    let target = event.target;
    if (target.value.length == target.maxLength){
        let form = target.form;
        for (let i = 0, len = form.elements.length; i < len; i++) {
            if (form.elements[i] == target) {
                if (form.elements[i+1]) {
                    form.elements[i+1].focus();
                }
                return;
            }
        }
    }
}
    
let inputIds = ["txtTel1", "txtTel2", "txtTel3"];
    
for (let id of inputIds) {
	let textbox = document.getElementById(id);
	textbox.addEventListener("keyup", tabForward);
}
    
let textbox1 = document.getElementById("txtTel1");
let textbox2 = document.getElementById("txtTel2");
let textbox3 = document.getElementById("txtTel3");
</script>
```

### 19.2.4 HTML5约束验证API

为了在将表单提交到服务器之前验证数据，HTML5新增了一些功能。

#### 必填字段

第一种情况是在表单字段中指定了`required`属性，如下面的例子所示：

```html
<input type="text" name="username" required>
```

#### 其他输入类型

HTML5为`<input>`元素的`type`属性又增加了几个值。这些新的类型不仅能反映数据类型的信息，而且还能提供一些默认的验证功能。其中，`"email"`和`"url"`是两个得到支持最多的类型，各浏览器也都为它们增加了定制的验证机制：

- `"email"`类型要求输入的文本必须符合电子邮件地址的模式；
- `"url"`类型要求输入的文本必须符合URL的模式。

```html
<input type="email" name ="email">
<input type="url" name="homepage">
```

#### 数值范围

除了`"email"`和`"url"`，HTML5还定义了另外几个输入元素。这几个元素都要求填写某种基于数字的值：`"number"`、`"range"`、`"datetime"`、`"datetime-local"`、`"date"`、`"month"`、`"week"`，还有`"time"`。

对所有这些数值类型的输入元素，可以指定`min`属性（最小的可能值）、`max`属性（最大的可能值）和`step`属性（从`min`到`max`的两个刻度间的差值）。例如，想让用户只能输入0到100的值，而且这个值必须是5的倍数，可以这样写代码：

```html
<input type="number" min="0" max="100" step="5" name="count">
```

以上这些属性在JavaScript中都能通过对应的元素访问（或修改）。此外，还有2个方法：

- `stepUp()`方法
- `stepDown()`方法

这两个方法都接收一个可选的参数：要在当前值基础上加上或减去的数值。（默认是加或减1。）

这两个方法还没有得到任何浏览器支持，但下面的例子演示了它们的用法：

```js
input.stepUp();     //加1
input.stepUp(5);    //加5
input.stepDown();   //减1
input.stepDown(10); //减10
```

#### 输入模式

HTML5为文本字段新增了`pattern`属性。这个属性的值是一个正则表达式，用于匹配文本框中的值。例如，如果只想允许在文本字段中输入数值，可以像下面的代码一样应用约束：

```html
<input type="text" pattern="\d+" name="count">
```

注意，模式的开头和末尾不用加`^`和`$`符号（假定已经有了）。这两个符号表示输入的值必须从头到尾都与模式匹配。

#### 检测有效性

**`checkValidity()`方法** ：使用`checkValidity()`方法可以检测表单中的某个字段是否有效。所有表单字段都有个方法，如果字段的值有效，这个方法返回`true`，否则返回`false`。字段的值是否有效的判断依据是本节前面介绍过的那些约束。换句话说，必填字段中如果没有值就是无效的，而字段中的值与`pattern`属性不匹配也是无效的。例如：

```js
if (document.forms[0].elements[0].checkValidity()){
    //字段有效，继续 
} else {
    //字段无效
}
```

要检测整个表单是否有效，可以在表单自身调用`checkValidity()`方法。如果所有表单字段都有效，这个方法返回`true`；即使有一个字段无效，这个方法也会返回`false`。

```js
if(document.forms[0].checkValidity()){
    //表单有效，继续
} else {
    //表单无效
}
```

**`validity`属性** ：会告诉你为什么字段有效或无效。这个对象中包含一系列属性，每个属性会返回一个布尔值：

- `customError`：如果设置了`setCustomValidity()`，则为`true`，否则返回`false`。
- `patternMismatch`：如果值与指定的`pattern`属性不匹配，返回`true`。
- `rangeOverflow`：如果值比`max`值大，返回`true`。
- `rangeUnderflow`：如果值比`min`值小，返回`true`。
- `stepMisMatch`：如果`min`和`max`之间的步长值不合理，返回`true`。
- `tooLong`：如果值的长度超过了`maxlength`属性指定的长度，返回`true`。有的浏览器（如Firefox 4）会自动约束字符数量，因此这个值可能永远都返回`false`。
- `typeMismatch`：如果值不是`"mail"`或`"url"`要求的格式，返回`true`。
- `valid`：如果这里的其他属性都是`false`，返回`true`。`checkValidity()`也要求相同的值。
- `valueMissing`：如果标注为`required`的字段中没有值，返回`true`。

#### 禁用验证

通过设置`novalidate`属性，可以告诉表单不进行验证：

```html
<form method="post" action="signup.php" novalidate>
    <!--这里插入表单元素--> 
</form>
```

在JavaScript中使用`noValidate`属性可以取得或设置这个值，如果这个属性存在，值为`true`，如果不存在，值为`false`。

```js
document.forms[0].noValidate = true; //禁用验证
```

如果一个表单中有多个提交按钮，为了指定点击某个提交按钮不必验证表单，可以在相应的按钮上添加`formnovalidate`属性。

```html
<form method="post" action="foo.php">
    <!--这里插入表单元素--> 
    <input type="submit" value="Regular Submit">
    <input type="submit" formnovalidate name="btnNoValidate"
        value="Non-validating Submit">
</form>
```



## 19.3 选择框脚本

选择框是通过`<select>`和`<option>`元素创建的。为了方便与这个控件交互，除了所有表单字段共有的属性和方法外，`HTMLSelectElement`类型还提供了下列属性和方法：

- `add(newOption, relOption)`：向控件中插入新`<option>`元素，其位置在相关项（`relOption`）之前。
- `multiple`：布尔值，表示是否允许多项选择；等价于HTML中的`multiple`特性。
- `options`：控件中所有`<option>`元素的`HTMLCollection`。
- `remove(index)`：移除给定位置的选项。
- `selectedIndex`：基于0的选中项的索引，如果没有选中项，则值为`-1`。对于支持多选的控件，只保存选中项中第一项的索引。
- `size`：选择框中可见的行数；等价于HTML中的`size`特性。
- `type`属性：不是`"select-one"`，就是`"select-multiple"`，这取决于HTML代码中有没有`multiple`特性。
- `value`属性：由当前选中项决定，相应规则如下：
  - 如果没有选中的项，则选择框的`value`属性保存空字符串。
  - 如果有一个选中项，而且该项的`value`特性已经在HTML中指定，则选择框的`value`属性等于选中项的`value`特性。即使`value`特性的值是空字符串，也同样遵循此条规则。
  - 如果有一个选中项，但该项的`value`特性在HTML中未指定，则选择框的`value`属性等于该项的文本。
  - 如果有多个选中项，则选择框的`value`属性将依据前两条规则取得第一个选中项的值。

在DOM中，每个`<option>`元素都有一个`HTMLOptionElement`对象表示。为便于访问数据，`HTMLOptionElement`对象添加了下列属性：

- `index`：当前选项在`options`集合中的索引。
- `label`：当前选项的标签；等价于HTML中的`label`特性。
- `selected`：布尔值，表示当前选项是否被选中。将这个属性设置为`true`可以选中当前选项。
- `text`：选项的文本。
- `value`：选项的值（等价于HTML中的`value`特性）。

在操作选项时，我们建议最好是使用特定于选项的属性，因为所有浏览器都支持这些属性。在将表单控件作为DOM节点的情况下，实际的交互方式则会因浏览器而异。我们不推荐使用标准DOM技术修改`<option>`元素的文本或者值。

最后，我们还想提醒读者注意一点：选择框的`change`事件与其他表单字段的`change`事件触发的条件不一样。其他表单字段的`change`事件是在值被修改且焦点离开当前字段时触发，而选择框的`change`事件只要选中了选项就会触发。

### 19.3.1 选择选项

对于只允许选择一项的选择框，访问选中项的最简单方式，就是使用选择框的`selectedIndex`属性，如下面的例子所示：

```js
let selectedOption = selectbox.options[selectbox.selectedIndex];
```

取得选中项之后，可以像下面这样显示该选项的索引、文本和值：

```js
let selectedIndex = selectbox.selectedIndex;
let selectedOption = selectbox.options[selectedIndex];
console.log('Selected index: $[selectedIndex}\n' +
            'Selected text: ${selectedOption.text}\n' +
            'Selected value: ${selectedOption.value}');
```

与`selectedIndex`不同，在允许多选的选择框中设置选项的`selected`属性，不会取消对其他选中项的选择，因而可以动态选中任意多个项。`selected`属性的使用方式就是：取得对某一项的引用，然后将其`selected`属性设置为`true`。例如，下面的代码会选中选择框中的第一项：

```js
selectbox.options[0].selected = true;
```

但是，如果是在单选选择框中，修改某个选项的`selected`属性则会取消对其他选项的选择。需要注意的是，将`selected`属性设置为`false`对单选选择框没有影响。

要取得所有选中的项，可以循环遍历选项集合，然后测试每个选项的`selected`属性。来看下面的例子：

```js
function getSelectedOptions(selectbox){
    let result = new Array();
    for (let option of selectbox.options) {
        if (option.selected) {
            result.push(option);
        }
    }
    return result;
}
```

### 19.3.2 添加选项

可以使用JavaScript动态创建选项，并将它们添加到选择框中。添加选项的方式有很多，常用的有3中：

- 使用DOM方法。
- 使用`Option`构造函数来创建新选项。
- 使用选择框的`add()`方法。

```js
/*使用DOM方法*/

let newOption = document.createElement("option");  // 创建了一个新的<option>元素
newOption.appendChild(document.createTextNode("Option text")); // 为它添加了一个文本节点
newOption.setAttribute("value", "Option value");  // 设置其value特性
selectbox.appendChild(newOption); // 将它添加到了选择框中
```

```js
/*使用Option构造函数来创建新选项*/

let newOption = new Option("Option text", "Option value");
selectbox.appendChild(newOption);     //在IE8及之前版本中有问题
```

```js
/*使用选择框的add()方法*/

var newOption = new Option("Option text", "Option value");
selectbox.add(newOption, undefined); // 第二个参数传入undefined，可以在所有浏览器中都将新选项插入到列表最后
```

### 19.3.3 移除选项

移除选项的方式也有很多种，常见的有：

- 使用DOM的`removeChild()`方法，为其传入要移除的选项。
- 使用选择框的`remove()`方法。这个方法接受一个参数，即要移除选项的索引。
- 将相应选项设置为`null`。

```js
/*使用DOM的removeChild()方法，为其传入要移除的选项。*/
selectbox.removeChild(selectbox.options[0]);     //移除第一个选项
```

```js
/*使用选择框的remove()方法。这个方法接受一个参数，即要移除选项的索引。*/
selectbox.remove(0);     //移除第一个选项
```

```js
/*将相应选项设置为null。*/
selectbox.options[0] = null;    //移除第一个选项
```

要清除选择框中所有的项，需要迭代所有选项并逐个移除它们，如下面的例子所示：

```js
function clearSelectbox(selectbox) {
    for (let option of selectbox.options) {
        selectbox.remove(0);
    }
}
```

#### 19.3.4 移动和重排选项

使用DOM的`appendChild()`方法，可以方便的移动。如果为`appendChild()`方法传入一个文档中已有的元素，那么就会先从该元素的父节点中移除它，再把它添加到指定的位置。

```js
/*将第一个选择框中的第一个选项移动到第二个选择框中*/

let selectbox1 = document.getElementById("selLocations1");
let selectbox2 = document.getElementById("selLocations2");
selectbox2.appendChild(selectbox1.options[0]);
```

使用`insertBefore()`方法可以方便地将选择框中的某一项移动到特定位置：

```js
/*在选择框中向前移动一个选项的位置*/

let optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove, selectbox.options[optionToMove.index-1]);
```

```js
/*将选择框中的选项向后移动一个位置*/

let optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove, selectbox.options[optionToMove.index+2]);
```



## 19.4 表单序列化

**在表单提交期间，浏览器将数据发送给服务器时会进行以下步骤：**

- 对表单字段的名称和值进行URL编码，使用和号（&）分隔。
- 不发送禁用的表单字段。
- 只发送勾选的复选框和单选按钮。
- 不发送`type`为`"reset"`和`"button"`的按钮。
- 多选选择框中的每个选中的值单独一个条目。
- 在单击提交按钮提交表单的情况下，也会发送提交按钮；否则，不发送提交按钮。也包括`type`为`"image"`的`<input>`元素。
- `<select>`元素的值，就是选中的`<option>`元素的`value`特性的值。如果`<option>`元素没有`value`特性，则是`<option>`元素的文本值。

在JavaScript中，可以利用表单字段的`type`属性，连同`name`和`value`属性一起实现对表单的序列化。

```js
function serialize(form) {
    let parts = [];
    let optValue;
    for (let field of form.elements) {
        switch (field.type) {
            case "select-one":
            case "select-multiple":
                if (field.name.length) {
                    for (let option of field.options) {
                        if (option.selected) {
                            if (option.hasAttribute) {
                                optValue = (option.hasAttribute("value") ?
                                    option.value : option.text);
                            } else {
                                optValue = (option.attributes["value"].specified ?
                                    option.value : option.text);
                            }
                            parts.push(encodeURIComponent(field.name)
                                } + "=" +
                                encodeURIComponent(optValue));
                    }
                }
        }
        break;
        case undefined: // fieldset
            case "file": // file input
            case "submit": // submit button
            case "reset": // reset button
            case "button": // custom button
            break;
        case "radio": // radio button
        case "checkbox": // checkbox
        if (!field.checked) {
            break;
        }
        default:
        // don't include form fields without names
        if (field.name.length) {
            parts.push('${encodeURIComponent(field.name)}=' +
                '${encodeURIComponent(field.value)}');
        }
    }
    return parts.join("&");
}
```



## 19.5 富文本编辑