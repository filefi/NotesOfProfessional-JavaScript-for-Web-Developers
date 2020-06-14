# 第8章 面向对象程序设计

ECMA-262把对象定义为：“无序属性的集合，其属性可以包含基本值、对象或者函数。” 严格来讲，这就相当于说对象是一组没有特定顺序的值。对象的每个属性或方法都有一个名字，而每个名字都映射到一个值。

## 8.1 理解对象

创建自定义对象的最简单方式就是创建一个`Object`的实例，然后再为它添加属性和方法：

```js
let person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";
person.sayName = function() {
    console.log(this.name);
};
```

对象字面量现在是创建这种对象的首选模式，前面的例子用对象字面量语法可以写成这样：

```js
let person = {
    name: "Nicholas",
    age: 29,
    job: "Software Engineer",
    sayName() {
        console.log(this.name);
    }
};
```

### 8.1.1 属性类型

ECMA-262在定义只有内部才用的特性（attribute）时，描述了属性（property）的各种特征。ECMA-262定义这些特性是为了实现JavaScript引擎用的，因此在JavaScript中不能直接访问它们。为了表示特性是内部值，该规范把它们放在了两对儿方括号中，例如`[[Enumerable]]`。

ECMAScript中有2种属性：

- 数据属性（data properties）
- 访问器属性（accessor properties）

#### 1.数据属性

**数据属性** 包含一个数据值的位置。在这个位置可以 **读取 **和 **写入值** 。数据属性有4个描述其行为的特性：

- `[[Configurable]]`：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为`true`。
- `[[Enumerable]]`：表示能否通过`for-in`循环返回属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为`true`。
- `[[Writable]]`：表示能否修改属性的值。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为`true`。
- `[[Value]]`：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。这个特性的默认值为`undefined`。

对于像前面例子中那样直接在对象上定义的属性，它们的`[[Configurable]]`、`[[Enumerable]]`和`[[Writable]]`特性都被设置为`true`，而`[[Value]]`特性被设置为指定的值。

```js
let person = {
    name: "Nicholas"
};
```

**要修改属性默认的特性，必须使用`Object.defineProperty()`方法。** 这个方法接收3个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符（descriptor）对象的属性必须是：`configurable`、`enumerable`、`writable`和`value`。设置其中的一或多个值，可以修改对应的特性值。例如：

```js
let person = {};
Object.defineProperty(person, "name", {
    writable: false,
    value: "Nicholas"
});
console.log(person.name); // "Nicholas"
person.name = "Greg";
console.log(person.name); // "Nicholas"
```

类似的规则也适用于不可配置的属性：

```js
let person = {};
Object.defineProperty(person, "name", {
    configurable: false,    //把configurable设置为false，表示不能从对象中删除属性。
    value: "Nicholas"
});
console.log(person.name); // "Nicholas"
delete person.name;
console.log(person.name); // "Nicholas"
```

而且，一旦把属性定义为不可配置的，就不能再把它变回可配置了。此时，再调用`Object.defineProperty()`方法修改除`writable`之外的特性，都会导致错误：

```js
let person = {};
Object.defineProperty(person, "name", {
    configurable: false,
    value: "Nicholas"
});

// 抛出错误
Object.defineProperty(person, "name", {
    configurable: true,
    value: "Nicholas"
});
```

在调用`Object.defineProperty()`方法时，如果不指定，`configurable`、`enumerable`和`writable`特性的默认值都是`false`。

#### 2.访问器属性

访问器属性不包含数据值；它们包含一对儿getter和setter函数（不过，这两个函数都不是必需的）。在读取访问器属性时，会调用getter函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter函数并传入新值，这个函数负责决定如何处理数据。访问器属性有如下4个特性。

- `[[Configurable]]`：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为`true`。
- `[[Enumerable]]`：表示能否通过`for-in`循环返回属性。对于直接在对象上定义的属性，这个特性的默认值为`true`。
- `[[Get]]`：在读取属性时调用的函数。默认值为`undefined`。
- `[[Set]]`：在写入属性时调用的函数。默认值为`undefined`。

**访问器属性不能直接定义，必须使用`Object.defineProperty()`来定义：** 

```js
// Define object with pseudo-private member 'year_'
// and public member 'edition'
let book = {
    year_: 2017, //year_的下划线是一种常用的记号，用于表示只能通过对象方法访问的属性。
    edition: 1
};

Object.defineProperty(book, "year", {
    get() {
        return this.year_;
    },
    set(newValue) {
        if (newValue > 2017) {
            this.year_ = newValue;
            this.edition += newValue - 2017;
        }
    }
});

book.year = 2018;
console.log(book.edition); // 2
```

**不一定非要同时指定getter和setter。** 只指定getter意味着属性是不能写，尝试写入属性会被忽略。在严格模式下，尝试写入只指定了getter函数的属性会抛出错误。类似地，只指定setter函数的属性也不能读，否则在非严格模式下会返回`undefined`，而在严格模式下会抛出错误。

### 8.1.2 定义多个属性

由于为对象定义多个属性的可能性很大，ECMAScript提供了一个`Object.defineProperties()`方法。利用这个方法可以通过描述符一次定义多个属性。这个方法接收2个对象作为参数：第1个对象是要添加和修改其属性的对象，第2个对象的属性与第1个对象中要添加或修改的属性一一对应。

