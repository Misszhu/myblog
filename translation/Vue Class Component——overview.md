# Vue Class Component

## 概述

vue-class-component是一个库，它允许你使用类风格（class-style）的语法创建 vue 组件。例如，用vue-class-component写一个简单的计算器组件：

```vue
<template>
  <div>
    <button v-on:click="decrement">-</button>
    {{ count }}
    <button v-on:click="increment">+</button>
  </div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

// Define the component in class-style
@Component
export default class Counter extends Vue {
  // Class properties will be component data
  count = 0

  // Methods will be component methods
  increment() {
    this.count++
  }

  decrement() {
    this.count--
  }
}
</script>
```

如示例所示，通过使用`@Component`装饰器装饰类，你就可以用直观而标准的类语法在组件中定义`data`和`method`。你可以直接用类组件来代替传统的Vue组件定义方式，因为它与普通选项对象组件是等价的。

用类风格的语法定义组件，你不仅可以改变语法，还可以用一些ECMAScript的新特性，比如继承和装饰器。vue-class-component还提供了`mixins` 辅助函数用于混入继承。另外你也可以用`createDecorator`辅助函数来轻松创建装饰器。 

你可能还需要查看vue-property-decorator提供的`@props`和 `@watch`装饰器。

