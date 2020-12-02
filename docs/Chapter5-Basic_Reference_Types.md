# 第5章 基本引用类型

**引用类型的值（对象）** 是 **引用类型** 的一个实例。在ECMAScript中，**引用类型**是一种数据结构，用于将数据和功能组织在一起。它也常被称为**类**，但这种称呼并不妥当。ECMAScript不具备传统的面向对象语言所支持的类和接口等基本结构。引用类型有时候也被称为**对象定义**，因为它们描述的是一类对象所具有的属性和方法。

## 5.1 `Date`类型

`Date`类型使用自UTC（Coordinated Universal Time，国际协调时间）1970年1月1日午夜（零时）开始经过的毫秒数来保存日期。在使用这种数据存储格式的条件下，`Date`类型保存的日期能够精确到1970年1月1日之前或之后的285,616年。

要创建一个日期对象，使用`new`操作符和`Date`构造函数即可：

```js
var now = new Date();
```

`Date`构造函数接收日期的毫秒数作为参数，在不传递参数的情况下，新创建的对象自动获得当前日期和时间。为了简化这一计算过程，ECMAScript提供了2个方法：

- `Date.parse()`
- `Date.UTC()`

`Date.parse()`方法接收一个表示日期的字符串参数，然后尝试根据这个字符串返回相应日期的毫秒数。如果传入`Date.parse()`方法的字符串不能表示日期，那么它会返回`NaN`。ECMA-262没有定义`Date.parse()`应该支持哪种日期格式，因此这个方法的行为因实现而异，而且通常是因地区而异。将地区设置为美国的浏览器通常都接受下列日期格式：

- “月/日/年”，如6/13/2004；
- “英文月名 日,年”，如January 12,2004；
- “英文星期几 英文月名 日 年 时:分:秒 时区”，如Tue May 25 2004 00:00:00 GMT-0700。
- ISO 8601扩展格式`YYYY-MM-DDTHH:mm:ss.sssZ`（例如2004-05-25T00:00:00）。只有兼容ECMAScript 5的实现支持这种格式。

例如，要为2019年5月23日创建一个日期对象，可以使用下面的代码：

```js
let someDate = new Date(Date.parse("May 23, 2019"));
```

如果直接将表示日期的字符串传递给`Date`构造函数，也会在后台调用`Date.parse()`，这意味着以下代码与上面是等价的：

```js
let someDate = new Date("May 23, 2019");
```

`Date.UTC()`方法同样也返回表示日期的毫秒数。`Date.UTC()`的参数分别是必选参数：

- 年份
- 基于0的月份（一月是0，二月是1，以此类推）

可选参数（如果不指定可选参数，默认为0）：
- 天（1到31）
- 小时数（0到23）
- 分钟
- 秒
- 毫秒数。

以下是两个使用`Date.UTC()`方法的例子：

```js
// GMT时间2000年1月1日午夜零时
let y2k = new Date(2000, 0);
// GMT时间2005年5月5日下午5:55:55
let allFives = new Date(2005, 4, 5, 17, 55, 55);
```

可以直接给`Date`构造函数传递`Date.UTC()`的参数，`Date`构造函数也会先调用`Date.UTC()`，所以以下代码与上面代码等价：

```js
// 本地时间2000年1月1日午夜零时
let y2k = new Date(Date.UTC(2000, 0));
// 本地时间2005年5月5日下午5:55:55
let allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55));
```

### 5.1.1 继承的方法

与其他引用类型一样，`Date`类型也重写了以下方法：

- `toLocaleString()`：按照与浏览器设置的地区相适应的格式返回日期和时间；
- `toString()`：通常返回带有时区信息的日期和时间，其中时间一般以军用时间（即小时的范围是0到23）表示；
- `valueOf()`方法：返回日期的毫秒表示。

### 5.1.2 日期格式化方法

`Date`类型还有一些专门用于将日期格式化为字符串的方法：

- `toDateString()`——以特定于实现的格式显示星期几、月、日和年；
- `toTimeString()`——以特定于实现的格式显示时、分、秒和时区；
- `toLocaleDateString()`——以特定于地区的格式显示星期几、月、日和年；
- `toLocaleTimeString()`——以特定于地区的格式显示时、分、秒；
- `toUTCString()`——以特定于实现的格式显示UTC日期。

