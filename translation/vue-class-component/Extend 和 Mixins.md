## 扩展

你可以像原生的类继承一样扩展类组件。假设你有一个`super`类组件：

```javascript
// super.js
import Vue from 'vue'
import Component from 'vue-class-component'

// Define a super class component
@Component
export default class Super extends Vue {
  superValue = 'Hello'
}
```

然后通过原生类继承的语法来扩展：

```js
import Super from './super'
import Component from 'vue-class-component'

// Extending the Super class component
@Component
export default class HelloWorld extends Super {
  created() {
    console.log(this.superValue) // -> Hello
  }
}
```

注意，`super`类必须要是类组件。换句话来说，就是它必须继承自 Vue 构造函数并且被`@Component`装饰器装饰。

## Mixins

vue-class-component提供了`mixin` 辅助函数，用于像类的行为一样使用 `mixins `功能(Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口)。通过使用`mixins`辅助函数，TypeScript 可以推断混入的类型并且在组件的类型中继承他们。

以声明要混入的 `hello` 和 `world`属性为例子：

```javascript
// mixins.js
import Vue from 'vue'
import Component from 'vue-class-component'

// You can declare mixins as the same style as components.
@Component
export class Hello extends Vue {
  hello = 'Hello'
}

@Component
export class World extends Vue {
  world = 'World'
}
```

使用：

```javascript
import Component, { mixins } from 'vue-class-component'
import { Hello, World } from './mixins'

// Use `mixins` helper function instead of `Vue`.
// `mixins` can receive any number of arguments.
@Component
export class HelloWorld extends mixins(Hello, World) {
  created () {
    console.log(this.hello + ' ' + this.world + '!') // -> Hello World!
  }
}
```

和所有的`super` 组件一样，所有的mixin都必须声明为类组件。