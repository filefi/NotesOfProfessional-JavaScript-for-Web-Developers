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
console.log(getSum(-1, ...values));          // 9
console.log(getSum(...values, 5));           // 15
console.log(getSum(-1, ...values, 5));       // 14
console.log(getSum(...values, ...[5,6,7]));  // 28
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

关于函数声明，它的一个重要特征就是**函数声明提升**（function declaration hoisting），意思是解析器在执行代码之前会率先读取函数声明。这就意味着可以把函数声明放在调用它的语句后面：

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

在函数内部的特殊对象：

- `arguments`：是一个类数组对象，包含着传入函数中的所有参数。
  - `arguments.callee`：`arguments`的属性`callee`是一个指针，它指向拥有这个`arguments`对象的函数。
- `this`：`this`引用的是函数执行的环境对象，或者也可以说是`this`值（当在网页的全局作用域中调用函数时，`this`对象引用的就是`window`）。
- `caller`：这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为`null`。
- `new.target`：

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

当函数在严格模式下运行时，访问`arguments.callee`会导致错误。

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

在箭头函数内部，`this`引用了箭头函数表达式被定义时所处的环境对象。

```js
window.color = 'red';
let o = {
    color: 'blue'
};

let sayColor = () => console.log(this.color);
sayColor(); // 'red'

o.sayColor = sayColor;
//箭头函数中的this依旧指向被定义时的环境，也就是window；
o.sayColor(); // 'red'
```

This is especially useful in situations where events or timeouts will invoke a function inside a callback where the invoking object is not the intended object. When an arrow function is used in these situations, the context referenced by `this` is preserved:

这在某些情况下是特别有用的，尤其是在事件或超时将在回调函数中调用函数，并且在这个函数中调用的对象不是预期对象的情况下。 在这些情况下使用箭头函数时，`this`引用的环境对象将被保留：

```js
function King() {
    this.royaltyName = 'Henry';
    // 'this' will be the King instance
    setTimeout(() => console.log(this.royaltyName), 1000);
}

function Queen() {
    this.royaltyName = 'Elizabeth';
    // 'this' will be the window object
    setTimeout(function() { console.log(this.royaltyName); }, 1000);
}

new King(); // Henry
new Queen(); // undefined
```

ECMAScript 5规范化了一个函数对象的属性：`caller`。这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为`null`。

```js
function outer(){
    return inner(); 
}

function inner(){
    return inner.caller;
}

outer();        //其返回值是outer本身
out() === out   // true
```

为了实现更松散的耦合，也可以通过`arguments.callee.caller`来访问相同的信息：

```js
function outer(){
    return inner();
}

function inner(){
    return arguments.callee.caller;
} 

outer();        //其返回值是outer本身
out() === out   // true
```

Functions have always been able to behave as both a constructor to instantiate a new object, and as a normal callable function. New in ECMAScript 6 is the ability to determine if a function was invoked with the `new` keyword using `new.target`. If a function is called normally, `new.target` will be undefined. If a function is called using the new keyword, `new.target` will reference the constructor or function.

函数始终能够像实例化新对象的构造函数一样，以及作为正常的可调用函数。 ECMAScript 6中的新增功能是使用`new.target`确定是否使用`new`关键字调用函数的功能。 如果正常调用函数，则`new.target`将是`undefined`。 如果使用new关键字调用函数，则`new.target`将引用构造函数或函数。

```js
function King() {
    if (!new.target) {
        throw 'King must be instantiated using "new"'
    }
    console.log('King instantiated using "new"';
}

new King();  // King instantiated using "new"
King();      // Error: King must be instantiated using "new"
```



## 10.10 函数属性（properties）和方法

每个函数都包含2个属性：

- `length`：表示函数希望接收的命名参数的个数；
- `prototype`：对于ECMAScript中的引用类型而言，`prototype`是保存它们所有实例方法的真正所在。换句话说，诸如`toString()`和`valueOf()`等方法实际上都保存在`prototype`名下，只不过是通过各自对象的实例访问罢了。在创建自定义引用类型以及实现继承时，`prototype`属性的作用是极为重要的。

每个函数都包含3个非继承而来的方法： 

- `apply()`： **在特定的作用域中调用函数，实际上等同于设置函数体内`this`对象的值。**  `apply()`方法接收2个参数：第1个是在其中运行函数的作用域，第2个是参数数组（可以是`Array`的实例，也可以是`arguments`对象）。
- `call()`： 与`apply()`方法的作用相同，区别在于接收参数的方式不同。第1个参数是`this`值，其余参数都直接传递给函数。换句话说，在使用`call()`方法时，传递给函数的参数必须逐个列举出来。
- `bind()`：这个方法会创建一个函数的实例，其`this`值会被绑定到传给`bind()`函数的值。

