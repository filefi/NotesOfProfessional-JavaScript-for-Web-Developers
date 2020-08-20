# 第24章 网络请求与远程资源

XHR为向服务器发送请求和解析服务器响应提供了流畅的接口。能够以异步方式从服务器取得更多信息，意味着用户单击后，可以不必刷新页面也能取得新数据。也就是说，可以使用XHR对象取得新数据，然后再通过DOM将新数据插入到页面中。另外，虽然名字中包含XML的成分，但Ajax通信与数据格式无关；这种技术就是无须刷新页面即可从服务器取得数据，但不一定是XML数据。

The XHR object’s API is widely regarded as difficult to use, and, in the time since its introduction, the Fetch API has blossomed as a modernized replacement for XHR. Its support for promises and service workers has made it an incredibly powerful web development tool.

## 24.1 `XMLHttpRequest`对象

所有现代浏览器都支持原生的XHR对象，在这些浏览器中创建XHR对象要像下面这样使用`XMLHttpRequest`构造函数：

```js
let xhr = new XMLHttpRequest();
```

### 24.1.1 XHR的用法

**使用XHR以*同步*方式发送请求：**

1. 创建XHR对象`let xhr = new XMLHttpRequest();`
2. 调用`open()`方法以创建一个请求（但不会实际发送请求）；
3. 使用`setRequestHeader()`方法设置自定义的请求头部信息。*此为可选步骤。*
4. 调用`send()`方法发送请求；



**使用XHR以*异步*方式发送请求：**

1. 创建XHR对象`let xhr = new XMLHttpRequest();`
2. 为XHR对象创建`onreadystatechange`事件处理程序，以监听XHR对象的`readyState`状态；
3. 调用`open()`方法以创建一个请求（但不会实际发送请求）；
4. 使用`setRequestHeader()`方法设置自定义的请求头部信息。*此为可选步骤。*
5. 调用`send()`方法发送请求；
6. 如果需要，可以使用`abort()`方法取消异步请求。在终止异步请求后，应该对XHR对象进行解引用操作`xhr = null;`。*此为可选步骤。*



**XHR对象方法：**

- **`open()`方法** ：它接受3个参数：

  - 要发送的请求的类型（`"get"`、`"post"`等）；
  - 请求的URL，可以使用相对路径，也可以使用绝对路径；
  - 表示是否异步发送请求的布尔值。
- **`send()`方法** ：接收一个参数，即要作为请求主体发送的数据。如果不需要通过请求主体发送数据，则必须传入`null`，因为这个参数对有些浏览器来说是必需的。调用`send()`之后，请求就会被分派到服务器。
- **`abort()`方法** ：这个方法可以用来取消异步请求。
- **`setRequestHeader()`方法** ：使用`setRequestHeader()`方法可以设置自定义的请求头部信息。这个方法接受2个参数：头部字段的名称和头部字段的值。
- **`getResponseHeader()`方法** ：调用`getResponseHeader()`方法并传入头部字段名称，可以取得相应的响应头部信息。
- **`getAllResponseHeaders()`方法** ：调用`getAllResponseHeaders()`方法则可以取得一个包含所有头部信息的长字符串。



**在收到响应后，响应的数据会自动填充XHR对象的属性：**

- `responseText`：作为响应主体被返回的文本。
- `responseXML`：如果响应的内容类型是`"text/xml"`或`"application/xml"`，这个属性中将保存包含着响应数据的XML DOM文档。对于非XML数据而言，`responseXML`属性的值将为`null`。
- `status`：响应的HTTP状态。
- `statusText`：HTTP状态的说明。



如果请求是同步的，JavaScript代码会等到服务器响应之后再继续执行。以下是发送同步请求的例子：

```js
// 创建XHR对象
let xhr = new XMLHttpRequest();

// 创建请求
xhr.open("get", "example.txt", false); // 创建一个同步get请求，请求URL为"example.txt"

// 发送请求
xhr.send(null);
```



如果发送异步请求，可以能让JavaScript继续执行而不必等待响应。此时，可以检测XHR对象的`readyState`属性，该属性表示请求/响应过程的当前活动阶段。这个属性可取的值如下：

- `0`：未初始化。尚未调用`open()`方法。
- `1`：启动。已经调用`open()`方法，但尚未调用`send()`方法。
- `2`：发送。已经调用`send()`方法，但尚未接收到响应。
- `3`：接收。已经接收到部分响应数据。
- `4`：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

只要`readyState`属性的值由一个值变成另一个值，都会触发一次`readystatechange`事件。可以利用这个事件来检测每次状态变化后`readyState`的值。必须在调用`open()`之前指定`onreadystatechange`事件处理程序才能确保跨浏览器兼容性。

