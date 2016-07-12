title: JavaScript 常见问题和解决方式
date: 2016-03-12 20:22:26
desc: 本文总结了 JavaScript 常见的问题，并对其做出了详细的解析。
---

JavaScript 因其历史原因一直存在诸多缺陷，本文所讨论的只是其中的一小部分，适合为初学者答疑解惑，此外文中观点尚存在不足之处，或者对部分问题 ES6 已经提出了新的解决方案。

<!-- more -->

## 类型判断

使用 typeof 运算符判断一个原始值变量的类型是没有问题的，但如果判断的是引用值类型就会有局限性，比如 `null` 和数组的结果都是 `object`。要想判断变量属于哪种内置类型，最靠谱的方式是调用 `Object.prototype.toString` 方法：

```js
Object.prototype.toString.call([])
// => "[object Array]"
```

在 JavaScript 规范（ECMA-262 19.1.3.6）中详细解释了 `Object.prototype.toString` 的解析过程：

1. 如果 `this` 的值为 `undefined`，则返回字符串 `"[object Undefined]"`
2. 如果 `this` 的值为 `null`，则返回字符串 `"[object Null]"`
3. 使用 `O` 表示 `ToObject(this)` 的值
4. 使用 `isArray` 表示 `IsArray(O)` 的值
5. 如果 `isArray` 不是正常值（比如抛出错误），则中断执行
6. 如果 `isArray === true`，则 `builtinTag = "Array"`，之后执行第 16 步
7. 如果 `O` 是一个 exotic 字符串对象，则 `builtinTag = "String"`，之后执行第 16 步
8. 如果 `O` 拥有内部属性 `[[ParameterMap]]`，则 `builtinTag = "Arguments"`，跳到第 16 步
9. 如果 `O` 拥有内部方法 `[[Call]]`，则 `builtinTag = "Function"`，跳到第 16 步
10. 如果 `O` 拥有内部属性 `[[ErrorData]]`，则 `builtinTag = "Error"`，跳到第 16 步
11. 如果 `O` 拥有内部属性 `[[BooleanData]]`，则 `builtinTag = "Boolean"`，跳到第 16 步
12. 如果 `O` 拥有内部属性 `[[NumberData]]`，则 `builtinTag = "Number"`，跳到第 16 步
13. 如果 `O` 拥有内部属性 `[[DateValue]]`，则 `builtinTag = "Date"`，跳到第 16 步
14. 如果 `O` 拥有内部属性 `[[RegExpMather]]`，则 `builtinTag = "RegExp"`，跳到第 16 步
15. 如果第 6 ~ 14 步都不符合，则 `builtinTag = "Object"`，跳到第 16 步
16. 使用 `tag` 表示 `Get (O, @@toStringTag)` 的值
17. 如果 `tag` 不是正常值，则中断执行
18. 如果 `Type(tag)` 不是一个字符串，则 `tag = builtinTag`
19. 返回一个 `"[object" + tag + "]"` 形式的字符串

<div class="tip">
开发者喜欢使用该方法获取内部属性 `[[Class]]` 的字符串值，用于检测内建对象的类型。值得注意的是，这一方法只对内建对象有效，对宿主对象等其他类型的对象则不具有可信度。
</div>

## 局部变量泄漏到全局

有如下所示的代码，猜测一下输出结果：

```js
(function(){
    var a = b = 3;
})();

console.log(a);
console.log(b);
```

很多时候不经意间，某些变量就会泄漏为全局变量，比如这里的变量 b。上述代码等同于：

```js
(function(){
    b = 3;
    var a = b;
})();
```

ES6 支持块级作用域，使用时需要遵循两个条件：一是添加 `"use strict;"` 字符串，声明严格模式；而是使用 `let` 和 `const` 声明变量。如果不能使用 ES6 的块级作用域，那么声明变量时要做到使用单独一行声明单个变量，比如：

```js
var b = 3;
var a = b;
```

## 立即执行函数

使用立即执行函数的好处是模块化和块级作用域。目前 ES6 对这两个方面都有相应的支持，所以除非为了保持兼容性，ES6 会是更好的开发方式。

在浏览器环境中，立即调用函数中的 `this` 指向全局变量 `window`：

```js
(function () {
    console.log(this);
    console.log(this === window);
    // => true
})();
```

## 严格模式

1. 使用 `'use strict;'` 声明严格模式
1. 全局变量必须显示声明
1. 禁止使用 with 语句，限制动态绑定
1. `eval()` 的作用域为独立作用域，独立于全局作用域和函数作用域之外，生成的变量只能用于 eval 内部
1. 禁止 `this` 关键字指向全局对象
1. 禁止使用 `delete` 删除变量，只有 `configurable === true` 的属性可以被删除
1. 对可读属性赋值、对禁止扩展的对象添加属性、删除不可删除属性都会报错
1. 禁止在对象中添加重名属性
1. 不允许对 arguments 赋值
1. 禁止使用 arguments.callee
1. 必须在顶层作用域声明函数
1. 新增保留字

