# 第12章 浏览器对象模型（BOM）

## 12.1 `window`对象

BOM的核心对象是`window`，它表示浏览器的一个实例。

在浏览器中，`window`对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的`Global`对象。这意味着在网页中定义的任何一个对象、变量和函数，都以`window`作为其`Global`对象.

### 12.1.1 全局作用域

