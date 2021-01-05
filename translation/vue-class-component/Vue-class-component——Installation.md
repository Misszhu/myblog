# Vue-class-component——Installation

## Vue CLI 安装

你可以用[Vue  CLI](https://cli.vuejs.org/)轻松设置vue-class-component项目。执行下面的命令，创建一个新项目：

```shell
$ vue create hello-world
```

你会被提示选取一个 preset。这里我们选“Manually select features（手动选择特性）“来选取需要的特性。

![vue-cli-1.67b86f9d](/Users/zhuhuiting/Downloads/vue-cli-1.67b86f9d.png)

选择 `TypeScript` 以使用vue-class-component。你也可以根据实际情况添加你需要的其他特性。

![vue-cli-2.388fcd7f](/Users/zhuhuiting/Downloads/vue-cli-2.388fcd7f.png)

遇到问题：`Use class-style component syntax?`时，回答 `y`并且按下回车键。

![vue-cli-3.92013345](/Users/zhuhuiting/Downloads/vue-cli-3.92013345.png)

接着根据个人喜好回答剩下的问题。完成设置过程后，Vue CLI会创建一个新的项目目录，项目中已经安装好vue-class-component了。

## 手动安装

如果更喜欢手动安装的方式，你也可以通过npm安装和配置你的构建工具。

### npm

用npm命令来安装vue-class-component。请先确保你有安装vue，因为vue-class-component是依赖于它的：

```shell
$ npm install --save vue vue-class-component
```

你也可以用yarn命令：

```shell
$ yarn add --save vue vue-class-component
```

### 构建配置

要使用vue-class-component，你需要在项目中配置 `Typescript` or  `Babel`，因为它是依赖于 ECMAScript 第一版装饰器的。装饰器相关的代码要编译后才能在浏览器运行。

> #### 警告
>
> 它目前还不支持stage 2的装饰器规范，因为TypeScript编译器仍然只支持旧的装饰器规范。

#### TypeScript

在项目根目录创建`tsconfig.json`文件并指定`experimentalDecorators`选项，用于指定装饰器语法的编译：

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "es2015",
    "moduleResolution": "node",
    "strict": true,
    "experimentalDecorators": true
  }
}
```

#### Babel

安装`@babel/plugin-proposal-decorators` 和 `@babel/plugin-proposal-class-properties`:

```shell
$ npm install --save-dev @babel/plugin-proposal-decorators @babel/plugin-proposal-class-properties
```

然后在项目根目录配置`.babelrc`

```json
{
  "plugins": [
    ["@babel/proposal-decorators", { "legacy": true }],
    ["@babel/proposal-class-properties", { "loose": true }]
  ]
}
```

注意，在这里`legacy` 和 `loose` 选项是必须的，因为vue-class-component目前只支持stage1的装饰器规范。

## CDN

[unpkg.com](https://unpkg.com/) 提供了 基于npm的CDN链接。你可以通过替换url中的`@latest` 部分来指定vue-class-component的版本。（比如`https://unpkg.com/vue-class-component@7.2.2/dist/vue-class-component.js `获取 v7.2.2）。

```html
<!-- UMD build -->
<script src="https://unpkg.com/vue-class-component@latest/dist/vue-class-component.js"></script>

<!-- UMD minified build -->
<script src="https://unpkg.com/vue-class-component@latest/dist/vue-class-component.min.js"></script>

<!-- ES Module build -->
<script src="https://unpkg.com/vue-class-component@latest/dist/vue-class-component.esm.browser.js"></script>

<!-- ES Module minified build -->
<script src="https://unpkg.com/vue-class-component@latest/dist/vue-class-component.esm.browser.min.js"></script>
```

## 不同的构建方式

vue-class-component为不同的环境和用途提供了不同的构建方式。

**开发环境**

`vue-class-component.js` (UMD)

`vue-class-component.common.js` (CommonJS)

`vue-class-component.esm.js` (ES Module for bundlers)

`vue-class-component.esm.browser.js` (ES Module for browsers)

**生产环境（已压缩）**

`vue-class-component.min.js` (UMD)

`vue-class-component.esm.browser.min.js` (ES Module for browsers)

