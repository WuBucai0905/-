# `Vuex`状态管理

**概念**

> 集中式存储管理应用的 所有组件的状态，以相应的规则保持状态以一种 可预测的方式发生变化
>
> 实现全局状态(数据) 管理的一种机制，可以方便的实现组件之间的共享

**好处**

- 集中管理共享的数据，易于开发和后期维护


- 能够高效的实现 组件之间的数据共享，提高开发效率


- 存储在`vuex`中的数据都是**响应式**的，能实时保持 数据和页面的同步


**管理什么状态**

> 组件之间共享的数据才有必要 存储在 `vuex` 中
>

- 多个状态 在多个界面间的共享问题
  - 比如 用户的登录状态、用户名称、头像、地理位置信息等等
  - 比如 商品的收藏、 购物车中的物品等等

## 1.基本使用

**安装**

```
npm install vuex --save
```

**导入**

```
import Vuex from 'vuex'
Vue.use(Vuex)
```

**创建store对象**

```
const store = new Vuex.Store({
	state: { count: 0 }
	// state中存放的是全局共享的数据
})
```

将store对下给你挂载到 `vue` 实例中

```
new Vue({
	el:
	render: h => h(),
	store
	// 所有的组件，就可以直接从 store 中获取全局的数据了
})
```

挂载时 `Vue.prototype.$store = store`

## 2.核心概念

state、mutation、action、getter、 module

| `state` 初始化时 在对象里定义一个 计数器 count               |
| ------------------------------------------------------------ |
| *mutation可以简单的理解为 组件当中的 methods*<br />通过mutation 注册一个事件，然后通过 `commit` 来提交事件来改变 state<br />默认 mutation 里面的所有事件方法 都有参数state，通过state可以获取到store里面的数据 |
| 可以理解为 在mutation 里面去注册事件， 在`action` 里面去触发事件 |

>在某个 组件中想使用 `vuex` 的数据，都是通过 **计算属性** 的方式
>
>- 原因： `vuex`的属性是可以动态改变的，我们需要追踪它的变化，而 计算属性正好能追踪 数据的变化
>
>
>当想要获取 `vue` 里面内部的数据时，都是通过 `$` 符号来获取的

### state

> 提供唯一的公共数据源，所有共享数据都统一放到 store 的state中存储

**state单一状态树**

> 单一数据源
>
> 状态信息保存到 多个Store对象中后之后的管理和维护会变得特别困难
>
> 所以`Vuex` 使用了单一状态树 来管理应用层级 的全部状态
>
> 单一状态树能够让我们用最直接的方式找到某个状态的片段，在之后的维护和调试过程中，会非常方便

#### 组件中访问state数据

**第一种方式**

> `this.$Store.state.全局名称`

**第二种方式**  

> 当一个组件想要获取 多个状态的时候，声明计算属性会显得冗余
>
> 辅助函数`mapState` 返回的是一个对象
>
> 将它与局部计算属性混合使用: 工具函数将多个对象合并为一个，然后最终对象 传给 computed 属性，使用对象展开运算符
>
> 又想使用计算属性，又想使用 `vuex`里面的数据时

```
import { mapState } from 'vuex'
// 将组件需要的全局数据 映射为 当前组件的计算属性
// ...表示 展开运算符，起到映射的作用
export default {
	// computed:{ ...mapState(['count']) }
	computed mapState({
        count: state => state.count, // 箭头函数更简练
        // count: 'count', // 传字符串参数相当于 state => state.count
        countPlusLocalState(state){
            return state.count + this.localCount
        }	
    })
}
```

### mutation

> mutation主要包括两部分
>
> - 字符串的事件类型
>
> - 一个回调函数，该回调函数的第一个参数就是 state

> 只有 mutations 中定义的函数，才有权利 修改 state中的数据
>
> - 只能通过mutation 变更 store数据，不可以直接操作 store 中的数据
> - 通过这种方式可能繁琐，但可以集中监控 所有的数据的变化【`Devtools`浏览器插件】

**传递参数**

- 参数被称为是 mutation的载荷(payload)
- 多个参数的传递 一般使用 对象的形式传递

**提交风格**

普通的提交风格

```
this.$store.commit('add', count)
```

包含 type属性的对象

> 此时提交后后面的参数 是 payload对象

```
this.$store.commit({
	type: 'add',
	count
})
```

**类型常量**

```
// mutation-types.js
export const INCREMENT = 'increment'

//index.js
mutations:{
	[INCREMENT](state){
		...
	},
	...
}
```

**第一种方式**

```
// 定义mutation
const store = new Vuex.Store({
	state:{},
	mutation:{
		add(state){// 变更状态},
		// addN(state, args){// 变更状态} // 传参
	}
})
// 触发mutation
methods:{
	// 第一种触发方式 commit作用就是调用 mutation函数
	// vuex生成了一个 $store 实例
	handle(){this.$store.commit('add')}
	// handle(){this.$store.commit('add', args)} // 传参
}
```

**第二种方式** 

```
// 触发mutation的第二种方式
import { mapMutation } from 'vuex'
// 将指定的 mutation 函数映射到当前组件的 methods 函数
methods:{...mapMutation(['add', 'addN'])}
```

### action

> mutations函数里面不能写 异步的代码
>
> action用于处理 异步任务 
>
> 在action 中，不能直接修改 state的数据; 必须通过`context.commit() `触发mutations才行	//默认属性：context上下文 

```
const store = new Vuex.Store({
	state:{},
	mutation:{
		add(state){// 变更状态},
		// addN(state, args){// 变更状态} // 传参
	},
	actions: {
		// context可以认为是一个 new 出来的实例对象
		addAsync(context, args){
			setTimeout(() => {context.commit('add', args)}, 1000)
		}
	}
})
```

**第一种方式**

```
// 触发action
methods:{
	handle(){
		this.$store.dispatch('addAsync', args)
	}
}
```

**第二种方式**

```
import { mapActions } from 'vuex'
// 触发action
methods:{
	...mapActions(['addAsync', 'addNAsync'])
}
```

### `Vuex`中的计算属性：getters

> 也是一个对象 
>
> 用于对 store 中的数据进行加工处理形成新的数据
>
> 不会修改原数据， 只是起到了一个包装数据的作用

**特性**

- 对store中已有的数据加工处理之后形成新的数据，类似 `Vue`的计算属性
- store 中数据发生变化，getter 的数据也会跟着变化

**定义**

```
const store = new Vuex.Store({
	state: {count: 0},
	getters: {
		showNum: state =>{
			return '当前数为【' + state.count + '】'
		}
		// showNum: (state, getters) =>{
		// 	return '当前数为【' + getters.showNum + '】'
		// }
	}
})
```

**接收外面的参数**

```
showNum: state =>{
			return function(args){
				return // 这里对 传入的 args 来做操作
			}
		}
```

**第一种方式**

> `this.$store.getters.名称`

**第二种方式**

```
import { mapGetters } from 'vuex'
// 触发getters
computed:{
	...mapGetters(['showNum'])
}
```

取不同名字时需要在 `mapGetters` 中使用对象的方法 做映射

### 模块化Modules

> 适用于各自的模块来负责自己的公共数据，不会互相影响

```
export default new Vuex.Store({
	modules:{
		count // 对象的属性简写:键名和值是一样 ES6语法
	}
})
```

此时计算属性中 如果时字符串传值需要换为 箭头函数传值

```
count: state => state.count.count[state.模块.数据]
```



## mutation的响应 规则

- 提前在store 中初始化好所需的属性【高版本可以了】
- 做到响应式
  - 添加 `Vue.set()`
  - 删除 `Vue.delete()`