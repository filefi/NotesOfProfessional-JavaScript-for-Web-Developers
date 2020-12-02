# 第3章 基本概念

本章接下来的内容主要基于ECMAScript6。

## 3.1 语法

**区分大小写：** ECMAScript中的一切（变量、函数名和操作符）都区分大小写。

**标识符：** 就是指变量、函数、属性或函数参数的名称。标识符可以由一或多个下列字符组成：

- 第一个字符必须是一个字母、下划线（`_`）或美元符号（`$`）；
- 剩下的其他字符可以是字母、下划线、美元符号或数字。

**注释：** 包括单行注释和块级注释：

```js
// 单行注释

/*
 *  这是一个多行
 *  （块级）注释
 */
```

**严格模式：** ECMAScript 5引入了 **严格模式（strict mode）** 的概念。在严格模式下，ECMAScript 3中的一些不确定的行为将得到处理，而且对某些不安全的操作也会抛出错误。要在整个脚本中启用严格模式，可以在顶部添加一行如下代码：

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

```js
break       do          in            typeof
case        else        instanceof    var
catch       export      new           void
class       extends     return        while
const       finally     super         with
continue    for         switch        yield
debugger    function    this
default     if          throw
delete      import      try
```

ECMA-262还描述了另外一组不能用作标识符的**保留字**。以下是所有ES6中定义的保留字：

```js
始终保留:

enum


严格模式下保留:

implements  package     public
interface   protected   static
let         private


模块代码中保留:

await
```



## 3.3 变量

有3个关键字可以声明变量：

- `var`
- `const`
- `let`

其中，`var`在ECMAScript的所有版本中都可以使用，而`const`和`let`只能在ECMAScript 6及更晚的版本中使用。

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

注意，使用`var`操作符定义的变量会成为包含它的函数的局部变量。也就是说，如果在函数中使用`var`定义一个变量，那么这个变量在函数退出后就会被销毁，例如：

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

当使用`var`时，下面的代码不会报错。因为使用`var`关键字声明的变量会自动被提升到函数作用域的顶部：

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

**`let`跟`var`最明显的区别是，`let`声明的范围是块作用域，而`var`声明的范围是函数作用域。这两个关键字声明的并不是不同类型的变量，它们只是指出变量在相关作用域如何存在。**

```js
if (true) {
  var name = 'Matt';
  console.log(name); // Matt
}
console.log(name);   // Matt

if (true) {
  let age = 26;
  console.log(age);   // 26
}
console.log(age);     // ReferenceError: age没有定义
```

`let`也不允许同一个块作用域中出现冗余声明。这样会导致报错：

```js
var name;
var name;

let age;
let age;  // SyntaxError；标识符age已经声明过了
```

嵌套使用相同的标识符不会报错，这是因为同一个块中没有重复声明：

```js
var name = 'Nicholas';
console.log(name);    // 'Nicholas'
if (true) {
  var name = 'Matt';
  console.log(name);  // 'Matt'
}
console.log(name);   // 'Matt'

let age = 30;
console.log(age);    // 30
if (true) {
  let age = 26;
  console.log(age);  // 26
}
console.log(age);    // 30
```

对声明冗余报错不会因混用`let`和`var`而受影响。

```js
var name;
let name; // SyntaxError

let age;
var age; // SyntaxError
```

#### 暂时性死区

`let`与`var`的另一个重要的区别，就是`let`声明的变量不会在作用域中被提升。

```js
// name会被提升
console.log(name); // undefined
var name = 'Matt';

// age不会被提升
console.log(age); // ReferenceError：age没有定义
let age = 26;
```

在解析代码时，JavaScript引擎也会注意出现在块后面的`let`声明，只不过在此之前不能以任何方式来引用未声明的变量。在`let`声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone），在此阶段引用任何后面才声明的变量都会抛出`ReferenceError`。

#### 全局声明

与`var`关键字不同，使用`var`在全局作用域中声明的变量会成为`window`对象的属性，`let`声明的变量则不会。

```js
var name = 'Matt';
console.log(window.name); // 'Matt'

let age = 26;
console.log(window.age);  // undefined
```

不过，`let`声明仍然是在全局作用域中发生的，相应变量会在页面的生命周期内存续。因此，为了避免`SyntaxError`，必须确保页面不会重复声明同一个变量。

#### 条件声明

对于`let`这个新的ES6声明关键字，不能依赖条件声明模式。

#### `for`循环中的`let`声明

在`let`出现之前，`for`循环定义的迭代变量会渗透到循环体外部：

```js
for (var i = 0; i < 5; ++i) {
  // 循环逻辑
}
console.log(i); // 5
```

改成使用`let`之后，这个问题就消失了，因为迭代变量的作用域仅限于`for`循环块内部：

```js
for (let i = 0; i < 5; ++i) {
  // 循环逻辑
}
console.log(i); // ReferenceError: i没有定义
```

在使用`var`的时候，最常见的问题就是对迭代变量的奇特声明和修改：

```js
for (var i = 0; i < 5; ++i) {
    setTimeout(() => console.log(i), 0)
}
// 你可能以为会输出0、1、2、3、4
// 实际上会输出5、5、5、5、5
```

而在使用`let`声明迭代变量时，JavaScript引擎在后台会为每个迭代循环声明一个新的迭代变量：

```js
for (let i = 0; i < 5; ++i) {
    setTimeout(() => console.log(i), 0)
}
// 会输出0、1、2、3、4
```

这种每次迭代声明一个独立变量实例的行为适用于所有风格的`for`循环，包括`for-in`和`for-of`循环。

### 3.3.3 `const`声明

`const`的行为与`let`基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量，且尝试修改`const`声明的变量会导致运行时错误。

```js
const age = 26;
age = 36; // TypeError: 给常量赋值

// const也不允许重复声明
const name = 'Matt';
const name = 'Nicholas'; // SyntaxError

// const声明的作用域也是块
const name = 'Matt';
if (true) {
  const name = 'Nicholas';
}
console.log(name); // Matt
```

`const`声明的限制只适用于它指向的变量的引用。换句话说，如果`const`变量引用的是一个对象，那么修改这个对象内部的属性并不违反`const`的限制。

```js
const person = {};
person.name = 'Matt';  // ok
```

即使JavaScript引擎会为`for`循环中的`let`声明分别创建独立的变量实例，而且`const`变量跟`let`变量很相似，也不能用`const`来声明迭代变量（因为迭代变量会自增）：

```js
for (const i = 0; i < 10; ++i) {} // TypeError：给常量赋值
```

不过，如果你只想用`const`声明一个不会被修改的`for`循环变量，那也是可以的。也就是说，每次迭代只是创建一个新变量。这对`for-of`和`for-in`循环特别有意义：

```js
let i = 0;
for (const j = 7; i < 5; ++i) {
  console.log(j);
}
// 7, 7, 7, 7, 7

for (const key in {a: 1, b: 2}) {
  console.log(key);
}
// a, b

for (const value of [1,2,3,4,5]) {
  console.log(value);
}
// 1, 2, 3, 4, 5
```

### 3.3.4 声明风格及最佳实践 (Declaration Styles and Best Practices)

1. **不使用`var`**

   限制自己只使用`let`和`const`有助于提升代码质量，因为变量有了明确的作用域、声明位置，以及不变的值。
   
2. **`const`优先，`let`次之**

   使用`const`声明可以让浏览器运行时强制保持变量不变，也可以让静态代码分析工具提前发现不合法的赋值操作。因此，很多开发者认为应该优先使用`const`来声明变量，只在提前知道未来会有修改时，再使用`let`。这样可以让开发者更有信心地推断某些变量的值永远不会变，同时也能迅速发现因意外赋值导致的非预期行为。

   

## 3.4 数据类型

ECMAScript中有6种简单数据类型（也称为**原始类型**）：

- `Undefined`
- `Null`
- `Boolean`
- `Number`
- `String`
- `Symbol`


还有1种复杂数据类型`Object`，`Object`本质上是由一组无序的名值对组成的。

### 3.4.1 `typeof`操作符

鉴于ECMAScript是松散类型的，因此需要`typeof`来检测给定变量的数据类型。对一个值使用`typeof`操作符可能返回下列某个字符串：

- `"undefined"`：表示值未定义；
- `"boolean"`：表示值为布尔值；
- `"string"`：表示值为字符串；
- `"number"`：表示值为数值；
- `"object"`：表示值为对象（而不是函数）或`null`；
- `"function"`：表示值为函数。
- `"symbol"`: 表示值为符号Symbol。

> **注意**　严格来讲，函数在ECMAScript中被认为是对象，并不代表一种数据类型。可是，函数也有自己特殊的属性。为此，就有必要通过`typeof`操作符来区分函数和其他对象。

像下面这样调用`typeof`操作符：

```js
let message = "some string";
console.log(typeof message);    // "string"
//注意，因为typeof是一个操作符而不是函数，所以不需要参数（但可以使用参数）。
console.log(typeof(message));   // "string"
console.log(typeof 95);         // "number"
console.log(typeof null)        // "object"
```

### 3.4.2 `Undefined`类型

`Undefined`类型只有一个值，就是特殊值`undefined`。当使用`var`或`let`声明了变量但没有初始化时，就相当于给变量赋予了`undefined`值：

```js
let message;
console.log(message == undefined); // true
```

