#  第10章 函数

每个函数都是对象，即`Function`类型的实例，而且都与其他引用类型一样具有属性和方法。因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。

**定义函数的3种语法形式：**

- 使用函数声明定义函数：

```js
function sum (num1, num2) {
    return num1 + num2;
}
```

- 使用函数表达式定义函数：

```js
let sum = function(num1, num2){
    return num1 + num2;
};

// 使用箭头函数语法
let sum = (num1, num2) => {
    return num1 + num2;
};
```

- 使用`Function`构造函数：

```js
// 这种语法会导致解析两次代码（第一次是解析常规ECMAScript代码，第二次是解析传入构造函数中的字符串），从而影响性能。
var sum = new Function("num1", "num2", "return num1 + num2"); // 不推荐使用这种方法定义函数
```



## 10.1 箭头函数（Arrow Functions）

ECMAScript 6允许使用箭头语法（fat-arrow syntax）来定义函数表达式。在大多数情况下，使用箭头函数实例化函数对象与使用函数表达式没有区别。

```js
let arrowSum = (a, b) => {
	return a + b;
};

let functionExpressionSum = function(a, b) {
	return a + b;
};

console.log(arrowSum(5, 8)); // 13
console.log(functionExpressionSum(5, 8)); // 13
```

箭头函数内联的情况下特别有用，它提供了更简洁的语法：
```js
let ints = [1, 2, 3];
console.log(ints.map(function(i) { return i + 1; })); // [2, 3, 4]
console.log(ints.map((i) => { return i + 1 })); // [2, 3, 4]
```

如果只想使用一个参数，则箭头函数不需要括号； 如果要使用零个参数或多个参数，则需要使用括号：
```js
// Both are valid
let double = (x) => { return 2 * x; };
let triple = x => { return 3 * x; };

// Zero parameters require an empty pair of parentheses
let getRandom = () => { return Math.random(); };

// Multiple parameters require parentheses
let sum = (a, b) => { return a + b; };

// Invalid syntax:
let multiply = a, b => { return a * b; };
```

箭头函数只包含单行代码的情况下（例如，赋值或表达式），可以不使用花括号。并且，这行的值将隐式地被返回：
```js
// Both are valid and will return the value
let double = (x) => { return 2 * x; };
let triple = (x) => 3 * x;

// Assignment is allowed
let value = {};
let setName = (x) => x.name = "Matt";
setName(value);
console.log(value.name); // "Matt"

// Invalid syntax:
let multiply = (a, b) => return a * b;
```
箭头函数尽管语法简洁，但在某些情况下并不适用。 它们不允许使用`arguments`，`super`或`new.target`，也不能用作构造函数。 此外，使用箭头语法创建的函数对象未定义`prototype`。




## 10.2 函数名

函数名仅仅是指向函数的指针，所以，一个函数可能会被多个变量所引用：

```js
function sum(num1, num2) {
	return num1 + num2;
}
console.log(sum(10, 10)); // 20

let anotherSum = sum;
console.log(anotherSum(10, 10)); // 20

sum = null;
console.log(anotherSum(10, 10)); // 20
```

ECMAScript 6中的所有函数对象都会暴露一个描述该函数的只读属性`name`：

- 使用函数声明定义的函数，其`name`属性值是函数名的字符串形式；
- 使用函数表达式定义的函数，其`name`属性值是引用该函数的变量名的字符串形式；
- 使用箭头函数定义的函数，其`name`属性值为空字符串；
- 使用`Function`构造函数定义的函数，其`name`属性值是`"anonymous"`；

```js
function foo() {}
let bar = function() {};
let baz = () => {};

console.log(foo.name);               // foo
console.log(bar.name);               // bar
console.log(baz.name);               // baz
console.log((() => {}).name);        // (empty string)
console.log((new Function()).name);  // anonymous
```

如果一个函数是getter， setter或是使用`bind()`实例化的， 将会添加一个前缀来识别它：

