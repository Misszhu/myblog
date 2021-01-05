# `$refs` 类型扩展

组件的 `$refs`的类型会被声明为最广泛的类型，以处理`ref`所有可能的类型。虽然理论上是这样，但在实际应用中，每个`ref`应该只有一个特定html元素或组件。

你可以在类组件中通过覆盖`refs`的类型来给指定某一个特定的`ref`的类型。

```javascript
<template>
  <input ref="input">
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class InputFocus extends Vue {
  // annotate refs type.
  // The symbol `!` (definite assignment assertion)
  // is needed to get rid of compilation error.
  $refs!: {
    input: HTMLInputElement
  }

  mounted() {
    // Use `input` ref without type cast.
    this.$refs.input.focus()
  }
}
</script>
```

在上面的例子中，你可以不转换`$refs.input` 的类型，直接在类组件访问`input`的类型。

注意，这里应该是类型声明（用冒号`:`）而不是赋值（=）。