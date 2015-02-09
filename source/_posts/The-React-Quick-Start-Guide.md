title: React JS快速开始手册
date: 2015-01-17 11:19:11
tags: [React, ReactJS, JS, JavaScript, Facebook]
---
怎样用React JS构建一个用户界面？本文将快速地给你一个React JS的概览。代码，请君移步[react-starter](https://github.com/owenyang0/react-starter)

## 概念
React只有很少的API，这使得它很容易去学习与理解。当然，使用它也是相当有意思的。但是，简约却并不简单。在我们开始之前，有一些概念是需要去理解的。

### React元素(React elements)
React元素是用于呈现HTML结构的JavaScript对象。它们不会存在于浏览器中，只是用于描述浏览器中的元素，比如`h1`, `div` 或者 `section`等等。

### 模块(Components)
模块是开发者创建的React元素。它们通过比用户界面的范围要大，因为它们同时包含了其结构与功能。想像一下导航栏，点赞按钮，图片上传这些模块的概念。

### JSX
JSX是一种用于创建React元素和模块的技术，或者说是规范、语言。比如 `<h1>Hello</h1>` 便是一个用JSX所写的React元素。同样的React元素，可以用原生的JavaScript实现，即 `React.DOM.h1(null, 'Hello');`。相比原生的JavaScript，JSX更加简洁。会让你花更少的精力去读和写代码，在上线的时候将其转换成原生的JavaScript即可。

### 虚拟DOM(Virtual DOM)
虚拟DOM是一个JavaScript的树形结构，包含了React元素和模块。React通过渲染虚拟DOM到浏览器，使得用户界面得以显示。React也会观察虚拟DOM的变化，根据虚拟DOM自动地改变浏览器DOM元素。

了解了这些概念之后，我们就可以畅快地敲React代码了。这里将会创建一系列的用户界面，每一个界面都将提前添加一层功能层。我们会做一个类似instagram的应用 - 当然，这个很粗糙。

## 渲染(Rendering)
业务的第一步是渲染一个虚拟的元素(React元素或者模块都可以)。由于每一个虚拟元素都存在于内存之中，所以我们必须显式地告诉React，将其渲染到浏览器的DOM之中。

```javascript
React.render(<img src='http://owenyang0.github.io/img/background.jpg' />, document.body);
```
`render` 函数接受两个参数，一个是虚拟元素，一个是真实的DOM节点。React就是获取到虚拟元素之后，将其插入到所给的节点之中。此时，在浏览器中便可以看到照片了。

## 模块(Components)
模块是React的核心与灵魂。它们可以自定义React元素。经常由单一的功能或者结构扩展而来。

```javascript
var Photo = React.createClass({

  render: function() {
    return <img src='http://owenyang0.github.io/img/background.jpg' />
  }
});

React.render(<Photo />, document.body);
```

`createClass` 函数接受一个对象，该对象实现了`render` 的函数。
这个`Photo`模块被构建好，`<Photo />`，然后渲染到document body中。
该模块并没有比上一个React图像元素做更多的事情，但这却更加有利于在功能和结构上进行扩展。

## 属性(Props)
属性可以认为是模块的一些配置选项。它们以参数(arguments)的形式传递给模块，看起来就像HTML的属性(attributes)。

```javascript
var Photo = React.createClass({

  render: function() {
    return (
      <div className='photo'>
        <img src={this.props.imageURL} />
        <span>{this.props.caption}</span>
      </div>
    )
  }
});

React.render(<Photo imageURL='http://owenyang0.github.io/img/background.jpg' caption='Headset' />, document.body);
```

在`render` 函数里面，两个属性(props)传到了`Photo`模块，`imageURL` 和 `caption`。`imageURL`属性被用作React元素中的`src`。而`caption`属性则以纯文本的方式在React中的span元素使用。

值得一提的是，模块永远不应该去改变属性的的值，它们是不可变的。如果一个模块有一个可变的数据，那应该应用使用状态对象(state object)。

## 状态(State)
状态对象是一个模块的内部对象。它会持有可变的数据。

```javascript
var Photo = React.createClass({

  toggleLiked: function() {
    this.setState({
      liked: !this.state.liked
    });
  },

  getInitialState: function() {
    return {
      liked: false
    }
  },

  render: function() {
    var buttonClass = this.state.liked ? 'active' : '';

    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={this.toggleLiked} className={buttonClass}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
});

React.render(<Photo src='http://owenyang0.github.io/img/background.jpg' caption='Headset'/>, document.body);
```
在模块中引入状态，会增加一点点的复杂度。

在这模块中，有一个新的函数`getInitialState`。当模块初始化的时候，React会调用这个函数。而返回的对象则作为React的初始化状态(看函数名就知道)。
还有一个新的函数叫`toggleLiked`。这个函数调用了模块上的setState方法，可以改变状态`liked`的值。
通过模块的render函数，变量`buttonClass`被赋值成了'active'或者空，这都依赖于`liked`状态。
`buttonClass`是React按钮元素的class名字。按钮还拥有一个`onClick`的事件回调，指向`toggleLiked`函数。

当模块渲染成浏览器DOM的时候，究竟发生全过程：
- 当按钮点击被触发时，调用了`toggleLiked`
- `liked`的状态被改变
- React将模块再次渲染成虚拟DOM
- 新的虚拟DOM与旧的虚拟DOM相比较
- React将改变的部分隔离，然后更新到浏览器DOM

在这个场景中，React会改变button上的类名。

## 组合(Composition)
组合的意思是说，将小的分散的模块组成一个大的整体。比如`Photo`模块可以在`PhotoGallery`中使用：
```javascript
var Photo = React.createClass({

  toggleLiked: function() {
    this.setState({
      liked: !this.state.liked
    });
  },

  getInitialState: function() {
    return {
      liked: false
    }
  },

  render: function() {
    var buttonClass = this.state.liked ? 'active' : '';

    return (
      <div className='photo'>
        <img src={this.props.src} />

        <div className='bar'>
          <button onClick={this.toggleLiked} className={buttonClass}>
            ♥
          </button>
          <span>{this.props.caption}</span>
        </div>
      </div>
    )
  }
});

var PhotoGallery = React.createClass({

  getDataFromServer: function() {
    return [{
      url: 'http://owenyang0.github.io/img/background.jpg',
      caption: 'Headset'
    },
    {
      url: 'http://owenyang0.github.io/images/mocha.png',
      caption: 'Mocha'
    },
    {
      url: 'http://owenyang0.github.io/images/catalog.png',
      caption: 'Catelog'
    }];
  },

  render: function() {
    var data = this.getDataFromServer();

    var photos = data.map(function(photo) {
      return <Photo src={photo.url} caption={photo.caption} />
    });

    return (
      <div className='photo-gallery'>
        {photos}
      </div>
    )
  }
});

React.render(<PhotoGallery />, document.body);
```

这个`Photo`模块完完全全和上面的一样。但新的`PhotoGallery`模块会生成`Photo`模块。该场景中，伪造了返回包含三个对象的数组的数据，每一个对象都返回一个url和其标题。通过循环，生成了三个`Photo`的模块，将最终的返回值插入到`render`函数之中。

## 小结
用React来构建你的用户界面，我想这也差不多了。React的文档手册，包含了所有的细节。强烈推荐大家去读一下。
同样的，该文也没有涉及你本地环境安装的细节。[文档](http://facebook.github.io/react/docs/getting-started.html)都会有的。