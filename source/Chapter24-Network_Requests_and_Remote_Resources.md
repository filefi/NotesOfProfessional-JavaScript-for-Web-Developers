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
4.  使用`overrideMimeType()`方法，用于重写XHR响应的MIME类型。*此为可选步骤。*
5. 调用`send()`方法发送请求；



**使用XHR以*异步*方式发送请求：**

1. 创建XHR对象`let xhr = new XMLHttpRequest();`
2. 为XHR对象创建`onreadystatechange`事件处理程序，以监听XHR对象的`readyState`状态；
3. 调用`open()`方法以创建一个请求（但不会实际发送请求）；
4. 使用`setRequestHeader()`方法设置自定义的请求头部信息。*此为可选步骤。*
5.  使用`overrideMimeType()`方法，用于重写XHR响应的MIME类型。*此为可选步骤。*
6. 使用`timeout`属性，设置请求在等待响应多少毫秒之后就终止。*此为可选步骤。*
7. 如果`timeout`属性被设置，当请求超时，会触发XHR对象的`ontimeout`事件，可以为XHR对象创建`ontimeout`事件处理程序。*此为可选步骤。*
8. 调用`send()`方法发送请求；
9. 如果需要，可以使用`abort()`方法取消异步请求。在终止异步请求后，应该对XHR对象进行解引用操作`xhr = null;`。*此为可选步骤。*



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

`POST`请求应该把数据作为请求的主体提交。`POST`请求的主体可以包含非常多的数据，而且格式不限。在`open()`方法第一个参数的位置传入`"post"`，就可以初始化一个`POST`请求，如下面的例子所示：

```js
xhr.open("post", "example.php", true);
```

发送`POST`请求的第二步就是向`send()`方法中传入某些数据。

我们可以使用XHR来模仿表单提交：首先将`Content-Type`头部信息设置为`application/x-www-form-urlencoded`，也就是表单提交时的内容类型，其次是以适当的格式创建一个字符串。`POST`数据的格式与查询字符串格式相同。如果需要将页面中表单的数据进行序列化，然后再通过XHR发送到服务器。

```js
function submitData() {
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
    xhr.open("post", "postexample.php", true);
    // 构造表单的Content-Type头部
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    let form = document.getElementById("user-info");
    // 将POST数据格式化为与查询字符串相同格式的字符串，然后序列化，在传给send()方法；
    xhr.send(serialize(form));
}
```



## 24.2 `XMLHttpRequest`2级

### 24.2.1 `FormData`类型

XMLHttpRequest 2级定义了`FormData`类型。`FormData`为序列化表单以及创建与表单格式相同的数据（用于通过XHR传输）提供了便利。使用`FormData`的方便之处体现在不必明确地在`XHR`对象上设置请求头部。XHR对象能够识别传入的数据类型是`FormData`的实例，并配置适当的头部信息。

`FormData`对象方法：

- **`append()`方法** ：这个方法接收两个参数：键和值，分别对应表单字段的名字和字段中包含的值。可以使用这个方法添加任意多个键值对。

下面的代码创建了一个`FormData`对象，并向其中添加了一些数据：

```js
let data = new FormData();
data.append("name", "Nicholas");
```

而通过向`FormData`构造函数中传入表单元素，也可以用表单元素的数据预先向其中填入键值对：

```js
let data = new FormData(document.forms[0]);
```

创建了`FormData`的实例后，可以将它直接传给XHR的`send()`方法，如下所示：

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
xhr.open("post", "postexample.php", true);
let form = document.getElementById("user-info");
xhr.send(new FormData(form));
```



### 24.2.2 超时设定

XHR对象的`timeout`属性，表示请求在等待响应多少毫秒之后就终止。在给`timeout`设置一个数值后，如果在规定的时间内浏览器还没有接收到响应，那么就会触发`timeout`事件，进而会调用`ontimeout`事件处理程序。

```js
let xhr = new XMLHttpRequest();

xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        // 在xhr超时终止时，readyState可能已经改变为4了，这意味着会调用onreadystatechange事件处理程序。
        // 如果在超时终止请求之后再访问status属性，就会导致错误。
        // 为避免浏览器报告错误，可以将检查status属性的语句封装在一个try-catch语句当中。
        try {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                alert(xhr.responseText);
            } else {
                alert("Request was unsuccessful: " + xhr.status);
            }
        } catch (ex) {
        	// 假设由ontimeout事件处理程序处理
        }
    }
};

xhr.open("get", "timeout.php", true);

// 将超时设置为1000毫秒
xhr.timeout = 1000; // 如果请求在1秒钟内还没有返回，就会自动终止。

// 请求终止时，会调用ontimeout事件处理程序。
xhr.ontimeout = function() {
    alert("Request did not return in a second.");
};

xhr.send(null);
```



### 24.2.3 `overrideMimeType()`方法

**`overrideMimeType()`方法** 用于重写XHR响应的MIME类型。因为返回响应的MIME类型决定了XHR对象如何处理它，所以提供一种方法能够重写服务器返回的MIME类型是很有用的。

服务器返回的MIME类型是`text/plain`，但数据中实际包含的是XML。根据MIME类型，即使数据是XML，`responseXML`属性中仍然是`null`。通过调用`overrideMimeType()`方法，可以保证把响应当作XML而非纯文本来处理。

```js
let xhr = new XMLHttpRequest();
xhr.open("get", "text.php", true);
// 强迫XHR对象将响应当作XML而非纯文本来处理。
xhr.overrideMimeType("text/xml"); // overrideMimeType()方法必须在发送请求前调用
xhr.send(null);
```



## 24.3 进度事件

Progress Events规范是W3C的一个工作草案，定义了与客户端服务器通信有关的事件。有以下6个进度事件：

- `loadstart`：在接收到响应数据的第一个字节时触发。
- `progress`：在接收响应期间持续不断地触发。
- `error`：在请求发生错误时触发。
- `abort`：在因为调用`abort()`方法而终止连接时触发。
- `load`：在接收到完整的响应数据时触发。
- `loadend`：在通信完成或者触发`error`、`abort`或`load`事件后触发。

每个请求都从触发`loadstart`事件开始，接下来是一或多个`progress`事件，然后触发`error`、`abort`或`load`事件中的一个，最后以触发`loadend`事件结束。

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