这个例子等同于如下示例：
```js
let message = undefined;
console.log(message == undefined); // true
```

已声明但未初始化的变量与尚未声明的变量是不一样的：

```js
let message;    // 这个变量被声明了，只是值为undefined

// 确保没有声明过这个变量
// let age

console.log(message); // "undefined"
console.log(age);     // 报错
```

对未初始化和未声明的变量执行`typeof`操作符都返回了`undefined`值：

```js
let message; // 这个变量被声明了，只是值为undefined

// 确保没有声明过这个变量
// let age

console.log(typeof message); // "undefined"
console.log(typeof age);     // "undefined"
```

值`undefined`是假的（falsy），因此，如果需要，可以用更简洁的方式检测它：

```js

let message; // 这个变量被声明了，只是值为undefined
// age没有声明

if (message) {
  // 这个块不会执行
}

if (!message) {
  // 这个块会执行
}

if (age) {
  // 这里会报错
}
```

### 3.4.3 Null类型

`Null`类型同样只有一个值，即特殊值`null`。逻辑上讲，`null`值表示一个空对象指针，而这也正是使用`typeof`操作符检测`null`值时会返回`"object"`的原因：

```js
let car = null;
console.log(typeof car); // "object"
```

在定义将来要保存对象值的变量时，建议使用`null`来初始化。这样，只要检查这个变量的值是不是`null`就可以知道这个变量是否在后来被重新赋予了一个对象的引用：

```js
if (car != null) {
  // car是一个对象的引用
}
```

实际上，`undefined`值是派生自`null`值的，因此ECMA-262规定对它们的相等性测试要返回`true`：

```js
console.log(null == undefined);  // true
console.log(null === undefined); // false
```

`null`类型是假的(falsy)， 因此，如果需要，可以用更简洁的方式检测它。不过要记住，也有很多其他可能的值同样是假值。所以一定要明确自己想检测的就是`null`这个字面值，而不仅仅是假值。

```js
let message = null;
let age;

if (message) {
  // 这个块不会执行
}

if (!message) {
  // 这个块会执行
}

if (age) {
  // 这个块不会执行
}

if (!age) {
  // 这个块会执行
}
```

即使`null`和`undefined`有关系，它们的用途也是完全不一样的。如前所述，永远不必显式地将变量值设置为`undefined`。但`null`不是这样的。任何时候，只要变量要保存对象，而当时又没有那个对象可保存，就要用`null`来填充该变量。这样就可以保持`null`是空对象指针的语义，并进一步将其与`undefined`区分开来。

### 3.4.4 Boolean类型

`Boolean`类型是ECMAScript中使用得最多的一种类型，该类型只有2个字面值：`true`和`false`。这两个布尔值不同于数值，因此`true`不等于1，`false`不等于0。以下是为变量赋`Boolean`类型值的例子：

```js
let found = true;
let lost = false;
```

ECMAScript中所有类型的值都有与这两个`Boolean`值等价的值。要将一个值转换为其对应的`Boolean`值，可以调用转型函数`Boolean()`：

```js
let message = "Hello world!";
let messageAsBoolean = Boolean(message);
```

转换规则如下：

| 数据类型    | 转换为true的值               | 转换为false的值  |
| :---------- | :--------------------------- | :--------------- |
| `Boolean`   | `true`                       | `false`          |
| `String`    | 任何非空字符串               | `""`（空字符串） |
| `Number`    | 任何非零数字值（包括无穷值） | 0和`NaN`         |
| `Object`    | 任何对象                     | `null`           |
| `Undefined` | `N/A`（不存在）              | `undefined`      |

理解以上转换非常重要，因为像`if`等流控制语句会自动执行其他类型值到布尔值的转换，例如：

```js
let message = "Hello world!";
if (message) {
  console.log("Value is true");
}
```

### 3.4.5 Number类型

`Number`类型使用IEEE754格式来表示**整数**和**浮点数值**。

为支持各种数值类型，ECMA-262定义了不同的数值字面量格式：

```js
let intNum = 55;  // 十进制整数

let octalNum1 = 070;  // 八进制的56
let octalNum2 = 079;  // 无效的八进制值，当成79处理
let octalNum3 = 08;   // 无效的八进制值，当成8处理

let hexNum1 = 0xA;   // 十六进制10
let hexNum2 = 0x1f;  // 十六进制31

let binNum1 = 0b101; // 二进制5
let binNum2 = 0b11000000; // 二进制192
```

> 八进制字面量在严格模式下是无效的，会导致JavaScript引擎抛出语法错误。ECMAScript 2015或ES6中的八进制值通过前缀`0o`来表示；严格模式下，前缀`0`会被视为语法错误，如果要表示八进制值，应该使用前缀`0o`。

> JavaScript可以保存正零（+0）和负零（-0），正零和负零被认为相等。

#### 浮点数值

浮点数值中必须包含一个小数点，并且小数点后面必须至少有一位数字。

```js
let floatNum1 = 1.1;
let floatNum2 = 0.1;
let floatNum3 = .1;   // 有效，但不推荐
```

由于浮点数值占用的内存空间是整数值的2倍，所以ECMAScript总是想方设法把值转换为整数：

```js
let floatNum1 = 1.;   // 小数点后面没有数字，当成整数1处理
let floatNum2 = 10.0; // 小数点后面是零，当成整数10处理
```

对于那些极大或极小的数值，可以用e表示法（即科学计数法）表示的浮点数值表示。默认情况下，ECMAScript会将小数点后至少包含6个零的浮点值转换为科学记数法（例如，0.000 000 3会被转换为3e-7）。

```js
let floatNum = 3.125e7; // equal to 31250000
let floatNum2 = 3e-17; // 等于0.00000000000000003
```

#### 值的范围

- 能够表示的最小数值保存在`Number.MIN_VALUE`中：在大多数浏览器中，这个值是5e-324；
- 能够表示的最大数值保存在`Number.MAX_VALUE`中：在大多数浏览器中，这个值是1.7976931348623157e+308。
- 超出JavaScript数值范围的值将被自动转换成特殊的`Infinity`（无穷）值。如果这个数值是负数，则会被转换成`-Infinity`（负无穷），如果是正数，则会被转换成`Infinity`（正无穷）。
- 属性`Number.NEGATIVE_INFINITY`和`Number.POSITIVE_INFINITY`分别保存着`-Infinity`和`Infinity`。

使用`isFinite()`函数来确定一个数值是不是有穷的（位于最小和最大的数值之间）：

```js
let result = Number.MAX_VALUE + Number.MAX_VALUE;
console.log(isFinite(result)); // false
```


#### `NaN`非数值

`NaN`，即非数值（Not a Number）是一个特殊的数值，用于表示一个本来要返回数值的操作未返回数值的情况（这样就不会抛出错误了）。

`NaN`本身有2个特点：

- 任何涉及`NaN`的操作（例如`NaN/10`）都会返回`NaN`，这个特点在多步计算中有可能导致问题。
- `NaN`与任何值都不相等，包括`NaN`本身。

但在ECMAScript中，`0`、`+0`或`-0`相除会返回`NaN`：

```js
console.log(0/0);    // NaN
console.log(-0/+0);  // NaN复制代码
```

如果分子是非0值，分母是有符号0或无符号0，则会返回`Infinity`或`-Infinity`：

```js
console.log(5/0);   // Infinity
console.log(5/-0);  // -Infinity
```

`isNaN()`函数会尝试将参数转换为数值，如果不能被转换为数值，则返回`true`，以此来确定其参数是否“不是数值”：

```js
console.log(isNaN(NaN));     // true
console.log(isNaN(10));      // false，10是数值
console.log(isNaN("10"));    // false，可以转换为数值10
console.log(isNaN("blue"));  // true，不可以转换为数值
console.log(isNaN(true));    // false，可以转换为数值1
```

> **注意**　虽然不常见，但`isNaN()`可以用于测试对象。

#### 数制转换

有3个函数可以把非数值转换为数值：

- `Number()`：用于任何数据类型
- `parseInt()`：用于把字符串转换成整形数值。`parseInt()`函数更专注于字符串是否包含数值模式。
- `parseFloat()`：用于把字符串转换成浮点型数值。

**`Number()`函数的转换规则如下：**

- 如果是`Boolean`值，`true`转换为1，`false`转换为0。
- 如果是数字值，直接返回。
- 如果是`null`值，返回0。
- 如果是`undefined`，返回`NaN`。
- 如果是字符串，遵循下列规则：
    - 如果字符串中只包含数字（包括前面带加号或负号的情况），则将其转换为十进制数值，即`"1"`会变成1，`"123"`会变成123，而`"011"`会变成11（注意：前导的零被忽略了）。
    - 如果字符串包含有效的浮点格式，如`"1.1"`，则将其转换为对应的浮点数值（同样，也会忽略前导零）。
    - 如果字符串包含有效的十六进制格式如`"0xf"`，则会转换为与该十六进制值对应的十进制整数值。
    - 如果是空字符串（不包含字符），则返回0。
    - 如果字符串包含除上述情况之外的其他字符，则返回`NaN`。
- 如果是对象，则调用对象的`valueOf()`方法，然后依照前面的规则转换返回的值。如果转换的结果是`NaN`，则调用对象的`toString()`方法，然后再次依照前面的规则转换返回的字符串值。

