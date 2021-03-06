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
2. 为XHR对象创建`onreadystatechange`事件处理程序，以监听XHR对象的`readyState`状态；也可以为XHR对象创建`onload`或`onprogress`等进度事件处理程序；*此为可选步骤。*
4. 调用`open()`方法以创建一个请求（但不会实际发送请求）；
5. 使用`setRequestHeader()`方法设置自定义的请求头部信息。*此为可选步骤。*
6. 使用`overrideMimeType()`方法，用于重写XHR响应的MIME类型。*此为可选步骤。*
7. 使用`timeout`属性，设置请求在等待响应多少毫秒之后就终止。*此为可选步骤。*
8. 如果`timeout`属性被设置，当请求超时，会触发XHR对象的`ontimeout`事件，可以为XHR对象创建`ontimeout`事件处理程序。*此为可选步骤。*
9. 调用`send()`方法发送请求；
10. 如果需要，可以使用`abort()`方法取消异步请求。在终止异步请求后，应该对XHR对象进行解引用操作`xhr = null;`。*此为可选步骤。*



**XHR对象方法：**

- **`open()`方法** ：它接受3个参数：

  - 要发送的请求的类型（`"get"`、`"post"`等）；
  - 请求的URL，可以使用相对路径，也可以使用绝对路径；
  - 表示是否异步发送请求的布尔值。
- **`send()`方法** ：接收一个参数，即要作为请求主体发送的数据。如果不需要通过请求主体发送数据，则必须传入`null`，因为这个参数对有些浏览器来说是必需的。调用`send()`之后，请求就会被分派到服务器。
- **`abort()`方法** ：这个方法可以用来取消异步请求。
- **`setRequestHeader()`方法** ：使用`setRequestHeader()`方法可以设置自定义的请求头部信息。这个方法接受2个参数：头部字段的名称和头部字段的值。
- **`getResponseHeader()`方法** ：调用`getResponseHeader()`方法并传入一个头部字段名称作为参数，可以取得相应的响应头部信息。
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

Progress Events规范是W3C的一个工作草案，定义了与客户端服务器通信有关的事件。这些事件最早其实只针对XHR操作，但目前也被其他API借鉴。有以下6个进度事件：

- `loadstart`：在接收到响应数据的第一个字节时触发。
- `progress`：在接收响应期间持续不断地触发。
- `error`：在请求发生错误时触发。
- `abort`：在因为调用`abort()`方法而终止连接时触发。
- `load`：在接收到完整的响应数据时触发。
- `loadend`：在通信完成或者触发`error`、`abort`或`load`事件后触发。

每个请求都从触发`loadstart`事件开始，接下来是一或多个`progress`事件，然后触发`error`、`abort`或`load`事件中的一个，最后以触发`loadend`事件结束。

### 24.3.1 `load`事件

为了简化异步交互模型，Firefox实现中引入了`load`事件，用以替代`readystatechange`事件。响应接收完毕后将触发`load`事件，因此也就没有必要去检查`readyState`属性了。而`onload`事件处理程序会接收到一个`event`对象，其`target`属性就指向XHR对象实例，因而可以访问到XHR对象的所有方法和属性。然而，并非所有浏览器都为这个事件实现了适当的事件对象。结果，开发人员还是要像下面这样被迫使用`XHR`对象变量：

```js
let xhr = new XMLHttpRequest();
xhr.onload = function() {
    // 只要浏览器接收到服务器的响应，不管其状态如何，都会触发load事件。
    // 必须要检查status属性，才能确定数据是否真的已经可用了。
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        alert(xhr.responseText);
    } else {
        alert("Request was unsuccessful: " + xhr.status);
    }
};
xhr.open("get", "altevents.php", true);
xhr.send(null);
```

### 24.3.2 `progress`事件

Mozilla为XHR添加了`progress`事件，这个事件会在浏览器接收新数据期间周期性地触发。而`onprogress`事件处理程序会接收到一个`event`对象，其`target`属性是`XHR`对象，但包含着3个额外的属性：

- `lengthComputable`是一个表示进度信息是否可用的布尔值；
- `position`表示已经接收的字节数；
- `totalSize`表示根据`Content-Length`响应头部确定的预期字节数。

有了这些信息，我们就可以为用户创建一个进度指示器了。下面展示了为用户创建进度指示器的一个示例：

