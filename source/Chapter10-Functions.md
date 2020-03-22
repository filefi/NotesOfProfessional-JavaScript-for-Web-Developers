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
var sum = function(num1, num2){
    return num1 + num2;
};
```

- 使用`Function`构造函数：

```js
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

使用`arguments`的`length`属性来获得传入参数的个数：

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

Strict mode makes several changes to how the arguments object can be used. First, assignment, as in the previous example, no longer works. The value of `num2` remains `undefined` even though `arguments[1]` has been assigned to 10. Second, trying to overwrite the value of `arguments` is a syntax error. (The code will not execute.)




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


## 10.6 多余参数和剩余参数


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