```js
// 1.创建XHR对象
let xhr = new XMLHttpRequest();

// 2.为XHR对象添加onreadystatechange事件处理程序
// 利用DOM 0级方法为XHR对象添加事件处理程序，原因是并非所有浏览器都支持DOM 2级方法。
xhr.onreadystatechange = function() {  // 与其他事件处理程序不同，这里没有向onreadystatechange事件处理程序中传递event对象；必须通过XHR对象本身来确定下一步该怎么做。
    if (xhr.readyState == 4) { // 响应数据已经全部接收
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) { // 如果响应状态正常
            alert(xhr.responseText);
        } else {
            alert("Request was unsuccessful: " + xhr.status);
        }
    }
};

// 3.创建请求
xhr.open("get", "example.txt", true); // 创建一个异步get请求，请求URL为"example.txt"

// 4.发送请求
xhr.send(null);
```

> **这个例子在`onreadystatechange`事件处理程序中使用了`xhr`对象，没有使用`this`对象，原因是`onreadystatechange`事件处理程序的作用域问题。如果使用`this`对象，在有的浏览器中会导致函数执行失败，或者导致错误发生。因此，使用实际的XHR对象实例变量是较为可靠的一种方式。**

> **注意：只能向同一个域中使用相同端口和协议的URL发送请求。如果URL与启动请求的页面有任何差别，都会抛出安全错误。**

另外，在接收到响应之前还可以调用`abort()`方法来取消异步请求，如下所示：

```js
xhr.abort();
```

调用这个方法后，`XHR`对象会停止触发事件，而且也不再允许访问任何与响应有关的对象属性。在终止请求之后，还应该对XHR对象进行解引用操作。由于内存原因，不建议重用`XHR`对象。



### 24.1.2 HTTP头部字段

每个HTTP请求和响应都会带有相应的头部信息。`XHR`对象也提供了操作 ***请求头部*** 和 ***响应头部*** 信息的方法。

默认情况下，在发送`XHR`请求的同时，还会发送下列头部信息：

- `Accept`：浏览器能够处理的内容类型。
- `Accept-Charset`：浏览器能够显示的字符集。
- `Accept-Encoding`：浏览器能够处理的压缩编码。
- `Accept-Language`：浏览器当前设置的语言。
- `Connection`：浏览器与服务器之间连接的类型。
- `Cookie`：当前页面设置的任何Cookie。
- `Host`：发出请求的页面所在的域 。
- `Referer`：发出请求的页面的URI。注意，HTTP规范将这个头部字段拼写错了，而为保证与规范一致，也只能将错就错了。（这个英文单词的正确拼法应该是referrer。）
- `User-Agent`：浏览器的用户代理字符串。

不同浏览器实际发送的头部信息会有所不同。

使用`setRequestHeader()`方法可以设置自定义的请求头部信息。这个方法接受两个参数：头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用`open()`方法之后且调用`send()`方法之前调用`setRequestHeader()`，如下面的例子所示：

```js
let xhr = new XMLHttpRequest();

xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            alert(xhr.responseText);
        } else {
            alert("Request was unsuccessful: " + xhr.status);
        }
    }
};

xhr.open("get", "example.php", true);

// 设置自定义头部字段
xhr.setRequestHeader("MyHeader", "MyValue");

xhr.send(null);
```



调用`XHR`对象的`getResponseHeader()`方法并传入头部字段名称，可以取得相应的响应头部信息。而调用`getAllResponseHeaders()`方法则可以取得一个包含所有头部信息的长字符串。来看下面的例子。

```js
let myHeader = xhr.getResponseHeader("MyHeader");
let allHeaders = xhr.getAllResponseHeaders();
```



### 24.1.3 `GET`请求

`GET`是最常见的请求类型，最常用于向服务器查询某些信息。

必要时，可以将查询字符串参数追加到URL的末尾，以便将信息发送给服务器。对XHR而言，位于传入`open()`方法的URL末尾的查询字符串必须使用`encodeURIComponent()`进行正确的编码才行，而且所有名-值对儿都必须由和号（&）分隔，如下面的例子所示。

```js
xhr.open("get", "example.php?name1=value1&name2=value2", true);
```



### 24.1.4 `POST`请求





## 24.2 `XMLHttpRequest`2级



### 24.2.1 `FormData`类型

### 24.2.2 超时设定

### 24.2.3 `overrideMimeType()`方法



## 24.3 进度事件

### 24.3.1 `load`事件

### 24.3.2 `progress`事件



## 24.4 跨域资源共享 (Cross-Origin Resource Sharing, CORS)

### 24.4.1 Preflighted Requests

### 24.4.2 Credentialed Requests



## 24.5 其他跨域技术

### 24.5.1 图像Pings

### 24.5.2 JSONP



## 24.6 The Fetch API

### 24.6.1 Basic API Utilization

### 24.6.2 Common Fetch Patterns

### 24.6.3 The Headers Object

### 24.6.4 The Request Object

### 24.6.5 The Response Object

### 24.6.6 Requests, Responses, and the Body Mixin



## 24.7 The Beacon API



## 24.8 Web Sockets

### 24.8.1 API

### 24.8.2 Sending/Receiving Data

### 24.8.3 Other Events



## 24.9 SECURITY

