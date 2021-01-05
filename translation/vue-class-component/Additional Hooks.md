# Additional Hooks

如果你使用Vue插件，比如Vue Router，你可能需要用到插件提供的钩子。在这种情况下，你可以用`Component.registerHooks`注册这些钩子：

```javascript
// class-component-hooks.js
import Component from 'vue-class-component'

// Register the router hooks with their names
Component.registerHooks([
  'beforeRouteEnter',
  'beforeRouteLeave',
  'beforeRouteUpdate'
])
```

注册了这些钩子之后，类组件就可以像类的原型普通方法一样实现他们：

```js
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // The class component now treats beforeRouteEnter,
  // beforeRouteUpdate and beforeRouteLeave as Vue Router hooks
  beforeRouteEnter(to, from, next) {
    console.log('beforeRouteEnter')
    next()
  }

  beforeRouteUpdate(to, from, next) {
    console.log('beforeRouteUpdate')
    next()
  }

  beforeRouteLeave(to, from, next) {
    console.log('beforeRouteLeave')
    next()
  }
}
```

建议在一个单独的文件中定义这些注册代码，因为你要在所有组件定义之前注册它们。你可以通过在入口文件顶部import钩子注册文件以确保代码执行顺序。

```javascript
// main.js

// Make sure to register before importing any components
import './class-component-hooks'

import Vue from 'vue'
import App from './App'

new Vue({
  el: '#app',
  render: h => h(App)
})
```