```js
let num1 = Number("Hello world!");  // NaN
let num2 = Number("");              // 0
let num3 = Number("000011");        // 11
let num4 = Number(true);            // 1
```

> 注意，后面会讨论到的一元加操作符与`Number()`函数遵循相同的转换规则。

**`parseInt()`函数转换规则：**

- `parseInt()`函数会忽略字符串前面的空格，从第一个非空格字符开始转换。
- 如果第一个字符不是数值字符、加号或减号，`parseInt()`立即返回`NaN`。也就是说，用`parseInt("")`会返回`NaN`，而`Number("")`返回`0`。
- 如果第一个字符是数值字符、加号或减号，则继续依次检测每个字符，直到字符串末尾，或碰到非数值字符。

```js
let num1 = parseInt("1234blue");  // 1234
let num2 = parseInt("");          // NaN
let num3 = parseInt("0xA");       // 10，解释为十六进制整数
let num4 = parseInt(22.5);        // 22
let num5 = parseInt("70");        // 70，解释为十进制值
let num6 = parseInt("0xf");       // 15，解释为十六进制整数
```

可以为`parseInt()`函数提供第2个参数：转换时使用的基数（即进制）：

```js
let num = parseInt("0xAF", 16); // 175
let num1 = parseInt("AF", 16); // 175
let num2 = parseInt("AF"); // NaN

let num1 = parseInt("10", 2);   // 2，按二进制解析
let num2 = parseInt("10", 8);   // 8，按八进制解析
let num3 = parseInt("10", 10);  // 10，按十进制解析
let num4 = parseInt("10", 16);  // 16，按十六进制解析
```

**`parseFloat()`函数转换规则与`parseInt()`函数类似：**

- `parseFloat()`函数从第一个字符（位置0）开始解析每个字符，一直解析到字符串末尾，或者解析到一个无效的浮点数值字符为止。也就是说，字符串中的第一个小数点是有效的，而第二个小数点就是无效的了，因此它后面的字符串将被忽略。举例来说，`"22.34.5"`将会被转换为22.34。
- `parseFloat()`始终忽略字符串开头的零。
- 如果字符串包含的是一个可解析为整数的数（没有小数点，或者小数点后都是零），`parseFloat()`会返回整数。

```js
let num1 = parseFloat("1234blue"); // 1234 （整数）
let num2 = parseFloat("0xA"); // 十六进制数值始终会返回0
let num3 = parseFloat("22.5"); // 22.5
let num4 = parseFloat("22.34.5"); // 22.34
let num5 = parseFloat("0908.5"); // 908.5
let num6 = parseFloat("3.125e7"); // 31250000
```

### 3.4.6 String类型

`String`类型用于表示由零或多个16位Unicode字符组成的字符序列，即字符串。字符串可以由双引号（"），单引号（'）表示或反单引号(\`)表示，这三种方式是等价的：

```js
let firstName = "John";
let lastName = 'Jacob';
let lastName = `Jingleheimerschmidt`
```

#### 字符字面量

| 字　面　量 | 含　　义                                                     |
| :--------- | :----------------------------------------------------------- |
| `\n`       | 换行                                                         |
| `\t`       | 制表                                                         |
| `\b`       | 退格                                                         |
| `\r`       | 回车                                                         |
| `\f`       | 进纸                                                         |
| `\\`       | 斜杠                                                         |
| `\'`       | 单引号（`'`），在用单引号表示的字符串中使用。例如：`'He said, \'hey.\''` |
| `\"`       | 双引号（`"`），在用双引号表示的字符串中使用。例如：`"He said, \"hey.\""` |
| `\xnn`     | 以十六进制代码`nn`表示的一个字符（其中`n`为0～F）。例如，`\x41`表示`"A"` |
| `\unnnn`   | 以十六进制编码`nnnn`表示的Unicode字符（其中`n`是十六进制数字0~F），例如`\u03a3`等于希腊字符`"Σ"` |

![](img/Chapter3-Language_Basics.assets/image-20200422225848537.png)

变量`text`有28个字符，其中6个字符长的转义序列表示1个字符：

```js
let text = "This is the letter sigma: \u03a3.";
console.log(text.length); // 28
```

#### 字符串的特点

ECMAScript中的字符串是不可变的（immutable）。要修改某个变量中的字符串值，必须先销毁原始的字符串，然后将包含新值的另一个字符串保存到该变量，例如：

```js
let lang = "Java";
lang = lang + "Script";
```

#### 转换为字符串

要把一个值转换为一个字符串有2种方式：

- 使用`toString()`方法。除了`null`和`undefined`值，数值、布尔值、对象和字符串值都有`toString()`方法。
- 转型函数`String()`能够将任何类型的值转换为字符串。

`toString()`方法唯一要做的就是返回相应值的字符串表现：

```js
let age = 11;
let ageAsString = age.toString();      // 字符串"11"
let found = true;
let foundAsString = found.toString();  // 字符串"true"
```

调用数值的`toString()`方法时，可以传递一个参数：输出数值的进制。默认为10进制。
```js
let num = 10;
console.log(num.toString()); // "10"
console.log(num.toString(2)); // "1010"
console.log(num.toString(8)); // "12"
console.log(num.toString(10)); // "10"
console.log(num.toString(16)); // "a"
```

在不知道要转换的值是不是`null`或`undefined`的情况下，可以使用转型函数`String()`将任何类型的值转换为字符串。`String()`函数遵循下列转换规则：

- 如果值有`toString()`方法，则调用该方法（没有参数）并返回相应的结果；
- 如果值是`null`，则返回`"null"`；
- 如果值是`undefined`，则返回`"undefined"`。

```js
var value1 = 10;
var value2 = true;
var value3 = null;
var value4;

alert(String(value1));     // "10"
alert(String(value2));     // "true"
alert(String(value3));     // "null"
alert(String(value4));     // "undefined"
```

#### 模板字面量（Template Literals）

ECMAScript 6支持使用模板字面量定义字符串。模板字面量保留换行符，可以定义多行内容。

```js
let myMultiLineString = 'first line\nsecond line';
let myMultiLineTemplateLiteral = `first line
second line`;

console.log(myMultiLineString);
// first line
// second line"

console.log(myMultiLineTemplateLiteral);
// first line
// second line

console.log(myMultiLineString === myMultiLinetemplateLiteral); // true
```

在定义模板（如HTML）时，模板字面量特别有用：

```js
let pageHTML = `
<div>
    <a href="#">
        <span>Jake</span>
    </a>
</div>`;
```

由于模板字面量将与反引号内的空格完全匹配，因此在定义它们时需要格外小心。格式正确的模板字符串可能会看起来缩进不当：

```js
// 这个模板字面量在换行符之后有25个空格符
let myTemplateLiteral = `first line
                         second line`;
console.log(myTemplateLiteral.length);  // 47

// 这个模板字面量以一个换行符开头
let secondTemplateLiteral = `
first line
second line`;
console.log(secondTemplateLiteral[0] === '\n'); // true

// 这个模板字面量没有意料之外的字符
let thirdTemplateLiteral = `first line
second line`;
console.log(thirdTemplateLiteral);
// first line
// second line
```



#### 字符串插值 (Interpolation)

模板字面量最常用的一个特性是支持字符串插值，也就是可以在一个连续定义中插入一个或多个值。技术上讲，模板字面量不是字符串，而是一种特殊的JavaScript句法表达式，只不过求值后得到的是字符串。模板字面量在定义时立即求值并转换为字符串实例，任何插入的变量也会从它们最接近的作用域中取值。

字符串插值通过在`${}`中使用一个JavaScript表达式实现：

```js
let value = 5;
let exponent = 'second';

// 以前，字符串插值是这样实现的：
let interpolatedString = value + ' to the ' + exponent + ' power is ' + (value * value);

// 现在，可以用模板字面量这样实现：
let interpolatedTemplateLiteral = `${ value } to the ${ exponent } power is ${ value * value }`;

console.log(interpolatedString);           // 5 to the second power is 25
console.log(interpolatedTemplateLiteral);  // 5 to the second power is 25
```

所有插入的值都会使用`toString()`强制转型为字符串，而且任何JavaScript表达式都可以用于插值。嵌套的模板字符串无须转义：

```js
console.log(`Hello, ${ `World` }!`);  // Hello, World!
```

将表达式转换为字符串时会调用`toString()`：

```js
let foo = { toString: () => 'World' };
console.log(`Hello, ${ foo }!`);      // Hello, World!
```

在插值表达式中可以调用函数和方法：

```js
function capitalize(word) {
  return `${ word[0].toUpperCase() }${ word.slice(1) }`;
}
console.log(`${ capitalize('hello') }, ${ capitalize('world') }!`); // Hello, World!
```

此外，模板也可以插入自己之前的值：

```js
let value = '';
function append() {
  value = `${value}abc`
  console.log(value);
}
append();  // abc
append();  // abcabc
append();  // abcabcabc
```



#### 模板字面量标签函数 (Template Literal Tag Functions)

模板字面量也支持定义**标签函数**（tag function），而通过标签函数可以自定义插值行为。标签函数会接收被插值记号分隔后的模板和对每个表达式求值的结果。

