# 第23章 JSON

JSON是一个轻量级的数据格式，可以简化表示复杂数据结构的工作量。JSON使用JavaScript语法的子集表示对象、数组、字符串、数值、布尔值和`null`。即使XML也能表示同样复杂的数据结果，但JSON没有那么烦琐，而且在JavaScript中使用更便利。

## 23.1 语法

JSON的语法可以表示以下3种类型的值：

- **简单值**：使用与JavaScript相同的语法，可以在JSON中表示字符串、数值、布尔值和`null`。但JSON不支持JavaScript中的特殊值`undefined`。
- **对象**：对象作为一种复杂数据类型，表示的是一组无序的键值对。而每个键值对儿中的值可以是简单值，也可以是复杂数据类型的值。
- **数组**：数组也是一种复杂数据类型，表示一组有序的值的列表，可以通过数值索引来访问其中的值。数组的值也可以是任意类型——简单值、对象或数组。

JSON不支持变量、函数或对象实例，它就是一种表示结构化数据的格式，虽然与JavaScript中表示数据的某些语法相同，但它并不局限于JavaScript的范畴。

### 23.1.1 简单值

最简单的JSON数据形式就是简单值。例如，下面这个值是有效的JSON数据：

```
5
```

这是JSON表示数值5的方式。类似地，下面是JSON表示字符串的方式：

```
"Hello world!"
```

JavaScript字符串与JSON字符串的最大区别在于，JSON字符串 **必须使用双引号（单引号会导致语法错误）** 。

布尔值和`null`也是有效的JSON形式。

### 23.1.2 对象

JSON中的对象要求给属性加引号。并且同一个对象中绝对不应该出现两个同名属性。JSON表示上述对象的方式如下：

```json
{ 
    "name": "Nicholas",
    "age": 29,
    "school": {
        "name": "Merrimack College",
        "location": "North Andover, MA"
    }
}
```

### 23.1.3 数组

JSON中的第二种复杂数据类型是数组。JSON数组采用的就是JavaScript中的数组字面量形式：

```json
[25, "hi", true]
```

同样要注意，JSON数组也没有变量和分号。把数组和对象结合起来，可以构成更复杂的数据集合，例如：

```json
[
    {
         "title": "Professional JavaScript",
         "authors": [
             "Nicholas C. Zakas"
         ],
         "edition": 3,
         "year": 2011
    },
    {
         "title": "Professional JavaScript",
         "authors": [
              "Nicholas C. Zakas"
         ],
         "edition": 2,
         "year": 2009
    },
    {
         "title": "Professional Ajax",
         "authors": [
             "Nicholas C. Zakas",
             "Jeremy McPeak",
             "Joe Fawcett"
         ],
         "edition": 2,
         "year": 2008
    },
    {
         "title": "Professional Ajax",
         "authors": [
             "Nicholas C. Zakas",
             "Jeremy McPeak",
             "Joe Fawcett"
         ],
         "edition": 1,
         "year": 2007
    },
    {
         "title": "Professional JavaScript",
         "authors": [
              "Nicholas C. Zakas"
         ],
         "edition": 1,
             "year": 2006
    }
]
```

## 23.2 解析与序列化

与XML数据结构要解析成DOM文档而且从中提取数据极为麻烦相比，JSON数据结构可以轻松地解析为JavaScript对象。

以上一节中包含一组图书的JSON数据结构为例，在解析为JavaScript对象后，只需要下面一行简单的代码就可以取得第三本书的书名：

```js
books[2].title
```

### 23.2.1 JSON对象

ECMAScript 5对解析JSON的行为进行规范，定义了全局对象`JSON`。JSON对象有2个方法：

- **`stringify()`方法** ：在最简单的情况下，这个方法用于把JavaScript对象序列化为JSON字符串。
- **`parse()` 方法**：在最简单的情况下，这个方法用于把JSON字符串解析为原生JavaScript值。

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};

// 使用JSON.stringify()把JavaScript对象序列化为一个JSON字符串
let jsonText = JSON.stringify(book);

// 将JSON字符串传递给JSON.parse()得到相应的JavaScript值
let bookCopy = JSON.parse(jsonText);
```



### 23.2.2 序列化选项

实际上，`JSON.stringify()`除了要序列化的JavaScript对象外，还可以接收另外2个参数，这两个参数用于指定以不同的方式序列化JavaScript对象：

- 第一个参数是个过滤器，可以是一个数组，也可以是一个函数；
- 第二个参数是一个选项，表示是否在JSON字符串中保留缩进。

单独或组合使用这两个参数，可以更全面深入地控制JSON的序列化。

#### 过滤结果

如果过滤器参数是数组，那么`JSON.stringify()`的结果中将只包含数组中列出的属性：

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};

let jsonText1 = JSON.stringify(book, ["title", "edition"]);
// "{"title":"Professional JavaScript","edition":4}"

let jsonText2 = JSON.stringify(book, ["title", "edition", "year"]);
// "{"title":"Professional JavaScript","edition":4,"year":2017}"
```

