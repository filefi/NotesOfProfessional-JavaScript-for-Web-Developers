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

## 6.2 `Array`类型

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

In ES6, three new methods are exposed on the Array prototype that allow you to inspect the contents of an array: 

- `keys()`: return an iterator of the array’s indices.
- `values()`: return an iterator of the array’s elements.
- `entries()`: return an iterator of index/value pairs.

```js
const a = ["foo", "bar", "baz", "qux"];

// Because these methods return iterators, you can funnel their contents
// into array instances using Array.from()
const aKeys = Array.from(a.keys());
const aValues = Array.from(a.values());
const aEntries = Array.from(a.entries());

alert(aKeys); // [0, 1, 2, 3]
alert(aValues); // ["foo", "bar", "baz", "qux"]
alert(aEntries); // [[0, "foo"], [1, "bar"], [2, "baz"], [3, "qux"]]
```


ES6 destructuring means it is now very easy to split out the key/value pairs inside a loop:

```js
const a = ["foo", "bar", "baz", "qux"];
for (const [idx, element] of a.entries()) {
    alert(idx);
    alert(element);
}
// 0
// foo
// 1
// bar
// 2
// baz
// 3
// qux
```

### 6.2.6 复制和填充方法 （Copy and Fill Methods）

第4版内容

### 6.2.7 转换方法

- `valueOf()`方法：此方法调用数组的`valueOf()`返回的还是数组本身；
- `toString()`方法：此方法会调用数组中每一项元素的`toString()`方法，以获取数组中每一项元素的值的字符串形式，再将数组中每项元素的字符串形式拼接成一个以逗号分隔的字符串，最后返回此字符串；
- `toLocaleString()`方法：与`toString()`方法类似，两者的不同之处在于此方法会调用数组中每一项元素的`toLocaleString()`方法；
- `join()`方法：此方法允许你使用不同的分隔符来构建这个字符串。`join()`方法只接收一个参数，即用作分隔符的字符串，然后返回包含所有数组项的字符串。

```js
let colors = ["red", "blue", "green"]; // creates an array with three strings
alert(colors.toString());   // red,blue,green
alert(colors.valueOf());    // red,blue,green
alert(colors);              // red,blue,green
```

```js
let person1 = {
    toLocaleString() {
        return "Nikolaos";
    },
    toString() {
        return "Nicholas";
    }
};

let person2 = {
    toLocaleString() {
        return "Grigorios";
    },
    toString() {
        return "Greg";
    }
};

let people = [person1, person2];
alert(people);                   // Nicholas,Greg
alert(people.toString());        // Nicholas,Greg
alert(people.toLocaleString());  // Nikolaos,Grigorios
```

```js
let colors = ["red", "green", "blue"];
alert(colors.join());          // red,blue,green
alert(colors.join(","));       // red,green,blue
alert(colors.join("||"));      // red||green||blue
alert(colors.join(undefined))  // red,blue,green
```

### 6.2.8 栈方法

栈是一种LIFO（Last-In-First-Out，后进先出）的数据结构，也就是最新添加的项最早被移除。而栈中项的插入（叫做**推入**）和移除（叫做**弹出**），只发生在栈的顶部。为了实现**栈**的行为，ECMAScript为数组专门提供了2个方法：

- `push()`方法：接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。
- `pop()`方法：从数组末尾移除最后一项，减少数组的`length`值，然后返回移除的项。

```js
let colors = new Array(); // create an array
let count = colors.push("red", "green"); // push two items
alert(count); // 2
count = colors.push("black"); // push another item on
alert(count); // 3
let item = colors.pop(); // get the last item
alert(item); // "black"
alert(colors.length); // 2
```

### 6.2.9 队列方法

**队列**数据结构的访问规则是FIFO（First-In-First-Out，先进先出）。队列在列表的末端添加项，从列表的前端移除项。

- `shift()`方法：移除数组中的第一个项并返回该项，同时将数组长度减1。

- `unshift()`方法：它能在数组前端添加任意个项并返回新数组的长度。

结合使用`shift()`和`push()`方法，可以像使用队列一样使用数组：

```js
let colors = new Array();                 // create an array
let count = colors.push("red", "green");  // push two items
alert(count);                             // 2

count = colors.push("black");    // push another item on
alert(count);                    // 3

let item = colors.shift();     // get the first item
alert(item);                   // "red"
alert(colors.length);          // 2
```

结合使用`unshift()`和`pop()`方法，可以从相反的方向来模拟队列，即在数组的前端添加项，从数组末端移除项：