标签函数本身是一个常规函数，通过前缀到模板字面量来应用自定义行为，如下例所示。标签函数接收到的参数依次是原始字符串数组和对每个表达式求值的结果。这个函数的返回值是对模板字面量求值得到的字符串。

最好通过一个例子来理解：

```js
let a = 6;
let b = 9;

function simpleTag(strings, aValExpression, bValExpression, sumExpression) {
  console.log(strings);
  console.log(aValExpression);
  console.log(bValExpression);
  console.log(sumExpression);

  return 'foobar';
}

let untaggedResult = `${ a } + ${ b } = ${ a + b }`;
let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
// ["", " + ", " = ", ""]
// 6
// 9
// 15

console.log(untaggedResult);   // "6 + 9 = 15"
console.log(taggedResult);     // "foobar"
```

因为表达式参数的数量是可变的，所以通常应该使用剩余操作符（rest operator）将它们收集到一个数组中：

```js
let a = 6;
let b = 9;

function simpleTag(strings, ...expressions) {
  console.log(strings);
  for(const expression of expressions) {
    console.log(expression);
  }

  return 'foobar';
}

let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
// ["", " + ", " = ", ""]
// 6
// 9
// 15

console.log(taggedResult);  // "foobar"
```

对于有`n`个插值的模板字面量，传给标签函数的表达式参数的个数始终是`n`，而传给标签函数的第一个参数所包含的字符串个数则始终是`n+1`。因此，如果你想把这些字符串和对表达式求值的结果拼接起来作为默认返回的字符串，可以这样做：

```js
let a = 6;
let b = 9;

function zipTag(strings, ...expressions) {
  return strings[0] +
         expressions.map((e, i) => `${e}${strings[i + 1]}`)
                    .join('');
}

let untaggedResult =    `${ a } + ${ b } = ${ a + b }`;
let taggedResult = zipTag`${ a } + ${ b } = ${ a + b }`;

console.log(untaggedResult);  // "6 + 9 = 15"
console.log(taggedResult);    // "6 + 9 = 15"
```



#### 原始字符串 (Raw Strings)

使用模板字面量也可以直接获取原始的模板字面量内容（如换行符或Unicode字符），而不是被转换后的字符表示。为此，可以使用默认的`String.raw`标签函数：

```js
// Unicode示例
// \u00A9是版权符号
console.log(`\u00A9`);            // ©
console.log(String.raw`\u00A9`);  // \u00A9

// 换行符示例
console.log(`first line\nsecond line`);
// first line
// second line

console.log(String.raw`first line\nsecond line`); // "first line\nsecond line"

// 对实际的换行符来说是不行的
// 它们不会被转换成转义序列的形式
console.log(`first line
second line`);
// first line
// second line

console.log(String.raw`first line
second line`);
// first line
// second line
```

另外，也可以通过标签函数的第一个参数，即字符串数组的`.raw`属性取得每个字符串的原始内容：

```js
function printRaw(strings) {
  console.log('Actual characters:');
  for (const string of strings) {
    console.log(string);
  }

  console.log('Escaped characters;');
  for (const rawString of strings.raw) {
    console.log(rawString);
  }
}

printRaw`\u00A9${ 'and' }\n`;
// Actual characters:
// ©
//（换行符）
// Escaped characters:
// \u00A9
// \n
```



### 3.4.7 Symbol类型

`Symbol`（符号）是ECMAScript 6新增的数据类型。符号是原始类型值，且符号实例是唯一的、不可变的。符号的***用途***是**确保对象属性使用唯一标识符，不会发生属性冲突。**

尽管听起来跟私有属性有点类似，但符号并不是为了提供私有属性的行为才增加的（尤其是因为Object API提供了方法，可以更方便地发现符号属性）。相反，符号就是用来创建唯一记号，进而用作非字符串形式的对象属性。



#### 符号的基本用法

符号需要使用`Symbol()`函数初始化。因为符号本身是原始类型，所以`typeof`操作符对符号返回`symbol`。

```js
let sym = Symbol();
console.log(typeof sym); // symbol
```

调用`Symbol()`函数时，也可以传入一个字符串参数作为对符号的描述（description），将来可以通过这个字符串来调试代码。但是，这个字符串参数与符号定义或标识完全无关：

```js
let genericSymbol = Symbol();
let otherGenericSymbol = Symbol();

let fooSymbol = Symbol('foo');
let otherFooSymbol = Symbol('foo');

console.log(genericSymbol == otherGenericSymbol);  // false
console.log(fooSymbol == otherFooSymbol);          // false
```

符号没有字面量语法，这也是它们发挥作用的关键。按照规范，你只要创建`Symbol()`实例并将其用作对象的新属性，就可以保证它不会覆盖已有的对象属性，无论是符号属性还是字符串属性。

```js
let genericSymbol = Symbol();
console.log(genericSymbol);  // Symbol()

let fooSymbol = Symbol('foo');
console.log(fooSymbol);      // Symbol(foo);
```

**最重要的是，`Symbol()`函数不能用作构造函数，与`new`关键字一起使用。** 这样做是为了避免创建符号包装对象，像使用`Boolean`、`String`或`Number`那样，它们都支持构造函数且可用于初始化包含原始值的包装对象：

```js
let myBoolean = new Boolean();
console.log(typeof myBoolean); // "object"

let myString = new String();
console.log(typeof myString);  // "object"

let myNumber = new Number();
console.log(typeof myNumber);  // "object"

let mySymbol = new Symbol(); // TypeError: Symbol is not a constructor
```

如果你确实想使用符号包装对象，可以借用`Object()`函数：

```js
let mySymbol = Symbol();
let myWrappedSymbol = Object(mySymbol);
console.log(typeof myWrappedSymbol);   // "object"
```



#### 使用全局符号注册表

如果运行时的不同部分需要共享和重用符号实例，那么可以用一个字符串作为键，在全局符号注册表中创建并重用符号。

为此，需要使用`Symbol.for()`方法：

```js
let fooGlobalSymbol = Symbol.for('foo');
console.log(typeof fooGlobalSymbol); // symbol
```

`Symbol.for()`对每个字符串键都执行幂等操作。第一次使用某个字符串调用时，它会检查全局运行时注册表，发现不存在对应的符号，于是就会生成一个新符号实例并添加到注册表中。后续使用相同字符串的调用同样会检查注册表，发现存在与该字符串对应的符号，然后就会返回该符号实例。

```js
let fooGlobalSymbol = Symbol.for('foo');       // 创建新符号
let otherFooGlobalSymbol = Symbol.for('foo');  // 重用已有符号

console.log(fooGlobalSymbol === otherFooGlobalSymbol);  // true
```

即使采用相同的符号描述，在全局注册表中定义的符号跟使用`Symbol()`定义的符号也并不等同：

```js
let localSymbol = Symbol('foo');
let globalSymbol = Symbol.for('foo');

console.log(localSymbol === globalSymbol); // false
```

全局注册表中的符号必须使用字符串键来创建，因此作为参数传给`Symbol.for()`的任何值都会被转换为字符串。此外，注册表中使用的键同时也会被用作符号描述。

```js
let emptyGlobalSymbol = Symbol.for();
console.log(emptyGlobalSymbol);    // Symbol(undefined)
```

还可以使用`Symbol.keyFor()`来查询全局注册表，这个方法接收符号，返回该全局符号对应的字符串键。如果查询的不是全局符号，则返回`undefined`。

```js
// 创建全局符号
let s = Symbol.for('foo');
console.log(Symbol.keyFor(s));   // foo

// 创建普通符号
let s2 = Symbol('bar');
console.log(Symbol.keyFor(s2));  // undefined
```

如果传给`Symbol.keyFor()`的不是符号，则该方法抛出`TypeError`：

```js
Symbol.keyFor(123); // TypeError: 123 is not a symbol
```



#### 使用符号作为属性

凡是可以使用字符串或数值作为属性的地方，都可以使用符号。这就包括了对象字面量属性和`Object.defineProperty()`/`Object.defineProperties()`定义的属性。对象字面量只能在计算属性语法中使用符号作为属性。

```js
let s1 = Symbol('foo'),
    s2 = Symbol('bar'),
    s3 = Symbol('baz'),
    s4 = Symbol('qux');

let o = {
  [s1]: 'foo val'
};
// 这样也可以：o[s1] = 'foo val';

console.log(o);
// {Symbol(foo): foo val}

Object.defineProperty(o, s2, {value: 'bar val'});

console.log(o);
// {Symbol(foo): foo val, Symbol(bar): bar val}

Object.defineProperties(o, {
  [s3]: {value: 'baz val'},
  [s4]: {value: 'qux val'}
});

console.log(o);
// {Symbol(foo): foo val, Symbol(bar): bar val,
//  Symbol(baz): baz val, Symbol(qux): qux val}
```

类似于`Object.getOwnPropertyNames()`返回对象实例的常规属性数组，`Object.getOwnPropertySymbols()`返回对象实例的符号属性数组。这两个方法的返回值彼此互斥。`Object.getOwnPropertyDescriptors()`会返回同时包含常规和符号属性描述符的对象。`Reflect.ownKeys()`会返回两种类型的键：

