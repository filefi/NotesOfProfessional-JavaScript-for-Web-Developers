# 第12章 浏览器对象模型（BOM）

## 12.1 `window`对象

BOM的核心对象是`window`，它表示浏览器的一个实例。

在浏览器中，`window`对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的`Global`对象。这意味着在网页中定义的任何一个对象、变量和函数，都以`window`作为其`Global`对象.

### 12.1.1 全局作用域

由于`window`对象同时扮演着ECMAScript中`Global`对象的角色，因此所有在全局作用域中声明的变量、函数都会变成`window`对象的属性和方法。

```js
var age = 29;
var sayAge = () => alert(this.age);

alert(window.age); // 29
sayAge();          // 29
window.sayAge();   // 29
```

定义全局变量与在`window`对象上直接定义属性还是有一点差别：全局变量不能通过`delete`操作符删除，而直接在`window`对象上的定义的属性可以。

```js
var age = 29;
window.color = "red";

//在IE < 9 时抛出错误，在其他所有浏览器中都返回false 
delete window.age;    // 返回false

//在IE < 9 时抛出错误，在其他所有浏览器中都返回true
delete window.color; // 返回true

console.log(window.age);   //29
console.log(window.color); //undefined
```

然而如果使用 `let` 或 `const` 替换`var`，变量不会成为`global`对象的属性：

```js
let age = 29;
const sayAge = () => alert(this.age);

console.log(window.age);   // undefined
sayAge();            // undefined
window.sayAge();     // TypeError: window.sayAge is not a function
```

注意：尝试访问未声明的变量会抛出错误，但是可以通过查找`window`对象的属性来检查一个变量是否存在：

```js
// this throws an error because oldValue is undeclared
var newValue = oldValue;

// this doesn't throw an error, because it's a property lookup
// newValue is set to undefined
var newValue = window.oldValue;
```

注意：JavaScript中的很多对象被认为是全局的，例如`location`和`navigator`，但其实它们是`window`对象的属性。

### 12.1.2 窗口关系

`top`对象始终指向最高（最外）层的框架，也就是浏览器窗口。

`parent`（父）对象始终指向当前框架的直接上层框架。对于顶层浏览器窗口，`parent`等于`top`。注意，除非最高层窗口是通过`window.open()`打开的，否则其`window`对象的`name`属性不会包含任何值。

`window`的`self`属性总是指向`window`。实际上，两者可以互换使用。

所有这些对象都是`window`对象的属性，可以通过`window.parent`、`window.top`等形式来访问。同时，这也意味着可以将不同层次的`window`对象串连起来，例如`window.parent.parent`。

### 12.1.3 窗口位置和像素比例

现代的浏览器都提供了`screenLeft`和`screenTop`属性，分别用于表示窗口相对于屏幕左边和上边的位置。

使用`moveTo()`和`moveBy()`方法有可能将窗口精确地移动到一个新位置。这2个方法都接收2个参数，其中`moveTo()`接收的是新位置的*x*和 *y*坐标值，而`moveBy()`接收的是在水平和垂直方向上移动的像素数。

```js
// 将窗口移动到屏幕左上角
window.moveTo(0,0);

// 将窗口向下移动100像素
window.moveBy(0, 100);

// 将窗口移动到(200,300)
window.moveTo(200, 300);

// 将窗口向左移动50像素
window.moveBy(-50, 0);
```

注意，这2个方法可能会被浏览器禁用，这取决于使用的浏览器。

### 12.1.4 窗口大小

跨浏览器确定一个窗口的大小不是一件简单的事。

`document.documentElement.clientWidth`和`document.documentElement.clientHeight`中保存了页面视口的信息。

虽然最终无法确定浏览器窗口本身的大小，但却可以取得页面视口的大小，如下所示：

```js
let pageWidth = window.innerWidth,
pageHeight = window.innerHeight;

if (typeof pageWidth != "number") {
    if (document.compatMode == "CSS1Compat"){
        pageWidth = document.documentElement.clientWidth;
        pageHeight = document.documentElement.clientHeight;
    } else {
        pageWidth = document.body.clientWidth;
        pageHeight = document.body.clientHeight;
    }
}
```

对于移动设备，`window.innerWidth`和`window.innerHeight`保存着可见视口，也就是屏幕上可见页面区域的大小。

在其他移动浏览器中，`document.documentElement`度量的是布局视口，即渲染后页面的实际大小（与可见视口不同，可见视口只是整个页面中的一小部分）。

