# 组件

> 组件： 为了拆分 `Vue` 实例的代码量，能够让我们以不同的组件来划分不同的功能模块，将来需要什么样的功能就去调用相应的组件

**组件化和模块化的区别**

- 模块化：从代码逻辑的角度进行划分
- 组件化：从UI界面的角度进行划分

## 1.注册时的模板分离

**方式一**

```
<script type='text/x-template' id='名称(id与模板联系起来)'> </script>
```

**方式二**

```
<template id='名称'></template>
```

## 2.组件的使用

> 组件的标签名  仅适用当作HTML的标签来使用

### 2.1创建组件构造器 

> `Vue.extend()`

```
var com1 = Vue.extend({
	template:	// 代表自定义组件的模板
})
```

### 2.2注册组件

> `Vue.component()`

```
Vue.component('组件的标签名', 组件构造器)
```

**语法糖：**( 省去了 `vue.extend()` 的步骤，直接使用一个对象来代替 )

​	注册全局组件：`Vue.component(组件的标签名, {template:})`

#### 2.2.1全局组件

> 全局组件意味着 该组件 可以在多个Vue实例下面使用

#### 2.2.2局部组件

> 组件的私有化
>
> 局部组件的注册方式【在 Vue 的实例下注册】
>
> ```
> new Vue{
>  components:{
>      使用组件时的标签名: 组件构造器
>  },
> }
> ```

### 2.3使用组件

> 组件的标签名如果是驼峰命名法 在引用组件的时候改为 '-' 的方式

#### 2.3.1data和methods

```
Vue.component('组件的名称', {
		data: function(){
			return {}
			},
		methods:{}
	})
```

**组件中的data 和 实例中的data**

> 组件内部 是不能 访问 `Vue` 实例里的数据的

- 实例data 可以为一个对象，而 组件中的data必须是一个方法，方法内部必须返回一个对象
- 但是使用方式完全一样

**组件中的data 为什么必须是一个方法**

> 为了实现变量的独立，
>
> **因为函数 在执行的时候都会在自己的 栈空间里面 创建新的数据对象，需要有自己的对象来保存自己的状态，**
>
> 所以  多个 组件实例对象 并不是共享 data 和methods。

#### 2.3.2组件的渲染

> 组件的渲染 `render` 会覆盖之前的元素，只展示自己的样式

```
new Vue({
	methods:{},
	// createElements是一个方法，调用它能够把 指定的组件模板渲染为 html结构
	render:function(createElements){
		// return 的结果会替换 页面中 el指定的那个容器
		return createElements(login)
	}
})
```

#### 2.3.3组件的切换

##### 两个组件的切换

> v-if 和 v-else 结合 flag 进行切换

##### 多个组件的切换

> `Vue` 提供了 component 元素，来展示对应名称的组件
>
> component 元素 是一个占位符
>
> ```
> <component :is="componentId"> </component>
> // :is属性 用来指定要展示 组件的名称【字符串】
> ```

##### 过渡动画

> 组建的过渡动画 不需要使用 key特性， 只需要使用动态组件

```
<transition mode="out-in">
// mode属性 设置组件切换时候的模式
</transition>
```

## 3.父组件和子组件

> 在注册的某个组件里面 注册另外一个组件

```
Vue.extend({
	template:
	conponents:{} //在注册的某个组件里面 注册另外一个组件
})
```