如果第2个参数是函数，行为会稍有不同。传入的函数接收两个参数，属性（键）名和属性值。根据属性（键）名可以知道应该如何处理要序列化的对象中的属性。属性名只能是字符串，而在值并非键值对结构的值时，键名可以是空字符串。

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};

let jsonText = JSON.stringify(book, (key, value) => {
    switch(key) {
        case "authors":
            return value.join(",")
        case "year":
            return 5000;
        case "edition":
            return undefined;  // 如果键为"edition",返回undefined表示删除该属性
        default:
            return value;
    }
});

console.log(jsonText); 
// "{"title":"Professional JavaScript","authors":"Nicholas C. Zakas,Matt Frisbie","year":5000}"
```

#### 字符串缩进

`JSON.stringify()`方法的第3个参数用于控制结果中的缩进和空白符。如果这个参数是一个数值，那它表示的是每个级别缩进的空格数。

例如，要在每个级别缩进4个空格，可以这样写代码：

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017
};

let jsonText = JSON.stringify(book, null, 4);
```

保存在jsonText中的字符串如下所示：
```js
{
    "title": "Professional JavaScript",
    "authors": [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    "edition": 4,
    "year": 2017
}
```

如果缩进参数是一个字符串而非数值，则这个字符串将在JSON字符串中被用作缩进字符（不再使用空格）。在使用字符串的情况下，可以将缩进字符设置为制表符，或者两个短划线之类的任意字符。

```js
let jsonText = JSON.stringify(book, null, "--");
```

`jsonText`中的字符串将变成如下所示：

```js
{
--"title": "Professional JavaScript",
--"authors": [
----"Nicholas C. Zakas",
----"Matt Frisbie"
--],
--"edition": 4,
--"year": 2017
}
```

#### `toJSON()`方法

`JSON.stringify()`还是不能满足对某些对象进行自定义序列化的需求。在这些情况下，可以给对象定义`toJSON()`方法，返回其自身的JSON数据格式。原生`Date`对象有一个`toJSON()`方法，能够将JavaScript的`Date`对象自动转换成ISO 8601日期字符串（与在`Date`对象上调用`toISOString()`的结果完全一样）。

可以为任何对象添加`toJSON()`方法，比如：

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017,
    toJSON: function() {
        return this.title;   //因为定义了toJSON方法，这个对象也将被序列化为一个简单的字符串而非对象。
    }
};

let jsonText = JSON.stringify(book);
```

可以让`toJSON()`方法返回任何值，它都能正常工作。比如，可以让这个方法返回`undefined`，此时如果包含它的对象嵌入在另一个对象中，会导致它的值变成`null`，而如果它是顶级对象，结果就是`undefined`。

**把一个对象传入`JSON.stringify()`，序列化该对象的顺序如下：**

1. 如果存在`toJSON()`方法而且能通过它取得有效的值，则调用该方法。否则，返回对象本身。
2. 如果提供了第二个参数，应用这个函数过滤器。传入函数过滤器的值是第1步返回的值。
3. 对第2步返回的每个值进行相应的序列化。
4. 如果提供了第三个参数，执行相应的序列化。

### 23.2.3 解析选项

`JSON.parse()`方法也可以接收另一个参数，该参数是一个函数，将在每个键值对儿上调用。为了区别`JSON.stringify()`接收的替换（过滤）函数（replacer），这个函数被称为还原函数（reviver），但实际上这两个函数的签名是相同的——它们都接收两个参数，一个键和一个值，而且都需要返回一个值。

如果还原函数返回`undefined`，则表示要从结果中删除相应的键；如果返回其他值，则将该值插入到结果中。在将日期字符串转换为`Date`对象时，经常要用到还原函数。例如：

```js
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017,
    releaseDate: new Date(2017, 11, 1)
};

let jsonText = JSON.stringify(book);
// 还原函数在遇到"releaseDate"键时，会基于相应的值创建一个新的Date对象。
let bookCopy = JSON.parse(jsonText, (key, value) => key == "releaseDate" ? new Date(value) : value);
alert(bookCopy.releaseDate.getFullYear());
```