由于与桌面浏览器间存在这些差异，最好是先检测一下用户是否在使用移动设备，然后再决定使用哪个属性。

使用`resizeTo()`和`resizeBy()`方法可以调整浏览器窗口的大小。这两个方法都接收两个参数，其中`resizeTo()`接收浏览器窗口的新宽度和新高度，而`resizeBy()`接收新窗口与原窗口的宽度和高度之差。注意，这两个方法与移动窗口位置的方法类似，也有可能被浏览器禁用。

```js
// resize to 100 x 100
window.resizeTo(100, 100);
// resize to 200 x 150
window.resizeBy(100, 50);
// resize to 300 x 300
window.resizeTo(300, 300);
```

### 12.1.5 窗口视口位置 (Window Viewport Position)



```js
// Scroll down 100px relative to the current viewport
window.scroll(0, 100);

// Scroll right 40px relative to the current viewport
Window.scroll(40, 0);

// Scroll to the top left corner of the page
// window.scrollTo(0, 0);

// Scroll to 100px from the top and left of the page
// window.scrollTo(100, 100);
```

这些方法也接受`ScrollToOptions`字典，通过这个字典`behavior`属性来告诉浏览器光滑的滚动：

```js
// normal scroll
window.scrollTo({
    left: 100,
    top: 100,
    behavior: 'auto'
});

// smooth scroll
window.scrollTo({
    left: 100,
    top: 100,
    behavior: 'smooth'
});
```



### 12.1.6 导航和打开窗口

使用`window.open()`方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口。这个方法可以接收4个参数：要加载的URL，窗口目标，一个特性字符串，一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。通常只须传递第一个参数，最后一个参数只在不打开新窗口的情况下使用。

如果为`window.open()`传递了第二个参数，而且该参数是已有窗口或框架的名称，那么就会在具有该名称的窗口或框架中加载第一个参数指定的URL：

```js
// 等同于 <a href="http://www.wrox.com" target="topFrame"/>
window.open("http://www.wrox.com/", "topFrame"); 
```

调用这行代码，就如同用户单击了`href`属性为`http://www.wrox.com/`，`target`属性为`"topFrame"`的链接。

#### 弹出窗口

如果给`window.open()`传递的第2个参数并不是一个已经存在的窗口或框架，那么该方法就会根据在第3个参数位置上传入的字符串创建一个新窗口或新标签页。如果没有传入第3个参数，那么就会打开一个带有全部默认设置（工具栏、地址栏和状态栏等）的新浏览器窗口（或者打开一个新标签页——根据浏览器设置）。

第3个参数是一个逗号分隔的设置字符串，表示在新窗口中都显示哪些特性：

| 设　　置     | 值          | 说　　明                                                     |
| :----------- | :---------- | :----------------------------------------------------------- |
| `fullscreen` | `yes`或`no` | 表示浏览器窗口是否最大化。仅限IE                             |
| `height`     | 数值        | 表示新窗口的高度。不能小于100                                |
| `left`       | 数值        | 表示新窗口的左坐标。不能是负值                               |
| `location`   | `yes`或`no` | 表示是否在浏览器窗口中显示地址栏。不同浏览器的默认值不同。如果设置为`no`，地址栏可能会隐藏，也可能会被禁用（取决于浏览器） |
| `menubar`    | `yes`或`no` | 表示是否在浏览器窗口中显示菜单栏。默认值为no                 |
| `resizable`  | `yes`或`no` | 表示是否可以通过拖动浏览器窗口的边框改变其大小。默认值为`no` |
| `scrollbars` | `yes`或`no` | 表示如果内容在视口中显示不下，是否允许滚动。默认值为`no`     |
| `status`     | `yes`或`no` | 表示是否在浏览器窗口中显示状态栏。默认值为`no`               |
| `toolbar`    | `yes`或`no` | 表示是否在浏览器窗口中显示工具栏。默认值为`no`               |
| `top`        | 数值        | 表示新窗口的上坐标。不能是负值                               |
| `width`      | 数值        | 表示新窗口的宽度。不能小于100                                |

通过逗号分隔的名值对列表来指定：

```js
window.open("http://www.wrox.com/",
            "wroxWindow",
            "height=400,width=400,top=10,left=10,resizable=yes"); // 打开一个新的可以调整大小的窗口，窗口初始大小为400×400像素，并且距屏幕上沿和左边各10像素。
```