```js
function foo() {}

console.log(foo.bind(null).name); // bound foo

let dog = {
    years: 1,
    get age() {
    	return this.years;
    },
    set age(newAge) {
    	this.years = newAge;
    }
}

let propertyDescriptor = Object.getOwnPropertyDescriptor(dog, 'age');
console.log(propertyDescriptor.get.name); // get age
console.log(propertyDescriptor.set.name); // set age
```



## 10.3 理解函数参数

ECMAScript中的函数参数在内部是以数组形式表示的。

当使用`function`关键字（而非箭头函数）定义函数时，在函数内部可以通过`arguments`对象取得传入函数的每个参数的值。`arguments`对象是一个类数组对象（尽管它不是`Array`的实例）。

```js
function sayHi(name, message) {
	console.log("Hello " + name + ", " + message);
}
```

同样的参数可以通过`arguments[0]`和`arguments[1]`引用被访问：

```js
function sayHi() {
	console.log("Hello " + arguments[0] + ", " + arguments[1]);
}
```

`arguments`的`length`属性表示传入参数的个数：

```js
function howManyArgs() {
	console.log(arguments.length);
}
howManyArgs("string", 45); // 2
howManyArgs(); // 0
howManyArgs(12); // 1
```

以这种方式可以让函数接受任意数量的参数：

```js
function doAdd() {
    if (arguments.length === 1) {
        console.log(arguments[0] + 10);
    } else if (arguments.length === 2) {
        console.log(arguments[0] + arguments[1]);
    }
}
doAdd(10); // 20
doAdd(30, 20); // 50
```

以下示例与上面等价：

```js
function doAdd(num1, num2) {
    if (arguments.length === 1) {
        console.log(num1 + 10);
    } else if (arguments.length === 2) {
        console.log(arguments[0] + num2);
    }
}
```

并且，`arguments`的值总是与相应形参的值保持同步：

```js
function doAdd(num1, num2) {
    arguments[1] = 10;
    console.log(arguments[0] + num2);
}

doAdd(100, 200)  //110
```

任何没有传入的命名参数都自动被赋为`undefined`值，这类似于声明变量但没有初始化：

```js
function fun(num1, num2){
    console.log(num1, num2);
}

fun('num1')    // num1 undefined
```

**严格模式下`arguments`对象的使用有2点不同：**

- 前例中的赋值不在生效。变量`num2`的值将保持`undefined`，即使`arguments[1]`被赋为10。
- 试图覆盖`arguments`将发生语法错误。

### 箭头函数中的`arguments`

使用箭头函数定义的函数，不能使用`arguments`关键字访问传入的参数：

```js
function foo() {
    console.log(arguments[0]);
}
foo(5); // 5

let bar = () => {
    console.log(arguments[0]);
};
bar(5); // ReferenceError: arguments is not defined
```

虽然`arguments`在箭头函数中不可用，但还是可以将包装函数的`arguments`提供给箭头函数的作用域：

```js
function foo() {
    let bar = () => {
        console.log(arguments[0]); // 5
    };
    bar();
}
foo(5);
```

> **注意！在ECMAScript中，所有参数都是按值传递。无法通过引用传递参数。如果一个对象被作为参数传递，它的值仅仅是这个对象的引用。**




## 10.4 JavaScript函数没有重载

JavaScript函数没有重载。如果声明了同名函数（即使两个函数的形参不同），结果是后面的函数将覆盖前面的函数：

```js
function addSomeNumber(num){
    return num + 100;
}

function addSomeNumber(num, num2){
    return num + num2;
}

let result = addSomeNumber(100, 200); //300
```



## 10.5 默认参数值

在ECMAScript 5.1 及其之前的版本中，实现函数参数默认值的常见策略是，通过检查参数是否为`undefined`来判断参数是否被提供：

```js
function makeKing(name) {
    name = (typeof name !== 'undefined') ? name : 'Henry';
    return `King ${name} VIII`;       //字符串的这种用法是不是和Python中的f字符串很像？
}

console.log(makeKing()); // 'King Henry VIII'
console.log(makeKing('Louis')); // 'King Louis VIII'
```

ECMAScript 6支持在函数签名中显式地使用`=`运算符来定义参数的默认值：

```js
function makeKing(name = 'Henry') {
    return `King ${name} VIII`;
}

console.log(makeKing('Louis')); // 'King Louis VIII'
console.log(makeKing()); // 'King Henry VIII'
```

