# $listeners & $attrs 在Vue组件中的应用

## 前言

Vue 组件间的通信是一个老生常谈的话题。最常用的组件通信方式不外乎以下三种：

1. prop：子组件通过 prop 接收从父组件传递过来的数据；
2. $emit：父组件通过监听子组件派发的事件实现某些功能和接收数据；
3. vuex：Vue的官方推荐的全局状态管理，能实现所有组件之间的通信。

在实际应用中，组件的通信方式当然不止父传子、子传父这么简单。如下图所示，子组件想和爷爷组件（即父组件的父组件）组件进行通信，但是又不想依赖vuex，要怎么做呢？

<img src="https://i.loli.net/2020/08/02/hqIBlVQUjknYxc9.jpg" alt="component.jpg" style="zoom: 33%;" />

当然，EventBus是其中一种方法，不过今天想介绍的如何利用Vue官方提供的`$listeners` 和 `$attrs` 实现嵌套组件之间的通信。

## 定义

首先我们看一下官方文档对`$listeners` 和 `$attrs`的定义。

[$listeners：](https://cn.vuejs.org/v2/api/#vm-listeners)

> 包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件——在创建更高层次的组件时非常有用。

[$attrs](https://cn.vuejs.org/v2/api/#vm-attrs)：

> 包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (`class` 和 `style` 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

单单看文档，可能会不理解这两个定义以及`$listeners` 和 `$attrs`作用。没关系，我们结合具体的栗子来理解一下。

### 示例

我们在使用子组件时，可以通过`v-bind="$attrs" ` 接收所有来自父组件、爷爷组件、爷爷组件的父组件等（即来自父作用域的）的attribute。注意，前提是这些attribute是没有作为prop识别。`class` 和 `style`这两个attribute则不会被接收。上代码会更好理解（[示例代码](https://codesandbox.io/s/jovial-edison-3hxjz?fontsize=14&hidenavigation=1&theme=dark&file=/src/main.js)）：

```vue
<!-- 爷爷组件内部 -->
<template>
    <div class="component-grandpa">
        爷爷组件
        <component-father @clickSon="handleClick" title="say hello from grandpa" level="1" />
    </div>
</template>
<script> 
import componentFather from './component-father';
export default {
    name: 'component-grandpa',
    components: {
        componentFather
    },
    methods: {
        handleClick(msg) {
            console.log(msg) // say hi from son
        }
    }
}
</script>
```

```vue
<!-- 父组件内部 -->
<template>
    <div class="component-father">
        父组件
        <component-son v-on="$listeners" v-bind="$attrs" origin="from component-father" />
    </div>
</template>
<script>
import componentSon from './component-son'
export default {
    name: 'component-father',
    components: {
        componentSon
    },
    mounted() {
        console.log('father.$attrs', this.$attrs)
      	// father.$attrs {title: "say hello from grandpa", level: "1"}
    },
}
</script>
```

```vue
<!-- 子组件内部 -->
<template>
    <div class="component-son">
        子组件
        <br />
        <button @click="clickSon">{{title}}</button>
    </div>
</template>

<script>
export default {
    name: 'component-son',
    props: {
        title: {
            type: String,
            default: ''
        }
    },
    mounted() {
        console.log('son.$attrs', this.$attrs);
        // son.$attrs {origin: "from component-father", level: "1"}
    },
    methods: {
        clickSon() {
            this.$emit('clickSon', 'say hi from son')
        }
    },
}
</script>
```

如上面上例所示，`$listeners`有“中转”的功能。它包含了来自父作用域的所有通过`v-on`指令绑定的事件监听。因此，给子组件`component-son` 绑定 `v-on="$listeners"`，`component-grandpa`即可监听`component-son`派发的事件`clickSon`。

同理，`$attrs`包含了父作用域中不被prop识别的`attribute`。譬如从爷爷组件和父组件传递过来的`title`、`level`、`origin`，在向下传递的过程中，父组件内部打印的是`{title: "say hello from grandpa", level: "1"}`，由于在子组件内部，`title`作为prop被识别了，子组件内部打印的是`{origin: "from component-father", level: "1"}`。

### [inheritAttrs](https://cn.vuejs.org/v2/api/#inheritAttrs)

通过`v-bind="$attrs"`给组件绑定的属性，也会作为attribute渲染搭HTML原生的标签上。这个结果可能不是我们想要的。如果我们不需要在HTML标签中把这些attitude渲染出来，把Vue实例的 inheritAttrs选项设为false即可。

<img src="https://i.loli.net/2020/08/03/YstKlE9If4eGc1d.jpg" alt="20200803172207.jpg" style="zoom:50%;" />

## 在组件的二次封装中的应用

在用element UI开发进行实际开发时，Drawer 抽屉是一个常用的组件。el-drawer往往会结合按钮el-button实现一些交互。当每次用el-drawer时都会用到el-button，我们就可以考虑对el-drawer进行二次封装，提高代码的可维护性和简洁性。

封装的`confirm-drawer`:

```vue
<template>
<div class="confirm-drawer">
  <el-drawer
    v-bind="$attrs"
    v-on="$listeners"
    :size="size"
  >
    <div class="el-drawer-footer" v-if="showFooter">
      <el-button @click="cancel">取消</el-button>
      <el-button @click="confirm">确定</el-button>
    </div>
  </el-drawer>
</div>
</template>
<script>
export default {
  name: 'confirm-drawer',
  methods: {
    cancel () {
      this.$emit('cancel')
    },
    confirm () {
      this.$emit('confirm')
    }    
  }
}
</script>
```

只需要给`el-drawer`绑定`v-bind="$attrs"`和 `v-on="$listeners"`，我们在使用`confirm-drawer`时就可以像使用`el-drawer`一样，直接监听`el-drawer`内部派发的事件、向`el-drawer`传递属性：

```vue
<confirm-drawer
  title="我嵌套了 Form !"
  :before-close="handleClose"
  size="50%"
>
</confirm-drawer>
```