**注意，整个特性字符串中不允许出现空格。**

`window.open()`方法会返回一个指向新窗口的引用。引用的对象与其他`window`对象大致相似，但我们可以对其进行更多控制。

```js
let wroxWin = window.open("http://www.wrox.com/",
                        "wroxWindow",
                        "height=400,width=400,top=10,left=10,resizable=yes");

// resize it
wroxWin.resizeTo(500, 500);

// move it
wroxWin.moveTo(100, 100);

// 关闭新打开的窗口
wroxWin.close();
```

新创建的`window`对象有一个`opener`属性，其中保存着打开它的原始窗口对象。这个属性只在弹出窗口中的最外层`window`对象（`top`）中有定义，而且指向调用`window.open()`的窗口或框架。例如：

```js
let wroxWin = window.open("http://www.wrox.com/",
                        "wroxWindow",
                        "height=400,width=400,top=10,left=10,resizable=yes");

console.log(wroxWin.opener === window); // true
```

有些浏览器会在独立的进程中运行每个标签页。当一个标签页打开另一个标签页时，如果两个`window`对象之间需要彼此通信，那么新标签页就不能运行在独立的进程中。在Chrome中，如果新创建的标签页不需要与打开它的标签页通信，可以将新创建的标签页的`opener`属性设置为`null`，即表示在单独的进程中运行新标签页。

```js
let wroxWin = window.open("http://www.wrox.com/",
                        "wroxWindow",
                        "height=400,width=400,top=10,left=10,resizable=yes");

wroxWin.opener = null;
```

#### 

#### 弹出窗口屏蔽程序

如果是浏览器内置的屏蔽程序阻止的弹出窗口，那么`window.open()`很可能会返回`null`。此时，只要检测这个返回的值就可以确定弹出窗口是否被屏蔽了。如果是浏览器扩展或其他程序阻止的弹出窗口，那么`window.open()`通常会抛出一个错误。因此，要想准确地检测出弹出窗口是否被屏蔽，必须在检测返回值的同时，将对`window.open()`的调用封装在一个`try-catch`块中，如下所示：

```js
let blocked = false;

try {
    let wroxWin = window.open("http://www.wrox.com", "_blank");
    if (wroxWin == null){
        blocked = true;
    }
} catch (ex){
    blocked = true;
}

if (blocked){
    alert("The popup was blocked!");
}
```

### 12.1.7 超时调用和间歇调用

JavaScript是单线程语言，但它允许通过设置超时值和间歇时间值来调度代码在特定的时刻执行。**超时调用**是在指定的时间过后执行代码，而**间歇调用**则是每隔指定的时间就执行一次代码。

超时调用需要使用`window`对象的`setTimeout()`方法，它接受2个参数：要执行的代码，以毫秒表示的时间（即在执行代码前需要等待多少毫秒）。其中，第1个参数可以是一个包含JavaScript代码的字符串（就和在`eval()`函数中使用的字符串一样），也可以是一个函数。

例如，下面对`setTimeout()`的两次调用都会在一秒钟后显示一个警告框：

```js
// schedules an alert to show after 1 second
setTimeout(() => alert("Hello world!"), 1000);
```

第二个参数是一个表示等待多长时间的毫秒数，但经过该时间后指定的代码不一定会执行。JavaScript是一个单线程的解释器，因此一定时间内只能执行一段代码。为了控制要执行的代码，就有一个JavaScript任务队列。这些任务会按照将它们添加到队列的顺序执行。`setTimeout()`的第二个参数告诉JavaScript再过多长时间把当前任务添加到队列中。如果队列是空的，那么添加的代码会立即执行；如果队列不是空的，那么它就要等前面的代码执行完了以后再执行。

调用`setTimeout()`之后，该方法会返回一个数值ID，表示超时调用。这个超时调用ID是计划执行代码的唯一标识符，可以通过它来取消超时调用。要取消尚未执行的超时调用计划，可以调用`clearTimeout()`方法并将相应的超时调用ID作为参数传递给它：

```js
// set the timeout
let timeoutId = setTimeout(() => alert("Hello world!"), 1000);

// cancel it
clearTimeout(timeoutId);
```

`setInterval()`会按照指定的时间间隔重复执行代码，直至间歇调用被取消或者页面被卸载。它接受的参数与`setTimeout()`相同：要执行的代码（字符串或函数）和每次执行之前需要等待的毫秒数。