将`undefined`作为参数传递，等同于没有传递参数。这允许支持多个独立的默认值：

```js
function makeKing(name = 'Henry', numerals = 'VIII') {
    return `King ${name} ${numerals}`;
}

console.log(makeKing()); // 'King Henry VIII'
console.log(makeKing('Louis')); // 'King Louis VIII'
console.log(makeKing(undefined, 'VI')); // 'King Henry VI'
```

当使用参数默认值时，`arguments`对象的值不会反映到参数的默认值，而是反映到传给函数的参数：

```js
function makeKing(name = 'Henry') {
    name = 'Louis';
    return `King ${arguments[0]}`;
}
console.log(makeKing()); // 'King undefined'
console.log(makeKing('Louis')); // 'King Louis'
console.log(makeKing('Peter')); // 'King Peter'
```

**注意，上面这个例子中第三个调用出现一个很迷的现象：**

```js
function makeKing(name = 'Henry') {
    name = 'Louis';
    return `arguments[0]=${arguments[0]}, name=${name}`;
}
console.log(makeKing()); // 'arguments[0]=undefined, name=Louis'
console.log(makeKing('Louis')); // 'arguments[0]=Louis, name=Louis'
console.log(makeKing('Peter')); // 'arguments[0]=Peter, name=Louis'

function f(arg){
    arguments[0] = 'yoyo';
    console.log(`arguments[0]=${arguments[0]}, arg=${arg}`);
}
f('haha')   // 'arguments[0]=yoyo, arg=yoyo'
```

**调用第一个函数并传入参数，重新赋值参数名变量，不会影响`arguments[0]`；调用第二个函数，重新对`arguments[0]`进行赋值，则变量`arg`也跟着改变了。** 在Node.js v13.11.0 和 Google Chrome：80.0.3987.132，V8引擎：8.0.426.26中都得到这一相同的结果。

默认参数不限于原始类型或对象类型，也可以将调用函数得到的计算值作为参数的默认值：

```js
let romanNumerals = ['I', 'II', 'III', 'IV', 'V', 'VI'];
let ordinality = 0;

function getNumerals() {
    // Increment the ordinality after using it to index into the numerals array
    return romanNumerals[ordinality++];
}

// 函数参数默认值只在函数本身被调用时才被调用，而不是在函数定义时被调用。
function makeKing(name = 'Henry', numerals = getNumerals()) {
    return `King ${name} ${numerals}`;
}

console.log(makeKing()); // 'King Henry I'
console.log(makeKing('Louis', 'XVI')); // 'King Louis XVI'
console.log(makeKing()); // 'King Henry II'
console.log(makeKing()); // 'King Henry III'
```

函数参数默认值只在函数本身被调用时才被调用，而不是在函数定义时被调用。

箭头函数也支持以同样的方式使用默认参数：

```js
let makeKing = (name = 'Henry') => `King ${name}`;
console.log(makeKing()); // King Henry
```

### 默认参数作用域和暂时死区（Temporal Dead Zone）

定义多个默认值参数就像使用`let`关键字连续定义变量一样高效：

```js
function makeKing(name = 'Henry', numerals = 'VIII') {
    return `King ${name} ${numerals}`;
}
console.log(makeKing()); // King Henry VIII
```

默认值参数以它们在参数列表中的顺序被初始化。你可以认为它的行为与下面代码类似：

```js
function makeKing() {
    let name = 'Henry';
    let numerals = 'VIII';
    return `King ${name} ${numerals}`;
}
```

因为参数按顺序被初始化，所以后定义的默认值参数可以引用前面的参数：

```js
function makeKing(name = 'Henry', numerals = name) {
    return `King ${name} ${numerals}`;
}
console.log(makeKing()); // King Henry Henry
```

参数初始化的顺序遵循同样的暂时死区（Temporal Dead Zone）规则，该规则指出，参数值不能引用其他在之后被定义的参数值。这会抛出错误：

```js
// Error
function makeKing(name = numerals, numerals = 'VIII') {
    return `King ${name} ${numerals}`;
}
```

