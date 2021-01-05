# 自定义装饰器

你可以通过创建自定义的**decorators(装饰器)**来扩展这个库的功能。vue-class-component提供了用于创建自定义装饰器的`createDecorator` 辅助函数。 `createDecorator` 的第一个参数是回调函数，这个回调函数的接收以下参数：

- options：Vue 组件的选项对象，这会影响到自定义的装饰器装饰的组件。
- key：自定义装饰器装饰的类属性或者方法。
- parameterIndex：如果自定义装饰器用于装饰的是参数，parameterIndex表示参数的索引。

以创建一个`Log`装饰器为例，被装饰的方法在被调用时会打印方法名和传入的参数的日志信息：

```javascript
// decorators.js
import { createDecorator } from 'vue-class-component'

// Declare Log decorator.
export const Log = createDecorator((options, key) => {
  // Keep the original method for later.
  const originalMethod = options.methods[key]

  // Wrap the method with the logging logic.
  options.methods[key] = function wrapperMethod(...args) {
    // Print a log.
    console.log(`Invoked: ${key}(`, ...args, ')')

    // Invoke the original method.
    originalMethod.apply(this, args)
  }
})
```

装饰一个方法：

```js
import Vue from 'vue'
import Component from 'vue-class-component'
import { Log } from './decorators'

@Component
class MyComp extends Vue {
  // It prints a log when `hello` method is invoked.
  @Log
  hello(value) {
    // ...
  }
}
```

在上面的代码中，调用`hello`方法并传入42时，会打印下面的日志信息：

```javascript
Invoked: hello( 42 )
```

