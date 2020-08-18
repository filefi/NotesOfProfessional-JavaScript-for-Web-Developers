# 第21章 错误处理与调试
## 21.1 浏览器报告的错误

### 21.1.1 Desktop Consoles

### 21.1.2 Mobile Consoles

## 21.2 错误处理

### 21.2.1 `try-catch`语句

ECMA-262第3版引入了`try-catch`语句，作为JavaScript中处理异常的一种标准方式。基本的语法如下所示，这与Java中的`try-catch`语句是完全相同的。

```js
try{
    // 可能会导致错误的代码
} catch(error){
    // 在错误发生时怎么处理
}
```

也就是说，我们应该把所有可能会抛出错误的代码都放在`try`语句块中，而把那些用于错误处理的代码放在`catch`块中。例如：

```js
try {
    window.someNonexistentFunction();
} catch (error){
    console.log(error.message);
}
```

如果`try`块中的任何代码发生了错误，就会立即退出代码执行过程，然后接着执行`catch`块。此时，`catch`块会接收到一个包含错误信息的对象。这个对象中包含的实际信息会因浏览器而异，但共同的是有一个保存着错误消息的`message`属性。ECMA-262还规定了一个保存错误类型的`name`属性。`message`属性是唯一一个能够保证所有浏览器都支持的属性，除此之外，IE、Firefox、Safari、Chrome以及Opera都为事件对象添加了其他相关信息。

#### `finally`子句

虽然在`try-catch`语句中是可选的，但`finally`子句一经使用，其代码无论如何都会执行。换句话说，`try`语句块中的代码全部正常执行，`finally`子句会执行；如果因为出错而执行了`catch`语句块，`finally`子句照样还会执行。

```js
/*要代码中包含finally子句，则无论try或catch语句块中包含什么代码*/

function testFinally(){
    try {
        return 2;
    } catch (error){
        return 1;
    } finally {
        return 0;
    }
}
```

> 记住，只要代码中包含**`finally`**子句，那么无论**`try`**还是**`catch`**语句块中的**`return`**语句都将被忽略。

#### 错误类型

执行代码期间可能会发生的错误有多种类型。每种错误都有对应的错误类型，而当错误发生时，就会抛出相应类型的错误对象。ECMA-262定义了下列7种错误类型：

- `Error` ：`Error`是基类型，其他错误类型都继承自该类型。这个基类型的主要目的是供开发人员抛出自定义错误。
- `EvalError` ：`EvalError`类型的错误会在使用`eval()`函数而发生异常时被抛出。简单地说，如果没有把`eval()`当成函数调用，就会抛出错误。
- `RangeError` ：`RangeError`类型的错误会在数值超出相应范围时触发。
- `ReferenceError` ：在找不到对象的情况下，会发生`ReferenceError`（这种情况下，会直接导致人所共知的`"object expected"`浏览器错误）。
- `SyntaxError` ：语法错误。
- `TypeError` ：`TypeError`类型在JavaScript中会经常用到，在变量中保存着意外的类型时，或者在访问不存在的方法时，都会导致这种错误。错误的原因虽然多种多样，但归根结底还是由于在执行特定于类型的操作时，变量的类型并不符合要求所致。
- `URIError` ：在使用`encodeURI()`或`decodeURI()`，而URI格式不正确时，就会导致`URIError`错误。

利用不同的错误类型，可以获悉更多有关异常的信息，从而有助于对错误作出恰当的处理。要想知道错误的类型，可以像下面这样在`try-catch`语句的`catch`语句中使用`instanceof`操作符。

```js
try {
    someFunction();
} catch (error){
    if (error instanceof TypeError){
        // handle type error
    } else if (error instanceof ReferenceError){
        // handle reference error
    } else {
        // handle all other error types
    }
}
```

在跨浏览器编程中，检查错误类型是确定处理方式的最简便途径；包含在`message`属性中的错误消息会因浏览器而异。

#### `try-catch`的使用

使用`try-catch`最适合处理那些我们无法控制的错误。在明确知道自己的代码会发生错误时，再使用`try-catch`语句就不太合适了。在这种情况下，不应用使用`try-catch`语句。

### 21.2.2 抛出错误

与`try-catch`语句相配的还有一个`throw`操作符，用于随时抛出自定义错误。在遇到`throw`操作符时，代码会立即停止执行。仅当有`try-catch`语句捕获到被抛出的值时，代码才会继续执行。

抛出错误时，必须要给`throw`操作符指定一个值，这个值是什么类型，没有要求。下列代码都是有效的：

