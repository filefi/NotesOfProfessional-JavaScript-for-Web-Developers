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

`Null`类型是第2个只有一个值的数据类型，这个特殊的值是`null`。

从逻辑角度来看，`null`值表示一个空对象指针，而这也正是使用`typeof`操作符检测`null`值时会返回`"object"`的原因，如下面的例子所示：

```js
let car = null;
console.log(typeof car); // "object"
```

只要意在保存对象的变量还没有真正保存对象，就应该明确地让该变量保存`null`值。这样一来，只要直接检查`null`值就可以知道相应的变量是否已经保存了一个对象的引用：

```js
if (car != null){
    // 对car对象执行某些操作
}
```

实际上，`undefined`值是派生自`null`值的，因此ECMA-262规定对它们的相等性测试要返回`true`：

```js
console.log(null == undefined); // true
```

`null`类型是假的(falsy)， 因此，可以在需要的地方进行更简洁的检查：

```js
let message = null;
let age;
if (message) {
// This block will not execute
}
if (!message) {
// This block will execute
}
if (age) {
// This block will not execute
}
if (!age) {
// This block will execute
}
```

### 3.4.4 Boolean类型

`Boolean`类型是ECMAScript中使用得最多的一种类型，该类型只有2个字面值：`true`和`false`。以下是为变量赋`Boolean`类型值的例子：

```js
let found = true;
let lost = false;
```

ECMAScript中所有类型的值都有与这2个`Boolean`值等价的值。要将一个值转换为其对应的`Boolean`值，可以调用转型函数`Boolean()`：

```js
let message = "Hello world!";
let messageAsBoolean = Boolean(message);
```

转换规则如下：

| 数据类型    | 转换为true的值               | 转换为false的值  |
| :---------- | :--------------------------- | :--------------- |
| `Boolean`   | `true`                       | `false`          |
| `String`    | 任何非空字符串               | `""`（空字符串） |
| `Number`    | 任何非零数字值（包括无穷大） | 0和`NaN`         |
| `Object`    | 任何对象                     | `null`           |
| `Undefined` | 不适用                       | `undefined`      |

### 3.4.5 Number类型

`Number`类型使用IEEE754格式来表示**整数**和**浮点数值**。

为支持各种数值类型，ECMA-262定义了不同的数值字面量格式：

```js
let intNum = 55;                // 十进制整数

let octalNum1 = 070;            // 八进制的56
let octalNum2 = 079;            // 无效的八进制数值——解析为79
let octalNum3 = 08;             // 无效的八进制数值——解析为8

let hexNum1 = 0xA;              // 十六进制的10
let hexNum2 = 0x1f;             // 十六进制的31
```

> 八进制字面量在严格模式下是无效的，会导致支持该模式的JavaScript引擎抛出错误。

> JavaScript可以保存正零（+0）和负零（-0），正零和负零被认为相等。

#### 浮点数值

浮点数值中必须包含一个小数点，并且小数点后面必须至少有一位数字。

```js
let floatNum1 = 1.1;
let floatNum2 = 0.1;
let floatNum3 = .1; // valid, but not recommended
```

由于浮点数值占用的内存空间是整数值的2倍，ECMAScript会不失时机地将浮点数值转换为整数值：

```js
let floatNum1 = 1.;             // 小数点后面没有数字——解析为1
let floatNum2 = 10.0;           // 整数——解析为10
```

对于那些极大或极小的数值，可以用e表示法（即科学计数法）表示的浮点数值表示：

```js
let floatNum = 3.125e7; // equal to 31250000
let floatNum2 = 3e-17; // 等于0.00000000000000003
```

#### 数值范围

- 能够表示的最小数值保存在`Number.MIN_VALUE`中：在大多数浏览器中，这个值是5e-324；
- 能够表示的最大数值保存在`Number.MAX_VALUE`中：在大多数浏览器中，这个值是1.7976931348623157e+308。
- 超出JavaScript数值范围的值将被自动转换成特殊的`Infinity`值。如果这个数值是负数，则会被转换成`-Infinity`（负无穷），如果是正数，则会被转换成`Infinity`（正无穷）。
- 属性`Number.NEGATIVE_INFINITY`和`Number.POSITIVE_INFINITY`分别保存着`-Infinity`和`Infinity`。

使用`isFinite()`函数来确定一个数值是不是有穷的（位于最小和最大的数值之间）：

```js
let result = Number.MAX_VALUE + Number.MAX_VALUE;
console.log(isFinite(result)); // false
```


#### `NaN`非数值

`NaN`，即非数值（Not a Number）是一个特殊的数值，这个数值用于表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了）。

`NaN`本身有2个非同寻常的特点：

- 任何涉及`NaN`的操作（例如`NaN`/10）都会返回`NaN`，这个特点在多步计算中有可能导致问题。
- `NaN`与任何值都不相等，包括`NaN`本身。

`isNaN()`函数会尝试将参数转换为数值，如果不能被转换为数值，则返回`true`，以此来确定其参数是否“不是数值”：

```js
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false - 10 is a number
console.log(isNaN("10")); // false - can be converted to number 10
console.log(isNaN("blue")); // true - cannot be converted to a number
console.log(isNaN(true)); // false - can be converted to number 1
```

#### 数制转换

有3个函数可以把非数值转换为数值：

- `Number()`：用于任何数据类型
- `parseInt()`：用于把字符串转换成整形数值
- `parseFloat()`：用于把字符串转换成浮点型数值。

**`Number()`函数的转换规则如下：**

