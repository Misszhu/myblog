# 属性类型声明

有时候，你可能需要在类组件的外部定义组件的属性或者方法。譬如，Vuex，专为 Vue.js 应用程序开发的**状态管理模式**。它提供了`mapGetters`和`mapActions` 辅助函数将store映射到组件的属性和方法中。这些辅助函数需要在组件选项对象中引用。

在这种情况下，你也可以将组件的选项当作参数传递给`@component`装饰器。但是它并不会在类型层面上自动声明运行时的属性和方法。

所以，你需要在类组件中手动声明他们的类型：

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'
import { mapGetters, mapActions } from 'vuex'

// Interface of post
import { Post } from './post'

@Component({
  computed: mapGetters([
    'posts'
  ]),

  methods: mapActions([
    'fetchPosts'
  ])
})
export default class Posts extends Vue {
  // Declare mapped getters and actions on type level.
  // You may need to add `!` after the property name
  // to avoid compilation error (definite assignment assertion).

  // Type the mapped posts getter.
  posts!: Post[]

  // Type the mapped fetchPosts action.
  fetchPosts!: () => Promise<void>

  mounted() {
    // Use the mapped getter and action.
    this.fetchPosts().then(() => {
      console.log(this.posts)
    })
  }
}
```