```js
throw 12345;
throw "Hello world!";
throw true;
throw { name: "JavaScript"};
```

通过使用某种内置错误类型，可以更真实地模拟浏览器错误。每种错误类型的构造函数接收一个参数，即实际的错误消息。下面是一个例子：

```js
throw new Error("Something bad happened.");   // 这行代码抛出了一个通用错误，带有一条自定义错误消息。

throw new SyntaxError("I don’t like your syntax.");
throw new TypeError("What type of variable do you take me for?");
throw new RangeError("Sorry, you just don’t have the range.");
throw new EvalError("That doesn’t evaluate.");
throw new URIError("Uri, is that you?");
throw new ReferenceError("You didn’t cite your references properly.");
```

另外，利用原型链还可以通过继承`Error`来创建自定义错误类型。此时，需要为新创建的错误类型指定`name`和`message`属性。来看一个例子：

```js
class CustomError extends Error {
    constructor(message) {
        super(message);
        this.name = "CustomError";
        this.message = message;
    }
}

throw new CustomError("My message");
```

浏览器对待继承自`Error`的自定义错误类型，就像对待其他错误类型一样。如果要捕获自己抛出的错误并且把它与浏览器错误区别对待的话，创建自定义错误是很有用的。

#### 抛出错误的时机

应该在出现某种特定的已知错误条件，导致函数无法正常执行时抛出错误。换句话说，浏览器会在某种特定的条件下执行函数时抛出错误。例如，下面的函数会在参数不是数组的情况下会抛出异常：

```js
function process(values){
    if (!(values instanceof Array)){
        throw new Error("process(): Argument must be an array.");
    }
    values.sort();
    for (let value of values){
        if (value > 100){
            return value;
        }
    }
    return -1;
}
```

#### 抛出错误VS使用`try-catch`

我们认为只应该捕获那些你确切地知道该如何处理的错误。捕获错误的目的在于避免浏览器以默认方式处理它们；而抛出错误的目的在于提供错误发生具体原因的消息。

### 21.2.3 `error`事件

任何没有通过`try-catch`处理的错误都会触发`window`对象的`error`事件。

在任何Web浏览器中，`onerror`事件处理程序都不会创建`event`对象，但它可以接收三个参数：错误消息、错误所在的URL和行号。要指定`onerror`事件处理程序，必须使用如下所示的DOM0级技术，它没有遵循“DOM2级事件”的标准格式：

```js
// 只要发生错误，无论是不是浏览器生成的，都会触发error事件，并执行这个事件处理程序。
window.onerror = (message, url, line) => {
    console.log(message);
    return false; // 返回false以阻止浏览器报告错误的默认行为，否则浏览器默认行为依然会生效；
};
```

图像也支持`error`事件。只要图像的`src`特性中的URL不能返回可以被识别的图像格式，就会触发`error`事件。此时的`error`事件遵循DOM格式，会返回一个以图像为目标的`event`对象：

```js
const image = new Image();

image.addEventListener("load", (event) => {
    console.log("Image loaded!");
});

image.addEventListener("error", (event) => {
    console.log("Image not loaded!");
});

image.src = "doesnotexist.gif"; // does not exist, resoure will fail to load
```

### 21.2.4 错误处理策略

作为开发人员，必须要知道代码何时可能出错，会出什么错，同时还要有一个跟踪此类问题的系统。

### 21.2.5 常见的错误类型

错误处理的核心，是首先要知道代码里会发生什么错误。由于JavaScript是松散类型的，而且也不会验证函数的参数，因此错误只会在代码运行期间出现。一般来说，需要关注三种错误：

- **类型转换错误** ：类型转换错误发生在使用某个操作符，或者使用其他可能会自动转换值的数据类型的语言结构时。在使用相等`==`和不相等`!=`操作符，或者在`if`、`for`及`while`等流控制语句中使用非布尔值时，最常发生类型转换错误。 **多数情况下，我们建议使用全等`===`和不全等`!==`操作符，并在条件比较时切实传入布尔值，以避免类型转换。**
- **数据类型错误** ：JavaScript是松散类型的，也就是说，在使用变量和函数参数之前，不会对它们进行比较以确保它们的数据类型正确。为了保证不会发生数据类型错误，只能依靠开发人员编写适当的数据类型检测代码。大体上来说，基本类型的值应该使用`typeof`来检测，而对象的值则应该使用`instanceof`来检测。
- **通信错误** ：在使用Ajax编程时，JavaScript与服务器之间的任何一次通信，都有可能会产生错误。