### 5.1.3 日期/时间组件方法

| 方　　法                   | 说　　明                                                     |
| :------------------------- | :----------------------------------------------------------- |
| `getTime()`                | 返回表示日期的毫秒数；与`valueOf()`方法返回的值相同          |
| `setTime(毫秒)`            | 以毫秒数设置日期，会改变整个日期                             |
| `getFullYear()`            | 取得4位数的年份（如2007而非仅07）                            |
| `getUTCFullYear()`         | 返回UTC日期的4位数年份                                       |
| `setFullYear(年)`          | 设置日期的年份。传入的年份值必须是4位数字（如2007而非仅07）  |
| `setUTCFullYear(年)`       | 设置UTC日期的年份。传入的年份值必须是4位数字（如2007而非仅07） |
| `getMonth()`               | 返回日期中的月份，其中0表示一月，11表示十二月                |
| `getUTCMonth()`            | 返回UTC日期中的月份，其中0表示一月，11表示十二月             |
| `setMonth(月)`             | 设置日期的月份。传入的月份值必须大于0，超过11则增加年份      |
| `setUTCMonth(月)`          | 设置UTC日期的月份。传入的月份值必须大于0，超过11则增加年份   |
| `getDate()`                | 返回日期月份中的天数（1到31）                                |
| `getUTCDate()`             | 返回UTC日期月份中的天数（1到31）                             |
| `setDate(日)`              | 设置日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份 |
| `setUTCDate(日)`           | 设置UTC日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份 |
| `getDay()`                 | 返回日期中星期的星期几（其中0表示星期日，6表示星期六）       |
| `getUTCDay()`              | 返回UTC日期中星期的星期几（其中0表示星期日，6表示星期六）    |
| `getHours()`               | 返回日期中的小时数（0到23）                                  |
| `getUTCHours()`            | 返回UTC日期中的小时数（0到23）                               |
| `setHours(时)`             | 设置日期中的小时数。传入的值超过了23则增加月份中的天数       |
| `setUTCHours(时)`          | 设置UTC日期中的小时数。传入的值超过了23则增加月份中的天数    |
| `getMinutes()`             | 返回日期中的分钟数（0到59）                                  |
| `getUTCMinutes()`          | 返回UTC日期中的分钟数（0到59）                               |
| `setMinutes(分)`           | 设置日期中的分钟数。传入的值超过59则增加小时数               |
| `setUTCMinutes(分)`        | 设置UTC日期中的分钟数。传入的值超过59则增加小时数            |
| `getSeconds()`             | 返回日期中的秒数（0到59）                                    |
| `getUTCSeconds()`          | 返回UTC日期中的秒数（0到59）                                 |
| `setSeconds(秒)`           | 设置日期中的秒数。传入的值超过了59会增加分钟数               |
| `setUTCSeconds(秒)`        | 设置UTC日期中的秒数。传入的值超过了59会增加分钟数            |
| `getMilliseconds()`        | 返回日期中的毫秒数                                           |
| `getUTCMilliseconds()`     | 返回UTC日期中的毫秒数                                        |
| `setMilliseconds(毫秒)`    | 设置日期中的毫秒数                                           |
| `setUTCMilliseconds(毫秒)` | 设置UTC日期中的毫秒数                                        |
| `getTimezoneOffset()`      | 返回本地时间与UTC时间相差的分钟数。例如，美国东部标准时间返回300。在某地进入夏令时的情况下，这个值会有所变化 |



## 5.2 `RegExp`类型

ECMAScript通过`RegExp`类型来支持正则表达式。创建正则表达式的第1种方式是使用字面量形式：

```js
let expression = /pattern/flags;
```

其中的模式（pattern）部分可以是任何简单或复杂的正则表达式，可以包含字符类、限定符、分组、向前查找以及反向引用。每个正则表达式都可带有一或多个标志（flags），用以标明正则表达式的行为。

第2种创建正则表达式的方式是使用`RegExp`构造函数，它接收两个参数：一个是要匹配的字符串模式，另一个是可选的标志字符串。以下两个正则表达式是等价的：

```js
// Match the first instance of "bat" or "cat", regardless of case.
let pattern1 = /[bc]at/i;
// Same as pattern1, just using the constructor.
let pattern2 = new RegExp("[bc]at", "i");
```

