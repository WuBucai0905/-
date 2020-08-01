**什么是前端渲染？什么是后端渲染？什么是前端路由？什么是后端路由？**

> HTML页面都是由 服务器 来渲染的
>
> 后端路由： 后端来处理 URL和页面之间的映射关系
>
> 前端渲染：浏览器中显示的网页中的大部分内容，都是由前端写的js代码在浏览器中执行，最终渲染出来的网页

> 现在发展到 单页面富应用阶段【SPA】。整个只有一个 html 页面
>
> 前端路由来做 支撑
>
> SPA最大的特点就是 在前后端分离的基础上 加了一层 前端路由，维护了一套映射关系。目的是 减少后端服务器的压力
>
> 前端路由：单页面应用程序，主要通过URL中的hash(#)实现不同网页之间的切换，HTTP请求中不会包含hash的相关内容
>
> **前端路由核心**： 改变 URL，但是页面 不进行整体的刷新。

**前端路由的额外知识点**

- 改变 `url` 的hash

  URL的hash也就是锚点(#), 本质是改变 `window.location`的`href`属性

  `location.hash = "xxx"`

- `HTML5`的`history` 模式: `pushState`

  `history.pushState({}, "", "home")` --- 是把这些 `url` 压入到 栈结构里面的

  - `history.back()` 移除栈顶元素

  - `history.go(-1)` === `history.back()`

  - `history.go(1)` === `history.forward()`

- `HTML5`的`history` 模式: `replaceState`

  `history.replaceState({}, "", "home")` --- 没有后退

## vue-router

单页面的应用控制中心	切换组件

> 对路由进行操作: $router
>
> 获取路由中的参数: $route

### 安装

```
npm install vue-router --save // 运行在客户端的时候还是需要路由
```

### 基本使用

1. 创建 路由对象，并且调用 `Vue.use(VueRouter)`，安装插件

   - 导入`vue-router` 包之后，在 window 全局对象中，就有了一个 路由的构造函数，叫做 `VueRouter`

2. 创建 路由实例， 并传入路由映射配置

   - 每个路由映射配置 都是一个对象，这个对象必须有两个属性

     ```
     // 在 `index.js` 中配置路由相关的信息
     // 配置路由和组件之间的映射关系
     routes: [
     	{path: '/home',
     	component: ()=> import('./views/Home.vue')
     	}
     ]
     ```

     - path 表示监听 哪个路由链接地址
     - component 表示如果 路由时前面匹配到的path，则展示 component 属性相应的那个组件
       - component的属性值，必须是一个 组件的模板对象，不能是组件的引用名称

3. 在`Vue`实例中 挂载 创建的 路由实例，通过 路由映射配置 去监听URL的地址变化，然后展示对应的组件

4. 注册 `router-view` 标签，专门用来当作占位符，路由规则匹配到的组件就会展示到这个  `router-view` 中去

   - 默认渲染为一个`a`标签，使用属性` tag` 标签可以改变
   - 可以搭配 `router-link` 中的 属性`to` 一起使用

### 首页默认路径值

**重定向**

```
routes: [
	{path: '',
	redirect: '/home'
	}
]
```

**修改为 history 模式**

创建路由实例的时候 添加 `mode: 'history'`

## 路由的跳转

> `vue` 中通过 `router-link`标签来实现 组件的跳转

**router-link**

> `to` 需要跳转的路径
>
> 其他属性：
>
> - `tag` 渲染成你想要的标签
> - `replace` 相当于 `HTML5`的`replaceState` 
> - `active-class = 'active'`
>   - 此时`router-link-active` ===  `active`点击谁，该标签就会出现该 class 值

### 编程式导航

应用场景: 登录页面

> 通过 事件 的方式来跳转 
>
> 全局变量 `$` 符
>
> `vue-router` 给所有组件里面都 加了 `$router` 的属性
>
> 跳转路由都是通过 : 
>
> ```
> this.$router.push(
> 	'{path:"/home"}'
> 	)
> `this.$router.replace('{path:"/home"}')`不能返回
> ```

## 动态路由

> 不同传值进入不同的组件
>
> `/home/:id` 

```
routes: [
	{path: '/home/:id',
	component: ()=> import('./views/Home.vue')
	}
]
```

获取传入的参数 `$route.params.id`

> `$route` 哪个路由处于活跃就是哪个路由
>
> `$router` 指的是 router 对象

### 编程式导航

**使用编程式导航来传递参数实现动态路由**

```
this.$router.push(
	// {path:"/home",query: {name: 'zyx'}}, // ?name=zyx
	{name:"/home",params: {id: 3}}
	)
```

>params 搭配 name【路由的名字】来使用
>
>query 搭配 path 来使用

## 参数传递

> 如果在路由中 使用查询字符串，给路由传递参数，不需要修改路由规则的path 属性
>
> 传递参数 主要有 两种类型: `params` 和 `query`
>
> params 搭配 name【路由的名字】来使用
>
> query 搭配 path 来使用

### params

- 配置路由格式: `/router/:id`
- 传递的方式: 在 path 后面跟上 对应的值
- `<router-link>` 需要有路径的 拼接
- 传递后形成的路径: /router/123

### query

- 配置路由格式:  `/router`
- 传递的方式: 对象中使用 query 的 key 作为传递方式
- 传递后形成的路径: /router?id=123

#### 编程式导航

> 传参显示在页面中可以通过`this.$route.query.id` 来显示

## 嵌套路由

> 在某个组件中显示另外一个组件

### 步骤：

1. 创建相应的子组件，并在路由映射中配置对应的子路由
   - 在该组件的 routes 中 定义 `children:[{}]` 
     - 配置 path 时 前面是不需要加 `/` 的
2. 在 **相应的组件** 内部使用 `<router-view>` 标签

## 导航守卫

> 进入路由的时候 需要先做一些事情【如: 获取token，判断用户权限等】

每个组件被 创建的时候都会来到 生命周期函数里面，可以在生命周期函数里面做一些事情

在`main.js`里面写入 钩子 

```
// 前置钩子 跳转之前进行 回调
router.beforeEach((to, from, next)=>{
	console.log(to.path)
	next()
})
```

> to 代表要访问的路由
>
> from 代表访问的路由的来源
>
> next 代表下一步的选择， 必须调用next() 方法，不然会被卡住

其实还有  后置钩子 `afterEach`  不需要主动调用 next() 函数

全局守卫

路由独享守卫

组件内守卫

**待补全**

## 路由懒加载

> 不使用懒加载，会默认 统一在`App.vue` 中加载所有路由的组件
>
> 而懒加载后只有进入该组件才会加载该组件
>
> 不同路由对应的组件 打包到不同的JavaScript文件里面【用到时在加载】

```
routes: [
	{path: '/home',
	component: ()=> import('./views/Home.vue')
	}
]
```

### 懒加载的方式

方式一： 结合`Vue`的异步组件和`Webpack`的代码分析

方式二： `AMD`写法

方式三： `ES6`中的写法，最常用的写法

**待补全**

## keep-alive

keep-alive是`Vue`内置的一个组件，使被包含的组件 保留状态，或者避免重新渲染

`activated()` 和 `deactivated()` 这两个函数只有在 该组件保持了状态使用了keep-alive 时才能被使用

### 属性

include 字符串或者正则表达，只有匹配的组件会被 缓存

exclude 字符串或者正则表达，任何匹配的组件都不会被缓存

**待补全**