```js
let s1 = Symbol('foo'),
    s2 = Symbol('bar');

let o = {
  [s1]: 'foo val',
  [s2]: 'bar val',
  baz: 'baz val',
  qux: 'qux val'
};

console.log(Object.getOwnPropertySymbols(o));
// [Symbol(foo), Symbol(bar)]

console.log(Object.getOwnPropertyNames(o));
// ["baz", "qux"]

console.log(Object.getOwnPropertyDescriptors(o));
// {baz: {...}, qux: {...}, Symbol(foo): {...}, Symbol(bar): {...}}

console.log(Reflect.ownKeys(o));
// ["baz", "qux", Symbol(foo), Symbol(bar)]
```

因为符号属性是对内存中符号的一个引用，所以直接创建并用作属性的符号不会丢失。但是，如果没有显式地保存对这些属性的引用，那么必须遍历对象的所有符号属性才能找到相应的属性键：

```js
let o = {
  [Symbol('foo')]: 'foo val',
  [Symbol('bar')]: 'bar val'
};

console.log(o);
// {Symbol(foo): "foo val", Symbol(bar): "bar val"}

let barSymbol = Object.getOwnPropertySymbols(o)
              .find((symbol) => symbol.toString().match(/bar/));

console.log(barSymbol);
// Symbol(bar)
```



#### 常用内置符号

ECMAScript 6也引入了一批**常用内置符号**（well-known symbol），用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为。这些内置符号都以`Symbol`工厂函数字符串属性的形式存在。

这些内置符号最重要的用途之一是重新定义它们，从而改变原生结构的行为。比如，我们知道`for-of`循环会在相关对象上使用`Symbol.iterator`属性，那么就可以通过在自定义对象上重新定义`Symbol.iterator`的值，来改变`for-of`在迭代该对象时的行为。

这些内置符号也没有什么特别之处，它们就是全局函数`Symbol`的普通字符串属性，指向一个符号的实例。所有内置符号属性都是**不可写、不可枚举、不可配置的。**

> **注意**　在提到ECMAScript规范时，经常会引用符号在规范中的名称，前缀为`@@`。比如，`@@iterator`指的就是`Symbol.iterator`。



#### `Symbol.asyncIterator`

根据ECMAScript规范，这个符号作为一个属性表示“一个方法，该方法返回对象默认的`AsyncIterator`。由`for-await-of`语句使用”。换句话说，这个符号表示实现异步迭代器API的函数。

`for-await-of`循环会利用这个函数执行异步迭代操作。循环时，它们会调用以`Symbol.asyncIterator`为键的函数，并期望这个函数会返回一个实现迭代器API的对象。很多时候，返回的对象是实现该API的`AsyncGenerator`：

```js
class Foo {
  async *[Symbol.asyncIterator]() {}
}

let f = new Foo();

console.log(f[Symbol.asyncIterator]());
// AsyncGenerator {<suspended>}
```

技术上，这个由`Symbol.asyncIterator`函数生成的对象应该通过其`next()`方法陆续返回`Promise`实例。可以通过显式地调用`next()`方法返回，也可以隐式地通过异步生成器函数返回：

```js
class Emitter {
  constructor(max) {
    this.max = max;
    this.asyncIdx = 0;
  }

  async *[Symbol.asyncIterator]() {
    while(this.asyncIdx < this.max) {
      yield new Promise((resolve) => resolve(this.asyncIdx++));
    }
  }
}

async function asyncCount() {
  let emitter = new Emitter(5);

  for await(const x of emitter) {
    console.log(x);
  }
}

asyncCount();
// 0
// 1
// 2
// 3
// 4
```



#### `Symbol.hasInstance`

根据ECMAScript规范，这个符号作为一个属性表示“一个方法，该方法决定一个构造器对象是否认可一个对象是它的实例。由`instanceof`操作符使用”。`instanceof`操作符可以用来确定一个对象实例的原型链上是否有原型。`instanceof`的典型使用场景如下：

```js
function Foo() {}
let f = new Foo();
console.log(f instanceof Foo); // true

class Bar {}
let b = new Bar();
console.log(b instanceof Bar); // true
```

在ES6中，`instanceof`操作符会使用`Symbol.hasInstance`函数来确定关系。以`Symbol.hasInstance`为键的函数会执行同样的操作，只是操作数对调了一下：

```js
function Foo() {}
let f = new Foo();
console.log(Foo[Symbol.hasInstance](f)); // true

class Bar {}
let b = new Bar();
console.log(Bar[Symbol.hasInstance](b)); // true
```

这个属性定义在`Function`的原型上，因此默认在所有函数和类上都可以调用。由于`instanceof`操作符会在原型链上寻找这个属性定义，就跟在原型链上寻找其他属性一样，因此可以在继承的类上通过静态方法重新定义这个函数：

```js
class Bar {}
class Baz extends Bar {
  static [Symbol.hasInstance]() {
    return false;
  }
}

let b = new Baz();
console.log(Bar[Symbol.hasInstance](b)); // true
console.log(b instanceof Bar);           // true
console.log(Baz[Symbol.hasInstance](b)); // false
console.log(b instanceof Baz);           // false
```



#### `Symbol.isConcatSpreadable`

根据ECMAScript规范，这个符号作为一个属性表示“一个布尔值，如果是`true`，则意味着对象应该用`Array.prototype.concat()`打平其数组元素”。ES6中的`Array.prototype.concat()`方法会根据接收到的对象类型选择如何将一个类数组对象拼接成数组实例。覆盖`Symbol.isConcatSpreadable`的值可以修改这个行为。

数组对象默认情况下会被打平到已有的数组，`false`或假值会导致整个对象被追加到数组末尾。类数组对象默认情况下会被追加到数组末尾，`true`或真值会导致这个类数组对象被打平到数组实例。其他不是类数组对象的对象在`Symbol.isConcatSpreadable`被设置为`true`的情况下将被忽略。

```js
let initial = ['foo'];

let array = ['bar'];
console.log(array[Symbol.isConcatSpreadable]);  // undefined
console.log(initial.concat(array));             // ['foo', 'bar']
array[Symbol.isConcatSpreadable] = false;
console.log(initial.concat(array));             // ['foo', Array(1)]

let arrayLikeObject = { length: 1, 0: 'baz' };
console.log(arrayLikeObject[Symbol.isConcatSpreadable]);  // undefined
console.log(initial.concat(arrayLikeObject));             // ['foo', {...}]
arrayLikeObject[Symbol.isConcatSpreadable] = true;
console.log(initial.concat(arrayLikeObject));             // ['foo', 'baz']

let otherObject = new Set().add('qux');
console.log(otherObject[Symbol.isConcatSpreadable]);  // undefined
console.log(initial.concat(otherObject));             // ['foo', Set(1)]
otherObject[Symbol.isConcatSpreadable] = true;
console.log(initial.concat(otherObject));             // ['foo']
```

 

#### `Symbol.iterator`

根据ECMAScript规范，这个符号作为一个属性表示“一个方法，该方法返回对象默认的迭代器。由`for-of`语句使用”。换句话说，这个符号表示实现迭代器API的函数。

`for-of`循环这样的语言结构会利用这个函数执行迭代操作。循环时，它们会调用以`Symbol.iterator`为键的函数，并默认这个函数会返回一个实现迭代器API的对象。很多时候，返回的对象是实现该API的`Generator`：

```js
class Foo {
  *[Symbol.iterator]() {}
}

let f = new Foo();

console.log(f[Symbol.iterator]());
// Generator {<suspended>}
```

技术上，这个由`Symbol.iterator`函数生成的对象应该通过其`next()`方法陆续返回值。可以通过显式地调用`next()`方法返回，也可以隐式地通过生成器函数返回：

```js
class Emitter {
  constructor(max) {
    this.max = max;
    this.idx = 0;
  }

  *[Symbol.iterator]() {
    while(this.idx < this.max) {
      yield this.idx++;
    }
  }
}

function count() {
  let emitter = new Emitter(5);

  for (const x of emitter) {
    console.log(x);
  }
}

count();
// 0
// 1
// 2
// 3
// 4
```



#### `Symbol.match`

根据ECMAScript规范，这个符号作为一个属性表示“一个正则表达式方法，该方法用正则表达式去匹配字符串。由`String.prototype.match()`方法使用”。`String.prototype.match()`方法会使用以`Symbol.match`为键的函数来对正则表达式求值。正则表达式的原型上默认有这个函数的定义，因此所有正则表达式实例默认是这个`String`方法的有效参数：

```js
console.log(RegExp.prototype[Symbol.match]);
// f [Symbol.match]() { [native code] }

console.log('foobar'.match(/bar/));
// ["bar", index: 3, input: "foobar", groups: undefined]
```

给这个方法传入非正则表达式值会导致该值被转换为`RegExp`对象。如果想改变这种行为，让方法直接使用参数，则可以重新定义`Symbol.match`函数以取代默认对正则表达式求值的行为，从而让`match()`方法使用非正则表达式实例。`Symbol.match`函数接收一个参数，就是调用`match()`方法的字符串实例。返回的值没有限制：

```js
class FooMatcher {
  static [Symbol.match](target) {
    return target.includes('foo');
  }
}

console.log('foobar'.match(FooMatcher)); // true
console.log('barbaz'.match(FooMatcher)); // false

class StringMatcher {
  constructor(str) {
    this.str = str;
  }

  [Symbol.match](target) {
    return target.includes(this.str);
  }
}

console.log('foobar'.match(new StringMatcher('foo'))); // true
console.log('barbaz'.match(new StringMatcher('qux'))); // false
```

 