```js
let xhr = new XMLHttpRequest();

xhr.onload = function(event) {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        console.log(xhr.responseText);
    } else {
        console.log("Request was unsuccessful: " + xhr.status);
    }
};

// 必须在调用open()方法之前添加onprogress事件处理程序。
xhr.onprogress = function(event) {
    let divStatus = document.getElementById("status");
    if (event.lengthComputable) {
        // 每次触发progress事件，都会以新的状态信息更新HTML元素的内容。
        divStatus.innerHTML = `Received ${event.position} of ${event.totalSize} bytes`;
    }
};

xhr.open("get", "altevents.php", true);
xhr.send(null);
```



## 24.4 跨域资源共享 (Cross-Origin Resource Sharing, CORS)

CORS（Cross-Origin Resource Sharing，跨源资源共享）是W3C的一个工作草案，定义了在必须访问跨源资源时，浏览器与服务器应该如何沟通。CORS背后的基本思想，就是使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。

比如一个简单的使用`GET`或`POST`发送的请求，它没有自定义的头部，而主体内容是`text/plain`。在发送该请求时，需要给它附加一个额外的`Origin`头部，其中包含请求页面的源信息（协议、域名和端口），以便服务器根据这个头部信息来决定是否给予响应。下面是`Origin`头部的一个示例：

```
Origin: http://www.nczonline.net
```

如果服务器认为这个请求可以接受，就在`Access-Control-Allow-Origin`头部中回发相同的源信息（如果是公共资源，可以回发`"*"`）。例如：

```
Access-Control-Allow-Origin: http://www.nczonline.net
```

**如果没有这个头部，或者有这个头部但源信息不匹配，浏览器就会驳回请求。如果头部信息正确，浏览器会处理请求。**

**注意，请求和响应都不包含cookie信息。**

现代浏览器都通过`XMLHttpRequest`对象实现了对CORS的原生支持。在尝试打开不同来源的资源时，无需额外编写代码就可以触发这个行为。要请求位于另一个域中的资源，使用标准的XHR对象并在`open()`方法中传入绝对URL即可，例如：

```js
let xhr = new XMLHttpRequest();

xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
        } else {
            console.log("Request was unsuccessful: " + xhr.status);
        }
    }
};

xhr.open("get", "http://www.somewhere-else.com/page/", true);
xhr.send(null);
```

**为了安全，跨域XHR对象有一些限制：**

- 不能使用`setRequestHeader()`设置自定义头部。
- 不能发送和接收cookie。
- 调用`getAllResponseHeaders()`方法总会返回空字符串。

由于无论同源请求还是跨源请求都使用相同的接口，因此对于本地资源，最好使用相对URL，在访问远程资源时再使用绝对URL。这样做能消除歧义，避免出现限制访问头部或本地cookie信息等问题。

### 24.4.1 Preflighted Requests

CORS通过一种叫做Preflighted Requests的透明服务器验证机制支持开发人员使用自定义的头部、GET或POST之外的方法，以及不同类型的主体内容。在使用下列高级选项来发送请求时，就会向服务器发送一个Preflight请求。这种请求使用OPTIONS方法，发送下列头部：

- `Origin`：与简单的请求相同。
- `Access-Control-Request-Method`：请求自身使用的方法。
- `Access-Control-Request-Headers`：（可选）自定义的头部信息，多个头部以逗号分隔。

以下是一个带有自定义头部NCZ的使用`POST`方法发送的请求。

```
Origin: http://www.nczonline.net
Access-Control-Request-Method: POST
Access-Control-Request-Headers: NCZ
```

发送这个请求后，服务器可以决定是否允许这种类型的请求。服务器通过在响应中发送如下头部与浏览器进行沟通：

- `Access-Control-Allow-Origin`：与简单的请求相同。
- `Access-Control-Allow-Methods`：允许的方法，多个方法以逗号分隔。
- `Access-Control-Allow-Headers`：允许的头部，多个头部以逗号分隔。
- `Access-Control-Max-Age`：应该将这个Preflight请求缓存多长时间（以秒表示）。

例如：

```
Access-Control-Allow-Origin: http://www.nczonline.net
Access-Control-Allow-Methods: POST, GET
Access-Control-Allow-Headers: NCZ
Access-Control-Max-Age: 1728000
```

Preflight请求结束后，结果将按照响应中指定的时间缓存起来。而为此付出的代价只是第一次发送这种请求时会多一次HTTP请求。

### 24.4.2 带凭据的请求 (Credentialed Requests)

