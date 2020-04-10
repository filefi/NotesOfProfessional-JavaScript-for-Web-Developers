# 第3章 基本概念

## 3.1 语法

**区分大小写：** ECMAScript中的一切（变量、函数名和操作符）都区分大小写。

**标识符：** 就是指变量、函数、属性的名字，或者函数的参数。标识符可以是按照下列格式规则组合起来的一或多个字符：

- 第一个字符必须是一个字母、下划线（_）或一个美元符号（$）；
- 其他字符可以是字母、下划线、美元符号或数字。

**注释：** 包括单行注释和块级注释：

```js
// 单行注释

/*
 *  这是一个多行
 *  （块级）注释
 */
```

**严格模式：** ECMAScript 5引入了 **严格模式（strict mode）** 的概念。在严格模式下，ECMAScript 3中的一些不确定的行为将得到处理，而且对某些不安全的操作也会抛出错误。要在整个脚本中启用严格模式，可以在顶部添加如下代码：

```js
"use strict";
```

在函数内部的上方包含这条编译指示，也可以指定函数在严格模式下执行：

```js
function doSomething(){
    "use strict"; 
    //函数体
}
```

**语句：** ECMAScript中的语句以一个分号结尾`；`如果省略分号，则由解析器确定语句的结尾，如下例所示：

```js
var sum = a + b                 // 即使没有分号也是有效的语句——不推荐
var diff = a - b;               // 有效的语句——推荐
```



## 3.2 关键字和保留字

ECMA-262描述了一组具有特定用途的**关键字**，这些关键字可用于表示控制语句的开始或结束，或者用于执行特定操作等。

```
break do in typeof
case else instanceof var
catch export new void
class extends return while
const finally super with
continue for switch yield
debugger function this
default if throw
delete import try
```

ECMA-262还描述了另外一组不能用作标识符的**保留字**。以下是所有ES6中定义的保留字：

```
Always reserved:
enum

Reserved in strict mode:
implements package public
interface protected static
let private

Reserved in module code:
await
```



## 3.3 变量

### 3.3.1 `var`关键字



### 3.3.2 `let`声明



### 3.3.3 `const`声明



