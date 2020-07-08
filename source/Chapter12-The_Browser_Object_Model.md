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