默认情况下，跨源请求不提供凭据（cookie、HTTP认证及客户端SSL证明等）。通过将`withCredentials`属性设置为`true`，可以指定某个请求应该发送凭据。如果服务器接受带凭据的请求，会用下面的HTTP头部来响应。

```
Access-Control-Allow-Credentials: true
```

如果发送的是带凭据的请求，但服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给JavaScript（于是，`responseText`中将是空字符串，`status`的值为0，而且会调用`onerror()`事件处理程序）。另外，服务器还可以在Preflight响应中发送这个HTTP头部，表示允许源发送带凭据的请求。



## 24.5 其他跨域技术

在CORS出现以前，开发人员利用DOM中能够执行跨域请求的功能，在不依赖XHR对象的情况下也能发送某种跨域请求。

### 24.5.1 图像Pings

第一种跨域请求技术使用`<img>`标签。我们知道，一个网页可以从任何网页中加载图像，不用担心跨域不跨域。也可以动态地创建图像，使用它们的`onload`和`onerror`事件处理程序来确定是否接收到了响应。

动态创建图像经常用于**图像**Ping。图像Ping是与服务器进行简单、单向的跨域通信的一种方式。请求的数据是通过查询字符串形式发送的，而响应可以是任意内容，但通常是像素图或204响应。通过图像Ping，浏览器得不到任何具体的数据，但通过侦听`load`和`error`事件，它能知道响应是什么时候接收到的。

```js
let img = new Image();

// onload和onerror事件处理程序指定为同一个函数，无论是什么响应，只要请求完成，就能得到通知。
img.onload = img.onerror = function() {
    alert("Done!");
};

// 设置src属性将发送请求
img.src = "http://www.example.com/test?name=Nicholas";
```

图像Ping最常用于跟踪用户点击页面或动态广告曝光次数。

图像Ping有两个主要的缺点：

- 一是只能发送`GET`请求；

- 二是无法访问服务器的响应文本。

因此，图像Ping只能用于浏览器与服务器间的单向通信。

### 24.5.2 JSONP

JSONP是JSON with padding（填充式JSON或参数式JSON）的简写，这种应用JSON的新方法已经变成了一种流行的Web服务。

JSONP看起来与JSON差不多，只不过是被包含在函数调用中的JSON，就像下面这样：

```js
callback({ "name": "Nicholas" });
```

JSONP由2部分组成：

- **回调函数** ：是当响应到来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。
- **数据** ：就是传入回调函数中的JSON数据。

下面是一个典型的JSONP请求，这里指定的回调函数的名字叫`handleResponse()`：
```
http://freegeoip.net/json/?callback=handleResponse
```

SONP是通过动态`<script>`元素来使用的，使用时可以为`src`属性指定一个跨域URL。`<script>`元素与`<img>`元素类似，都有能力不受限制地从其他域加载资源。

```js
function handleResponse(response) {
    console.log(
        `You're at IP address ${response.ip}, which is in
        ${response.city}, ${response.region_name}`
    );
}

let script = document.createElement("script");

// 设置src属性发送请求
script.src = "http://freegeoip.net/json/?callback=handleResponse";

// 在JSONP响应加载到页面中以后，就会立即执行。
document.body.insertBefore(script, document.body.firstChild);
```

与图像Ping相比，它的**优点**在于能够直接访问响应文本，支持在浏览器与服务器之间双向通信。

JSONP也有2点**不足** ：

- 首先，JSONP是从其他域中加载代码执行。如果其他域不安全，很可能会在响应中夹带一些恶意代码。

- 其次，要确定JSONP请求是否失败并不容易。虽然HTML5给`<script>`元素新增了一个`onerror`事件处理程序，但目前还没有得到任何浏览器支持。为此，开发人员不得不使用计时器检测指定时间内是否接收到了响应。但就算这样也不能尽如人意，毕竟不是每个用户上网的速度和带宽都一样。



## 24.6 The Fetch API

### 24.6.1 Basic API Utilization

### 24.6.2 Common Fetch Patterns

### 24.6.3 The Headers Object

### 24.6.4 The Request Object

### 24.6.5 The Response Object

### 24.6.6 Requests, Responses, and the Body Mixin



## 24.7 The Beacon API



## 24.8 Web Sockets

Web Sockets的目标是在一个单独的持久连接上提供全双工、双向通信。在JavaScript中创建了Web Socket之后，会有一个HTTP请求发送到服务器以发起连接。在取得服务器响应后，建立的连接会使用HTTP`Upgrade`头部 (header) 来将HTTP协议切换为Web Socket协议。也就是说，使用标准的HTTP服务器无法实现Web Sockets，只有支持这种协议的专门服务器才能正常工作。

