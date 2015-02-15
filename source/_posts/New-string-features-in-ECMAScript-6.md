title: ECMAScript 6中字符串的新特性
date: 2015-02-12 20:26:43
tags: [Template, Template String, JavaScript, ES6, ECMAScript 6]
---
本文将覆盖在ECMAScript 6 (ES6)中，字符串的新特性。

## Unicode 码位(code point)转义
Unicode字符码位的长度是21位[[2]](http://speakingjs.com/es5/ch24.html)。而JavaScript的字符串，是16位的，以UTF-16的方式编码。因此，超出16位码长的码位范围(the Basic Multilingual Pane, BMP, 基本多文种平面)则用两个JavaScript字符表示。直到现在，如果想用数字指定这样的码位，需要两个叫Unicode转义符的东西。以下，则会以相应的码位(0x1F680)打印出一个火箭。
```javascript
  console.log('\uD83D\uDE80');
```
在ECMAScript 6中，有一个新的Unicode转义符，能让你指定任意的码位（不用再管是否是16位）：
```javascript
  console.log('\u{1F680}');
```

## 字符串的插值，多行及原始字符语法
模板字符串 [[3]](http://www.2ality.com/2011/09/quasi-literals.html)提供了3个有意思的特性。
**1. 模板字符中，支持字符串插值：**
```javascript
  let first = 'Jane';
  let last = 'Doe';
  console.log(`Hello ${first} ${last}!`);
  // Hello Jane Doe!
```
**2. 模板字符串可以包含多行：**
```javascript
  let multiLine = `
    This is
    a string
    with multiple
    lines`;
```

**3. 模板字符串可以是原始的：**
若使用`String.raw` 作为模板字符串的前缀，则模板字符串可以是*原始(raw)*的。反斜线也不再是特殊字符，`\n` 也不会被解释成换行符：
```javascript
  let raw = String.raw`Not a newline: \n`;
  console.log(raw === 'Not a newline: \\n'); // true
```

## 字符串迭代那些事
字符串是可迭代的 [[4]](http://www.2ality.com/2013/06/iterators-generators.html)，这就意味着可以使用`for-of` 去迭代其中的字符：
```javascript
  for (let ch of 'abc') {
      console.log(ch);
  }
  // Output:
  // a
  // b
  // c
```
亦可用展开运行符(...) 将字符串转换成数组：
```javascript
  let chars = [...'abc'];
  // ['a', 'b', 'c']
```

### 处理Unicode的码位
字符串迭代器，会以码位边界将字符串进行划分。这将导致迭代器的返回值，会是一个或两个字符：
```javascript
  for (let ch of 'x\uD83D\uDE80y') {
      console.log(ch.length);
  }
  // Output:
  // 1
  // 2
  // 1
```

这将会为我提供一个快速而简单的方法，去计算字符串Unicode码位的数量：
```javascript
  > [...'x\uD83D\uDE80y'].length
  3
```

这同样会在，不包含基本多文种平面(non-BMP)码位的字符串操作中提供方便。如：反转字符串：
```javascript
  let str = 'x\uD83D\uDE80y';

  // ES5: \uD83D\uDE80 are (incorrectly) reversed
  console.log(str.split('').reverse().join(''));
  // 'y\uDE80\uD83Dx'

  // ES6: order of \uD83D\uDE80 is preserved
  console.log([...str].reverse().join(''));
  // 'y\uD83D\uDE80x'
```
这是在firefox控制台中，两个反转之后的结果：
![Unicode in Firefox](/images/firefox_unicode.png)

### 码位中数值
有一个新的方法 `codePointAt()` 将返回字符串，给定索引的码位数字值：
```javascript
  let str = 'x\uD83D\uDE80y';
  console.log(str.codePointAt(0).toString(16)); // 78
  console.log(str.codePointAt(1).toString(16)); // 1f680
  console.log(str.codePointAt(3).toString(16)); // 79
```
该方法在与字符串迭代操作结合时，也能很好的运行使用：
```javascript
  for (let ch of 'x\uD83D\uDE80y') {
      console.log(ch.codePointAt(0).toString(16));
  }
  // Output:
  // 78
  // 1f680
  // 79
```

与方法`codePointAt()`相对的则是`String.fromCodePoint()`：
```javascript
  > String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
  true
```

## 包含与重要字符串的方法
有三个方法，可以检查一个字符串是否存在于另一个字符串：
```javascript
  > 'hello'.startsWith('hell')
  true
  > 'hello'.endsWith('ello')
  true
  > 'hello'.includes('ell')
  true
```

每一个方法都有一个可选的，第二个参数。可以指定被搜寻字符串的开始或结束位置：
```javascript
  > 'hello'.startsWith('ello', 1)
  true
  > 'hello'.endsWith('hell', 4)
  true

  > 'hello'.includes('ell', 1)
  true
  > 'hello'.includes('ell', 2)
  false
```

方法`repeat()` 则用以重复某个字符串：
```javascript
  > 'doo '.repeat(3)
  'doo doo doo '
```

## 所有新方法
### 模板字符串：
- String.raw(callSite, ...substitutions) : string
用于产生*原始(raw)*字串(反斜杠不会被转义)。

### Unicode和码位：
- String.fromCodePoint(...codePoints : number[]) : string
将数字值码位转换成字符串。

- String.prototype.codePointAt(pos) : number
返回码位开始位置的数字值(会包含一个或两个JavaScript的字符)。

- String.prototype.normalize(form? : string) : string
不同的码位组合，最后可能有一致的显示。[Unicode normalization]()可以将它们转化成同样的值，这会被叫做规范的表示方式(canonical representation)。这将有助于比较和查找字符串。在一般的文本之中，'NFC'是一种被推荐的形式。

### 查找字符串：
- String.prototype.startsWith(searchString, position=0) : boolean
检查一个字符串是否以另一个字符串(searchString)开始。位置(position)指定从哪开始执行检查。

- String.prototype.endsWith(searchString, endPosition=searchString.length) : boolean
检查一个字符串是否以另一个字符串(searchString)结尾。结束位置(endPosition)指定在哪执行结尾检查。

- String.prototype.includes(searchString, position=0) : boolean
检查一个字符字符串是否包含另一个字符串(searchString)。位置(position)指定从哪开始检查。

### 重复字符串：
- String.prototype.repeat(count) : string
返回给定字符串N次。

## 参考
1. [Using ECMAScript 6 today](http://www.2ality.com/2014/08/es6-today.html)
2. Chapter 24, “[Unicode and JavaScript](http://speakingjs.com/es5/ch24.html)” of “Speaking JavaScript”; includes an introduction to Unicode.
3. [Template strings: embedded DSLs in ECMAScript 6](http://www.2ality.com/2011/09/quasi-literals.html)
4. [Iterators and generators in ECMAScript 6](http://www.2ality.com/2013/06/iterators-generators.html)
5. 译自 [New string features in ECMAScript 6](http://www.2ality.com/2015/01/es6-strings.html)