```js
let colors = new Array();                     // create an array
let count = colors.unshift("red", "green");   // push two items
alert(count);                                 // 2

count = colors.unshift("black");              // push another item on
alert(count);                                 // 3

let item = colors.pop();                      // get the first item
alert(item);                                  // "green"
alert(colors.length);                         // 2
```

### 6.2.10 重排序方法

数组中已经存在两个可以直接用来重排序的方法：

- `reverse()`方法：反转数组项的顺序。
- `sort()`方法：在默认情况下，`sort()`方法按升序排列数组项——即最小的值位于最前面，最大的值排在最后面。为了实现排序，`sort()`方法会调用每个数组项的`toString()`转型方法，然后比较得到的字符串，以确定如何排序。`sort()`方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。

```js
let values = [1, 2, 3, 4, 5];
values.reverse();
alert(values); // 5,4,3,2,1
```

```js
let values = [0, 1, 5, 10, 15];
values.sort();
// 注意：即使数组中的每一项都是数值，sort()方法比较的也是字符串！如下所示：
alert(values); // 0,1,10,15,5
```

比较函数接收2个参数，如果第1个参数应该位于第2个之前则返回一个负数，如果两个参数相等则返回0，如果第1个参数应该位于第2个之后则返回一个正数。以下就是一个简单的比较函数：

```js
function compare(value1, value2) {
    if (value1 < value2) {
        return -1;
    } else if (value1 > value2) {
        return 1;
    } else {
        return 0;
    }
}

let values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // 0,1,5,10,15
```

对于数值类型或者其`valueOf()`方法会返回数值类型的对象类型，可以使用一个更简单的比较函数：

```js
function compare(value1, value2){
    return value2 - value1;
}
```

### 6.2.11 操作方法

ECMAScript为操作已经包含在数组中的项提供了很多方法：

- `concat()`方法：这个方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。
- `slice()`方法：基于当前数组中的一或多个项创建一个新数组。`slice()`方法可以接受一或两个参数，即要返回项的起始和结束位置。
- `splice()`方法：主要用途是向数组的特定位置插入项，并返回删除的项。根据参数的不同，可以实现3种不同的效果：删除项，插入项，替换项。

```js
let colors = ["red", "green", "blue"];
let colors2 = colors.concat("yellow", ["black", "brown"]);
let colors3 = colors.concat(["yellow","white"], ["black", "brown"]);
alert(colors); // ["red", "green","blue"]
alert(colors2); // ["red", "green", "blue", "yellow", "black", "brown"]
alert(colors3); // ["red", "green", "blue", "yellow", "white", "black", "brown"]
```

You can override this force-flattening default behavior by specifying a special symbol on the argument array instance, `Symbol.isConcatSpreadable`. This will prevent the `concat()` method from flattening the result. Conversely, setting the value to `true` will force array-like objects to be flattened:

```js
let colors = ["red", "green", "blue"];
let newColors = ["black", "brown"];
let moreNewColors = {
    [Symbol.isConcatSpreadable]: true,
    length: 2,
    0: "pink",
    1: "cyan"
};
newColors[Symbol.isConcatSpreadable] = false;

// Force the array to not be flattened
let colors2 = colors.concat("yellow", newColors);

// Force the array-like object to be flattened
let colors3 = colors.concat(moreNewColors);

alert(colors); // ["red", "green","blue"]
alert(colors2); // ["red", "green", "blue", "yellow", ["black", "brown"]]
alert(colors3); // ["red", "green", "blue", "pink, "cyan"]
```

```js
let colors = ["red", "green", "blue", "yellow", "purple"];
let colors2 = colors.slice(1);
let colors3 = colors.slice(1, 4);
alert(colors2); // green,blue,yellow,purple
alert(colors3); // green,blue,yellow
```

`splice()`方法主要用途是向数组的特定位置插入项，并返回删除的项。根据参数的不同，可以实现3种不同的效果：删除项，插入项，替换项：

- **删除**：可以删除任意数量的项，只需指定2个参数：要删除的第一项的位置和要删除的项数。例如，`splice(0,2)`会删除数组中的前两项。
- **插入**：可以向指定位置插入任意数量的项，只需提供3个参数：起始位置、0（要删除的项数）和要插入的项。如果要插入多个项，可以再传入第四、第五，以至任意多个项。例如，`splice(2,0,"red","green")`会从当前数组的位置2开始插入字符串`"red"`和`"green"`。
- **替换**：可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定3个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。例如，`splice (2,1,"red","green")`会删除当前数组位置2的项，然后再从位置2开始插入字符串`"red"`和`"green"`。