#### `Symbol.replace`

根据ECMAScript规范，这个符号作为一个属性表示“一个正则表达式方法，该方法替换一个字符串中匹配的子串。由`String.prototype.replace()`方法使用”。`String.prototype.replace()`方法会使用以`Symbol.replace`为键的函数来对正则表达式求值。正则表达式的原型上默认有这个函数的定义，因此所有正则表达式实例默认是这个`String`方法的有效参数：

```js
console.log(RegExp.prototype[Symbol.replace]);
// f [Symbol.replace]() { [native code] }

console.log('foobarbaz'.replace(/bar/, 'qux'));
// 'fooquxbaz'
```

给这个方法传入非正则表达式值会导致该值被转换为`RegExp`对象。如果想改变这种行为，让方法直接使用参数，可以重新定义`Symbol.replace`函数以取代默认对正则表达式求值的行为，从而让`replace()`方法使用非正则表达式实例。`Symbol.replace`函数接收两个参数，即调用`replace()`方法的字符串实例和替换字符串。返回的值没有限制：

```js
class FooReplacer {
  static [Symbol.replace](target, replacement) {
    return target.split('foo').join(replacement);
  }
}

console.log('barfoobaz'.replace(FooReplacer, 'qux'));
// "barquxbaz"

class StringReplacer {
  constructor(str) {
    this.str = str;
  }

  [Symbol.replace](target, replacement) {
    return target.split(this.str).join(replacement);
  }
}

console.log('barfoobaz'.replace(new StringReplacer('foo'), 'qux'));
// "barquxbaz"
```

 

#### `Symbol.search`

根据ECMAScript规范，这个符号作为一个属性表示“一个正则表达式方法，该方法返回字符串中匹配正则表达式的索引。由`String.prototype.search()`方法使用”。`String.prototype.search()`方法会使用以`Symbol.search`为键的函数来对正则表达式求值。正则表达式的原型上默认有这个函数的定义，因此所有正则表达式实例默认是这个`String`方法的有效参数：

```js
console.log(RegExp.prototype[Symbol.search]);
// f [Symbol.search]() { [native code] }

console.log('foobar'.search(/bar/));
// 3
```

给这个方法传入非正则表达式值会导致该值被转换为`RegExp`对象。如果想改变这种行为，让方法直接使用参数，可以重新定义`Symbol.search`函数以取代默认对正则表达式求值的行为，从而让`search()`方法使用非正则表达式实例。`Symbol.search`函数接收一个参数，就是调用`match()`方法的字符串实例。返回的值没有限制：

```js
class FooSearcher {
  static [Symbol.search](target) {
    return target.indexOf('foo');
  }
}

console.log('foobar'.search(FooSearcher)); // 0
console.log('barfoo'.search(FooSearcher)); // 3
console.log('barbaz'.search(FooSearcher)); // -1

class StringSearcher {
  constructor(str) {
    this.str = str;
  }

  [Symbol.search](target) {
    return target.indexOf(this.str);
  }
}

console.log('foobar'.search(new StringSearcher('foo'))); // 0
console.log('barfoo'.search(new StringSearcher('foo'))); // 3
console.log('barbaz'.search(new StringSearcher('qux'))); // -1
```

 

#### `Symbol.species`

根据ECMAScript规范，这个符号作为一个属性表示“一个函数值，该函数作为创建派生对象的构造函数”。这个属性在内置类型中最常用，用于对内置类型实例方法的返回值暴露实例化派生对象的方法。用`Symbol.species`定义静态的获取器（getter）方法，可以覆盖新创建实例的原型定义：

```js
class Bar extends Array {}
class Baz extends Array {
  static get [Symbol.species]() {
    return Array;
  }
}

let bar = new Bar();
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar);   // true
bar = bar.concat('bar');
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar);   // true

let baz = new Baz();
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz);   // true
baz = baz.concat('baz');
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz);   // false
```

 

#### `Symbol.split`

根据ECMAScript规范，这个符号作为一个属性表示“一个正则表达式方法，该方法在匹配正则表达式的索引位置拆分字符串。由`String.prototype.split()`方法使用”。`String.prototype.split()`方法会使用以`Symbol.split`为键的函数来对正则表达式求值。正则表达式的原型上默认有这个函数的定义，因此所有正则表达式实例默认是这个`String`方法的有效参数：

```js
console.log(RegExp.prototype[Symbol.split]);
// f [Symbol.split]() { [native code] }

console.log('foobarbaz'.split(/bar/));
// ['foo', 'baz']
```

给这个方法传入非正则表达式值会导致该值被转换为`RegExp`对象。如果想改变这种行为，让方法直接使用参数，可以重新定义`Symbol.split`函数以取代默认对正则表达式求值的行为，从而让`split()`方法使用非正则表达式实例。`Symbol.split`函数接收一个参数，就是调用`match()`方法的字符串实例。返回的值没有限制：

```js
class FooSplitter {
  static [Symbol.split](target) {
    return target.split('foo');
  }
}

console.log('barfoobaz'.split(FooSplitter));
// ["bar", "baz"]

class StringSplitter {
  constructor(str) {
    this.str = str;
  }

  [Symbol.split](target) {
    return target.split(this.str);
  }
}

console.log('barfoobaz'.split(new StringSplitter('foo')));
// ["bar", "baz"]
```

 

#### `Symbol.toPrimitive`

根据ECMAScript规范，这个符号作为一个属性表示“一个方法，该方法将对象转换为相应的原始值。由`ToPrimitive`抽象操作使用”。很多内置操作都会尝试强制将对象转换为原始值，包括字符串、数值和未指定的原始类型。对于一个自定义对象实例，通过在这个实例的`Symbol.toPrimitive`属性上定义一个函数可以改变默认行为。

根据提供给这个函数的参数（`string`、`number`或`default`），可以控制返回的原始值：

```js
class Foo {}
let foo = new Foo();

console.log(3 + foo);       // "3[object Object]"
console.log(3 - foo);       // NaN
console.log(String(foo));   // "[object Object]"

class Bar {
  constructor() {
    this[Symbol.toPrimitive] = function(hint) {
      switch (hint) {
        case 'number':
          return 3;
        case 'string':
          return 'string bar';
        case 'default':
        default:
          return 'default bar';
      }
    }
  }
}
let bar = new Bar();

console.log(3 + bar);     // "3default bar"
console.log(3 - bar);     // 0
console.log(String(bar)); // "string bar"
```

 

#### `Symbol.toStringTag`

根据ECMAScript规范，这个符号作为一个属性表示“一个字符串，该字符串用于创建对象的默认字符串描述。由内置方法`Object.prototype.toString()`使用”。

通过`toString()`方法获取对象标识时，会检索由`Symbol.toStringTag`指定的实例标识符，默认为`"Object"`。内置类型已经指定了这个值，但自定义类实例还需要明确定义：

```js
let s = new Set();

console.log(s);                      // Set(0) {}
console.log(s.toString());           // [object Set]
console.log(s[Symbol.toStringTag]);  // Set

class Foo {}
let foo = new Foo();

console.log(foo);                      // Foo {}
console.log(foo.toString());           // [object Object]
console.log(foo[Symbol.toStringTag]);  // undefined

class Bar {
  constructor() {
    this[Symbol.toStringTag] = 'Bar';
  }
}
let bar = new Bar();

console.log(bar);                      // Bar {}
console.log(bar.toString());           // [object Bar]
console.log(bar[Symbol.toStringTag]);  // Bar
```

 

#### `Symbol.unscopables`

根据ECMAScript规范，这个符号作为一个属性表示“一个对象，该对象所有的以及继承的属性，都会从关联对象的`with`环境绑定中排除”。设置这个符号并让其映射对应属性的键值为`true`，就可以阻止该属性出现在`with`环境绑定中，如下例所示：

```js
let o = { foo: 'bar' };

with (o) {
  console.log(foo); // bar
}

o[Symbol.unscopables] = {
  foo: true
};

with (o) {
  console.log(foo); // ReferenceError
}
```

> **注意**　不推荐使用`with`，因此也不推荐使用`Symbol.unscopables`。



### 3.4.8 Object类型

ECMAScript中的对象其实就是一组数据和功能的集合。对象可以通过执行`new`操作符后跟要创建的对象类型的名称来创建。而创建`Object`类型的实例并为其添加属性和（或）方法，就可以创建自定义对象，如下所示：

```js
let o = new Object();
```

在ECMAScript中，如果不给构造函数传递参数，则可以省略后面的那一对圆括号。

```js
let o = new Object; // 有效，但不推荐省略圆括号
```

`Object`的每个实例都具有下列属性和方法：

- `constructor`：保存着用于创建当前对象的函数。对于前面的例子而言，构造函数（constructor）就是`Object()`。
- `hasOwnProperty(propertyName)`：用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在。其中，作为参数的属性名（`propertyName`）必须以字符串形式指定（例如：`o.hasOwnProperty("name")`）。
- `isPrototypeOf(object)`：用于检查传入的对象是否是另一个对象的原型。
- `propertyIsEnumerable(propertyName)`：用于检查给定的属性是否能够使用`for-in`语句来枚举。与`hasOwnProperty()`方法一样，作为参数的属性名必须以字符串形式指定。
- `toLocaleString()`：返回对象的字符串表示，该字符串与执行环境的地区对应。
- `toString()`：返回对象的字符串表示。
- `valueOf()`：返回对象的字符串、数值或布尔值表示。通常与`toString()`方法的返回值相同。

