# 第6章 集合引用类型（Collection Reference Types）

## 6.1 `Object`类型

大多数引用类型值都是`Object`类型的实例。创建`Object`实例的方式有两种。第1种是使用`new`操作符后跟`Object`构造函数，如下所示：

```js
let person = new Object();
person.name = "Nicholas";
person.age = 29;
```

第2中方式是使用对象字面量表示法：

```js
let person = {
    name: "Nicholas",
    age: 29
};
```

使用对象字面量语法时，如果留空其花括号，则可以定义只包含默认属性和方法的对象：

```js
let person = {};         //与new Object()相同
person.name = "Nicholas";
person.age = 29;
```

在JavaScript中，访问对象属性时可以使用点表示法，也可以使用方括号表示法来访问对象的属性。在使用方括号语法时，应该将要访问的属性以字符串的形式放在方括号中：

```js
console.log(person["name"]);          //"Nicholas"
console.log(person.name);             //"Nicholas"
```

## 6.2　`Array`类型

与其他语言中的数组类似，ECMAScript的数组也是数据的有序列表。但与其他语言不同的是，ECMAScript数组的每一项可以保存任何不同类型的数据。

### 6.2.1 创建数组

第1种创建数组的基本方式是使用`Array`构造函数：

```js
let colors = new Array();
```

如果预先知道数组要保存的项目数量，也可以给构造函数传递该数量，而该数量会自动变成`length`属性的值。例如，下面的代码将创建`length`值为20的数组：

```js
let colors = new Array(20);
```

也可以向`Array`构造函数传递数组中应该包含的项。以下代码创建了一个包含3个字符串值的数组：

```js
let colors = new Array("red", "blue", "green");
```

第2种创建数组的基本方式是使用数组字面量表示法。数组字面量由一对包含数组项的方括号表示，多个数组项之间以逗号隔开，如下所示：

```js
let colors = ["red", "blue", "green"];   // 创建一个包含3个字符串的数组
let names = [];                          // 创建一个空数组
let values = [1,2,];                     // Creates an array with 2 items
```

在ES6中，`Array`构造函数还具有2个其他的静态方法来创建数组： 
- `from()` ：用于将类似数组的构造转换为数组实例。第1个参数是一个可迭代对象，或者具有`length`属性和元素可被索引的对象。第2个参数（可选）接受一个map函数。
- `of()`：用于将`arguments`参数对象转换为数组实例。在ES6之前，这一功能通常使用`Array.prototype.slice.call(arguments)`来实现。

```js
// Strings will be broken up into an array of single characters
alert(Array.from("Matt")); // ["M", "a", "t", "t"]

// Sets and Maps can be converted into an new array instance using from()
const m = new Map().set(1, 2).set(3, 4);
const s = new Set().add(1).add(2).add(3).add(4);
alert(Array.from(m)); // [[1, 2], [3, 4]]
alert(Array.from(s)); // [1, 2, 3, 4]

// Array.from() performs a shallow copy of an existing array
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1);
alert(a1); // [1, 2, 3, 4]
alert(a1 === a2); // false

// Any iterable object can be used
const iter = {
    *[Symbol.iterator]() {
        yield 1;
        yield 2;
        yield 3;
        yield 4;
    }
};
alert(Array.from(iter)); // [1, 2, 3, 4]

// The arguments object can now easily be casted into an array:
function getArgsArray() {
    return Array.from(arguments);
}
alert(getArgsArray(1, 2, 3, 4)); // [1, 2, 3, 4]

// from() will happily use a custom object with required properties
const arrayLikeObject = {
    0: 1,
    1: 2,
    2: 3,
    3: 4,
    length: 4
};
alert(Array.from(arrayLikeObject)); // [1, 2, 3, 4]
```

`from()`方法的第2个参数（可选）接受一个map函数。This allows you to augment the new array’s values without creating an intermediate array first, which is the case if the same were performed with `Array.from().map()`. A third optional argument specifies the value of `this` inside the map function. The overridden `this` value is not applied inside an arrow function:

```js
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1, x => x**2);
const a3 = Array.from(a1, function(x) {return x**this.exponent}, {exponent: 2});
alert(a2); // [1, 4, 9, 16]
alert(a3); // [1, 4, 9, 16]
```

`of()`方法用于将`arguments`参数对象转换为数组实例。在ES6之前，这一功能通常使用`Array.prototype.slice.call(arguments)`来实现：

```js
alert(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4]
alert(Array.of(undefined)); // [undefined]
```

### 6.2.2 Array Holes

使用带有连续逗号`,`的数组字面量初始化一个数组，允许你创建"holes"。ECMAScript将逗号之间的索引处的值视为一个hole，ES6规范完善了如何处理这些hole。如下创建了一个洞的数组：

```js
const options = [,,,,,];  // Creates an array with 5 items
alert(options.length);    // 5
alert(options);           // [,,,,,]
```

Methods and iterators introduced in ES6 behave differently than methods present in earlier ECMAScript editions. ES6 additions will universally treat the holes as an existing entry with a value of `undefined`:

```js
const options = [1,,,,5];
for (const option of options) {
    alert(option === undefined);
}
// false
// true
// true
// true
// false

const a = Array.from([,,,]); // Array of 3 holes created with ES6's Array.from()
for (const val of a) {
    alert(val === undefined);
}
// true
// true
// true

alert(Array.of(...[,,,])); // [undefined, undefined, undefined]
for (const [index, value] of options.entries()) {
    alert(value);
}
// 1
// undefined
// undefined
// undefined
// 5
```

Conversely, methods available before ES6 will tend to ignore the holes, although exact behavior can vary slightly between methods:

```js
const options = [1,,,,5];

// map() will skip the holes entirely
alert(options.map(() => 6)); // [6, undefined, undefined, undefined, 6]

// join() treats holes as empty strings
alert(options.join('-')); // "1----5"
```

> Due to their bizarre behavior and performance issues, avoid using array holes in your code. Prefer to use an explicit `undefined` in place of a hole.

### 6.2.3 Indexing into Arrays

第4版内容

### 6.2.4 检测数组

ECMAScript 5新增了`Array.isArray()`方法。这个方法的最终目的是确定某个值到底是不是数组，而不管它是在哪个全局执行环境中创建的：

```js
if (Array.isArray(value)){
    //对数组执行某些操作
}
```

### 6.2.5 迭代器方法

第4版内容

### 6.2.6 复制和填充方法 （Copy and Fill Methods）

第4版内容

### 6.2.7 转换方法

