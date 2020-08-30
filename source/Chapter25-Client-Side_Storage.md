# 第25章 客户端侧存储 (Client-Side Storage)

## 25.1 Cookies

HTTP Cookie，通常直接叫做cookie，最初是在客户端用于存储会话信息的。该标准要求服务器对任意HTTP请求发送Set-Cookie HTTP头作为响应的一部分，其中包含会话信息。例如，这种服务器响应的头可能如下：

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value
```

这个HTTP响应设置以`name`为名称、以`value`为值的一个cookie，名称和值在传送时都必须是URL编码的。浏览器会存储这样的会话信息，并在这之后，通过为每个请求添加Cookie HTTP头将信息发送回服务器，如下所示：

```
GET /index.html HTTP/1.1
Cookie: name=value
Other-header: other-header-value
```

发送回服务器的额外信息可以用于唯一验证客户来自于发送的哪个请求。



### 25.1.1 限制 (Restrictions)

- 储存在Cookie中的信息只能让*设置Cookie的域* 或 *指定的域* 或 *指定路径页面* 访问，而无法被其他域访问。

- 为确保Cookie不会被恶意使用并占用太多磁盘空间，每个域的cookie总数是有限的，浏览器各有不同。

- **浏览器中对于cookie的尺寸也有限制。** 为了最佳的浏览器兼容性，最好将整个cookie长度限制在4096字节以内。尺寸限制影响到一个域下所有的cookie，而并非每个cookie单独限制。

一般来说，按照下列限制使用cookie，就不会在任何浏览器中遇到问题：

- 300 cookies total
- 4096 bytes per cookie
- 20 cookies per domain
- 81920 bytes per domain



### 25.1.2 Cookie的构成 (Cookie Parts)

cookie由浏览器保存的以下几块信息构成：

- **名称** ：一个唯一确定cookie的名称。cookie名称是不区分大小写的，所以`myCookie`和`MyCookie`被认为是同一个cookie。然而，实践中最好将cookie名称看作是区分大小写的，因为某些服务器会这样处理cookie。**cookie的名称必须是经过URL编码的。**
- **值** ：储存在cookie中的字符串值。**值必须被URL编码。**
- **域** ：`domain`用于指定cookie对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。这个值可以包含子域（subdomain，如`www.wrox.com`），也可以不包含它（如`.wrox.com`，则对于wrox.com的所有子域都有效）。如果没有明确设定，那么这个域会被认作来自设置cookie的那个域。
- **路径** ：`path`用于指定对于指定域中的哪个路径，应该向服务器发送cookie。例如，你可以指定cookie只有从`http://www.wrox.com/books/`中才能访问，那么`http://www.wrox.com`的页面就不会发送cookie信息，即使请求都是来自同一个域的。
- **失效时间** ：`expires`表示cookie何时应该被删除的时间戳（也就是，何时应该停止向服务器发送这个cookie）。默认情况下，浏览器会话结束时即将所有cookie删除；不过也可以自己设置删除时间。这个值是个GMT格式的日期（Wdy, DD-Mon-YYYY HH:MM:SS GMT），用于指定应该删除cookie的准确时间。因此，cookie可在浏览器关闭后依然保存在用户的机器上。如果你设置的失效日期是个以前的时间，则cookie会被立刻删除。
- **安全标志** ：指定`secure`标志后，cookie只有在使用SSL连接的时候才发送到服务器。`secure`标志是cookie中唯一一个非名值对儿的部分，直接包含一个`secure`单词。例如，cookie信息只能发送给`https://www.wrox.com`，而`http://www.wrox.com`的请求则不能发送 cookie。

每一段信息都作为`Set-Cookie`头的一部分，使用分号加空格分隔每一段，如下例所示。

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value; expires=Mon, 22-Jan-07 07:10:24 GMT; domain=.wrox.com; path=/; secure
Other-header: other-header-value
```

**注意：域、路径、失效时间和secure标志都是服务器给浏览器的指示，以指定何时应该发送cookie。这些参数并不会作为发送到服务器的cookie信息的一部分，只有名值对儿才会被发送。**



### 25.1.3 JavaScript中的Cookies (Cookies in JavaScript)

使用`document.cookie`属性可以在JavaScript对Cookies进行操作。基本的cookie操作有三种：

- 读取
- 写入
- 删除

由于JavaScript中读、写、删除cookie不是非常直观，常常需要写一些函数来简化cookie的功能。它们在`CookieUtil`对象中如下表示：

```js
class CookieUtil {
    static get(name) {
        let cookieName = `${encodeURIComponent(name)}=`,
            cookieStart = document.cookie.indexOf(cookieName),
            cookieValue = null;
        
        if (cookieStart > -1){
            let cookieEnd = document.cookie.indexOf(";", cookieStart);
            if (cookieEnd == -1){
                cookieEnd = document.cookie.length;
            }
            cookieValue = decodeURIComponent(document.cookie.substring(cookieStart
                                                                + cookieName.length, cookieEnd));
        }
        return cookieValue;
    }
    
