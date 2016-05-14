title: Babel和Rollup，开始构建发布你的ES6模块(Modules)
date: 2016-05-14 18:06:26
tags: [Babel, Rollup, Modules, ES6, NPM]
category:
---
早在2015年六月，ES2015规范或者叫ES6就正式被ECMA国际协会(Ecma International)批准，成为了新一代的语言标准。2016年四月，Node.js团队发布了号称支持93%的ES6语言特性的Node v6，不过这一切还是由于V8引擎v5.0的发布。

是应该使用最新的语言特性，ES6+的语法，还是应该使用第三方库与polyfills来编写一个JavaScript库？相信这个问题一定困扰着写JavaScript库的你，因为这很难说前者会比后者有更大的优势。私以为，前者能减少代码冗余，让代码更可读，使用更少的抽象，让代码库更容易维护与扩展，提高开发效率等等。

如果你正在开发一个全新的基于Node.js平台的JavaScript库(npm 包), 那基于Node.js v6环境使用最新的特性将是一个很好的选择。你也可以同时提供给Node v5或者更老版本的开发者一个回退版本(fallback)，如此一来Node v6的用户也可以正常地使用你的库：
```js
const MyLibrary = require('my-library');
```

在Node.js v6的环境，代码将是最简洁优雅的。同时也得保证使用Node 0.x, 4.x, 5.x的用户能够导入ES5.1版本的库：
```js
var MyLibrary = require('my-library/legacy');
```

除此以外，强烈推荐将使用ES2015模块语法的版本也放在你的NPM包中。虽然ES2015的[模块系统](https://twitter.com/koistya/status/726042867211325440)还无法在Node.js或者V8中使用，但Node.js和前端社区早已大量地使用这种规范组织代码，这一切都归功于模块打包工具(module bundlers)，如Webpack, Berify, JSPM，Babel等等。

若要使用ES6的方式发布你的包，你项目的目录结构看起来可能会像这样：
```
.
├── /dist/                  # Temp folder for compiled output
│   ├── /legacy/            # Legacy bundle(s) for Node 0.x, 4.x
│   │   ├── /main.js        # ES5.1 bundle for Node 0.x, 4.x
│   │   └── /package.json   # Legacy NPM module settings
│   ├── /main.js            # ES6 bundle /w CommonJS for Node v6
│   ├── /main.mjs           # ES6 bundle /w Modules for cool kids
│   ├── /main.browser.js    # ES5.1 bundle for browsers
│   ├── /my-library.js      # UMD bundle for browsers
│   ├── /my-library.min.js  # UMD bundle, minified and optimized
│   └── /package.json       # NPM module settings
├── /node_modules/          # 3rd-party libraries and utilities
├── /src/                   # ES2015+ source code
│   ├── /main.js            # The main entry point
│   ├── /sub-module-a.js    # A module referenced in main.js
│   └── /sub-module-b.js    # A module referenced in main.js
├── /test/                  # Unit and end-to-end tests
├── /tools/                 # Build automation scripts and utilities
│   └── /build.js           # Builds the project with Babel/Rollup
└── package.json            # Project settings
```
在 _src_ 目录里包含了你项目的源代码(当然是ES2015+规范)，_dist_ (或者 _build_ )目录则是在你构建工程时动态生成的。通过 _dist_ 目录，你可以发布你的NPM包(package)，里面包含了使用Babel和Webapck编译出来的符合CommonJS, ES6，和UMD规范的包(bundle).

在你的 _package.json_ 文件里面会包含这些bundle的引用：
```
{
  "name": "my-library",
  "version": "1.0.0",
  "main": "main.js",
  "jsnext:main": "main.mjs",
  "browser": "main.browser.js",
  ...
}
```

`tools/build.js`脚本是一种便捷的方式去配置你的编译过程，它看起来可能会像这样：
<script src="https://gist.github.com/koistya/1abc156c61814fc307210f33e127f124.js"></script>

现在你只需要简单运行 `node tools/build` (假定你已经在本地装好了Node.js v6)，就可以构建你的牛X库了，然后你只需要在 `dist` 目录下，将代码发布到NPM就OK了。

希望该博可以对你有所帮助，这或许是将你的牛库是以ES6发布在NPM上的最好方式。你也可以在这里看到预配置好的发布NPM包的样板文件：[https://github.com/kriasoft/babel-starter-kit](https://github.com/kriasoft/babel-starter-kit)

如果你发现有什么遗漏，不清楚，欢迎留言 :)

[译][How to Build and Publish ES6 Modules Today, with Babel and Rollup](https://medium.com/@tarkus/how-to-build-and-publish-es6-modules-today-with-babel-and-rollup-4426d9c7ca71#.38s3c7m0q)
