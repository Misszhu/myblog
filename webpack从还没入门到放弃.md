# webpack从还没入门到放弃

我在项目开发中接触到webpack是在20l7年，当时实习公司开始技术转型，从jQuery转到vue。不过当时作为一个前端傻白不甜，无论是对于和jQuery完全不一样的Vue，还是第一次接触的webpack，都觉得相当陌生，对webpack的认知仅限于在terminal执行`npm run dev`运行项目，执行`npm run build`打包项目。

## 什么是webpack

官方文档给出的定义是：

> webpack 用于编译 JavaScript 模块。*本质上，*webpack 是一个现代 JavaScript 应用程序的*静态模块打包器(module bundler)*。当 webpack 处理应用程序时，它会递归地构建一个*依赖关系图(dependency graph)*，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 *bundle*。

初识weback，你可能会觉得**静态模块打包器**、**依赖关系图**、**bundle**这几个概念晦涩难懂。不过没关系，现阶段我们不需要完全领悟这些概念的意思。我们的首要任务是尝试一下用webpack，感受webpack带来的便利，并在这个过程中去领悟上面这一段定义的意思。

这里先抛出我对webpack的理解：webpack是一个前端项目的构建工具。它帮助我们把项目中的HTML、CSS、JS，按照我们想要的方式经过处理，再重新输出到某个地方。

## 用webpack实现一个基本的构建过程

### 基本安装

首先创建一个目录，初始化**npm**，然后安装**webpack & webpack-cli**(webpack-cli用于在命令行中运行webpack命令)。

```
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

现在我们将创建以下目录结构、文件和内容：

**Project**

```diff
 webpack-demo
  |- package.json
+ |- webpack.config.js
+ |- /src
+   |- index.js
+   |- hello-world.js
```

**src/index.js**

```
import { helloworld } from './hello-world';
 
document.write(helloworld());
```

**src/hello-world.js**

```
export function helloworld(params) {
    return 'Hello World'
}
```

**webpack.config.js**

```
'use strict'

const path = require('path');

module.export = {
  entry: './src/index.js',
	output: {
		path: path.join(__dirname, 'dist'),
		filename: 'bundle.js'
	},
	mode: 'production'
}
```

到这里，我们就完成了一个最简单的webpack配置文件。

在命令行执行下面的命令：

```
./node_modules/.bin/webpack
```

webpack会帮我们在项目根目录生成一个dist文件夹，该文件夹下有一个bundle.js文件。

我们再在dist文件夹添加一个index.html文件：

dist/index.html**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <title>webpack demo</title>
</head>
<body>
    <sript type="text/javascript" src="./bundle"></script>
</body>
</html>
```

在浏览器打开index.html，就能看到"Hello World"。