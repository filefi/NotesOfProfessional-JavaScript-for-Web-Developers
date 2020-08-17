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