由于`RegExp`构造函数的模式参数是字符串，所以在某些情况下要对字符进行双重转义。

正则表达式的匹配模式支持下列标志：

- **`g`**：表示全局（global）模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止；
- **`i`**：表示不区分大小写（case-insensitive）模式，即在确定匹配项时忽略模式与字符串的大小写；
- **`m`**：表示多行（multiline）模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项；
- `y`：Indicates sticky mode, meaning the pattern will only look at the string contents beginning at *lastIndex*.
- `u`：Indicates Unicode mode is enabled.

### 5.2.1 `RegExp`实例属性

`RegExp`的每个实例都具有下列属性，通过这些属性可以取得有关模式的各种信息。

- **`global`**：布尔值，表示是否设置了`g`标志。
- **`ignoreCase`**：布尔值，表示是否设置了`i`标志。
- **`unicode`**: A Boolean value indicating whether the `u` flag has been set.
- **`sticky`**: A Boolean value indicating whether the `y` flag has been set.
- **`lastIndex`**：整数，表示开始搜索下一个匹配项的字符位置，从0算起。
- **`multiline`**：布尔值，表示是否设置了`m`标志。
- **`source`**：正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回。
- **`flags`**: The string flags of the regular expression. This is always returned as if specified in
    literal form (without opening and closing slashes) rather than a string pattern as passed into
    the constructor.

```js
let pattern1 = /\[bc\]at/i;
console.log(pattern1.global); // false
console.log(pattern1.ignoreCase); // true
console.log(pattern1.multiline); // false
console.log(pattern1.lastIndex); // 0
console.log(pattern1.source); // "\[bc\]at"
console.log(pattern1.flags); // "i"

let pattern2 = new RegExp("\\[bc\\]at", "i");
console.log(pattern2.global); // false
console.log(pattern2.ignoreCase); // true
console.log(pattern2.multiline); // false
console.log(pattern2.lastIndex); // 0
console.log(pattern2.source); // "\[bc\]at"
console.log(pattern2.flags); // "i"
```

### 5.2.2 `RegExp`实例方法

`RegExp`对象的主要方法：

- `exec()`：接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组；或者在没有匹配项的情况下返回`null`。在数组中，第1项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串（如果模式中没有捕获组，则该数组只包含一项）。
- `test()`：它接受一个字符串参数。在模式与该参数匹配的情况下返回`true`；否则，返回`false`。
- `toLocaleString()`：返回正则表达式的字面量。
- `toString()`：返回正则表达式的字面量。

`exec()`方法的实例：

```js
let text = "cat, bat, sat, fat";
let pattern = /.at/;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat
console.log(pattern.lastIndex); // 0

matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat

let text = "cat, bat, sat, fat";
let pattern = /.at/g;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat
console.log(pattern.lastIndex); // 3

matches = pattern.exec(text);
console.log(matches.index); // 5
console.log(matches[0]); // bat
console.log(pattern.lastIndex); // 8

matches = pattern.exec(text);
console.log(matches.index); // 10
console.log(matches[0]); // sat
console.log(pattern.lastIndex); // 13
```

### 5.2.3 `RegExp`构造函数属性

`RegExp`构造函数包含一些属性（这些属性在其他语言中被看成是静态属性）：

| 长属性名       | 短属性名 | 说　　明                             |
| :------------- | :------- | :----------------------------------- |
| `input`        | `$_`     | 最近一次要匹配的字符串。             |
| `lastMatch`    | `$&`     | 最近一次的匹配项。                   |
| `lastParen`    | `$+`     | 最近一次匹配的捕获组。               |
| `leftContext`  | $\`      | `input`字符串中`lastMatch`之前的文本 |
| `rightContext` | `$'`     | `input`字符串中`lastMatch`之后的文本 |

```js
let text = "this has been a short summer";
let pattern = /(.)hort/g;
if (pattern.test(text)) {
    console.log(RegExp.input);          // this has been a short summer
    console.log(RegExp.leftContext);    // this has been a
    console.log(RegExp.rightContext);   // summer
    console.log(RegExp.lastMatch);      // short
    console.log(RegExp.lastParen);      // s
}
```