由于在ECMAScript中`Object`是所有对象的基础，因此所有对象都具有这些基本的属性和方法。



## 3.5 运算符

ECMA-262描述了一组用于操作数据值的**运算符**，包括算术运算符（如加号和减号）、位运算符、关系运算符和相等运算符。 **在应用于对象时，相应的运算符通常都会调用对象的`valueOf()`和（或）`toString()`方法，以便取得可以运算的值。**

### 3.5.1 一元运算符

只能操作一个值的操作符叫做**一元运算符**。

#### 1. 递增和递减运算符

递增：

```js
let age = 29;
++age;
age++;
```

递减：

```js
let age = 29;
--age;
age--;
```

所有这4个操作符对任何值都适用，也就是它们不仅适用于整数，还可以用于字符串、布尔值、浮点数值和对象。在应用于不同的值时，递增和递减操作符遵循下列规则：

- 在应用于一个包含有效数字字符的字符串时，先将其转换为数字值，再执行加减1的操作。字符串变量变成数值变量。
- 在应用于一个不包含有效数字字符的字符串时，将变量的值设置为`NaN`。字符串变量变成数值变量。
- 在应用于布尔值`false`时，先将其转换为0再执行加减1的操作。布尔值变量变成数值变量。
- 在应用于布尔值`true`时，先将其转换为1再执行加减1的操作。布尔值变量变成数值变量。
- 在应用于浮点数值时，执行加减1的操作。
- 在应用于对象时，先调用对象的`valueOf()`方法以取得一个可供操作的值。然后对该值应用前述规则。如果结果是`NaN`，则在调用`toString()`方法后再应用前述规则。对象变量变成数值变量。

```js
let s1 = "2";
let s2 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};

s1++; // value becomes numeric 3
s2++; // value becomes NaN
b++; // value becomes numeric 1
f--; // value becomes 0.10000000000000009 (due to floating-point inaccuracies)
o--; // value becomes numeric -2
```



#### 2. 一元加和减运算符

一元加减运算符相当于正负号：

```js
let num = 25;
num = +num;
console.log(num); // 25

let num = 25;
num = -num;
console.log(num); // -25
```

下面的例子展示了对不同数据类型应用一元加运算符的结果：

```js
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};


s1 = +s1;        // 值变成数值1
s2 = +s2;        // 值变成数值1.1
s3 = +s3;        // 值变成NaN
b = +b;          // 值变成数值0
f = +f;          // 值未变，仍然是1.1
o = +o;          // 值变成数值-1
```

下面的例子展示了对不同数据类型应用一元减运算符的结果：

```js
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
        return -1;
    }
};

s1 = -s1;         // 值变成了数值-1
s2 = -s2;         // 值变成了数值-1.1
s3 = -s3;         // 值变成了NaN
b = -b;           // 值变成了数值0
f = -f;           // 变成了-1.1
o = -o;           // 值变成了数值1
```



### 3.5.2 位运算符



### 3.5.3 布尔运算符

#### 逻辑非

逻辑非操作符由一个叹号（`!`）表示，可以应用于ECMAScript中的任何值。无论这个值是什么数据类型，这个操作符都会返回一个布尔值。逻辑非操作符首先会将它的操作数转换为一个布尔值，然后再对其求反。也就是说，逻辑非操作符遵循下列规则：

- 如果操作数是一个对象，返回`false`；
- 如果操作数是一个空字符串，返回`true`；
- 如果操作数是一个非空字符串，返回`false`；
- 如果操作数是数值0，返回`true`；
- 如果操作数是任意非0数值（包括`Infinity`），返回`false`；
- 如果操作数是`null`，返回`true`；
- 如果操作数是`NaN`，返回`true`；
- 如果操作数是`undefined`，返回`true`。

```js
console.log(!false); // true
console.log(!"blue"); // false
console.log(!0); // true
console.log(!NaN); // true
console.log(!""); // true
console.log(!12345); // false
```

同时使用两个逻辑非运算符可以将一个值转换为与其对应的布尔值，实际上等同于`Boolean()`转型函数的行为：

```js
console.log(!!"blue"); // true
console.log(!!0); // false
console.log(!!NaN); // false
console.log(!!""); // false
console.log(!!12345); // true
```

#### 逻辑与

逻辑与运算符由两个和号（`&&`）表示，有两个操作数：

```js
let result = true && false;
```

在有一个操作数不是布尔值的情况下，逻辑与操作就不一定返回布尔值；此时，它遵循下列规则：

- 如果第一个操作数是对象，则返回第二个操作数；
- 如果第二个操作数是对象，则只有在第一个操作数的求值结果为`true`的情况下才会返回该对象；
- 如果两个操作数都是对象，则返回第二个操作数；
- 如果第一个操作数是`null`，则返回`null`；
- 如果第一个操作数是`NaN`，则返回`NaN`；
- 如果第一个操作数是`undefined`，则返回`undefined`。

#### 逻辑或

逻辑或操作符由两个竖线符号（`||`）表示，有两个操作数：

```js
var result = true || false;
```

如果有一个操作数不是布尔值，逻辑或也不一定返回布尔值；此时，它遵循下列规则：

- 如果第一个操作数是对象，则返回第一个操作数；
- 如果第一个操作数的求值结果为`false`，则返回第二个操作数；
- 如果两个操作数都是对象，则返回第一个操作数；
- 如果两个操作数都是`null`，则返回`null`；
- 如果两个操作数都是`NaN`，则返回`NaN`；
- 如果两个操作数都是`undefined`，则返回`undefined`。

利用逻辑或的**短路操作**来避免为变量赋`null`或`undefined`值。例如：

```js
var myObject = preferredObject || backupObject;
```

如果`preferredObject`的值不是`null`，那么它的值将被赋给`myObject`；如果是`null`，则将`backupObject`的值赋给`myObject`。ECMAScript程序的赋值语句经常会使用这种模式。

### 3.5.4 乘性运算符

- 乘法 ：`*`
- 除法：`/`
- 求模：`%`

```js
let result = 34 * 56;
let result = 66 / 11;
let result = 26 % 5; // equal to 1
```

### 3.5.5 求幂运算符

New in ECMAScript 7, `Math.pow()` now gets its own `**` operator, which behaves identically.

```js
console.log(Math.pow(3, 2); // 9
console.log(3 ** 2); // 9

console.log(Math.pow(16, 0.5); // 4
console.log(16** 0.5); // 4
```

What’s more, the operator also gets its own exponentiate assignment operator, `**=`, which performs the exponentiation and subsequent assignment of the result:
```js
let squared = 3;
squared **= 2;
console.log(squared); // 9

let sqrt = 16;
sqrt **= 0.5;
console.log(sqrt); // 4
```

### 3.5.6 加性运算符

#### 加法

如果有一个操作数是字符串，那么就要应用如下规则：

- 如果两个操作数都是字符串，则将第二个操作数与第一个操作数拼接起来；
- 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，然后再将两个字符串拼接起来。

```js
let result1 = 5 + 5; // 两个数值相加
console.log(result1); // 10

let result2 = 5 + "5"; // 一个数值和一个字符串相加
console.log(result2); // "55"

let num1 = 5;
let num2 = 10;
let message = "The sum of 5 and 10 is " + num1 + num2;
console.log(message); // "The sum of 5 and 10 is 510"

let num1 = 5;
let num2 = 10;
let message = "The sum of 5 and 10 is " + (num1 + num2);
console.log(message); // "The sum of 5 and 10 is 15"
```

#### 减法

- 如果有一个操作数是字符串、布尔值、`null`或`undefined`，则先在后台调用`Number()`函数将其转换为数值，然后再根据前面的规则执行减法计算。如果转换的结果是`NaN`，则减法的结果就是`NaN`；
- 如果有一个操作数是对象，则调用对象的`valueOf()`方法以取得表示该对象的数值。如果得到的值是`NaN`，则减法的结果就是`NaN`。如果对象没有`valueOf()`方法，则调用其`toString()`方法并将得到的字符串转换为数值。

```js
let result1 = 5 - true; // 4 because true is converted to 1
let result2 = NaN - 1; // NaN
let result3 = 5 - 3; // 2

let result4 = 5 - ""; // 5 because "" is converted to 0
let result5 = 5 - "2"; // 3 because "2" is converted to 2
let result6 = 5 - null; // 5 because null is converted to 0
```



### 3.5.7 关系运算符

与ECMAScript中的其他操作符一样，当关系操作符的操作数使用了非数值时，也要进行数据转换或完成某些奇怪的操作。以下就是相应的规则。

- 如果两个操作数都是数值，则执行数值比较。
- 如果两个操作数都是字符串，则比较两个字符串对应的字符编码值。
- 如果一个操作数是数值，则将另一个操作数转换为一个数值，然后执行数值比较。
- 如果一个操作数是对象，则调用这个对象的`valueOf()`方法，用得到的结果按照前面的规则执行比较。如果对象没有`valueOf()`方法，则调用`toString()`方法，并用得到的结果根据前面的规则执行比较。
- 如果一个操作数是布尔值，则先将其转换为数值，然后再执行比较。