以上错误分别会在特定的模式下或者没有对值进行足够的检查的情况下发生。

### 21.2.6 区分致命错误和非致命错误

任何错误处理策略中最重要的一个部分，就是确定错误是否致命。

对于非致命错误，可以根据下列一或多个条件来确定：

- 不影响用户的主要任务；
- 只影响页面的一部分；
- 可以恢复；
- 重复相同操作可以消除错误。

致命错误，可以通过以下一或多个条件来确定：

- 应用程序根本无法继续运行；
- 错误明显影响到了用户的主要操作；
- 会导致其他连带错误。

在发生致命错误时，应该立即给用户发送一条消息，告诉他们无法再继续手头的事情了。假如必须刷新页面才能让应用程序正常运行，就必须通知用户，同时给用户提供一个点击即可刷新页面的按钮。

### 21.2.7 把错误记录到服务器

在复杂的Web应用程序中，我们推荐你把JavaScript错误也回写到服务器。把前后端的错误集中起来，能够极大地方便对数据的分析。

要建立这样一种JavaScript错误记录系统，首先需要在服务器上创建一个页面（或者一个服务器入口点），用于处理错误数据。这个页面的作用无非就是从查询字符串中取得数据，然后再将数据写入错误日志中。这个页面可能会使用如下所示的函数：

```js
// 接收两个参数：表示严重程度的数值或字符串（视所用系统而异）及错误消息。
function logError(sev, msg) {
    let img = new Image(),
    encodedSev = encodeURIComponent(sev),
    encodedMsg = encodeURIComponent(msg);
    img.src = 'log.php?sev=${encodedSev}&msg=${encodedMsg}';
}
```

使用了`Image`对象来发送请求，这样做非常灵活，主要表现如下几方面：

- 所有浏览器都支持`Image`对象，包括那些不支持`XMLHttpRequest`对象的浏览器。
- 可以避免跨域限制。通常都是一台服务器要负责处理多台服务器的错误，而这种情况下使用`XMLHttpRequest`是不行的。
- 在记录错误的过程中出问题的概率比较低。大多数Ajax通信都是由JavaScript库提供的包装函数来处理的，如果库代码本身有问题，而你还在依赖该库记录错误，可想而知，错误消息是不可能得到记录的。

使用`try-catch`语句把相应错误记录到日志中：

```js
for (let mod of mods){
    try {
        mod.init();
    } catch (ex){
        logError("nonfatal", 'Module init failed: ${ex.message}');
    }
}
```



## 21.3 调试技术

### 21.3.1 将消息记录到控制台

所有主流的浏览器都有JavaScript控制台，可以用来查看JavaScript错误，也可以通过`console`对象向JavaScript控制台输出消息，主要通过使用以下方法：

- `error(message)`：将错误消息记录到控制台；
- `info(message)`：将信息性消息记录到控制台；
- `log(message)`：将一般消息记录到控制台；
- `warn(message)`：将警告消息记录到控制台；

### 21.3.2 Understanding the Console Runtime

### 21.3.3 Using the JavaScript Debugger

### 21.3.4 将消息记录到当前页面

另一种输出调试消息的方式，就是在页面中开辟一小块区域，用以显示消息。

```js
function log(message) {
    // Lexical scope of this function will use the following instance
    // instead of window.console
    const console = document.getElementById("debuginfo");
    if (console === null){
        console = document.createElement("div");
        console.id = "debuginfo";
        console.style.background = "#dedede";
        console.style.border = "1px solid silver";
        console.style.padding = "5px";
        console.style.width = "400px";
        console.style.position = "absolute";
        console.style.right = "0px";
        console.style.top = "0px";
        document.body.appendChild(console);
    }
    console.innerHTML += '<p> ${message}</p>';
}
```

### 21.3.5 Shimming Console Methods

### 21.3.6 抛出错误

如前所述，抛出错误也是一种调试代码的好办法。如果错误消息很具体，基本上就可以把它当作确定错误来源的依据。

对于大型应用程序来说，自定义的错误通常都使用`assert()`函数抛出。这个函数接受两个参数，一个是求值结果应该为`true`的条件，另一个是条件为`false`时要抛出的错误。以下就是一个非常基本的`assert()`函数：

```js
function assert(condition, message){
    if (!condition){
        throw new Error(message);
    }
}
```

下面是`assert()`函数的使用方法：

```js
function divide(num1, num2){
    assert(typeof num1 == "number" && typeof num2 == "number",
           "divide(): Both arguments must be numbers.");
    return num1 / num2;
}
```

## 21.4 常见的IE错误

略