```js
let book = {};

Object.defineProperties(book, {
    year_: {
        value: 2017
    },
    edition: {
        value: 1
    },
    year: {
        get() {
            return this.year_;
        },
        set(newValue) {
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue - 2017;
            }
        }
    }
});
```

### 8.1.3　读取属性的特性

使用`Object.getOwnPropertyDescriptor()`方法，可以取得给定属性的描述符。这个方法接收2个参数：属性所在的对象和要读取其描述符的属性名称。返回值是一个对象，如果是访问器属性，这个对象的属性有`configurable`、`enumerable`、`get`和`set`；如果是数据属性，这个对象的属性有`configurable`、`enumerable`、`writable`和`value`。

```js
let book = {};

Object.defineProperties(book, {
    year_: {
        value: 2017
    },
    edition: {
        value: 1
    },
    year: {
        get: function() {
            return this.year_;
        },
        set: function(newValue){
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue - 2017;
            }
        }
    }
});

let descriptor = Object.getOwnPropertyDescriptor(book, "year_");
console.log(descriptor.value); // 2017
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // "undefined"

let descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value); // undefined
console.log(descriptor.enumerable); // false
console.log(typeof descriptor.get); // "function"
```

New in ECMAScript 2017 is the `Object.getOwnPropertyDescriptors()` static method. This method effectively performs on `Object.getOwnPropertyDescriptor()` on all own properties and returns them in a new object. For the previous example, using this static method would return the following object:

```js
let book = {};

Object.defineProperties(book, {
    year_: {
        value: 2017
    },
    edition: {
        value: 1
    },
    year: {
        get: function() {
            return this.year_;
        },
        set: function(newValue){
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue - 2017;
            }
        }
    }
});

console.log(Object.getOwnPropertyDescriptors(book));
// {
//     edition: {
//         configurable: false,
//         enumerable: false,
//         value: 1,
//         writable: false
//     },
//     year: {
//         configurable: false,
//         enumerable: false,
//         get: f(),
//         set: f(newValue),
//     },
//     year_: {
//         configurable: false,
//         enumerable: false,
//         value: 2019,
//         writable: false
//     }
// }
```



### 8.1.4 Merging Objects

### 8.1.5 Object Identity and Equality

### 8.1.6 Enhanced Object Syntax

### 8.1.7 Object Destructuring



## 8.2 创建对象

虽然`Object`构造函数或对象字面量都可以用来创建单个对象，但这些方式有个明显的 **缺点** ： **使用同一个接口创建很多对象，会产生大量的重复代码。**

### 8.2.1 概述

通过连续的规范，ECMAScript的可用功能遵循了非常不寻常的模式。 通过ECMAScript 5.1规范，没有对类或继承等面向对象的构造的正式支持。 但是，正如您在以下各节中将看到的那样，原型继承的巧妙应用使JavaScript开发人员能够成功模仿这种行为。

通过ECMAScript 6规范，引入了对类和继承的正式支持。 这些ES6类旨在完全包含先前规范中设计的基于原型的类解决方案。 但是，它们的实现在许多方面仅是ES5.1样式的构造函数和原型继承的语法抽象。

### 8.2.2 工厂模式

工厂模式抽象了创建具体对象的过程。这是一种用函数来封装以特定接口创建对象的方法：

```js
function createPerson(name, age, job) {
    let o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        console.log(this.name);
    };
    return o;
}

let person1 = createPerson("Nicholas", 29, "Software Engineer");
let person2 = createPerson("Greg", 27, "Doctor");
```

函数`createPerson()`能够根据接受的参数来构建一个包含所有必要信息的`Person`对象。可以无数次地调用这个函数，而每次它都会返回一个包含三个属性一个方法的对象。

**工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。**



### 8.2.3 构造函数模式

ECMAScript中的构造函数可用来创建特定类型的对象。我们也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。

例如，可以使用构造函数模式将前面的例子重写如下。

```js
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        console.log(this.name);
    };
}

// 创建2个Person的不同实例：person1和person2
let person1 = new Person("Nicholas", 29, "Software Engineer");
let person2 = new Person("Greg", 27, "Doctor");
person1.sayName(); // Nicholas
person2.sayName(); // Greg

// 这两个对象都有一个constructor（构造函数）属性，该属性指向Person
console.log(person1.constructor == Person); // true
console.log(person2.constructor == Person); // true

// 这两个对象既是Object的实例，同时也是Person的实例
console.log(person1 instanceof Object); // true
console.log(person1 instanceof Person); // true
console.log(person2 instanceof Object); // true
console.log(person2 instanceof Person); // true
```

在这个例子中，`Person()`函数取代了`createPerson()`函数。我们注意到，`Person()`中的代码除了与`createPerson()`存在以下不同之处：

- 没有显式地创建对象；
- 直接将属性和方法赋给了`this`对象；
- 没有`return`语句。

按照惯例，*构造函数* 始终都应该以一个大写字母开头，而 *非构造函数* 则应该以一个小写字母开头。

**要创建`Person`的新实例，必须使用`new`操作符。以这种方式调用构造函数实际上会经历以下5个步骤：**

1. 在内存中创建一个新对象；
2. 将新对象内部的`[[Prototype]]`指针赋给构造函数的`prototype`属性；
3. 将构造函数中的`this`值赋给新对象（因此`this`就指向了这个新对象）；
4. 执行构造函数中的代码（为这个新对象添加属性）；
5. 除非构造函数显式地返回一个其他非空值；否则，将返回新创建的对象。