```js
setInterval(() => alert("Hello world!"), 10000);
```

调用`setInterval()`方法同样也会返回一个间歇调用ID，该ID可用于在将来某个时刻使用`clearInterval()`方法取消间歇调用。

```js
let num = 0, intervalId = null;
let max = 10;
let incrementNumber = function() {
    num++;
    // if the max has been reached, cancel all pending executions
    if (num == max) {
        clearInterval(intervalId);
        alert("Done");
    }
}
intervalId = setInterval(incrementNumber, 500);
```

这个模式也可以使用超时调用来实现：

```js
let num = 0;
let max = 10;
let incrementNumber = function() {
    num++;
    //if the max has not been reached, set another timeout
    if (num < max) {
        setTimeout(incrementNumber, 500);
    } else {
        alert("Done");
    }
}
setTimeout(incrementNumber, 500);
```

**在开发环境下，很少使用真正的间歇调用，原因是后一个间歇调用可能会在前一个间歇调用结束之前启动。而像前面示例中那样使用超时调用，则完全可以避免这一点。所以，最好不要使用间歇调用。**

### 12.1.8 系统对话框

浏览器通过`alert()`、`confirm()`和`prompt()`方法可以调用系统对话框向用户显示消息。这3个方法打开的对话框都是**同步**和模态的。也就是说，显示这些对话框的时候代码会停止执行，而关掉这些对话框后代码又会恢复执行。

- `alert()`方法：这个方法接受一个字符串并将其显示给用户；
- `confirm()`方法：返回布尔值，`true`表示单击了OK，`false`表示单击了Cancel或单击了右上角的X按钮；
- `prompt()`方法：返回文本输入域的值；如果用户单击了Cancel或没有单击OK而是通过其他方式关闭了对话框，则该方法返回`null`。

```js
alert("Hello world!")
```

```js
if (confirm("Are you sure?")) {
    alert("I'm so glad you're sure!");
} else {
    alert("I'm sorry to hear you're not sure.");
}
```

```js
let result = prompt("What is your name? ", "");
if (result !== null) {
    alert("Welcome, " + result);
}
```

还有两个可以通过JavaScript打开的对话框，即“查找”和“打印”。这两个对话框都是**异步**显示的，能够将控制权立即交还给脚本。这两个对话框与用户通过浏览器菜单的“查找”和“打印”命令打开的对话框相同。在JavaScript中则可以像下面这样通过`window`对象的`find()`和`print()`方法打开它们。

```js
//显示“打印”对话框
window.print();

//显示“查找”对话框
window.find();
```

## 12.2 `location`对象

`location`是最有用的BOM对象之一，它提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。

`location`对象既是`window`对象的属性，也是`document`对象的属性，即`window.location`和`document.location`引用的是同一个对象。

下表列出了`location`对象的所有属性：

| 属　性　名          | 例　　子                | 说　　明                                                     |
| :------------------ | :---------------------- | :----------------------------------------------------------- |
| `location.hash`     | `"#contents"`           | 返回URL中的`hash`（#号后跟零或多个字符），如果URL中不包含散列，则返回空字符串 |
| `location.host`     | `"www.wrox.com:80"`     | 返回服务器名称和端口号（如果有）                             |
| `location.hostname` | `"www.wrox.com"`        | 返回不带端口号的服务器名称                                   |
| `location.href`     | `"http://www.wrox.com"` | 返回当前加载页面的完整URL。而`location`对象的`toString()`方法也返回这个值 |
| `location.pathname` | `"/WileyCDA/"`          | 返回URL中的目录和（或）文件名                                |
| `location.port`     | `"8080"`                | 返回URL中指定的端口号。如果URL中不包含端口号，则这个属性返回空字符串 |
| `location.protocol` | `"http:"`               | 返回页面使用的协议。通常是`http:`或`https:`                  |
| `location.search`   | `"?q=javascript"`       | 返回URL的查询字符串。这个字符串以问号开头                    |
| `location.username` | `"foouser"` | The username specified before the domain name. |
| `location.password` | `"barpassword"` | The password specified before the domain name. |
| `location.origin`  | `"http:// www.wrox.com"` | The origin of the URL. Read only. |

### 12.2.1 查询字符串参数

访问`location`对象中URL包含的查询字符串的属性并不方便。为此，可以像下面这样创建一个函数，用以解析查询字符串，然后返回包含所有参数的一个对象：