- 如果是`Boolean`值，`true`和`false`将分别被转换为1和0。
- 如果是数字值，只是简单的传入和返回。
- 如果是`null`值，返回0。
- 如果是`undefined`，返回`NaN`。
- 如果是字符串，遵循下列规则：
    - 如果字符串中只包含数字（包括前面带加号或负号的情况），则将其转换为十进制数值，即`"1"`会变成1，`"123"`会变成123，而`"011"`会变成11（注意：前导的零被忽略了）；
    - 如果字符串中包含有效的浮点格式，如`"1.1"`，则将其转换为对应的浮点数值（同样，也会忽略前导零）；
    - 如果字符串中包含有效的十六进制格式，例如`"0xf"`，则将其转换为相同大小的十进制整数值；
    - 如果字符串是空的（不包含任何字符），则将其转换为0；
    - 如果字符串中包含除上述格式之外的字符，则将其转换为`NaN`。
- 如果是对象，则调用对象的`valueOf()`方法，然后依照前面的规则转换返回的值。如果转换的结果是`NaN`，则调用对象的`toString()`方法，然后再次依照前面的规则转换返回的字符串值。

```js
let num1 = Number("Hello world!");      //NaN
let num2 = Number("");                  //0
let num3 = Number("000011");            //11
let num4 = Number(true);                //1
```

**`parseInt()`函数转换规则：**

- `parseInt()`函数会忽略字符串前面的空格，直至找到第一个非空格字符。
- 如果第一个字符不是数字字符或者负号，`parseInt()`就会返回`NaN`；也就是说，用`parseInt()`转换空字符串会返回`NaN`（`Number()`对空字符返回0）。
- 如果第一个字符是数字字符，`parseInt()`会继续解析第二个字符，直到解析完所有后续字符或者遇到了一个非数字字符。

```js
let num1 = parseInt("1234blue"); // 1234
let num2 = parseInt(""); // NaN
let num3 = parseInt("0xA"); // 10 - hexadecimal
let num4 = parseInt(22.5); // 22
let num5 = parseInt("70"); // 70 - decimal
let num6 = parseInt("0xf"); // 15 - hexadecimal
```

可以为`parseInt()`函数提供第2个参数：转换时使用的基数（即多少进制）：

```js
let num = parseInt("0xAF", 16); // 175
let num1 = parseInt("AF", 16); // 175
let num2 = parseInt("AF"); // NaN

let num1 = parseInt("10", 2); // 2 - parsed as binary
let num2 = parseInt("10", 8); // 8 - parsed as octal
let num3 = parseInt("10", 10); // 10 - parsed as decimal
let num4 = parseInt("10", 16); // 16 - parsed as hexadecimal
```

**`parseFloat()`函数转换规则与`parseInt()`函数类似：**

- `parseFloat()`函数从第一个字符（位置0）开始解析每个字符，一直解析到字符串末尾，或者解析到遇见一个无效的浮点数字字符为止。也就是说，字符串中的第一个小数点是有效的，而第二个小数点就是无效的了，因此它后面的字符串将被忽略。举例来说，`"22.34.5"`将会被转换为22.34。
- `parseFloat()`会忽略前导的零。
- 如果字符串包含的是一个可解析为整数的数（没有小数点，或者小数点后都是零），`parseFloat()`会返回整数。

```js
let num1 = parseFloat("1234blue"); // 1234 （整数）
let num2 = parseFloat("0xA"); // 0
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
| `\unnnn`   | 以十六进制代码`nnnn`表示的一个Unicode字符（其中`n`为0～F）。例如，`\u03a3`表示希腊字符Σ |

![](_static/images/Chapter3-Language_Basics.assets/image-20200422225848537.png)

变量`text`有28个字符，其中6个字符长的转义序列表示1个字符：

```js
let text = "This is the letter sigma: \u03a3.";
console.log(text.length); // 28
```

#### 字符串的特点

ECMAScript中的字符串是不可变的。要改变某个变量保存的字符串，首先要销毁原来的字符串，然后再用另一个包含新值的字符串填充该变量，例如：

```js
let lang = "Java";
lang = lang + "Script";
```

#### 转换为字符串

要把一个值转换为一个字符串有2种方式

- 使用`toString()`方法。除了`null`和`undefined`值，数值、布尔值、对象和字符串值都有`toString()`方法。
- 转型函数`String()`能够将任何类型的值转换为字符串。

`toString()`方法唯一要做的就是返回相应值的字符串表现：
```js
let age = 11;
let ageAsString = age.toString(); // 字符串 "11"
let found = true;
let foundAsString = found.toString(); // 字符串 "true"
```

调用数值的`toString()`方法时，可以传递一个参数：输出数值的基数。默认为10进制。
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

ECMAScript 6支持使用模板字面量定义字符串。模板字面量遵循换行符，可以定义多行内容。

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

由于模板字面量将与反引号内的空格完全匹配，因此在定义它们时需要格外小心。

```js
// This template literal has 25 spaces following the line return character
let myTemplateLiteral = `first line
                         second line`;
console.log(myTemplateLiteral.length); // 47

// This template literal begins with a line return character
let secondTemplateLiteral = `
first line
second line`;
console.log(secondTemplateLiteral[0] === '\n'); // true

// This template literal has no unexpected whitespace characters
let thirdTemplateLiteral = `first line
second line`;
console.log(thirdTemplateLiteral[0]);
// f
```



#### Interpolation



#### Template Literal Tag Functions



#### Raw Strings



### 3.4.7 Symbol类型



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

ECMA-262描述了一组用于操作数据值的**运算符**，包括算术运算符（如加号和减号）、位运算符、关系运算符和相等运算符。在应用于对象时，相应的运算符通常都会调用对象的`valueOf()`和（或）`toString()`方法，以便取得可以运算的值。

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