```js
let result = "Brick" < "alphabet"; // true
let result = "Brick".toLowerCase() < "alphabet".toLowerCase(); // false
let result = "23" < "3"; // true
let result = "23" < 3; // false
let result1 = NaN < 3; // false
let result2 = NaN >= 3; // false
```



### 3.5.8 相等运算符

ECMAScript的解决方案就是提供两组操作符：**相等**和**不相等**——先转换再比较，**全等**和**不全等**——仅比较而不转换。

#### 相等和不相等

在转换不同的数据类型时，相等和不相等运算符遵循下列基本规则：

- 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——`false`转换为0，而`true`转换为1；
- 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值；
- 如果一个操作数是对象，另一个操作数不是，则调用对象的`valueOf()`方法，用得到的基本类型值按照前面的规则进行比较；

这2个运算符在进行比较时则要遵循下列规则：

- `null`和`undefined`是相等的。
- 要比较相等性之前，不能将`null`和`undefined`转换成其他任何值。
- 如果有一个操作数是`NaN`，则相等运算符返回`false`，而不相等操作符返回`true`。重要提示：即使两个操作数都是`NaN`，相等操作符也返回`false`；因为按照规则，`NaN`不等于`NaN`。
- 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回`true`；否则，返回`false`。

一些特殊情况及比较结果：

| 表　达　式            | 值      |
| :------------------ | :------ |
| `null == undefined` | `true`  |
| `"NaN" == NaN`      | `false` |
| `5 == NaN`          | `false` |
| `NaN == NaN`        | `false` |
| `NaN != NaN`        | `true`  |
| `false == 0`        | `true`  |
| `true == 1`         | `true`  |
| `true == 2`         | `false` |
| `undefined == 0`    | `false` |
| `null == 0`         | `false` |
| `"5"==5`            | `true`  |



#### 全等和全不等

全等操作符由3个等于号（`===`）表示，它只在两个操作数未经转换就相等的情况下返回`true`：

```js
let result1 = ("55" == 55);     //true，因为转换后相等
let result2 = ("55" === 55);    //false，因为不同的数据类型不相等
```

全不等操作符由一个叹号后跟两个等于号（`!==`）表示，它在两个操作数未经转换就不相等的情况下返回`true`：

```js
let result1 = ("55" != 55);     //false，因为转换后相等
let result2 = ("55" !== 55);    //true，因为不同的数据类型不相等
```

注意：`null == undefined`会返回`true`，因为它们是类似的值；但`null === undefined`会返回`false`，因为它们是不同类型的值。



### 3.5.9 条件运算符

条件操作符应该算是ECMAScript中最灵活的一种操作符了，而且它遵循与Java中的条件操作符相同的语法形式，如下面的例子所示：

```js
variable = boolean_expression ? true_value : false_value;
```

这行代码的含义就是基于对`boolean_expression`求值的结果，决定给变量`variable`赋什么值。如果求值结果为`true`，则给变量`variable`赋`true_value`值；如果求值结果为`false`，则给变量`variable`赋`false_value`值。



### 3.5.10 赋值运算符

简单的赋值操作符由等于号（`=`）表示，其作用就是把右侧的值赋给左侧的变量：

```js
let num = 10;
```

每个主要算术操作符（以及个别的其他操作符）都有对应的复合赋值操作符：

- 乘/赋值（`*=`）；
- 除/赋值（`/=`）；
- 模/赋值（`%=`）；
- 加/赋值（`+=`）；
- 减/赋值（`-=`）；
- 左移/赋值（`<<=`）；
- 有符号右移/赋值（`>>=`）；
- 无符号右移/赋值（`>>>=`）。

### 3.5.11 逗号运算符

使用逗号操作符可以在一条语句中执行多个操作：

```js
let num1=1, num2=2, num3=3;
```

逗号操作符还可以用于赋值。在用于赋值时，逗号操作符总会返回表达式中的最后一项，如下面的例子所示：

```js
let num = (5, 1, 4, 8, 0); // num的值为0
```



## 3.6 语句

### 3.6.1 `if`语句

以下是`if`语句的语法：

```js
if (condition1) statement1 else if (condition2) statement2 else statement3
```

```js
if (i > 25) {
    console.log("Greater than 25.");
} else if (i < 0) {
    console.log("Less than 0.");
} else {
    console.log("Between 0 and 25, inclusive.");
}
```

### 3.6.2 `do-while`语句

以下是`do-while`语句的 语法：

```js
do {
    statement
} while (expression);
```

```js
let i = 0;
do {
    i += 2;
} while (i < 10);
```

### 3.6.3 `while`语句

以下是`while`语句的语法：

```js
while(expression) statement
```

下面是一个示例：

```js
let i = 0;
while (i < 10) {
    i += 2;
}
```

### 3.6.4 `for`语句

以下是`for`语句的语法：

```js
for (initialization; expression; post-loop-expression) statement
```

下面是一个示例：

```js
let count = 10;
for (let i = 0; i < count; i++) {
    console.log(i);
}
```

`for`语句中的初始化表达式、控制表达式和循环后表达式都是可选的。将这三个表达式全部省略，就会创建一个无限循环，例如：

```js
for (;;) {          // 无限循环
    doSomething();
}
```

### 3.6.5 `for-in`语句

`for-in`语句是一种精准的迭代语句，可以用来枚举对象的non-symbol keyed属性。以下是`for-in`语句的语法：

```js
for (property in expression) statement
```

下面是一个示例：

```js
for (const propName in window) {
    console.log(propName);
}
```

`for`语句中的`const`定义不是必须的，但是建议这样使用，以确保本地变量不会被修改。

### 3.6.6 `for-of`语句

`for-of`语句是一种精准的迭代语句，可以用来遍历(loop through)可迭代对象的元素。以下是`for-of`语句的语法：

```js
for (property of expression) statement
```

下面是一个示例：

```js
for (const el in [2,4,6,8) {
	console.log(el);
}
```

`for`语句中的`const`定义不是必须的，但是建议这样使用，以确保本地变量不会被修改。

> NOTE In ES2018, the for-of statement is extended as a for-await-of loop to support async iterables which produce promises.

### 3.6.7 `label`语句

使用`label`语句可以在代码中添加标签，以便将来使用。以下是`label`语句的语法：

```js
label: statement
```

下面是一个示例：

```js
start: for (let i = 0; i < count; i++) {
console.log(i);
}
```

例子中定义的`start`标签可以在将来由`break`或`continue`语句引用。加标签的语句一般都要与`for`语句等循环语句配合使用。

### 3.6.8 `break`和`continue`语句

```js
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        break;
    }
    num++;
}
console.log(num); // 4
```

```js
let num = 0;
for (let i = 1; i < 10; i++) {
    if (i % 5 == 0) {
        continue;
    }
    num++;
}
console.log(num); // 8
```

`break`和`continue`语句都可以与`label`语句联合使用，从而返回代码中特定的位置：

```js
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outermost;
        }
        num++;
    }
}
console.log(num); // 55
```

```js
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
    for (let j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            continue outermost;
        }
        num++;
    }
}
console.log(num); // 95
```

### 3.6.9 `with`语句

`with`语句的作用是将代码的作用域设置到一个特定的对象中。`with`语句的语法如下：

```js
with (expression) statement;
```

```js
with(location) {
    let qs = search.substring(1);
    let hostName = hostname;
    let url = href;
}
```

**严格模式下不允许使用`with`语句，否则将视为语法错误。**

> 由于大量使用 **`with`**语句会导致性能下降，同时也会给调试代码造成困难，因此在开发大型应用程序时，不建议使用**`with`** 语句。

### 3.6.10 `switch`语句

```js
switch (expression) {
    case value1:
        statement
        break;
    case value2:
        statement
        break;
    case value3:
        statement
        break;
    case value4:
        statement
        break;
    default:
        statement
}
```

```js
switch (expression) {
    case expression1:
        statement
        break;
    case expression2:
        statement
        break;
    case expression3:
        statement
        break;
    case expression4:
        statement
        break;
    default:
        statement
}
```

有意省略`break`关键字可以做到混合几种情形的效果：

```js
switch (i) {
    case 25: 
        /* 合并两种情形 */
    case 35: 
        alert("25 or 35");
        break;
    case 45: 
        alert("45");
        break;
    default: 
        alert("Other");
}
```



## 3.7 函数

ECMAScript中的函数使用`function`关键字来声明，后跟一组参数以及函数体。函数的基本语法如下所示：

```js
function functionName(arg0, arg1,...,argN) {
    statements
    return value; // 通过return语句后跟要返回的值来实现返回值。
    statments // 位于return语句之后的任何代码都永远不会执行。
}
```

示例：

```js
function sayHi(name, message) {
    console.log("Hello " + name + ", " + message);
}
```

函数可以通过其函数名来调用，后面还要加上一对圆括号和参数（圆括号中的参数如果有多个，可以用逗号隔开）。调用`sayHi()`函数的代码如下所示：

```js
sayHi("Nicholas", "how are you today?");
```

严格模式对函数有一些限制：

- 不能把函数命名为`eval`或`arguments`；
- 不能把参数命名为`eval`或`arguments`；
- 不能出现两个命名参数同名的情况。