```js
let getQueryStringArgs = function() {
    // get query string without the initial '?'
    let qs = (location.search.length > 0 ? location.search.substring(1) : ""),
    // object to hold data
    args = {};
    // assign each item onto the args object
    for (let item of qs.split("&").map(kv => kv.split("="))) {
        let name = decodeURIComponent(item[0]),
        value = decodeURIComponent(item[1]);
        if (name.length) {
            args[name] = value;
        }
    }
    return args;
}

// assume query string of ?q=javascript&num=10
let args = getQueryStringArgs();
alert(args["q"]); // "javascript"
alert(args["num"]); // "10"
```

#### URLSearchParams

`URLSearchParams` offers a collection of utility methods which allow you to inspect and modify query parameters using a standardized API. A `URLSearchParams` instance is created by passing a query string to the constructor. The instance exposes various methods like `get()`, `set()`, and `delete()` to perform query string operations. These are demonstrated here:

```js
let qs = "?q=javascript&num=10";
let searchParams = new URLSearchParams(qs);

alert(searchParams.toString()); // " q=javascript&num=10"
searchParams.has("num"); // true
searchParams.get("num"); // 10

searchParams.set("page", "3");
alert(searchParams.toString()); // " q=javascript&num=10&page=3"

searchParams.delete("q");
alert(searchParams.toString()); // " num=10&page=3"
```

Most browsers that support `URLSearchParams` also support using the `URLSearchParams` as an iterable object:

```js
let qs = "?q=javascript&num=10";
let searchParams = new URLSearchParams(qs);
for (let param of searchParams) {
    console.log(param);
}
// ["q", "javascript"]
// ["num", "10"]
```

### 12.2.2 操作`location`

使用`assign()`方法并为其传递一个URL，就会立即打开新URL并在浏览器的历史记录中生成一条记录：

```js
location.assign("http://www.wrox.com");
```

将`location.href`或`window.location`设置为一个URL值，也会以该值调用`assign()`方法：

```js
// 下列两行代码与显式调用assign()方法的效果完全一样
window.location = "http://www.wrox.com";
location.href = "http://www.wrox.com";
```

修改`location`对象的其他属性（`hash`除外）也可以改变当前加载的页面：

```js
// assume starting at http://www.wrox.com/WileyCDA/
// changes URL to "http://www.wrox.com/WileyCDA/#section1"
location.hash = "#section1";

// changes URL to "http://www.wrox.com/WileyCDA/?q=javascript"
location.search = "?q=javascript";

// changes URL to "http://www.yahoo.com/WileyCDA/"
location.hostname = "www.yahoo.com";

// changes URL to "http://www.yahoo.com/mydir/"
location.pathname = "mydir";

// changes URL to "http://www.yahoo.com:8080/WileyCDA/
Location.port = 8080;
```

当通过上述任何一种方式修改URL之后，浏览器的历史记录中就会生成一条新记录，因此用户通过单击“后退”按钮都会导航到前一个页面。使用`replace()`方法虽然会导致浏览器位置改变，但不会在历史记录中生成新记录。在调用`replace()`方法之后，用户不能回到前一个页面：

```js
location.replace("http://www.wrox.com/")
```

`reload()`方法的作用是重新加载当前显示的页面。如果页面自上次请求以来并没有改变过，页面就会从浏览器缓存中重新加载。如果要强制从服务器重新加载，则需要像下面这样为该方法传递参数`true`。

```js
location.reload();        //重新加载（有可能从缓存中加载）
location.reload(true);    //重新加载（从服务器重新加载）
```



## 12.3　`navigator`对象

### 12.3.1 检测插件

检测浏览器中是否安装了特定的插件是一种最常见的检测例程。对于非IE浏览器，可以使用`plugins`数组来达到这个目的。该数组中的每一项都包含下列属性：

- `name`：插件的名字。
- `description`：插件的描述。
- `filename`：插件的文件名。
- `length`：插件所处理的MIME类型数量。

```js
// plugin detection - doesn't work in Internet Explorer 10 or below
let hasPlugin = function(name) {
    name = name.toLowerCase();
    for (let plugin of window.navigator.plugins){
        if (plugin.name.toLowerCase().indexOf(name) > -1){
            return true;
        }
    }
    return false;
}
// detect flash
alert(hasPlugin("Flash"));
// detect quicktime
alert(hasPlugin("QuickTime"));
```