```js
let text = "this has been a short summer";
let pattern = /(.)hort/g;
/*
* Note: Opera doesn't short property names.
* Internet Explorer doesn't support multiline.
*/
if (pattern.test(text)) {
    console.log(RegExp.$_); // this has been a short summer
    console.log(RegExp["$`"]); // this has been a
    console.log(RegExp["$'"]); // summer
    console.log(RegExp["$&"]); // short
    console.log(RegExp["$+"]); // s
    console.log(RegExp["$*"]); // false
}
```

除了上面的属性，还有9个用于存储捕获组的构造函数属性：`RegExp.$1`、`RegExp.$2`...`RegExp.$9`，分别用于存储第1至第9个匹配的捕获组。在调用`exec()`或`test()`方法时，这些属性会被自动填充。

```js
let text = "this has been a short summer";
let pattern = /(..)or(.)/g;
if (pattern.test(text)) {
    console.log(RegExp.$1); // sh
    console.log(RegExp.$2); // t
}
```

### 5.2.4 模式的局限性

下面列出了ECMAScript正则表达式不支持的特性：

- 匹配字符串开始和结尾的`\A`和`\Z`锚
- 向后查找（lookbehind）
- 并集和交集类
- 原子组（atomic grouping）
- Unicode支持（单个字符除外，如`\uFFFF`）
- 命名的捕获组
- `s`（single，单行）和`x`（free-spacing，无间隔）匹配模式
- 条件匹配
- 正则表达式注释



## 5.3 基本包装类型（PRIMITIVE WRAPPER TYPES）

为了便于操作基本类型值，ECMAScript还提供了3个特殊的引用类型：

- `Boolean`
- `Number`
- `String`

当访问基本包装类型值时，访问过程处于一种读取模式，也就是要从内存中读取这个字符串的值。而在读取模式中访问字符串时，后台都会自动完成下列处理。

1. 创建`String`类型的一个实例；
2. 在实例上调用指定的方法；
3. 销毁这个实例。

可以将以上三个步骤想象成是执行了下列ECMAScript代码：

```js
let s1 = new String("some text");
let s2 = s1.substring(2);
s1 = null;
```

引用类型与基本包装类型的主要区别就是**对象的生存期**。使用`new`操作符创建的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中。

**不能在运行时为基本类型值添加属性和方法：**

```js
let s1 = "some text";
s1.color = "red";
console.log(s1.color); // undefined
```

可以显式地调用`Boolean`、`Number`和`String`来创建基本包装类型的对象。

`Object`构造函数会像工厂方法一样，根据传入值的类型返回相应基本包装类型的实例。把字符串传给`Object`构造函数，就会创建`String`的实例；而传入数值参数会得到`Number`的实例，传入布尔值参数就会得到`Boolean`的实例。例如：

```js
let obj = new Object("some text");
console.log(obj instanceof String); // true
```

要注意的是，使用`new`调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的：

```js
let value = "25";
let number = Number(value); // 转型函数
console.log(typeof number); // "number"

let obj = new Number(value); // 构造函数
console.log(typeof obj); // "object"
```



### 5.3.1 `Boolean`类型

 `Boolean`类型是与布尔值对应的引用类型。要创建`Boolean`对象，可以像下面这样调用`Boolean`构造函数并传入`true`或`false`值：

```js
let booleanObject = new Boolean(true);
```

`Boolean`类型的实例重写以下方法：

- `valueOf()`方法：返回基本类型值`true`或`false`；
- `toString()`方法：返回字符串`"true"`和`"false"`。



### 5.3.2 `Number`类型

`Number`是与数字值对应的引用类型。要创建`Number`对象，可以在调用`Number`构造函数时向其中传递相应的数值：

```js
let numberObject = new Number(10);
```

`Number`类型重写了以下方法：

- `valueOf()`：返回对象表示的基本类型的数值。
- `toLocaleString()`：返回字符串形式的数值。
- `toString()`：返回字符串形式的数值。

可以为`toString()`方法传递一个表示基数的参数，告诉它返回几进制数值的字符串形式，如下面的例子所示：

```js
let num = 10;
console.log(num.toString()); // "10"
console.log(num.toString(2)); // "1010"
console.log(num.toString(8)); // "12"
console.log(num.toString(10)); // "10"
console.log(num.toString(16)); // "a"
```

`Number`类型还提供了一些用于将数值格式化为字符串的方法：

- `toFixed()`：按照指定的小数位返回数值的字符串表示；
- `toExponential()`：返回以指数表示法（也称e表示法）表示的数值的字符串形式。
- `toPrecision()`：这个方法接收一个参数，即表示数值的所有数字的位数（不包括指数部分）。可能会返回固定大小（fixed）格式，也可能返回指数（exponential）格式。

```js
let num = 10;
console.log(num.toFixed(2)); // "10.00"

