# 第3章 基本概念

## 3.1 语法

**区分大小写：** ECMAScript中的一切（变量、函数名和操作符）都区分大小写。

**标识符：** 就是指变量、函数、属性的名字，或者函数的参数。标识符可以是按照下列格式规则组合起来的一或多个字符：

- 第一个字符必须是一个字母、下划线（_）或一个美元符号（$）；
- 其他字符可以是字母、下划线、美元符号或数字。

**注释：** 包括单行注释和块级注释：

```js
// 单行注释

/*
 *  这是一个多行
 *  （块级）注释
 */
```

**严格模式：** ECMAScript 5引入了 **严格模式（strict mode）** 的概念。在严格模式下，ECMAScript 3中的一些不确定的行为将得到处理，而且对某些不安全的操作也会抛出错误。要在整个脚本中启用严格模式，可以在顶部添加如下代码：

```js
"use strict";
```

在函数内部的上方包含这条编译指示，也可以指定函数在严格模式下执行：

```js
function doSomething(){
    "use strict"; 
    //函数体
}
```

**语句：** ECMAScript中的语句以一个分号结尾`；`如果省略分号，则由解析器确定语句的结尾，如下例所示：

```js
var sum = a + b                 // 即使没有分号也是有效的语句——不推荐
var diff = a - b;               // 有效的语句——推荐
```



## 3.2 关键字和保留字

ECMA-262描述了一组具有特定用途的**关键字**，这些关键字可用于表示控制语句的开始或结束，或者用于执行特定操作等。

```
break do in typeof
case else instanceof var
catch export new void
class extends return while
const finally super with
continue for switch yield
debugger function this
default if throw
delete import try
```

ECMA-262还描述了另外一组不能用作标识符的**保留字**。以下是所有ES6中定义的保留字：

```
Always reserved:
enum

Reserved in strict mode:
implements package public
interface protected static
let private

Reserved in module code:
await
```



## 3.3 变量

### 3.3.1 `var`关键字

要定义变量，可以使用`var`操作符，后跟变量名（即一个标识符），如下所示：

```js
var message;
```

ECMAScript也支持在定义变量的同时设置变量的值：

```js
var message = "hi";
```

#### `var`声明的作用域

注意，即用`var`操作符定义的变量将成为定义该变量的作用域中的局部变量。也就是说，如果在函数中使用`var`定义一个变量，那么这个变量在函数退出后就会被销毁，例如：

```js
function test() {
    var message = "hi"; // local variable
}
test();
console.log(message); // error!
```

像下面这样省略`var`操作符，从而创建一个全局变量：

```js
function test() {
    message = "hi"; // global variable
}
test();
console.log(message); // "hi"
```

可以使用一条语句定义多个变量（初始化或不初始化均可）：

```js
var message = "hi",
    found = false,
    age = 29;
```

#### `var`声明提升（Declaration Hoisting）

当使用`var`时，下面这样的声明是可行的。因为使用`var`关键字声明的变量会被提升到函数作用域的顶部：

```js
function foo() {
    console.log(age);
    var age = 26;
}
foo(); // undefined
```

这样声明不会抛出错误，在ECMAScript 运行时看来，它等同于如下形式：

```js
function foo() {
    var age;
    console.log(age);
    age = 26;
}
foo(); // undefined
```

声明提升还允许你使用冗余的`var`声明：

```js
function foo() {
    var age = 16;
    var age = 26;
    var age = 36;
    console.log(age);
}
foo(); // 36
```



### 3.3.2 `let`声明



### 3.3.3 `const`声明



### 3.3.4 Declaration Styles and Best Practices



## 3.4 数据类型

ECMAScript中有6种简单数据类型（也称为基本数据类型）：

- `Undefined`
- `Null`
- `Boolean`
- `Number`
- `String`
- `Symbol`


还有1种复杂数据类型`Object`，`Object`本质上是由一组无序的名值对组成的。

### 3.4.1 `typeof`操作符

鉴于ECMAScript是松散类型的，因此需要`typeof`来检测给定变量的数据类型。对一个值使用`typeof`操作符可能返回下列某个字符串：

- `"undefined"`：如果这个值未定义；
- `"boolean"`：如果这个值是布尔值；
- `"string"`：如果这个值是字符串；
- `"number"`：如果这个值是数值；
- `"object"`：如果这个值是对象或`null`；
- `"function"`：如果这个值是函数。
- `"symbol"`: 如果值是Symbol。

像下面这样调用`typeof`操作符：

```js
let message = "some string";
console.log(typeof message); // "string"
console.log(typeof(message)); // "string"
console.log(typeof 95); // "number"
```

### 3.4.2 `Undefined`类型

`Undefined`类型只有一个值，即特殊的`undefined`。在使用`var`或`let`声明变量但未对其加以初始化时，这个变量的值就是`undefined`，例如：

```js
let message;
console.log(message == undefined); // true
```

已声明但未初始化的变量与尚未声明的变量是不一样的：

```js
let message; // this variable is declared but has a value of undefined
// make sure this variable isn't declared
// let age
console.log(message); // "undefined"
console.log(age); // causes an error
```

对未初始化和未声明的变量执行`typeof`操作符都返回了`undefined`值：

```js
let message; // this variable is declared but has a value of undefined
// make sure this variable isn't declared
// let age
console.log(typeof message); // "undefined"
console.log(typeof age); // "undefined"
```

值`undefined`是假的（falsy）， 因此，您可以在需要的地方进行更简洁的检查：

```js
let message; // this variable is declared but has a value of undefined
// 'age' is not declared
if (message) {
    // This block will not execute
}
if (!message) {
    // This block will execute
}
if (age) {
    // This will throw an error
}
```

### 3.4.3 Null类型

