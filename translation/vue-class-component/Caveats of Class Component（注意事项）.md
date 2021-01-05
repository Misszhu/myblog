# 注意事项

Vue-class-component 通过实例化底层原始构造函数的方式来将自定义的类组件的属性转换为Vue实例数据。尽管我们已经可以像原生类的行为一样定义实例的数据了，但有时候我们应该知道这一切是如何运行的。

## 属性初始化时`this` 的指向

如果你把类的属性定义为箭头函数，并且在这个方法中访问`this `，`this`并不会指向这个类的实例。这是因为在初始化类的属性的时候，`this`只是Vue实例的一个代理对象。

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class MyComp extends Vue {
  foo = 123

  // DO NOT do this
  bar = () => {
    // Does not update the expected property.
    // `this` value is not a Vue instance in fact.
    this.foo = 456
  }
}
```

在这种情况下，你可以定义方法而不是类的属性，因为Vue会自动帮我们把`this`和实例绑定在一起：

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class MyComp extends Vue {
  foo = 123

  // DO this
  bar() {
    // Correctly update the expected property.
    this.foo = 456
  }
}
```

## 用生命周期钩子代替 `constructor`

当需要在构造函数中初始化组件的数据时，建议你不要自己声明构造函数：

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class Posts extends Vue {
  posts = []

  // DO NOT do this
  constructor() {
    fetch('/posts.json')
      .then(res => res.json())
      .then(posts => {
        this.posts = posts
      })
  }
}
```

上面的代码旨在初始化组件时获取`posts`的数据。但是由于vue-class-component的运行方式，`fetch`会被调用两次。

建议你用声明周期钩子，比如`created`代替`constructor`：

```javascript
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class Posts extends Vue {
  posts = []

  // DO this
  created() {
    fetch('/posts.json')
      .then(res => res.json())
      .then(posts => {
        this.posts = posts
      })
  }
}
```