    static set(name, value, expires, path, domain, secure) {
        let cookieText = `${encodeURIComponent(name)}=${encodeURIComponent(value)}`;
        if (expires instanceof Date) {
            cookieText += `; expires=${expires.toGMTString()}`;
        }
        if (path) {
            cookieText += `; path=${path}`;
        }
        if (domain) {
            cookieText += `; domain=${domain}`;
        }
        if (secure) {
            cookieText += "; secure";
        }
        document.cookie = cookieText;
    }
    
    static unset(name, path, domain, secure) {
        CookieUtil.set(name, "", new Date(0), path, domain, secure);
    }
};
```

可以像下面这样使用这些方法：

```js
// set cookies
CookieUtil.set("name", "Nicholas");
CookieUtil.set("book", "Professional JavaScript");

// read the values
alert(CookieUtil.get("name")); // "Nicholas"
alert(CookieUtil.get("book")); // "Professional JavaScript"

// remove the cookies
CookieUtil.unset("name");
CookieUtil.unset("book");

// set a cookie with path, domain, and expiration date
CookieUtil.set("name", "Nicholas", "/books/projs/", "www.wrox.com", new Date("January 1, 2010"));

// delete that same cookie
CookieUtil.unset("name", "/books/projs/", "www.wrox.com");

// set a secure cookie
CookieUtil.set("name", "Nicholas", null, null, null, true);
```



#### 读取

当用来**读取属性值**时，`document.cookie`返回当前页面可用的（根据cookie的域、路径、失效时间和安全设置）所有cookie的字符串，一系列由分号隔开的名值对，如下例所示：

```
name1=value1;name2=value2;name3=value3
```

所有名字和值都是经过URL编码的，所以必须使用`decodeURIComponent()`来解码。

#### 写入

当用于**写入属性值**时，`document.cookie`属性可以设置为一个新的cookie字符串。这个cookie字符串会被解释并添加到现有的cookie集合中。设置`document.cookie`并不会覆盖cookie，除非设置的cookie的名称已经存在。设置cookie的格式如下，和`Set-Cookie`头中使用的格式一样。

```
name=value; expires=expiration_time; path=domain_path; domain=domain_name; secure
```

最好每次设置cookie时都使用`encodeURIComponent()`对cookie的名称和值进行URL编码：

```js
document.cookie = encodeURIComponent("name") + "=" +
                  encodeURIComponent("Nicholas"); 
```

要给被创建的cookie指定额外的信息，只要将参数追加到该字符串，和`Set-Cookie`头中的格式一样，如下所示：

```js
document.cookie = encodeURIComponent("name") + "=" +
                   encodeURIComponent("Nicholas") + "; domain=.wrox.com; path=/";
```

#### 删除

没有删除已有cookie的直接方法。所以，需要使用相同的路径、域和安全选项再次设置cookie，并将失效时间设置为过去的时间。`CookieUtil.unset()`方法可以处理这种事情。它接收4个参数：要删除的cookie的名称、可选的路径参数、可选的域参数和可选的安全参数。



### 25.1.4 Subcookies

为了绕开浏览器的单域名下的cookie数限制，一些开发人员使用了一种称为**子cookie**（subcookie）的概念。子cookie是存放在单个cookie中的更小段的数据。也就是使用cookie值来存储多个名称值对儿。子cookie最常见的的格式如下所示。

```
name=name1=value1&name2=value2&name3=value3&name4=value4&name5=value5
```

子cookie一般也以查询字符串的格式进行格式化。然后这些值可以使用单个cookie进行存储和访问，而非对每个“名称值对”使用不同的cookie存储。最后网站或者Web应用程序可以无需达到单域名cookie上限也可以存储更加结构化的数据。



### 25.1.5 关于cookie的思考 (Cookie Considerations)

还有一类cookie被称为 *“HTTP-only”*。HTTP-only cookie可以从浏览器或者服务器设置，但是只能从服务器端读取，因为JavaScript无法获取HTTP-only cookie的值。

最好还是尽可能在cookie中少存储信息，以避免影响性能。

**注意：一定不要在cookie中存储重要和敏感的数据。**



## 25.2 WEB STORAGE

Web Storage的目的是克服由cookie带来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。Web Storage的两个主要目标是：

- 提供一种在cookie之外存储会话数据的途径；
- 提供一种存储大量可以跨会话存在的数据的机制。

最初的Web Storage规范包含了两种对象的定义：`sessionStorage`和`localStorage`。这两个对象在支持的浏览器中都是以`window`对象属性的形式存在的。

### 25.2.1 `Storage`类型

`Storage`类型提供最大的存储空间（因浏览器而异）来存储名值对儿。`Storage`的实例与其他对象类似，有如下方法。

- `clear()`： 删除所有值；Firefox中没有实现 。
- `getItem(name)`：根据指定的名字`name`获取对应的值。
- `key(index)`：获得index位置处的值的名字。
- `removeItem(name)`：删除由`name`指定的名值对儿。
- `setItem(name, value)`：为指定的`name`设置一个对应的值。

其中，`getItem()`、`removeItem()`和`setItem()`方法可以直接调用，也可通过`Storage`对象间接调用。因为每个项目都是作为属性存储在该对象上的，所以可以通过点语法或者方括号语法访问属性来设置、读取值，或者通过`delete`操作符进行删除。

### 25.2.2 `sessionStorage`对象

`sessionStorage`对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。这个对象就像会话cookie，也会在浏览器关闭后消失。存储在`sessionStorage`中的数据可以跨越页面刷新而存在，同时如果浏览器支持，浏览器崩溃并重启之后依然可用。

因为`sessionStorage`对象绑定于某个服务器会话，所以当文件在本地运行的时候是不可用的。存储在`sessionStorage`中的数据只能由最初给对象存储数据的页面访问到，所以对多页面应用有限制。

**`sessionStorage`对象应该主要用于仅针对会话的小段数据的存储。如果需要跨越会话存储数据，那么`globalStorage`或者`localStorage`更为合适。**

`sessionStorage`对象其实是`Storage`的一个实例，所以可以使用`setItem()`或者直接设置新的属性来存储数据：

```js
//使用方法存储数据
sessionStorage.setItem("name", "Nicholas");