let num = 10.005;
console.log(num.toFixed(2)); // "10.01"

let num = 10;
console.log(num.toExponential(1)); // "1.0e+1"

let num = 99;
console.log(num.toPrecision(1)); // "1e+2"
console.log(num.toPrecision(2)); // "99"
console.log(num.toPrecision(3)); // "99.0"
```

#### The `isInteger()` Method and Safe Integers

ES6中新引入的`Number.isInteger()`方法能够辨别数字值是否存储为整数：

```js
console.log(Number.isInteger(1)); // true
console.log(Number.isInteger(1.00)); // true
console.log(Number.isInteger(1.01)); // false
```



### 5.3.3 `String`类型

`String`类型是字符串的对象包装类型，可以像下面这样使用`String`构造函数来创建：

```js
let stringObject = new String("hello world");
```

继承的`valueOf()`、`toLocaleString()`和`toString()`方法，都返回对象所表示的基本字符串值。

`String`类型的每个实例都有一个`length`属性，表示字符串中包含字符的个数。

#### 5.3.3.1 JavaScript字符

第4版内容

涉及方法：

- `charAt()`
- `charCodeAt()`
- `fromCharCode()`
- `codePointAt()`
- `fromCodePoint()`

#### 5.3.3.2 `normalize()`方法

第4版内容

#### 5.3.3.3 字符串操作方法

- `concat()`：用于将一或多个字符串拼接起来，返回拼接得到的新字符串。
- `slice()`：返回字符串的切片。第1个参数指定子字符串的开始位置，第2个参数（可选参数）表示子字符串到哪里结束；不指定则将字符串末尾作为结束位置。
- `substr()`：返回字符串的子串。第1个参数指定子字符串的开始位置，第2个参数（可选参数）表示子字符串最后一个字符后面的位置；不指定则将字符串末尾作为结束位置。
- `substring()`：返回字符串的子串。第1个参数指定子字符串的开始位置，第2个参数（可选参数）表示返回的字符个数；不指定则将字符串末尾作为结束位置。

```js
let stringValue = "hello ";
let result = stringValue.concat("world", "!");
console.log(result); // "hello world!"
console.log(stringValue); // "hello"

let stringValue = "hello world";
console.log(stringValue.slice(3)); // "lo world"
console.log(stringValue.substring(3)); // "lo world"
console.log(stringValue.substr(3)); // "lo world"
console.log(stringValue.slice(3, 7)); // "lo w"
console.log(stringValue.substring(3,7)); // "lo w"
console.log(stringValue.substr(3, 7)); // "lo worl"

console.log(stringValue.slice(-3)); // "rld"
console.log(stringValue.substring(-3)); // "hello world"
console.log(stringValue.substr(-3)); // "rld"
console.log(stringValue.slice(3, -4)); // "lo w"
console.log(stringValue.substring(3, -4)); // "hel"
console.log(stringValue.substr(3, -4)); // "" (empty string)
```



#### 5.3.3.4 字符串位置方法

以下2个方法搜索给定的子字符串，然后返子字符串的位置（如果没有找到该子字符串，则返回`-1`）：

- `indexOf()`：从字符串的开头向后搜索。
- `lastIndexOf()`：从字符串的末尾向前搜索。

第1个参数表示待搜索的子字符串；第2个参数（可选）表示从字符串中的哪个位置开始搜索：

```js
let stringValue = "hello world";
console.log(stringValue.indexOf("o")); // 4
console.log(stringValue.lastIndexOf("o")); // 7

let stringValue = "hello world";
console.log(stringValue.indexOf("o", 6)); // 7
console.log(stringValue.lastIndexOf("o", 6)); // 4
```



#### 5.3.3.5 字符串包含方法（String Inclusion Methods）

ECMAScript 6 添加了3个额外的方法来判断给定的字符串是否被包含在另一个字符串中；是，则返回`true`，否则返回`false`：

- `startsWith()`：从索引0开始匹配。

- `endsWith()`：从索引 `(string.length – substring.length)`开始查找。

- `includes()`：检查整个字符串。

```js
let message = "foobarbaz";
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("bar")); // false