`call()`与`apply()`的作用和结果完全相同。至于是使用`apply()`还是`call()`，完全取决于你采取哪种给函数传递参数的方式最方便。在不给函数传递参数的情况下，使用哪个方法都无所谓。

每个函数继承的`toLocaleString()`、`toString()`和`valueOf()`方法始终都返回函数的代码。

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

`apply()`方法的使用：

```js
function sum(num1, num2) {
    return num1 + num2;
}

//callSum1()在执行sum()函数时传入了this作为sum函数的this值（因为是在全局作用域中调用的，所以传入的就是window对象）和arguments对象。
function callSum1(num1, num2) {
    return sum.apply(this, arguments);    // 传入arguments对象
}

//callSum2()在执行sum()函数时传入了this作为sum函数的this值（因为是在全局作用域中调用的，所以传入的就是window对象）和数组对象。
function callSum2(num1, num2) {
    return sum.apply(this, [num1, num2]); // 传入数组
}

console.log(callSum1(10, 10));    // 20
console.log(callSum2(10, 10));    // 20
```

`call()`方法的使用：

```js
function sum(num1, num2) {
    return num1 + num2;
}

function callSum(num1, num2) {
    return sum.call(this, num1, num2);
}

console.log(callSum(10, 10)); // 20
```

**事实上，`apply()`和`call()`真正强大的地方是能够扩展函数中的`this`值，即显式地指定函数的执行环境（作用域）。** 来看下面这个例子：

```js
window.color = 'red';

let o = {
    color: 'blue',
    sayColor: function(){
        console.log(this.color);
    }
};

function sayColor() {
    console.log(this.color);
}

// sayColor()作为全局函数定义的，而且当在全局作用域中调用它时, 其函数体中的this默认为window
sayColor();               // red
// 显式地在全局作用域中调用函数的方式，
sayColor.call(this);      // red
// 显式地在全局作用域中调用函数的方式
sayColor.call(window);    // red

// 显式地指定函数中的this对象指向对象o
sayColor.call(o);         // blue

// 对象方法中this对象默认指向对象自身
o.sayColor()             // blue
// 显式地指定方法中的this对象指向window
o.sayColor.call(this)    // red
o.sayColor.call(window)  // red
o.sayColor.apply(this)   // red
o.sayColor.apply(window) // red
```

**使用`call()`（或`apply()`）来扩充作用域的最大好处，就是对象不需要与方法有任何耦合关系。**

`bind()`方法的使用：

```js
window.color = 'red';
    var o = {
    color: 'blue'
};
function sayColor() {
    console.log(this.color);
}

let objectSayColor = sayColor.bind(o); //调用bind()并传入对象o
// 即使是在全局作用域中调用bjectSayColor(), 其函数体中的this值也等于o
objectSayColor(); // blue
```



## 10.11 函数表达式

理解函数提升的关键，就是理解函数声明与函数表达式之间的区别。

例如，以下代码在ECMAScript中属于无效语法，JavaScript引擎会尝试修正错误，将其转换为合理的状态。但问题是浏览器尝试修正错误的做法并不一致，这会导致问题。

```js
//不要这样做！ 
if (condition) {
    function sayHi() {
        console.log('Hi!');
    }
} else {
    function sayHi() {
        console.log('Yo!');
    }
}
```

如果是使用函数表达式，那就没有什么问题了，不同的函数会根据`condition`被赋值给`sayHi`：

```js
//可以这样做
var sayHi;

if (condition) {
    sayHi = function() {
        console.log("Hi!");
    };
} else {
    sayHi = function() {
        console.log("Yo!");
    };
}
```

能够创建函数再赋值给变量，也就能够把函数作为其他函数的值返回：

```js
function createComparisonFunction(propertyName) {

    return function(object1, object2){
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];

        if (value1 < value2){
            return -1;
        } else if (value1 > value2){
            return 1;
        } else {
            return 0;
        }
    };
}
```



## 10.12 递归

递归函数是在一个函数通过名字调用自身的情况下构成的，如下所示：

```js
function factorial(num){
    if (num <= 1){
        return 1;
    } else {
        return num * factorial(num-1);
    }
}
```

`arguments.callee`是一个指向正在执行的函数的指针，因此可以用它来实现对函数的递归调用，例如：

```js
function factorial(num){
    if (num <= 1){
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}
```

但在严格模式下，不能通过脚本访问`arguments.callee`，访问这个属性会导致错误。不过，可以使用命名函数表达式来达成相同的结果。例如：

```js
var factorial = (function f(num){
    if (num <= 1){ 
        return 1;
    } else {
        return num * f(num-1);
    }
});
```



## 10.13 尾调用优化

## 10.14 闭包

**闭包**是指有权访问另一个函数作用域中的变量的函数。

