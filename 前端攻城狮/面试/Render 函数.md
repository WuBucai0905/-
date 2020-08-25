与 DOM 操作相比，Virtual DOM 是基于 JavaScript 计算的，所以开销会小很多。下图演示了 Virtual DOM 运行的过程

![](https://user-gold-cdn.xitu.io/2018/11/13/1670bc4c26b9c667?w=1964&h=636&f=png&s=105521)

正常的 DOM 节点在 HTML 中是这样的：

```html
<div id="main">
  <p>文本内容</p>
  <p>文本内容</p>
</div>
```

用 Virtual DOM 创建的 JavaScript 对象一般会是这样的：

```js
const vNode = {
  tag: 'div',
  attributes: {
    id: 'main'
  },
  children: [
    // p 节点
  ]
}
```

vNode 对象通过一些特定的选项描述了真实的 DOM 结构。

### Render 函数

与 Virtual DOM 示例一样，Vue.js 的 Render 函数也是类似的语法，需要使用一些特定的选项，将 template 的内容改写成一个 JavaScript 对象。

Render 函数返回的是一个 JS 对象，没有传统 DOM 的层级关系，配合上 if、else、for 等语句，将节点拆分成不同 JS 对象再组装，如果模板复杂，那一个 Render 函数是难读且难维护的。所以，绝大部分组件开发和业务开发，我们直接使用 template 语法就可以了。

Render函数的配置项特别多，但归根结底，Render函数只有三个参数

看 template 和 Render 写法的对照：

```
<template>
  <div id="main" class="container" style="color: red">
    <p v-if="show">内容 1</p>
    <p v-else>内容 2</p>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        show: false
      }
    }
  }
</script>
```

```
export default {
  data () {
    return {
      show: false
    }
  },
  render: (h) => {
    let childNode;
    if (this.show) {
      childNode = h('p', '内容 1');
    } else {
      childNode = h('p', '内容 2');
    }
    
    return h('div', {
      attrs: {
        id: 'main'
      },
      class: {
        container: true
      },
      style: {
        color: 'red'
      }
    }, [childNode]);
  }
}
```

这里的 `h`，即 `createElement`，是 Render 函数的核心。

h 有 3 个参数，分别是：

1. 要渲染的元素或组件，可以是一个 html 标签、组件选项或一个函数（不常用），该参数为必填项。
2. 对应属性的数据对象，比如组件的 props、元素的 class、绑定的事件、slot、自定义指令等，该参数是可选的，上文所说的 Render 配置项多，指的就是这个参数。该参数的完整配置和示例，可以到 Vue.js 的文档查看，没必要全部记住，用到时查阅就好：[createElement 参数](https://cn.vuejs.org/v2/guide/render-function.html#createElement-参数)。
3. 子节点，可选，String 或 Array，它同样是一个 h

#### 使用场景

- 使用两个相同 slot。在 template 中，Vue.js 不允许使用两个相同的 slot
- 在 SSR 环境（服务端渲染），如果不是常规的 template 写法，比如通过 Vue.extend 和 new Vue 构造来生成的组件实例，是编译不过的
- 在 runtime 版本的 Vue.js 中，如果使用 Vue.extend 手动构造一个实例，使用 template 选项是会报错的
- 一个 Vue.js 组件，有一部分内容需要从父级传递来显示，如果是文本之类的，直接通过 `props` 就可以，如果这个内容带有样式或复杂一点的 html 结构，可以使用 `v-html` 指令来渲染，父级传递的仍然是一个 HTML Element 字符串，不过它仅仅是能解析正常的 html 节点且有 XSS 风险。当需要最大化程度自定义显示内容时，就需要 `Render` 函数，它可以渲染一个完整的 Vue.js 组件