console.log(message.endsWith("baz")); // true
console.log(message.endsWith("bar")); // false

console.log(message.includes("bar")); // true
console.log(message.includes("qux")); // false
```

`startsWith()`和`includes()` 方法接受第2个参数（可选） ：表示开始搜索的位置。

```js
let message = "foobarbaz";
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("foo", 1)); // false

console.log(message.includes("bar")); // true
console.log(message.includes("bar", 4)); // false
```

`endsWith()` 方法接受第2个参数（可选），该参数指出哪个位置将被视作字符串的末尾：

```js
let message = "foobarbaz";
console.log(message.endsWith("bar")); // false
console.log(message.endsWith("bar", 6)); // true
```



#### 5.3.3.6 `trim()`方法

`trim()`方法会创建一个字符串的副本，删除前置及后缀的所有空格，然后返回结果：

```js
let stringValue = " hello world ";
let trimmedStringValue = stringValue.trim();
console.log(stringValue); // " hello world "
console.log(trimmedStringValue); // "hello world"
```

`trimLeft()`和`trimRight()`方法分别只删除字符串开头和字符串末尾的空白符。



#### 5.3.3.7 `repeat()`方法

`repeat()` 接受1个整数作为参数，并按照给定的参数拷贝这个字符串n次，并将所有字符串拼接起来。

```js
let stringValue = "na ";
console.log(stringValue.repeat(16) + "batman");
// na na na na na na na na na na na na na na na na batman
```



#### 5.3.3.8 `padStart()`和`padEnd()`方法

第4版内容



#### 5.3.3.9 String Iterators and Destructuring

第4版内容



#### 5.3.3.10 字符串大小写方法

ECMAScript中涉及字符串大小写转换的方法有4个：

- `toLowerCase()`
- `toLocaleLowerCase()`
- `toUpperCase()`
- `toLocaleUpperCase()`。

```js
let stringValue = "hello world";
console.log(stringValue.toLocaleUpperCase()); // "HELLO WORLD"
console.log(stringValue.toUpperCase()); // "HELLO WORLD"
console.log(stringValue.toLocaleLowerCase()); // "hello world"
console.log(stringValue.toLowerCase()); // "hello world"
```



#### 5.3.3.11 字符串模式匹配方法

`String`类型定义了用于在字符串中匹配模式的方法：

- `match()`：本质上等同于调用`RegExp`的`exec()`方法。`match()`方法只接受1个参数，要么是一个正则表达式，要么是一个`RegExp`对象。
- `search()`：返回字符串中第一个匹配项的索引；如果没有找到匹配项，则返回`-1`。而且，`search()`方法始终是从字符串开头向后查找模式。
- `replace()`：这个方法接受两个参数：第1个参数可以是一个`RegExp`对象或者一个字符串，第2个参数可以是一个字符串或者一个***函数***。如果第1个参数是字符串，那么只会替换第一个子字符串；如果第1个参数是正则表达式并指定了全局标志（`g`），则会替换所有子字符串。 **注意，Python的`re`模块的`sub()`方法的第2个参数同样既可以是字符串也可以是函数！**
- `split()`：基于指定的分隔符将一个字符串分割成多个子字符串，并将结果放在一个数组中。分隔符可以是一个字符串或一个`RegExp`对象。

```js
let text = "cat, bat, sat, fat";
let pattern = /.at/;

//与pattern.exec(text)相同
let matches = text.match(pattern);
console.log(matches.index); // 0
console.log(matches[0]); // "cat"
console.log(pattern.lastIndex); // 0
```

```js
let text = "cat, bat, sat, fat";
let pos = text.search(/at/);
console.log(pos); // 1
```

```js
let text = "cat, bat, sat, fat";
let result = text.replace("at", "ond");
console.log(result); // "cond, bat, sat, fat"