有关如何创建作用域链以及作用域链有什么作用的细节，对彻底理解闭包至关重要。当某个函数被调用时，会创建一个执行环境（execution context）及相应的作用域链。然后，使用`arguments`和其他命名参数的值来初始化函数的活动对象（activation object）。但在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位，……直至作为作用域链终点的全局执行环境。

在另一个函数内部定义的函数会将包含函数（即外部函数）的活动对象添加到它的作用域链中。

> 注意！由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。过度使用闭包可能会导致内存占用过多，我们建议读者只在绝对必要时再考虑使用闭包。虽然像V8等优化后的JavaScript引擎会尝试回收被闭包占用的内存，但请大家还是要慎重使用闭包。

### 10.14.1 闭包与变量

作用域链的这种配置机制引出了一个值得注意的副作用，即闭包只能取得包含函数中任何变量的最后一个值。

下面这个函数会返回一个函数数组。表面上看，似乎每个函数都应该返自己的索引值，即位置0的函数返回0，位置1的函数返回1，以此类推。但实际上，每个函数都返回10。因为每个函数的作用域链中都保存着`createFunctions()`函数的活动对象，所以它们引用的都是同一个变量`i`。当`createFunctions()`函数返回后，变量`i`的值是10，此时每个函数都引用着保存变量`i`的同一个变量对象，所以在每个函数内部`i`的值都是10。

```js
function createFunctions(){
    var result = new Array();
    for (var i=0; i < 10; i++){
        result[i] = function(){
            return i;
        };
    }
    return result;
}

createFunctions().forEach((item, index, arr)=> {console.log(item());})
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
```

我们可以通过创建另一个匿名函数强制让闭包的行为符合预期，如下所示：

```js
function createFunctions(){
    var result = new Array();
    for (var i=0; i < 10; i++){
        result[i] = function(num){
            return function(){
                return num;
            };
        }(i);
    }
    return result;
}

createFunctions().forEach((item, index, arr)=> {console.log(item());})
// 0
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
```

但是，建议使用`let`将每个变量`i`声明为不同的变量，这样每个闭包引用的就不再是同一个变量：

```js
function createFunctions(){
    var result = new Array();
    for (let i=0; i < 10; i++){ // 使用let
        result[i] = function(){
            return i;
        };
    }
    return result;
}

createFunctions().forEach((item, index, arr)=> {console.log(item());})
// 0
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
```



### 10.14.2 `this`对象

`this`对象是在运行时基于函数的执行环境绑定的：在全局函数中，`this`等于`window`，而当函数被作为某个对象的方法调用时，`this`等于那个对象。不过，匿名函数的执行环境具有全局性，因此其`this`对象通常指向`window`。

```js
window.identity = 'The Window';

let object = {
    identity: 'My Object',
    getIdentityFunc() {
        return function() {
            return this.identity; 
        };
    }
};

console.log(object.getIdentityFunc()()); // 'The Window'
// 为什么匿名函数没有取得其包含作用域（或外部作用域）的this对象呢？
```

由于每个函数都有自己的`this`对象，所以闭包会在作用域链中优先搜索到自己的`this`对象，所以闭包不会继续搜索外部函数的`this`对象并引用它；而匿名函数的`this`对象通常指向`window`，所以上例中闭包返回了自己的`this.identity`，即`window.identity`。把外部作用域中的`this`对象保存在一个闭包能够访问到的变量里，就可以让闭包访问该对象：

```js
window.identity = 'The Window';
let object = {
    identity: 'My Object',
    getIdentityFunc() {
        let that = this;
        return function() {
            return that.identity;
        };
    }
};
console.log(object.getIdentityFunc()()); // 'My Object'
```



### 10.14.3 内存泄漏

由于IE9之前的版本对JScript对象和COM对象使用不同的垃圾收集例程，因此闭包在IE的这些版本中会导致一些特殊的问题。具体来说，如果闭包的作用域链中保存着一个HTML元素，那么就意味着该元素将无法被销毁。

最好的办法是，手动解除对DOM对象的引用，如下所示：

```js
function assignHandler() {
    let element = document.getElementById('someElement');
    let id = element.id;
    element.onclick = () => console.log(id);
    element = null; // 解除对DOM对象的引用，顺利地减少其引用数，确保正常回收其占用的内存。
}
```

记住，闭包会引用包含函数的整个活动对象，而其中包含着`element`。即使闭包不直接引用`element`，包含函数的活动对象中也仍然会保存一个引用。因此，有必要把`element`变量设置为`null`。这样就能够解除对DOM对象的引用，顺利地减少其引用数，确保正常回收其占用的内存。

## 10.15 立即调用的函数表达式 (IMMEDIATELY INVOKED FUNCTION EXPRESSIONS, IIFE)



