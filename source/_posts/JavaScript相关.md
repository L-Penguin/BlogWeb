---
title: JavaScript 相关
tags: JS
categories: Python
desc: '1er1rqfsag'
cover: ../assets/JavaScript.png
---
# `JavaScript`相关

整理`JS`相关的知识体系、问题、理解……

## 模块导入

`JS`中，模块是一种可充用的代码块，将一些代码打包成一个单独的单元，并且可以在其他代码中进行导入和使用。在导入模块时，`JS`中有两种常用的方式：`import`和`require`。

### `require`函数

`Node.js`如果需要导入某些数据，需要使用`module.exports`.

#### 模块类型

1. **内置模块**：`Node.js`提供的原生功能，比如`fs`、`path`等等，这些模块在`Node.js`进程时就加载了；
2. **文件模块**：自己书写的文件代码或第三方模块，即`node_modules`下面的模块。

#### 加载顺序

1. 优先加载内置模块（即使有同名文件）；
2. 不是内置模块，先去缓存中找；
3. 缓存没有就去找对应路径的文件；
4. 不存在对应的文件，就将这个路径作为文件夹加载（寻找`package.json`中的`main`字段或者文件夹下的`index.[js|ts|...]`）；
5. 对应的文件和文件夹都找不到就去`node_modules`中查找；
6. 上述不存在就报错。

#### 手写`require`

文件存在全局对象`module`，`require`会默认导入文件的`exports`字段，即`module.export`

`exports`是对`module.exports`的引用，对其属性赋值会改变`module.exports`中的字段

**ps：**直接对`exports`进行赋值相当于解除引用地址，无法改变`module.exports`，失去导出能力

```javascript
const Module = {
  id: '.',  // require 的路径
  path: '/Users/l_penguin/Projects/algorithm_js', // 当前文件夹路径
  exports: {},  // 文件导出对象
  filename: '/Users/l_penguin/Projects/algorithm_js/require.js',  // 当前文件路径
  loaded: false,  // 当前文件是否缓存
  children: [  // 当前文件的子依赖
    Module {
      id: '/Users/l_penguin/Projects/algorithm_js/a.js',
      path: '/Users/l_penguin/Projects/algorithm_js',
      exports: '000',
      filename: '/Users/l_penguin/Projects/algorithm_js/a.js',
      loaded: true,
      children: [],
      paths: [Array]
    }
  ],
  paths: [  // 第三方依赖路径
    '/Users/l_penguin/Projects/algorithm_js/node_modules',
    '/Users/l_penguin/Projects/node_modules',
    '/Users/l_penguin/node_modules',
    '/Users/node_modules',
    '/node_modules'
  ]
}
```



### `import`和`require`理解

`import`是`ES6`引入的新特性，允许以声明式的方式导入其他模块中的内容。
`require`是`Node.js`中的特性，允许使用一个函数来加载和导入其他模块。

#### 导入方式

`import`的命名导出不属于解构赋值

`require`的命名导出属于解构赋值

```javascript
// ES6: 静态导入、动态导入
import { func1, func2 } from './module';
import('./module').then(obj => {});
// Node.js
const module = require('./module');
```

#### 文件类型

`import`只能导入`ES6`模块或者使用`Babel`等工具转化为`ES6`模块的代码；`require`则可以导入`CommonJS`模块、`AMD`模块、`UMD`模块和`Node.js`内置模块等多种类型的模块。

#### 变量提升

`ES6`中，`import`是静态执行的，意味着在模块内部的顶层执行，并在模块内部创建一个局部作用域。导入的变量只在模块内部可见，不会影响模块外部的变量。因此，`import`导入的变量是不会被提升的。

`require`函数是动态执行的，在运行时执行，并且不会在模块内部创建一个局部作用域。因此，使用`require`导入的变量是可以被提升的。

#### 导出方式

```javascript
// import: 命名导出、默认导出
export function func() {};
export default {};
// require: 默认导出
module.exports = {};
//exports是module.exports的一个引用
exports.func = () => {};
```

#### 模块作用域

在使用`import`导入模块时，实际是在模块内部创建一个指向被导入模块的引用，而不是直接复制模块的变量。因此，当不同的文件中使用`import`导入相同模块时，它们实际上是共享了同一个模块实例，所以可以访问和修改同一个模块中的变量。

在使用`require`导入模块时，实际上将导入模块中的变量直接复制（类似前拷贝）到当前模块的作用域中。因此，当不同的文件中使用`require`导入相同的模块时，它们实际上是拥有各自独立的模块实例，彼此之间不会共享模块中的变量。

**PS：**需要注意的是，如果使用`require`导入的模块中含有可变状态的对象，那么在不同文件中修改改对象的变量会相互影响。（由于`require`会将导入模块中的变量直接复制到当前模块的作用域中，类似于浅拷贝，模块中的普通变量是非共享的，而对象的变量则是能够被修改共用的）

```javascript
// module.js
let module = {a: 1, b: 2};
module.exports = module;
// a.js
const module = require('./module.js');
module.a = 100;
// b.js
const module = require('./module.js');
console.log(module.a); // 100
```



### `import`和`imoport()`的区别

`import`是在代码加载阶段执行的；`import()`用于在动态环境中导入模块的函数，它是在运行时执行的。

```javascript
import(moduleName).then(module => {
  // 使用模块中的内容；
}).catch(error => {
  // 处理措施
})
```

当第一个文件调用`import()`，会开始加载并解析'module.js'模块，当第二个文件调用`import()`函数时，由于该模块已经被加载并解析过了，因此不会重新加载解析，而是直接返回已经加载并解析好的模块实例。也就是在应用程序中多次调用`import()`函数导入同一个模块，只有第一次调用会真正加载和解析操作，后续调用都会直接返回缓存的模块实例。

**PS：**由于`import()`是异步的，因此在模块加载完成之前，模块中导出的变量或函数是无法使用的。



### 总结

1. `import` 和 `import()` 都是 ES6 中用于导入模块的语句，而 `require()` 则是 `Node.js` 中用于导入模块的函数。
2. 使用 `import` 语句导入模块时，模块会被静态加载，也就是在编译时就已经确定了导入的模块;`import()` 和 `require()` 都是动态加载模块的方式。它们都允许在代码运行时根据需要加载模块，而不是在编译时就将所有模块都加载进来。不过两者的实现方式略有不同：`import()` 是基于 Promise 的异步加载，而 `require()` 是同步加载模块。
3. 在整个应用程序中，使用 `import` 和 `import()` 语句导入的模块都是`单例模式`，也就是共用同一个模块实例，而使用 `require()` 导入的模块则会因为复制而产生多个实例。
4. `import` 和 `import()` 语句支持模块的默认导出和命名导出，而 `require()` 只支持模块的默认导出 `(module.exports)` 导出。



## 执行模式