//使用属性存储数据
sessionStorage.book = "Professional JavaScript"
```

不同浏览器写入数据方式略有不同。Firefox和WebKit实现了同步写入，所以添加到存储空间中的数据是立刻被提交的。而IE的实现则是异步写入数据，所以在设置数据和将数据实际写入磁盘之间可能有一些延迟。

`sessionStorage`中有数据时，可以使用`getItem()`或者通过直接访问属性名来获取数据：

```js
// get data using method
let name = sessionStorage.getItem("name");

// get data using property
let book = sessionStorage.book;
```

还可以通过结合`length`属性和`key()`方法来迭代`sessionStorage`中的值，如下所示：

```js
for (let i = 0, len = sessionStorage.length; i < len; i++){
    let key = sessionStorage.key(i);
    let value = sessionStorage.getItem(key);
    alert(`${key}=`${value}`);
}
```

还可以使用`for-in`循环来迭代`sessionStorage`中的值：

```js
for (let key in sessionStorage){
    let value = sessionStorage.getItem(key);
    alert(`${key}=${value}`);
}
```

要从`sessionStorage`中删除数据，可以使用`delete`操作符删除对象属性，也可调用`removeItem()`方法：

```js
// use delete to remove a value
delete sessionStorage.name;

// use method to remove a value
sessionStorage.removeItem("book");
```



### 25.2.3 The localStorage Object

`localStorage`对象在修订过的HTML 5规范中作为持久保存客户端数据的方案取代了`globalStorage`。与`globalStorage`不同，不能给`localStorage`指定任何访问规则；规则事先就设定好了。要访问同一个`localStorage`对象，页面必须来自同一个域名（子域名无效），使用同一种协议，在同一个端口上。这相当于`globalStorage[location.host]`。

由于`localStorage`是`Storage`的实例，所以可以像使用`sessionStorage`一样来使用它。下面是一些例子。

```js
//使用方法存储数据
localStorage.setItem("name", "Nicholas");

//使用属性存储数据
localStorage.book = "Professional JavaScript";

//使用方法读取数据
let name = localStorage.getItem("name");

//使用属性读取数据
let book = localStorage.book;
```

存储在`localStorage`中的数据和存储在`globalStorage`中的数据一样，都遵循相同的规则：数据保留到通过`JavaScript`删除或者是用户清除浏览器缓存。

### 25.2.4 `storage`事件

对`Storage`对象（`sessionStorage`、`globalStorage`、`localStorage`）进行任何修改，都会在文档上触发`storage`事件。当通过属性或`setItem()`方法保存数据，使用`delete`操作符或`removeItem()`删除数据，或者调用`clear()`方法时，都会发生该事件。这个事件的`event`对象有以下属性。

- `domain`：发生变化的存储空间的域名。
- `key`：设置或者删除的键名。
- `newValue`：如果是设置值，则是新值；如果是删除键，则是`null`。
- `oldValue`：键被更改之前的值。

```js
window.addEventListener("storage",
    (event) => alert('Storage changed for ${event.domain}'));
```

### 25.2.5 Limits and Restrictions

与其他客户端数据存储方案类似，Web Storage同样也有限制。这些限制因浏览器而异。一般来说，对存储空间大小的限制都是以每个来源（协议、域和端口）为单位的。换句话说，每个来源都有固定大小的空间用于保存自己的数据。

对`localStorage`和`sessionStorage`的存储限制因浏览器而异，大多数桌面浏览器会设置每个来源5MB的限制。



## 25.3 IndexedDB

### 25.3.1 Databases

### 25.3.2 Object Stores

### 25.3.3 Transactions

### 25.3.4 Insertion

### 25.3.5 Querying with Cursors

### 25.3.6 Key Ranges

### 25.3.7 Setting Cursor Direction

### 25.3.8 Indexes

### 25.3.9 Concurrency Issues

### 25.3.10 Limits and Restrictions