参数也存在于它们自己的作用域中，并因此不能引用函数体的作用域。这会抛出错误：

```js
// Error
function makeKing(name = 'Henry', numerals = defaultNumeral) {
    let defaultNumeral = 'VIII';
    return `King ${name} ${numerals}`;
}
```



## 10.6 展开参数（Spread Arguments）和剩余参数（Rest Arguments）

展开运算符（spread operator）在调用函数以及定义函数的参数时非常有用。

### 10.6.1 展开参数（Spread Arguments）

**与其将数组作为单个参数传递给函数，不如将一个数组的值分解并分别将每个值作为单独的参数传递，这通常更加实用。**

假设您定义了以下函数，该函数对作为参数传递的所有值求和：

```js
let values = [1, 2, 3, 4];
function getSum() {
    let sum = 0;
    for (let i = 0; i < arguments.length; ++i) {
        sum += arguments[i];
    }
    return sum;
}
```

This function expects each of its arguments to be an individual number that will be iterated through to find the sum. An array outside the function containing all the values you want to sum is a logical format, but the most prudent way to flatten this array into separate parameters is to inelegantly utilize `.apply()`:

该函数期望其每个参数都是一个单独的数字，将对其进行迭代以查找总和。 函数外部的数组包含要求和的所有值，将此数组展平为单独（individual）参数的最明智的方法是不太优雅地使用`.apply()`方法：

```js
console.log(getSum.apply(null, values)); // 10
```

In ECMAScript 6, you are now able to perform this action more succinctly using the spread operator. Applying the spread operator to an iterable object and passing that as a single argument to a function will break apart that iterable object of size N and pass it to the function as N separate arguments.

在ECMAScript 6中，您现在可以使用展开运算符（spread operator）更简洁地执行此操作。 将展开运算符（spread operator）应用于可迭代对象，并将其作为单个参数传递给函数，这会把这个大小为N的可迭代对象进行拆分，并将其作为N个单独的参数传递给函数。

With the spread operator, you can unpack the outer array into individual arguments directly inside the function invocation:

有了展开运算符（spread operator），可以直接在函数调用内部将外部数组解包(unpack)为多个单独的（individual）参数：

```js
console.log(getSum(...values)); // 10
```

Because the size of the array is known, there are no restrictions on other parameters appearing before or after the spread operator, including other spread operators:

因为数组的大小是已知的，所以在展开运算符（spread operator）之前或之后出现的其他参数（包括其他扩展运算符）没有任何限制：

```js
console.log(getSum(-1, ...values)); // 9
console.log(getSum(...values, 5)); // 15
console.log(getSum(-1, ...values, 5)); // 14
console.log(getSum(...values, ...[5,6,7])); // 28
```

The presence of the spread operator is totally unknown to the `arguments` object; it will treat the value being broken apart as separate pieces because that is how they are passed to the function:

`arguments`对象完全不知道展开运算符（spread operator）的存在。 它将把这个被分解的值看作是单独的部分，因为这些参数就是以这种方式传递给函数的：

```js
let values = [1,2,3,4]

function countArguments() {
    console.log(arguments.length);
}

countArguments(-1, ...values);          // 5
countArguments(...values, 5);           // 5
countArguments(-1, ...values, 5);       // 6
countArguments(...values, ...[5,6,7]);  // 7
```

The `arguments` object is only one way to consume spread arguments. Spread arguments can be used as named parameters in both standard functions and arrow functions, as well as alongside default arguments:

`arguments`对象只是消耗 (consume) 展开参数的一种方法。 展开参数可以在标准函数和箭头函数中被用作命名参数，也可以用作默认参数：

```js
function getProduct(a, b, c = 1) {
    return a * b * c;
}

let getSum = (a, b, c = 0) => {
    return a + b + c;
}

console.log(getProduct(...[1,2]));      // 2
console.log(getProduct(...[1,2,3]));    // 6
console.log(getProduct(...[1,2,3,4]));  // 6

console.log(getSum(...[0,1]));        // 1
console.log(getSum(...[0,1,2]));      // 3
console.log(getSum(...[0,1,2,3]));    // 3
```



