title: "两分钟了解ReactiveX"
date: 2015-05-08 11:59:09
tags: ['Rx', 'RxJS', 'Reactive Programming']
---

可能在之前，你就已经看过这篇[响应式编程的入门](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)。什么？太长？好吧，这都没关系，[Rx](https://github.com/Reactive-Extensions/RxJS)并不难，你甚至可以自己实现一个这样的框架。
知道数组吧？你当然知道，这就是：
```
[ 14, 9, 5, 2, 10, 13, 4 ]
```

如果说这是一个**非可变数组**，但需要移除所有的奇数，你会怎样做呢？可能会是这样：

```javascript
[ 14, 9, 5, 2, 10, 13, 4 ]

filter( (x) -> x % 2 == 0 )
// => [ 14, 2, 10, 4 ]
```

直到现在也没什么新鲜东西，这在`underscore.js`, `ECMAScript 5.1`, `LINQ`, `Guava`, 等等之中，早已司空见惯。这些都是来自于函数式的编程范型。

-----

再来想想带来坐标位置数据的点击事件。如果画一条时间线，它们看起来应该是这样。
![click events line](/images/click-events.png)

是的，这是一个事件流，它有一妮称，叫 "Observable"。

这些点击事件，都来自于鼠标，意味着整个事件流都是**不可变的**。事件被触发后，我们没有办法对事件进行添加或删除。
但我如果只是关心`x < 250`的事件呢？能不能创建一个像数组一样的新的流，再对其进行过滤呢？
![click events line](/images/click-events.png)

```javascript
filter( (event) -> event.x < 250 )
```
![click events filted line](/images/click-events-filtered.png)

非可变数组和Observables有什么区别呢？其实不多，两个都可以使用`map`, `filter`, `reduce`等高阶函数。在Observables中，你也可以使用`merge`, `delay`, `concat`, `buffer`, `distinct`, `first`, `last`, `zip`, `startWith`, `window`, `takeUntil`, `skip`, `scan`, `sample`, `amb`, `n`, `flatMap`等函数。

**想像一下异步的非可变数组**
`Rx`可以说是操作事件的`underscore.js`。但，等一下，什么是事件？在你的程序中，难道不是绝大多数代码都是事件吗？
程序启动事件，API响应事件，键盘事件，设备休眠事件等等。

基本上，所有东西都可以是事件流。所需要的只是要适当地进行排列组合。

这就是`Rx`的两分钟。