```js
let colors = ["red", "green", "blue"];
let removed = colors.splice(0,1);                   // remove the first item
alert(colors);                                      // green,blue
alert(removed);                                     // red - one item array

removed = colors.splice(1, 0, "yellow", "orange");  // insert two items at position 1
alert(colors);                                      // green,yellow,orange,blue
alert(removed);                                     // empty array

removed = colors.splice(1, 1, "red", "purple");     // insert two values, remove one
alert(colors);                                      // green,red,purple,orange,blue
alert(removed);                                     // yellow - one item array
```



### 6.2.12 搜索和位置方法

ECMAScript为数组实例添加了3个位置方法：

- `indexOf()`方法：从数组的开头（位置0）开始向后查找。
- `lastIndexOf()`方法：从数组的末尾开始向前查找。
- `includes()`方法：返回一个布尔值，该值指示搜索数组中是否有至少一个元素与提供的元素匹配。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];

alert(numbers.indexOf(4)); // 3
alert(numbers.lastIndexOf(4)); // 5
alert(numbers.includes(4)); // true

alert(numbers.indexOf(4, 4)); // 5
alert(numbers.lastIndexOf(4, 4)); // 3
alert(numbers.includes(4, 7)); // false

let person = { name: "Nicholas" };
let people = [{ name: "Nicholas" }];
let morePeople = [person];

alert(people.indexOf(person)); // -1
alert(morePeople.indexOf(person)); // 0
alert(people.includes(person)); // false
alert(morePeople.includes(person)); // true
```

#### Predicate Search
ECMAScript also allows you to define a ***predicate*** function that will be invoked at each index. The return value of the function determines if the element at that index is considered a match. A predicate function takes the form `predicate(element, index, array)`, where `element` is the current element in the array being examined, `index` is the index of element inside the array, and `array` is the array instance. A truthy return value indicates a match. The two methods that make use of this are `find()` and `findIndex()`. Both begin searching at the lowest index in the array; `find()` returns the first matching element, and `findIndex()` returns the index of the first matching element. Both methods also accept a second optional argument that allows you to specify the value of `this` inside the predicate.

```js
const people = [
    {
        name: "Matt",
        age: 27
    },
    {
        name: "Nicholas",
        age: 29
    }
];

alert(people.find((element, index, array) => element.age < 28));
// {name: "Matt", age: 27}

alert(people.findIndex((element, index, array) => element.age < 28));
// 0
```

Neither method will continue searching once a match has been found.

```js
const evens = [2, 4, 6];
// Last element of array will never be inspected after match is found
evens.find((element, index, array) => {
    alert(element);
    alert(index);
    alert(array);
    return element === 4;
});
// 2
// 0
// [2, 4, 6]
// 4
// 1
// [2, 4, 6]
```



### 6.2.13 迭代方法

ECMAScript 5为数组定义了5个迭代方法：

- `every()`：对数组中的每一项运行给定函数，如果该函数对每一项都返回`true`，则返回`true`。
- `filter()`：对数组中的每一项运行给定函数，返回该函数会返回`true`的项组成的数组。
- `forEach()`：对数组中的每一项运行给定函数。这个方法没有返回值。
- `map()`：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
- `some()`：对数组中的每一项运行给定函数，如果该函数对任一项返回`true`，则返回`true`。

> ***以上方法都不会修改数组中的包含的值。***

每个方法都接收2个参数：要在每一项上运行的函数和（可选的）运行该函数的作用域对象——影响`this`的值。传入这些方法中的函数会接收3个参数：数组项的值、该项在数组中的位置和数组对象本身。根据使用的方法不同，这个函数执行后的返回值可能会也可能不会影响方法的返回值。

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];

let everyResult = numbers.every((item, index, array) => item > 2);
alert(everyResult); // false

let someResult = numbers.some((item, index, array) => item > 2);
alert(someResult); // true
```

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
// 返回一个所有数值都大于2的数组:
let filterResult = numbers.filter((item, index, array) => item > 2);
alert(filterResult); // [3,4,5,4,3]
```

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
//给数组中的每一项乘以2，然后返回这些乘积组成的数组
let mapResult = numbers.map((item, index, array) => item * 2);
alert(mapResult); // [2,4,6,8,10,8,6,4,2]
```

```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
numbers.forEach((item, index, array) => {
    // do something here
});
```

### 6.2.14 Reduction方法

ECMAScript 提供了2个缩小数组的方法。这2个方法都会迭代数组的所有项，然后构建一个最终返回的值。

- `reduce()`方法：从数组的第一项开始，逐个遍历到最后。
- `reduceRight()`方法：从数组的最后一项开始，向前遍历到第一项。

