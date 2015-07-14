title: "Overview of ES6"
date: 2015-05-31 17:03:25
tags:
---
Slides in ES6 overview workshop

## Intro
### What
ECMAScript 6（以下简称ES6）是JavaScript语言的下一代标准
### Goal
ES6的目标，是使得JavaScript语言可以用来编写大型的复杂的应用程序，成为企业级开发语言。
新的语言特性，代码更优雅，程序更健壮，减少不必要的重复，减少不必要的第三方依赖。

## Agenda
- template strings
- const
- let
- block-level declaration
- Destructuring Assignment
- arrow functions
- default function params
- class
- module

## Requirements
Babel [https://babeljs.io/docs/usage/cli/](https://babeljs.io/docs/usage/cli/)

```bash
$ npm install --global babel
```

## Talk is cheap, show me the code.

## template strings
```javascript
var first = "Song"
var last = "Yang";
var name = `I'm ${first} ${last}!`;
```

**性能对比**
[http://heeroluo.net/article/detail/54](http://heeroluo.net/article/detail/54)

它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

## const
```javascript
const PI = 3.14
```

```javascript
Object.defineProperty(typeof global === "object" ? global : window, "PI", {
    value:        3.14,
    enumerable:   true,
    writable:     false,
    configurable: false
})
```

## let
声明变量，变量只在let命令所在代码块内有效。

```javascript
{
  var a = 1;
  let b = 10;
}

a //1
b // ReferenceError: b is not defined.
```

let不允许在相同作用域内，重复声明同一个变量。
```javascript
// error
{
    let a = 10;
    let a = 1;
}
```

## block-level declaration
- let, const 块级作用域
- var 函数作用域

```javascript
function fn() {
    for(var i=0,len=5;i<len;i++) {
        //body
    }
    console.log(i,len);=> 5,5
}
```
ES5的JavaScript的不支持块级作用域，变量仅仅被限制到函数作用域内。

### Pros
```javascript
// IIFE写法
(function () {
    var tmp = ...;
    ...
}());

// 块级作用域写法
{
    let tmp = ...;
    ...
}
```

### function scope
```javascript
function f() { console.log('I am outside!'); }
(function () {
  if(false) {
    // What should happen with this redeclaration?
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```

## Destructuring Assignment
ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

### Array destructuring
```javascript
var a = 1;
var b = 2;
var c = 3;

var [a, b, c] = [1, 2, 3];

[a, b] = [b, a];
```

### 模式匹配（嵌套数组）
```javascript
var [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

var [,,third] = ["foo", "bar", "baz"];
third // "baz"

var [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]
```

### 解构失败

```javascript
var [foo] = [];
var [foo] = 1;
var [foo] = 'Hello';
var [foo] = false;
var [foo] = NaN;
var [bar, foo] = [1];

foo // undefined
```

- 如果对undefined或null进行解构，会报错。
```javascript
// 报错
var [foo] = undefined;
var [foo] = null;
```

- 解构赋值允许指定默认值。
```javascript
[x, y='b'] = ['a'] // x=3, y='b'
```

- 解构赋值不仅适用于var命令，也适用于let和const命令。
```javascript
var [v1, v2, ..., vN ] = array;
let [v1, v2, ..., vN ] = array;
const [v1, v2, ..., vN ] = array;
```

### Object destructuring
```javascript
var { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```

如果变量名与属性名不一致，必须写成下面这样。
```javascript
var { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
```

```javascript
var o = {
  p: [
    "Hello",
    { y: "World" }
  ]
};

var { p: [x, { y }] } = o;
x // "Hello"
y // "World"
```

- 对象的解构也可以指定默认值。
- 如果要将一个已经声明的变量用于解构赋值
```javascript
// 错误的写法

var x;
{x} = {x:1};
// SyntaxError: syntax error

({x} = {x:1});
```
JavaScript引擎会将{x}理解成一个代码块，从而发生语法错误。

**NOTE** 数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

### Pros
- 交换变量的值
- 从函数返回多个值
- 遍历Map结构
  配合变量的解构赋值，获取键名和键值
  ```javascript
  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  ```
- 输入模块的指定方法
  ```javascript
  const { SourceMapConsumer, SourceNode } = require("source-map");
  ```

## Arrow Function
- 语法糖：function的简写，或者是使用胖箭头 () => 的Lamda表达式。
- 不仅仅是语法糖：自动绑定外部作用域的"this".

```javascript
const items = [1, 2, 3, 4];

var double = (x) => x * 2;
var byTwo = items.map(double);
// => [2, 4, 6, 8]
```

```javascript
var foot = {
    kickNormal: function () {
      this.yelp = "Ouch!";
      setImmediate(function () {
        console.log(this.yelp);
      }.bind(this));
    },
    kick: function () {
        this.yelp = "Ouch!";
        setImmediate(() => console.log(this.yelp));
    }
};
```

## default function params
在ES6默认值特性出现前，手动处理默认值有几种方式：

```javascript
function log(message, level) {
  level = level || 'warning';
  console.log(level, ': ', message);
}

log('low memory'); // warning: low memory
log('out of memory', 'error'); // error: out of memory
```

为了处理参数未传递的情况，我们常看到typeof检测:

```javascript
if (typeof level == 'undefined') {
  level = 'warning';
}

```

有时也可以检查arguments.length

```javascript
if (arguments.length == 1) {
  level = 'warning';
}

```
这些方法都可以很好的工作，但都过于手动且缺少抽象。

```javascript
function log(message, level = 'warning') {
  console.log(level, ': ', message);
}

log('low memory'); // warning: low memory
log('out of memory', 'error'); // error: out of memory
```
参数默认值使用方便且毫无违和感。


## Classes

A formal inheritance syntax comes to JavaScript
Really just syntactical sugar over JS prototypical inheritance

```javascript
class Greeter {
  constructor(msg) {
    this.msg = msg;
  }

  static create(msg) {
    return new this(msg);
  }

  greet() {
    return `Hello ${this.msg}`;
  }

  set message(msg) {
    this.msg = msg;
  }

  get message() {
    return this.msg;
  }

}
```

```javascript
var _createClass = (function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ('value' in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; })();

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError('Cannot call a class as a function'); } }

var Greeter = (function () {
  function Greeter(msg) {
    _classCallCheck(this, Greeter);

    this.msg = msg;
  }

  Greeter.create = function create(msg) {
    return new this(msg);
  };

  Greeter.prototype.greet = function greet() {
    return 'Hello ' + this.msg;
  };

  _createClass(Greeter, [{
    key: 'message',
    set: function (msg) {
      this.msg = msg;
    },
    get: function () {
      return this.msg;
    }
  }]);

  return Greeter;
})();
```

### Extends
```javascript
class FancyGreeter extends Greeter {
  constructor(msg, name) {
    super(msg);
    this.name = name;
  }

  greet () {
    return `${this.name}, Hello ${this.msg}`;
  }
}
```

```javascript
var fg = new FancyGreeter('World', 'Song');
console.log(fg instanceof FancyGreeter);
console.log(fg instanceof Greeter);
```

## Module
### export和import
ES6实现了模块功能，试图解决JavaScript代码的依赖和部署上的问题，取代现有的CommonJS和AMD规范，成为浏览器和服务器通用的模块解决方案。

```javascript
export default function foo() {}
```

```javascript
var first = 'David';
var last = 'Belle';
var year = 1973;

export {first, last, year};
```

```javascript
import {first, last, year} from './profile';
import {first as f, last} from './profile';
import * as profile from './profile';
```

## NOT Included
- Map, WeakMap, Set, WeakSet
- Promise
- Generator
- Iterator
- Rest Parameter
- Spread Operator

### 新的内建方法

- Object.assign
- [arr].find
- "str".repeat
- "str".startsWith
- "str".endsWith
- "str".includes
- Number.isNaN
- Number.isFinite
- Number.isSafeInteger
- Math.trunc
- Math.sign

## Using ES6 Today
### Server
- io 2.2.0
- node 0.12.4

### Browser
- Browserify
- Babel

## Resources
- [ECMAScript 6 — New Features](http://es6-features.org/)