result = text.replace(/at/g, "ond");
console.log(result); // "cond, bond, sond, fond"
```

如果`replace()`方法的第2个参数是字符串，那么还可以使用一些特殊的字符序列，将正则表达式操作得到的值插入到结果字符串中：

```js
let text = "cat, bat, sat, fat";
result = text.replace(/(.at)/g, "word ($1)");
console.log(result); // word (cat), word (bat), word (sat), word (fat)
```



#### 5.3.3.12 `localeCompare()`方法

`localeCompare()`方法比较两个字符串，并返回下列值中的一个：

- 如果字符串在字母表中应该排在字符串参数之前，则返回一个负数（大多数情况下是`-1`，具体的值要视实现而定）；
- 如果字符串等于字符串参数，则返回`0`；
- 如果字符串在字母表中应该排在字符串参数之后，则返回一个正数（大多数情况下是`1`，具体的值同样要视实现而定）。

```js
let stringValue = "yellow";
console.log(stringValue.localeCompare("brick")); // 1
console.log(stringValue.localeCompare("yellow")); // 0
console.log(stringValue.localeCompare("zoo")); // -1
```



#### 5.3.3.13 HTML方法

| 方　　法             | 输出结果            |
| :------------------- | :------------------ |
| `anchor(name)`     | `<a name= "name">string</a>`          |
| `big()`              | `<big>string</big>`          |
| `bold()`             | `<b>string</b>`          |
| `fixed()`            | `<tt>string</tt>`          |
| `fontcolor(color)` | `<font color="color">string</font>`          |
| `fontsize(size)`   | `<font size="size">string</font>`          |
| `italics()`          | `<i>string</i>`          |
| `link(url)`          | `<a href="url">string</a>` |
| `small()`            | `<small>string</small>`          |
| `strike()`           | `<strike>string</strike>`      |
| `sub()`              | `<sub>string</sub>`          |
| `sup()`              | `<sup>string</sup>`          |



## 5.4 单体内置对象

ECMA-262对内置对象的定义是：“由ECMAScript实现提供的、不依赖于宿主环境的对象，这些对象在ECMAScript程序执行之前就已经存在了。”

ECMA-262还定义了两个单体内置对象：

- `Global`
- `Math`

### 5.4.1 `Global`对象

**事实上，没有全局变量或全局函数；所有在全局作用域中定义的属性和函数，都是`Global`对象的属性。**

#### URI编码方法

`Global`对象的以下方法可以对URI进行编码：

- `encodeURI()`：用于整个URI进行编码。
- `decodeURI()`：用于对URI中的某一段进行编码。
- `encodeURIComponent()`：用于整个URI进行解码。
- `decodeURIComponent()`：用于对URI中的某一段进行解码。

```js
let uri = "http:// www.wrox.com/illegal value.js#start";
// "http:// www.wrox.com/illegal%20value.js#start"
console.log(encodeURI(uri));
// "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start"
console.log(encodeURIComponent(uri));
```

```js
let uri = "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.js%23start";
// http%3A%2F%2Fwww.wrox.com%2Fillegal value.js%23start
console.log(decodeURI(uri));
// http:// www.wrox.com/illegal value.js#start
console.log(decodeURIComponent(uri));
```



#### `eval()`方法

`eval()`方法就像是一个完整的ECMAScript解析器，它只接受一个参数，即要执行的ECMAScript（或JavaScript）字符串。看下面的例子：

```js
eval("console.log('hi')");
```

通过`eval()`执行的代码被认为是包含该次调用的执行环境的一部分，因此被执行的代码具有与该执行环境相同的作用域链：

```js
// 通过eval()执行的代码可以引用在包含环境中定义的变量
let msg = "hello world";
eval("console.log(msg)"); // "hello world"