由于Web Sockets使用了自定义的协议，所以URL模式也略有不同。未加密的连接不再是`http://`，而是`ws://`；加密的连接也不是`https://`，而是`wss://`。在使用Web Socket URL时，必须带着这个模式，因为将来还有可能支持其他模式。

### 24.8.1 API

要创建Web Socket，先实例一个`WebSocket`对象并传入要连接的绝对URL。实例化了`WebSocket`对象后，浏览器就会马上尝试创建连接。

```js
var socket = new WebSocket("ws://www.example.com/server.php");
```

**注意，必须给`WebSocket`构造函数传入绝对URL。**

**注意，同源策略对Web Sockets不适用，因此可以通过它打开到任何站点的连接。** 至于是否会与某个域中的页面通信，则完全取决于服务器。（通过握手信息就可以知道请求来自何方。）

与XHR类似，`WebSocket`也有一个表示当前状态的`readyState`属性：

- `WebSocket.OPENING (0)`：正在建立连接。
- `WebSocket.OPEN (1)`：已经建立连接。
- `WebSocket.CLOSING (2)`：正在关闭连接。
- `WebSocket.CLOSE (3)`：已经关闭连接。

`WebSocket`没有`readystatechange`事件；不过，它有其他事件，对应着不同的状态。`readyState`的值永远从0开始。

要关闭Web Socket连接，可以在任何时候调用`close()`方法：

```js
socket.close();
```

调用了`close()`之后，`readyState`的值立即变为2（正在关闭），而在关闭连接后就会变成3。

### 24.8.2 Sending/Receiving Data

Web Socket打开之后，就可以通过连接发送和接收数据。要向服务器发送数据，使用`send()`方法并传入任意字符串、`ArrayBuffer`或者`Blob`，例如：

```js
let socket = new WebSocket("ws://www.example.com/server.php");

let stringData = "Hello world!";
let arrayBufferData = Uint8Array.from(['f', 'o' 'o']);
let blobData = new Blob(['f', 'o' 'o']);

socket.send(stringData);
socket.send(arrayBufferData.buffer);
socket.send(blobData);
```

当服务器向客户端发来消息时，`WebSocket`对象就会触发`message`事件。这个`message`事件与其他传递消息的协议类似，也是把返回的数据保存在`event.data`属性中：

```js
socket.onmessage = function(event) {
    let data = event.data;
    // do something with data
};
```

与通过`send()`发送到服务器的数据一样，`event.data`中返回的数据也可以被解析为`ArrayBuffer`或者`Blob`。这是被WebSocket对象的`binaryType`所控制的，它可以是`blob`或者`arraybuffer`。

### 24.8.3 Other Events

`WebSocket`对象还有其他3个事件，在连接生命周期的不同阶段触发：

- `open`：在成功建立连接时触发。
- `error`：在发生错误时触发，连接不能持续。
- `close`：在连接关闭时触发。

`WebSocket`对象不支持DOM 2级事件侦听器，因此必须使用DOM 0级语法分别定义每个事件处理程序。

```js
var socket = new WebSocket("ws://www.example.com/server.php");

socket.onopen = function(){ 
    alert("Connection established.");
};

socket.onerror = function(){
    alert("Connection error.");
};

socket.onclose = function(){
    alert("Connection closed.");
};
```

在这三个事件中，只有`close`事件的`event`对象有额外的信息。这个事件的事件对象有三个额外的属性：

- `wasClean`是一个布尔值，表示连接是否已经明确地关闭；
- `code`是服务器返回的数值状态码；
- `reason`是一个字符串，包含服务器发回的消息。

可以把这些信息显示给用户，也可以记录到日志中以便将来分析：

```js
socket.onclose = function(event) {
    console.log(`as clean? ${event.wasClean} Code=${event.code} Reason=${event.reason}`);
};
```



## 24.9 SECURITY

为确保通过XHR访问的URL安全，通行的做法就是验证发送请求者是否有权限访问相应的资源。有下列几种方式可供选择。

- 要求以SSL连接来访问可以通过XHR请求的资源。
- 要求每一次请求都要附带经过相应算法计算得到的验证码。

请注意，下列措施对防范CSRF攻击不起作用。

- 要求发送`POST`而不是`GET`请求——很容易改变。
- 检查来源URL以确定是否可信——来源记录很容易伪造。
- 基于cookie信息进行验证——同样很容易伪造。