这两个方法都接收两个参数：一个在每一项上调用的函数和（可选的）作为缩小基础的初始值。传给`reduce()`和`reduceRight()`的函数接收4个参数：前一个值、当前值、项的索引和数组对象。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。

```js
let values = [1, 2, 3, 4, 5];
// 使用reduce()方法求数组中所有值之和
let sum = values.reduce((prev, cur, index, array) => prev + cur);
// 第一次执行回调函数，prev是1，cur是2。第二次，prev是3（1加2的结果），cur是3（数组的第三项）。这个过程会持续到把数组中的每一项都访问一遍，最后返回结果。
alert(sum); // 15
```

`reduceRight()`的作用类似，只不过方向相反而已：

```js
let values = [1, 2, 3, 4, 5];
let sum = values.reduceRight(function(prev, cur, index, array){
    return prev + cur;
});
//在这个例子中，第一次执行回调函数，prev是5，cur是4。
alert(sum); // 15
```



## 6.3 TYPED ARRAYS



## 6.4 THE MAP TYPE



## 6.5 THE WEAKMAP TYPE



## 6.6 THE SET TYPE



## 6.7 THE WEAKSET TYPE



## 6.8 迭代和展开运算符（ITERATION AND SPREAD OPERATORS）

ECMAScript 6引入了迭代器和散布运算符，它们在集合引用类型的上下文中特别有用。 这些新工具可轻松实现互操作性，克隆和修改集合类型。

以下集合引用类型定义了一个默认迭代器：

- Array
- All typed arrays
- Map
- Set

简单地说，这意味着所有功能都支持有序迭代，并且可以将其传递给`for..of`循环：

```js
let iterableThings = [
    Array.of(1, 2),
    typedArr = Int16Array.of(3, 4),
    new Map([[5, 6], [7, 8]]),
    new Set([9, 10])
];

for (const iterableThing of iterableThings) {
    for (const x of iterableThing) {
        console.log(x);
    }
}
// 1
// 2
// 3
// 4
// [5, 6]
// [7, 8]
// 9
// 10
```

This also means that all these types are compatible with the spread operator. The spread operator is especially useful as it performs a shallow copy on the iterable object. This allows you to easily clone entire objects with a succinct syntax:

这也意味着所有这些类型都与展开运算符（spread operator）兼容。 展开运算符（spread operator）在对可迭代对象执行浅拷贝时特别有用。 可以使用简洁的语法轻松克隆整个对象：

```js
let arr1 = [1, 2, 3];
let arr2 = [...arr1];
console.log(arr1); // [1, 2, 3]
console.log(arr2); // [1, 2, 3]
console.log(arr1 === arr2); // false
```

Constructors which expect an iterable object can just be passed the iterable instance to be cloned:

期望可迭代对象的构造函数可以只传递要克隆的可迭代实例：

```js
let map1 = new Map([[1, 2], [3, 4]]);
let map2 = new Map(map1);
console.log(map1); // Map {1 => 2, 3 => 4}
console.log(map2); // Map {1 => 2, 3 => 4}
```

It also allows for partial array construction:

它还允许部分数组构造：

```js
let arr1 = [1, 2, 3];
let arr2 = [0, ...arr1, 4, 5];
console.log(arr2); // [0, 1, 2, 3, 4, 5]
```

The shallow copy mechanism means that only object references are copied:

浅拷贝机制意味着仅拷贝对象引用：

```js
let arr1 = [{}];
let arr2 = [...arr1];
arr1[0].foo = ‘bar’;
console.log(arr2[0]); // { foo: ‘bar’ }
```

Each of these collection types support multiple methods of construction, such as the `Array.of()` and `Array.from()` static methods. When combined with the spread operator, this makes for extremely easy interoperability:

这些集合类型中的每一个都支持多种构造方法，例如`Array.of()`和`Array.from()`静态方法。 当与展开运算符（spread operator）结合使用时，这将使互操作性变得非常容易：

```js
let arr1 = [1, 2, 3];

// Copy array into typed array
let typedArr1 = Int16Array.of(...arr1);
let typedArr2 = Int16Array.from(arr1);
console.log(typedArr1); // Int16Array [1, 2, 3]
console.log(typedArr2); // Int16Array [1, 2, 3]

// Copy array into map
let map = new Map(arr1.map((x) => [x, ‘val’ + x]));
console.log(map); // Map {1 => ‘val 1’, 2 => ‘val 2’, 3 => ‘val 3’}

// Copy array in to set
let set = new Set(typedArr2);
console.log(set); // Set {1, 2, 3}

// Copy set back into array
let arr2 = [...set];
console.log(arr2); // [1, 2, 3]
```