## 浮点数的精度问题

对于使用 IEEE 754 存储双精度 64 位浮点数的语言都会遇到这个问题，且最简单的复现方式就是计算 `0.1 + 0.2`。这一表达式的值不等于 `0.3`，其原因就是 IEEE 754 不能正确表示 0.1。IEEE 754 规定的浮点数由一位符号位 `s`、五十二位小数位 `m` 和十一位指数位组成 `e`：

$$s \times m \times 2^e$$

JavaScript 规定 e 的范围为 `[-1074, 971]`，则 `Number.MAX_VALUE` 的值为：

$$ 1 \times ( 2^{53} - 1) \times 2^{971} $$

了解以上基础知识后求取 `0.1` 和 `0.2` 的二进制表示：

```js
(0.1).toString(2)
// => "0.0001100110011001100110011001100110011001100110011001101"

(0.2).toString(2)
// => "0.001100110011001100110011001100110011001100110011001101"

(0.1 + 0.2).toString(2)
// => "0.0100110011001100110011001100110011001100110011001101"
```

关于这一问题的详细解析和其他问题，建议参考文章：

- [http://www.cnblogs.com/zichi/p/5034201.html](http://www.cnblogs.com/zichi/p/5034201.html)
- [http://www.cnblogs.com/zichi/p/5043540.html](http://www.cnblogs.com/zichi/p/5043540.html)

## setTimeout

JavaScript 是单线程语言，异步事件的优先级低于其他代码，`setTimeout(callback, 0)` 表示加入事件队列以异步的方式执行，且在事件队列中优先执行：

```js
(function() {
    console.log(1); 
    setTimeout(function(){console.log(2)}, 1000); 
    setTimeout(function(){console.log(3)}, 0); 
    console.log(4);
})();
```

## Object key

```js
var a={},
    b={key:'b'},
    c={key:'c'};
 
a[b]=123;
a[c]=456;
 
console.log(a[b]);
// => 456
```

上面的代码有一个特点，就是没有使用字符串或 Symbol 作为对象的属性名，所以系统会将其转换为字符串，相当于：

```js
a[Object.prototype.toString.call(b)]=123;
a[Object.prototype.toString.call(c)]=456;

// equal to
a["[object Object]"]=123;
a["[object Object]"]=456;
```

## eval 作用域

```js
var y = 1;

if (function f(){}) {
    y += typeof f;
}

console.log(y);
// => "1undefined"
```

在上面的代码中，最奇怪的有点在于 `typeof f === "undefined"`。之所以有这样的结果，是因为 if 条件语句是使用 `eval()` 解析的。由于 `eval(function f(){})` 只会返回值而不会向全局作用域暴漏变量 `f`，所以执行 `typeof f` 时并没有找到函数 `f`，则值为 undefined。

## 私有方法

在 JavaScript 中模拟私有方法的缺点就是内存占用高：

```js
var Employee = function (name, company, salary) {
    this.name = name || "";       
    this.company = company || ""; 
    this.salary = salary || 5000; 

    // Private method
    var increaseSalary = function () {
        this.salary = this.salary + 1000;
    };

    // Public method
    this.dispalyIncreasedSalary = function() {
        increaseSlary();
        console.log(this.salary);
    };
};

var emp1 = new Employee("John","Pluto",3000);
var emp2 = new Employee("Merry","Pluto",2000);
var emp3 = new Employee("Ren","Pluto",2500);
```

对于 `Employee` 的每一个实例 emp1 / emp2 / emp3，它们都拥有各自的一个 `increaseSalary` 方法，所以除非确有必要，尽量不要使用私有方法。

## delete 操作符

```js
var output = (function(x){
    delete x;
    return x;
})(0);

console.log(output);
// => 0
```

`delete` 操作符用于删除对象的属性，对于其他变量无效。

```js
var Employee = {
  company: 'xyz'
}
var emp1 = Object.create(Employee);
delete emp1.company;
console.log(emp1.company);
// => "xyz"
```

在上面的代码中，emp1 通过原形链继承了 `Employee` 的属性 `company`，emp1 本身并没有 `company` 属性，即 `emp1.company === emp1.__proto__.company`，所以 `delete` 操作是无效的。

## 延迟定义

```js
// 第一种声明方式
var foo = function(){ 
    // Some code
}; 

// 第二种声明方式
function bar(){ 
    // Some code
}; 
```

这两种声明方式的差异在于，`foo()` 在运行时定义，而 `bar()` 在解析时定义。

```js
foo();
// 执行 foo() 时，foo 的值为 undefined
var foo = function(){ 
    console.log("Hi I am inside Foo");
}; 

bar();
// 执行 bar() 时，由于 bar() 已经被 JavaScript 引擎解析过了，所以不会报错
function bar(){ 
    console.log("Hi I am inside Foo");
};
```

根据这一特点，我们可以根据需要延迟定义某些功能：

```js
if(testCondition) { 
    var foo = function(){ 
        console.log("inside Foo with testCondition True value");
    }; 
}else{
    var foo = function(){ 
        console.log("inside Foo with testCondition false value");
    }; 
}
```

在上面的代码中，布尔值 `testCondition` 决定了是定义何种功能的 `foo()` 函数，在定义之前，`foo` 只是一个原始值 `undefined`，并没有引用具体的函数。

## 错误处理

当 JavaScript 解析或运行发生错误时，系统会抛出一个 Error 对象的实例，该实例包含 name、message 和 stack 三个属性，分别表示错误名称、错误提示信息和错误堆栈跟踪信息。

JavaScript 中存在六种原生错误类型：

- URIError: URI 错误
- TypeError: 类型错误
- RangeError: 越界错误
- SyntaxError: 语法错误
- ReferenceError: 引用错误
- EvalError: evel() 执行错误

错误捕获语句：

```js
// 尝试捕获异常
try {
    throw new Error('Error is out!');
}
// 捕获异常后执行该分支
catch (e) {
    console.log(`${e.name}: ${e.message}`);
    console.log(e.stack);
}
// 无论是否存在异常都执行该分支
finally {
    console.log('Error is captured!');
}

// 典型应用：读写文件
try {
    writeFile(Data);
} 
catch(e) {
    handleError(e);
} 
finally {
    closeFile();
}
```

## 对象属性

- `Object.kyes(obj)`，以数组的形式返回 `obj` 对象的所有属性
- `key in obj`，检查 `obj` 对象中是否存在 `key` 属性，存在就返回 `true`，否则返回 `false`
- `for (var key in obj)`，遍历 `obj` 对象的全部属性，该对象必须 enumberable
- `obj.hasOwnProperty(key)`，判断 `key` 是否是 `obj` 对象本身的属性，而不是继承来的属性

## 位运算和右结合

位运算是对二进制位的直接计算，在 JavaScript 中进行位运算需要注意的是，由于其使用浮点数保存数值，所以数值执行位运算前会被转换为 32 位带符号的整数，最终的返回值也是一个 32 位的带符号整数，所以这种取整方法不适用超过 32 位带符号整数的最大值 2^31。一共有七种位运算：

- `|`，或运算
- `&`，与运算
- `~`，否运算
- `^`，异或运算
- `<<`，左移运算，将一个整数的二进制向左移动，尾部补 0，相当于乘二操作
- `>>`，右移运算，将一个整数的二进制向右移动，正数头部补 0，负数头部补 1，相当于除二操作
- `>>>`，带符号右移运算，将一个整数的二进制向右移动，正负数头部一律补 0

```js
4 << 1
// => 8

4 >> 1
// => 2

-4 >> 1
// => 2
```

大部分运算符是左结合的，但赋值运算符和三元运算符则是右结合的：

```js
w = x = y = z;
// 等同于
w = (x = (y = z));

q = a ? b : c ? d : e ? f : g;
// 等同于
q = (a ? b : (c ? d: (e ? f : g)));
```

## Base64 编码

JavaScript 原生提供了两个和 Base64 编码相关的方法：

- `window.atob()`，将 Base64 转换为 ASCII 编码
- `window.btoa()`，将 ASCII 转换为 Base64 编码

此外，对于要转换为 Base64 的非 ASCII 编码，可以使用 `encodeURIComponent()` 方法进行预处理：

```js
window.btoa(encodeURIComponent('http://pinggod.com~'));
// => "aHR0cCUzQSUyRiUyRnBpbmdnb2QuY29tfg=="
```

> RFC-2396 将 URI 中的字符分为三类：
一类是保留字符，包括 `;`、`/`、`?`、`:`、`@`、`&`、`=`、`+`、`$`、`,`；
一类是 Mark 字符，包括 `-`、`_`、`.`、`!`、`~`、`*`、`'`、`(`、`)`；
一类是基本字符，包括数字和大小写字母。
`encodeURI()` 和 `encodeURIComponent()` 的不同之处就在于，前者不会处理保留字符。

###### 参考资料

- [http://es6.ruanyifeng.com/](http://es6.ruanyifeng.com/)
- [http://javascript.ruanyifeng.com/](http://javascript.ruanyifeng.com/)
- [25 Essential JavaScript Interview Questions](https://www.toptal.com/javascript/interview-questions)
- [21 Essential JavaScript Tech Interview Practive Questions answers](https://www.codementor.io/javascript/tutorial/21-essential-javascript-tech-interview-practice-questions-answers)
