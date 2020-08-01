# Vue组件通信

**浏览器事件传参 和 自定义事件传参 的区别--[不跟参数]**

- 浏览器事件不跟参数默认会将 `event` 参数传递过去
- 自定义事件 会默认将 `args` 参数传递过去。 因为这不是浏览器产生的事件对象

> 为什么需要使用 组件间的通信？
>
> 根组件中请求网络数据，子组件中需要网络数据时 并不会让子组件 再次发送一个网络请求，而是直接让 大组件 将数据传递给 小组件

![组件关系](https://user-gold-cdn.xitu.io/2018/10/18/166864d066bbcf69?w=790&h=632&f=png&s=36436)

## 1.父子组件通信

### 1.1父通子

> - 在父组件中 把需要传递的数据 以属性绑定(v-bind: )的形式
> - 在子组件中 传过来的属性 在 `props: []` 数组中定义

**父组件中**

```
data: {
	message: "xxxx",
	movies: ["海王", "海贼王", "海尔兄弟"]
}
```

```
<cpn v-bind:cmovies='movies' v-bind:cmessage='message'></cpn>
```

**子组件中**

```
cpn = {
	template: '#cpn',
	// props传数组形式
	props: ['cmovies', 'cmessage'] //明明是变量，看起来是字符串,所以推荐使用 传对象的形式
	// props传对象形式
	props: {
		cmovies: {
		type: [Array, String] // 支持多类型
		default: ''
		}
	}
}
```

**子组件中 比较data中的 数据对象和 props中的数据**

- data中的数据都是可读可写的，props中的数据是可读的，无法重新赋值
- data的数据是子组件本身私有的，props的数据是通过父组件传过来的

### 1.2子通父

> 通过 事件触发机制
>
> - 在子组件中， 通过 `this.$emit()` 来触发事件
> - 在父组件中， 通过 `v-on` 来监听 子组件事件

**子组件中**

```
methods:{
	btnClick(args){
		// 发射事件 事件的类型需要自定义
		this.$emit('自定义事件名字', args)
	}
}
```

**父组件中**

```
// 父组件 methods 中来处理这个事件
// 来监听事件
v-on:item-click='方法名称'
```



## 2.父子组件访问

### 2.1父访子

父组件拿到子组件的对象来进行操作

- $children 或 $refs(引用：给元素或组件注册引用信息)

$children返回的是 组件对象

开发中用的多的是 $refs 【对象类型，默认是空对象】

- 组件上加上 `ref='aaa'`
- 取值使用时 使用 `$refs.aaa`

### 2.2子访父

子组件拿到父组件的对象来进行操作

- $parent



## 3.非父子通信

### 3.1事件总线

> 通过`vue`中的实例传递自定义事件；
>
> 通过监听自定义事件达到传值的功能
>
> 传递组件中 传递自定义事件都是通过 `$emit` 来传递的.
>
> 接受组件中 接听自定义事件都是通过 `$on` 来监听的，监听自定义事件需要来触发这个事件，我们一般可以通过 mounted 这个钩子来触发

**待补全**



### 3.2$attrs / $listeners

> 解决多级组件间传值的问题
>
> `$attrs` 将父组件中不包含 `props`的属性传入子组件，通过配合 `interitAttrs` 选项一起使用.
>
> `$listeners` 监听子组件中数据的变化，传递给 父组件.

**待补全**



### 3.3深化

> 当组件 A 和组件 B 中间隔了数代（甚至不确定具体级别）时，以往会借助 Vuex 或 Bus 这样的解决方案，不得不引入三方库来支持。
>
> 这里 介绍一种无依赖的组件通信方法：Vue.js 内置的 provide / inject 接口
>
> provide / inject 接口 主要还是在独立组件中发挥作用。不推荐直接在应用程序中使用
>
> 一个组件使用了 `provide` 向下提供数据，那其下所有的子组件都可以通过 `inject` 来注入

#### provide / inject

> 这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效
>
> **注意：**provide 和 inject 绑定并**不是可响应**的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

**例子**

假设有两个组件： **A.vue** 和 **B.vue**，B 是 A 的子组件。

```js
// A.vue
export default {
  provide: {
    name: 'Aresn'
  }
}

// B.vue
export default {
  inject: ['name'],
  mounted () {
    console.log(this.name);  // Aresn
  }
}
```

可以看到，在 A.vue 里，我们设置了一个 **provide: name**，值为 Aresn，它的作用就是将 **name** 这个变量提供给它的所有子组件。而在 B.vue 中，通过 `inject` 注入了从 A 组件中提供的 **name** 变量，那么在组件 B 中，就可以直接通过 **this.name** 访问这个变量了，它的值也是 Aresn。这就是 provide / inject API 最核心的用法。 

##### 替代 Vuex

**根组件** 用来存储所有需要的全局数据和状态，甚至是计算属性（computed）、方法（methods）等。

因为你的项目中所有的组件（包含路由），它的父组件（或根组件）都是 app.vue，所以我们**把整个 app.vue 实例通过 `provide` 对外提供**。

```
// app.vue
<template>
  <div>
    <router-view></router-view>
  </div>
</template>
<script>
  export default {
    provide () {
      return {
        app: this
      }
    }
  }
</script>
```

上面，我们把整个 app.vue 的实例 `this` 对外提供，命名为 **app**（这个名字可以自定义，推荐使用 app，使用这个名字后，子组件不能再使用它作为局部属性）。

接下来，任何组件（或路由）只要通过 `inject` 注入 app.vue 的 app 的话，都可以直接通过 **this.app.xxx** 来访问 app.vue 的 `data`、`computed`、`methods` 等内容。

app.vue 是整个项目第一个被渲染的组件，而且只会渲染一次（即使切换路由，app.vue 也不会被再次渲染）

**栗子：保存登录信息**

```
// app.vue
<script>
  export default {
    provide () {
      return {
        app: this
      }
    },
    data () {
      return {
        userInfo: null
      }
    },
    methods: {
      getUserInfo () {
        // 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
        $.ajax('/user/info', (data) => {
          this.userInfo = data;
        });
      }
    },
    mounted () {
      this.getUserInfo();
    }
  }
</script>
```

这样，任何页面或组件，只要通过 `inject` 注入 `app` 后，就可以直接访问 `userInfo` 的数据了

```
<template>
  <div>
    {{ app.userInfo }}
  </div>
</template>
<script>
  export default {
    inject: ['app']
  }
</script>
```

##### 混合`mixins`

> 使用 混合`mixins`，将不同的逻辑 分开到 不同的js文件中

**栗子**

```js
// user.js
export default {
  data () {
    return {
      userInfo: null
    }
  },
  methods: {
    getUserInfo () {
      // 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
      $.ajax('/user/info', (data) => {
        this.userInfo = data;
      });
    }
  },
  mounted () {
    this.getUserInfo();
  }
}
```

然后在 `app.vue` 中混合：

```html
// app.vue
<script>
  import mixins_user from '../mixins/user.js';

  export default {
    mixins: [mixins_user],
    data () {
      return {

      }
    }
  }
</script>
```

这样，跟用户信息相关的逻辑，都可以在 `user.js` 里维护

#### dispatch 和 broadcast

> 父子组件间通信原理

##### $on 与 \$emit

`$emit` 会在**当前组件**实例上触发自定义事件，并传递一些参数给监听器的回调，一般来说，都是在父级调用这个组件时，使用 `@on` 的方式来监听自定义事件的

```
// child.vue
export default {
  methods: {
    handleEmitEvent () {
      this.$emit('test', 'Hello Vue.js');
    }
  }
}
```

```
// parent.vue，在父组件监听child.vue触发的自定义事件test
<template>
  <child-component @test="handleEvent">
</template>
<script>
  export default {
    methods: {
      handleEvent (text) {
      	console.log(text);  // Hello Vue.js
      }
    }
  }
</script>
```

看似是在父组件 *parent.vue* 中绑定的自定义事件 **test** 的处理句柄，然而事件 test 并不是在父组件上触发的，而是在子组件 *child.vue* 里触发的，只是通过 `v-on` 在父组件中监听。

子组件也可以使用 `$on` 来监听实例上的事件

`$on` 监听了自己触发的自定义事件，因为有时不确定何时会触发事件，一般会在 `mounted` 或 `created` 钩子中来监听。

##### $dispatch 与 \$broadcast

> 过时

`$dispatch` 用于下级向上级派发事件，只要是它的父级（一级或多级以上），都可以在组件内通过 `$on` （或 events）监听到

 `$broadcast`  用于上级向下级广播事件

```
// 子组件 Vue.js 1.x版本
<template>
  <button @click="handleDispatch">派发事件</button>
</template>
<script>
export default {
  methods: {
    handleDispatch () {
      this.$dispatch('test', 'Hello, Vue.js');
    }
  }
}
</script>
```

```
// 父组件
<template>
  <child-component></child-component>
</template>
<script>
  export default {
    mounted () {
      this.$on('test', (text) => {
        console.log(text);  // Hello, Vue.js
      });
    }
  }
</script>
```

$broadcast 类似，只不过方向相反。这两种方法一旦发出事件后，任何组件都是可以接收到的，就近原则，而且会在第一次接收到后停止冒泡，除非返回 true。

**废弃的原因**

> 因为基于组件树结构的事件流方式有时让人难以理解，并且在组件结构扩展的过程中会变得越来越脆弱

##### 自行实现 dispatch 和 broadcast(********)

> 功能：
>
> - 在子组件调用 dispatch 方法，向上级指定的组件实例（最近的）上触发自定义事件，并传递数据，且该上级组件已预先通过 `$on` 监听了这个事件；
> - 相反，在父组件调用 broadcast 方法，向下级指定的组件实例（最近的）上触发自定义事件，并传递数据，且该下级组件已预先通过 `$on` 监听了这个事件。

复用性考虑封装到 混合mixins 内

```
import Emitter from '../mixins/emitter.js'

export default {
  mixins: [ Emitter ],
  methods: {
    handleDispatch () {
      this.dispatch();  // ①
    },
    handleBroadcast () {
      this.broadcast();  // ②
    }
  }
}
```

分析 方法传入什么参数

- 第1个参数应当是组件名【唯一值，用于寻找组件实例】
- 第2个参数是自定义事件名
- 第3个参数是传递的数据

**待补全**

## 插槽

> slot
>
> 组件的插槽 是 为了让我们封装的组件更加具有扩展性
>
> 封装： 抽取共性，保留不同

### 基本使用

`<slot></slot>`

**默认值**`<slot>默认值</slot>`

如果有多个值同时 放入到 组件进行替换时， 一起作为替换元素

### 具名插槽

> 当需要多个插槽时会用到 具名slot 。

```
<template>
	...
	<slot name='xxx'></slot>
</template>
```

**使用**

```
slot='xxx'
```

### 作用域插槽

> 父组件替换插槽的标签，但是内容由子组件提供
>
> 白话说作用就是 子组件中同样的数据，用不同的方式在父组件中 展示

在 `Vue` 实例 中 获取 子组件中的数据

```
<slot :data='pLanguage'>
```

```
<template slot-scope='slot'>
	<span v-for='item in slot.data'></span>
</template>
```

