# 第23章 JSON

JSON是一种数据格式，不是一种编程语言。并不是只有JavaScript才使用JSON，很多编程语言都有针对JSON的解析器和序列化器。JSON目前已经成为Web服务开发中交换数据的事实标准。

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

如果第二个参数是函数，行为会稍有不同。传入的函数接收两个参数，属性（键）名和属性值。根据属性（键）名可以知道应该如何处理要序列化的对象中的属性。属性名只能是字符串，而在值并非键值对结构的值时，键名可以是空字符串。

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

