## 1.Vue实例的生命周期

> 从`Vue`实例的创建、运行到销毁期间发生的各种各样的事件统称为 生命周期
>
> 生命周期钩子 又称生命周期事件，生命周期函数等

**创建期间**:	`brforeCreate` | `created` | `beforeMount` | `mounted`

**运行期间**:	`beforeUpdate` | `updated`

**销毁期间**:	`beforeDestory` | `destoryed`

![image](./1060770-20170716220427050-1577910455.png)

1. new Vue() [创建实例对象]

2. init Events & Lifecycle [初始化实例对象，只有默认的生命周期函数和默认的事件]

   (`beforeCreate`) [表示实例完全被创建出来之前会执行它]

3. init data & methods

   (`created`)

4. 模板编译

   内存中生成模板字符串，生成 内存中的DOM。 并未挂载

   (`beforeMount`) [此时 模板尚未渲染到页面中]

5. 模板挂载

   (`mounted`) [内存模板挂载到页面]

**数据改变**

1. data changes [根据 data数据的改变 有选择性的触发 0次到无数次]

   (`beforeUpdate`) [界面没有更新，数据已经更新]

2. virtual DOM re-render and patch [根据data最新的数据，在内存中重新渲染新的内存DOM树，当最新的内存DOM树更新后会重新加载到真实页面中去]

   (`updated`) [页面和data数据已经保持同步]

**实例销毁**

1. mounted

   (`beforeDestory`) [Vue实例身上的所有的data 和methods 以及过滤器，指令...都处于可用状态]

2. .

   (`destory`) [Vue实例所有的... 都不可用]

```
new Vue()[创建实例对象] -->
init Events & Lifecycle[初始化实例对象，只有默认的生命周期函数和默认的事件] --> 
(`beforeCreate`)[表示实例完全被创建出来之前会执行它]
init injections & reactivity/data & methods -->
(`created`)
############ 进行模板编译 ################
has "el" option? -->yes/ no[when vm.$mount(el) is called]
has "template" option? -->yes[compile templates into render function *]/ no[compile el's outerHTML as template *]
############ 进行模板编译 ################
// 模板编译的时候还在内存中实现生成模板字符串，生成为内存中的 DOM,并没有将模板挂载到页面中去
(`beforeMount`)[模板已经在内存中编辑完成了，但尚未把模板渲染到页面中,页面中的元素还未真正替换过来，只是之前写的一些模板字符串] -->
create vm.$el and replace "el" with it[把内存中编译好的模板 真实的替换到 页面中去] -->
(`mounted`)[内存中的模板已经真实挂载到页面中了]

when data changes[根据 data数据的改变 有选择性的触发 0次到无数次]
(`beforeUpdate`)[表示界面没有被更新，但数据已经被更新了] -->
virtual DOM re-render and patch[根据data中最新的数据，在内存中重新渲染出一份最新的 内存DOM树，当最新的内存DOM树更新后会重新加载到真实页面中去，完成了data(Model)到View的更新] -->
(`updated`)[页面和data数据已经保持同步] -->

(`beforeDestory`)[Vue实例从运行阶段进入到了 销毁阶段，实例身上的所有的data 和methods 以及过滤器，指令...都处于 可用状态] -->
(`destory`)[Vue实例中所有的... 都不可用]
```

## 2.Vue中监听数据变化

### 2.1keyup

### 2.2watch

> watch属性可以监视 data中指定数据的变化，然后触发 watch 中对应的 function 函数
>
> 监视的东西是看不见摸不着的，如路由的改变，此时能通过watch来监听

```
new Vue({
	watch:{
		'frist-name': function(){}
		// 有横线必须加引号，没横线可加可不加
		// 参数中有 newVal 和 oldVal
		
		// 监听路由
		this.$route.path
	}
})
```

### 2.3computed

> 本质是一个方法，使用计算属性是直接当作**属性**来使用
>
> 计算属性 function内部用到的 任何 data中的数据发生了变化，就会立即重新计算这个计算属性的值
>
> 计算属性的求值结果，会被缓存起来方便下次直接调用。

### 2.4对比

- `computed`属性的结果会被缓存，除非依赖的响应式属性变化才会去重新计算，主要当作属性来使用
- `methods`方法表示一个具体的操作，主要书写业务逻辑
- `watch` 是一个对象，键是需要观察的表达式，值是对应回调函数，主要用来监听某些特定数据的变化，可以看作是 `computed` 和 `methods`的结合体。
  - 进行某些具体的业务逻辑操作



## 2.Vue中的动画

> 提高用户体验，帮助用户更好的理解页面中的功能

**基本使用**

1. 使用 transition 元素把 需要被 动画控制的元素包裹起来。transition 元素是`Vue`官方提供的元素
2. 自定义两组样式来控制 transition内部的元素实现动画

enter

- v-enter [这是一个时间点] 进入之前元素的起始状态
- v-enter-to
- v-enter-active [入场动画的时间段]

leave

- v-leave
- v--leave-to[这是一个时间点]动画离开之后离开的终止状态，元素动画已经结束了
- v-leave-active [离场动画的时间段]

**修改前缀**

修改 `v-` 前缀

```
<transiton name="my"> 改为了 my-
```

**钩子函数**

> 动画的生命周期函数

- `beforeEnter` 动画入场之前，动画尚未开始。可以设置元素开始动画之前的起始样式
- `enter` 动画开始之后的样式。

​		enter里面的done 其实就是 `afterEnter`这个函数

- ...[详情见文档]

钩子函数的第一个参数`el`，表示要执行动画的那个DOM元素，是个原生的`DOM`对象

- 通过 `document.getElementById("") `方式获取到原生`JS DOM`对象

### 2.1列表动画

> 实现列表过渡的时候，需要过渡的元素是通过 v-for 循环渲染出来的，则使用`transition-group` 包裹
>
> 要为 `v-for` 循环创建的元素设置动画，必须为每一个元素 设置 :key 属性

#### 2.1.1transition-group属性

`appear`属性，实现页面刚展示出来入场时候的效果

`tag`属性，指定 transition-group 渲染为指定的元素。不指定tag属性默认渲染为 span 标签，不符合规范

**列表删除**

.v-move 和 .v-leave-active配合使用能够实现 列表后续元素 渐渐飘上来的效果

```
.v-move{}
.v-leave-active{position:obsolute}
// obsolute不指定宽度，默认最小值
```

#### 2.1.2第三方类

`animate.css`

```
<link rel="stylesheet" href="style.css">
// 使用
<transiton enter-active-class="animated xxx" leave-active-class="animated xxx" :duration="400">
简写可以为 class="animated"放到元素身上
// :duration入场/离场的动画时长 ，单位毫秒 也可以写为:duration{enter:200 leave:800}
```