### 10.6.2 剩余参数（Rest Arguments）

When composing a function definition, instead of handling parameters individually, it is possible to use the spread operator to combine ranges of parameters of variable length into a single array. In many ways, this is very similar to how the `arguments` object works, but in this case the rest parameter becomes a formal `Array` object.

```js
function getSum(...values) {
    // Sequentially sum all elements in 'values'
    // Initial total = 0
    return values.reduce((x, y) => x + y, 0);
}

console.log(getSum(1,2,3)); // 6
```

If there are named parameters preceding the rest parameter, it will assume the size of the remaining parameters that remain unnamed, or an empty array if there are none. Because the rest parameter is variable in size, you are only able to use it as the last formal parameter:

```js
// Error
function getProduct(...values, lastValue) {}

// OK
function ignoreFirst(firstValue, ...values) {
    console.log(values);
}

ignoreFirst();         // []
ignoreFirst(1);        // []
ignoreFirst(1,2);      // [2]
ignoreFirst(1,2,3);    // [2, 3]
```

Although arrow functions do not support the `arguments` object, they do support rest parameters, which affords you behavior that is extremely similar to arguments:

```js
let getSum = (...values) => {
    return values.reduce((x, y) => x + y, 0);
}

console.log(getSum(1,2,3)); // 6
```

As you might expect, using a rest parameter does not affect the `arguments` object—it will still exactly reflect what was passed to the function:

```js
function getSum(...values) {
    console.log(arguments.length);    // 3
    console.log(arguments);           // [1, 2, 3]
    console.log(values);              // [1, 2, 3]
}

console.log(getSum(1,2,3));
```




## 10.7 函数声明与函数表达式

解析器会率先读取函数声明，并使其在执行任何代码之前可用（可以访问）：

```js
alert(sum(10,10));    // 不会报错
function sum(num1, num2){
    return num1 + num2;
}
```

至于函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被解释执行：

```js
alert(sum(10,10));     // Uncaught TypeError: sum is not a function
var sum = function(num1, num2){
    return num1 + num2;
};
```

## 10.8 函数作为值

ECMAScript中的函数名本身就是变量，所以函数也可以作为值来使用。

```js
function callSomeFunction(someFunction, someArgument){
    return someFunction(someArgument);
}
```

## 10.9 函数内部属性

在函数内部，有两个特殊的对象：

- `arguments`：是一个类数组对象，包含着传入函数中的所有参数。
  - `arguments.callee`：`arguments`的属性`callee`是一个指针，它指向拥有这个`arguments`对象的函数。
- `this`：`this`引用的是函数执行的环境对象，或者也可以说是`this`值（当在网页的全局作用域中调用函数时，`this`对象引用的就是`window`）。

`arguments.callee`的使用：

```js
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * factorial(num-1)    //修改函数名后将会递归失败；
    }
}
```
```js
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1);//实现松耦合，修改函数名不影响递归正常执行；
    }
}

var trueFactorial = factorial;

factorial = function(){
    return 0;
};

console.log(trueFactorial(5));     //120
console.log(factorial(5));         //0
```

`this`对象的使用：

```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    console.log(this.color);
}

sayColor();     //"red"

o.sayColor = sayColor;
o.sayColor();   //"blue"
```

ECMAScript 5规范化了一个函数对象的属性：`caller`。这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为`null`。

```js
function outer(){
    inner(); 
}

function inner(){
    return inner.caller;
}

outer();  //其返回值是outer本身
```

为了实现更松散的耦合，也可以通过`arguments.callee.caller`来访问相同的信息：

```js
function outer(){
    inner();
}

function inner(){
    return arguments.callee.caller;
} 

outer();
```

## 10.10 函数属性（properties）和方法

每个函数都包含2个属性：

- `length`：表示函数希望接收的命名参数的个数；
- `prototype`

`length`属性的使用：

```js
function sayName(name){
    alert(name);
}      

function sum(num1, num2){
    return num1 + num2;
}

function sayHi(){
    alert("hi");
}

alert(sayName.length);      //1
alert(sum.length);          //2
alert(sayHi.length);        //0
```