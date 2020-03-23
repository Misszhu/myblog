# JavaScript模块化简述

## 概述

作为一个前端工程师，我们在项目中经常看到下面这样的代码：

```javascript
import {JSEncrypt} from 'jsencrypt';
import formatDate from '@/const/filter';
export const merchants = `${basicUrl}/merchants`;
```

```javascript
const path = require('path');
module.exports = {};
```

大部分人可能都大概了解以上代码的意思是 **导入（引用）** or  **导出**一些代码块。但是大家有没有想过，同样是导入、导出功能，为什么一个项目中常常能同时看到`import`和 `require`呢？他们又有什么区别的呢？

要回答这些问题，我们首先要对JavaScript模块化有一个大体的了解。阮一峰老师的[JavaScript模块化编程系列文章](http://www.ruanyifeng.com/blog/2012/11/require_js.html)对JavaScript模块化的起源和目前主流的模块化规范都做了介绍，建议大家先去了解这部分内容。

## 模块化规范

随着网页的功能越来越丰富，JavaScript代码的开发工作量肯定会越来越多。用最原始的方法，把所有不同功能的js代码放在一个文件内管理是不现实的。为了方便我们专注开发业务代码，引用一些库或者组件也是我们在日常开发中经常遇到的事情。在这个背景下，JavaScript模块化编程应运而生。

实现特定功能的代码组合在一起，就是一个模块。

在ES6之前，主流的模块化规范有**CommonJS**和**AMD**。CommonJS是服务端的规范，AMD是浏览器端的规范。之所以要作服务端和浏览器端的区分，是有原因的。

## CommonJS规范

Node.js的模块化系统，就是参照CommonJS规范实现的。在CommonJS中，有一个全局性方法require()，用于加载模块。

```javascript
const path = require('path');
function resolve(dir) {
  return path.join(__dirname, dir);
}
```

像上面这样，加载模块后，就可以直接调用模块的方法。下面简单介绍CommonJS规范语法。

### CommonJS规范语法

Node应用由模块组成，采用CommonJS模块规范。

根据这个规范，每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。

```javascript
// example.js
var x = 5;
var addX = function (value) {
  return value + x;
}
```

上面代码中，变量`x`和函数`addX`，是当前文件example.js私有的，其他文件不可见。

如果想在多个文件分享变量，必须定义为`global`对象的属性。

```javascript
global.warning = true;
```

上面代码的`warning`变量，可以被所有文件读取。当然，这样的写法是不推荐的。

CommonJS规范规定，每个模块内部，`module`变量代表当前模块。这个变量是一个对象，它的`exports`属性（即`module.exports`）是对外的接口。加载某个模块，其实就是加载该模块的`module.exports`属性。

```javascript
//example.js
var x = 5;
var addX = function (value) {
  return value + x;
};
module.exports.x = x;
module.exports.addX = addX;
```

`require` 方法用于加载模块。

```javascript
var example = require('./example.js');

console.log(example.x); // 5
console.log(example.addX(1)); // 6
```

CommonJS模块的特点如下。

> \> 所有代码都运行在模块作用域，不会污染全局作用域。
>
> \> 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
>
> \> 模块加载的顺序，按照其在代码中出现的顺序。

### module对象

Node内部提供一个Module构件函数。所有模块都是Module的实例。

```javascript
function Module(id, parent) {
  this.id = id;
  this.exports = {};
  this.parent = parent;
  //...
}
```

每个模块内部，都有一个`module` 对象，代表当前模块。它有以下属性。

> \> module.id 模块的识别符，通常是带有绝对路径的模块文件名。
>
> \> module.filename 模块的文件名，带有绝对路径。
>
> \> module.loaded 返回一个布尔值，表示模块是否已经完成加载。
>
> \> module.parent 返回一个对象，表示调用该模块的模块。
>
> \> module.children 返回一个数组，表示该模块要用到的其他模块。
>
> \> module.exports 表示模块对外输出的值。

### Module.exports属性

`module.exports`属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取`module.exports`变量。

### exports变量

为了方便，Node为每个模块提供了一个exports变量，指向module.exports，这等同在每个模块头部，有一行这样的命令。

```javascript
var exports = module.exports;
```

造成的结果是，在对外输出模块接口时，可以向exports对象添加方法。

```javascript
exports.area = function (r) {
  return Math.PI * r * r;
};

exports.circumference = function (r) {
  return 2 * Math.PI * r;
};
```

注意，不能直接将exports变量指向一个值，因为这样等于切断了exports与module.exports的联系。

```javascript
exports = function (x) {
  console.log(x);
};
```

上面的写法是无效的，因为`exports`不再指向 `module.exports`了。

下面的写法也是无效的。

```javascript
exports.hello = function () {
  return 'hello';
};
module.exports = 'hello world';
```

上面代码中，`hello`函数是无法对外输出的，因为`modules.exports`被重新赋值了。

为了避免混淆，推荐只用`module.exports`。

### require命令

Node使用CommonJS模块规范，内置的`require`命令用于加载模块文件。

require命令的基本功能是，读入并执行一个JavaScript文件，然后返回该模块的exports对象。如果没有发现指定模块，会报错。

```javascript
// example.js
const sayHello = function () {
  console.log('hello world');
}
module.exports = sayHello;
```

```javascript
// main.js
const sayHello = require('./example.js');
sayhello(); //hello world
```

但是**CommonJS**规范不适用于浏览器端。

在上面代码中，`sayHello`方法再在第一行`require('./example.js');`之后运行，因此必须等example.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等，造成浏览器假死现象。

这对服务器来说不是问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但对浏览器来说就是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，这可能会造成非常差的用户体验。AMD规范给浏览器端提供了模块化解决方案。

## AMD规范

[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：

```javascript
require([module], callback);
```

module是需要加载的模块，callback是模块加载完成执行的回调函数。由于模块的加载和回调函数的执行是不同步的，所以浏览器等待的时间不会太长。



## module

module是ES6在推出的模块规范。前面提到的CommonJS服务于服务器，AMD服务于浏览器，而ES6的实现的模块功能是在语言层面上实现的，是浏览器和服务器通用的模块解决方案。

### Export 命令

摘抄自阮一峰老师的[ECMAScript 6 入门 Module的语法一节（有删改）](https://es6.ruanyifeng.com/#docs/module#export-%E5%91%BD%E4%BB%A4)：

> 模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。
>
> 一个模块就是一个独立文件。该文件内部的所有变量、方法（即函数），外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该**变量 **。注意，要导出的是**变量** or **方法**。
>
> 需要特别注意的是，`export`命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。

我一开始不理解，查阅了一些资料，节选自[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export#Using_the_default_export):

>There are two types of exports:
>
>1. Named Exports (Zero or more exports per module)
>2. Default Exports (One per module)
>
>Identifier to be exported (so that it can be imported via [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) in another script).
>
>拙劣的翻译：要导出的是有标识符的变量（以便其他模块通过 [`import`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import) 语句进行导入）。

现在再分别看一下报错和正确的语法。

```JavaScript
// export 一个常量1，报错
export 1；

// 表面上看起来是定义了一个名为m变量1，实际上导出的是m的值1
// 想象一下，在其他文件import的时候，找不到对应的入口，报错
var m = 1;
export m;

// 直接导出一个变量 m，正确
export var m = 1;

// 先定义一个变量m，再将变量m导出，正确
var m = 1;
export { m };

// 先定义一个变量n，再把n当作m导出
// 这样其他文件import {m} 就能找到这里对应的n
var n = 1;
export {n as m};
```

为什么要强调导出**变量** or **方法**呢？好比我们出去买便当，加入我点了一个手撕鸡+白饭+青菜，如果店家不用饭盒把食物装起来给我，那我付钱之后，难道要徒手拿店家准备好的食物吗？MDN提到的**Named Exports**和饭盒一样，都是起到载体、装载的作用，导出的是**变量** or **方法**，其他模块在导入的时候才会有迹可寻。

### import命令

使用`export`定义了变量和方法以后，其他模块就可以通过import命令加载这个模块的内容。

```javascript
// export.js
var m = 1;
export { m };
```

```javascript
// import.js
import { m } from './export.js';
console.log(m); // 1
```

`import`命令输入的变量是只读的。但如果import的变量是一个对象，这个对象的属性是可以修改的（但是不建议这样做，不好查错）。

可以结合`as`关键字给import的变量重命名：

```javascript
// import.js
import { n as m } from './export.js';
console.log(n); // 1
```

### 模块的整体加载

可以用`*` 指定一个对象，整体加载一个模块。所有输出值都加载在这个对象上面。

```javascript
// export.js
var m = 1;
var n = 2
export { m, n };
```

```javascript
// import.js
import * as test from './export.js';
console.log(test.m); // 1
console.log(test.n); // 2
```

### export default命令

`export default`命令为特定模块指定默认输出。这样，其他模块不用知道名称的情况下就可以加载该模块。

```javascript
// export-default.js
function foo () {
	console.log('export default');
}
export default foo;
```

```javascript
// import.js
// foo可以取任意命名
import foo from './export-default.js';

foo();// export default
```

在一个模块内，`exoprt default`命令只能使用一次。

## 结语

了解过上面的知识，我们应该不难知道，`module.exports` & `require`、`export` &   `import` 都是**导出** or **导入** 模块内容的写法，都属于JavaScript模块规范的一种。`module.exports` & `require`是**CommonJS**和**AMD**模块规范，是ES6的**module**模块规范。

Node.js有它自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的。目前的解决方案是，将两者分开，ES6 模块和 CommonJS 采用各自的加载方案。

如果我们更细心一点，应该就会发现，在前端工程化已经是主流趋势的今天，在前端项目中，用到`module.exports` & `require`的地方大都是webpack相关的配置文件，因为webpack是npm生态中的一个模块。webpack的运行依赖于node环境。而在日常开发中运用ES6新特性，可以说是每一个前端工程师的必备技能，在所以业务代码中`export` &   `import` 随处可见。

参考链接

[阮一峰ECMAScript 6 入门]: https://es6.ruanyifeng.com/#docs/module
[JavaScript 标准参考教程（alpha）]: http://www.w3cbus.com/nodejs/module.html
[JavaScript模块化编程]: http://www.ruanyifeng.com/blog/2012/11/require_js.html
[MDN]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export