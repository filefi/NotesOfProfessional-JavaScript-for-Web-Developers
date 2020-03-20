# 第5章 基本引用类型

## 5.5　`Function`类型

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

### 5.5.1 JavaScript函数没有重载

声明了两个形参不同的同名函数，结果是后面的函数覆盖了前面的函数：

```js
function addSomeNumber(num){
    return num + 100;
}

function addSomeNumber(num1, num2) {
    return num1 + num2;
}

var result = addSomeNumber(100, 200); //300
```

### 5.5.2　函数声明与函数表达式

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

### 5.5.3　作为值的函数

ECMAScript中的函数名本身就是变量，所以函数也可以作为值来使用。

```js
function callSomeFunction(someFunction, someArgument){
    return someFunction(someArgument);
}
```

### 5.5.4　函数内部属性

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

### 5.5.5　函数属性和方法

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

