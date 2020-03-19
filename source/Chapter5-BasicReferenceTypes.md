# 第5章 基本引用类型

## 5.5　`Function`类型

每个函数都是对象，即`Function`类型的实例，而且都与其他引用类型一样具有属性和方法。因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。

**定义函数的3种语法形式：**

- 函数定义语法：

```js
function sum (num1, num2) {
    return num1 + num2;
}
```

- 使用函数表达式定义函数：

```js
var sum = function(num1, num2){
    return num1 + num2;
};
```

- 使用`Function`构造函数：

```js
var sum = new Function("num1", "num2", "return num1 + num2"); // 不推荐使用这种方法定义函数
```