// 在eval()调用中定义一个变量或函数，可以在该调用的外部代码中引用这个变量或函数
eval("function sayHi() { console.log('hi'); }");
sayHi();
```

> **注意：严格模式下，在外部访问不到`eval()`中创建的任何变量或函数，因此前面两个例子都会导致错误。**

#### `window`对象

ECMAScript虽然没有指出如何直接访问`Global`对象，但Web浏览器都是将这个全局对象作为`window`对象的一部分加以实现的。因此，在全局作用域中声明的所有变量和函数，就都成为了`window`对象的属性。



### 5.4.2 `Math`对象

`Math`对象是ECMAScript为保存数学公式和信息提供的一个公共位置。

#### `Math`对象的属性

| 属　　性       | 说　　明                         |
| :------------- | :------------------------------- |
| `Math.E`       | 自然对数的底数，即常量`e`的值    |
| `Math.LN10`    | 10的自然对数                     |
| `Math.LN2`     | 2的自然对数                      |
| `Math.LOG2E`   | 以2为底`e`的对数                 |
| `Math.LOG10E`  | 以10为底`e`的对数                |
| `Math.PI`      | π的值                            |
| `Math.SQRT1_2` | 1/2的平方根（即2的平方根的倒数） |
| `Math.SQRT2`   | 2的平方根                        |

####  `min()`和`max()`方法

`min()`和`max()`方法用于确定一组数值中的最小值和最大值。这两个方法都可以接收任意多个数值参数：

```js
let max = Math.max(3, 54, 32, 16);
console.log(max); // 54
let min = Math.min(3, 54, 32, 16);
console.log(min); // 3
```

要找到数组中的最大或最小值，可以像下面这样使用展开运算符（spread operator）：

```js
let values = [1, 2, 3, 4, 5, 6, 7, 8];
let max = Math.max(...values);
```

也可以使用`apply()`方法：

```js
let values = [1, 2, 3, 4, 5, 6, 7, 8];
let max = Math.max.apply(Math, values);
```

#### 舍入方法

舍入方法分别遵循下列舍入规则：

- `Math.ceil()`：执行向上舍入，即它总是将数值向上舍入为最接近的整数；
- `Math.floor()`：执行向下舍入，即它总是将数值向下舍入为最接近的整数；
- `Math.round()`：执行标准舍入，即它总是将数值四舍五入为最接近的整数；
- `Math.fround()`: This method returns the nearest single precision (32 bits) floating point representation
    of the number.

```js
console.log(Math.ceil(25.9)); // 26
console.log(Math.ceil(25.5)); // 26
console.log(Math.ceil(25.1)); // 26

console.log(Math.round(25.9)); // 26
console.log(Math.round(25.5)); // 26
console.log(Math.round(25.1)); // 25

console.log(Math.fround(0.4)); // 0.4000000059604645
console.log(Math.fround(0.5)); // 0.5
console.log(Math.fround(25.9)); // 25.899999618530273

console.log(Math.floor(25.9)); // 25
console.log(Math.floor(25.5)); // 25
console.log(Math.floor(25.1)); // 25
```



#### `random()`方法

`Math.random()`方法返回大于等于0小于1的一个随机数。套用下面的公式，就可以利用`Math.random()`从某个整数范围内随机选择一个值：

```js
值 = Math.floor(Math.random() * 可能值的总数 + 第一个可能的值)
```

举例来说，如果你想选择一个1到10之间的数值，可以像下面这样编写代码：

```js
let num = Math.floor(Math.random() * 10 + 1);
```

如果想要选择一个介于2到10之间的值，就应该将上面的代码改成这样：

```js
let num = Math.floor(Math.random() * 9 + 2);
```



#### 其他方法

- `Math.abs(x)` Returns the absolute value of `x`
- `Math.exp(x)` Returns `Math.E` raised to the power of `x`
- `Math.expm1(x)` Equivalent to `Math.exp(x) - 1`
- `Math.log(x)` Returns the natural logarithm of `x`
- `Math.log1p(x)` Equivalent to `1 + Math.log(x)`
- `Math.pow(x, power)` Returns `x` raised to the power of power
- `Math.pow(...nums)` Returns the square root of the sum of the squares of each number in `nums`
- `Math.clz32(x)` Returns the number of leading zeroes of a 32-bit integer x
- `Math.sign(x)` Returns `1`, `0`, `-0`, or `-1` indicating the sign of `x`
- `Math.trunc(x)` Returns the integer component of `x`, removing any decimals
- `Math.sqrt(x)` Returns the square root of `x`
- `Math.cbrt(x)` Returns the cubic root of `x`
- `Math.acos(x)` Returns the arc cosine of `x`
- `Math.acosh(x)` Returns the hyperbolic arc cosine of `x`
- `Math.asin(x)` Returns the arc sine of `x`
- `Math.asin(x)` Returns the hyperbolic arc sine of `x`
- `Math.atan(x)` Returns the arc tangent of `x`
- `Math.atanh(x)` Returns the hyperbolic arc tangent of `x`
- `Math.atan2(y, x)` Returns the arc tangent of `y/x`
- `Math.cos(x)` Returns the cosine of `x`
- `Math.sin(x)` Returns the sine of `x`
- `Math.tan(x)` Returns the tangent of `x`

