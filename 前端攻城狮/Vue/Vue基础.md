# Vue基础

## 1.指令

> `v-on` 事件绑定
>
> - 使用事件绑定机制为元素指定处理函数的时候，加了小括号就可以给函数传参
>
> `v-bind` 绑定属性
>
> - 只能实现数据的单项绑定，从M自动绑定到V，无法实现数据的双向绑定
>- 绑定时还可以进行拼接字符串的操作
> 
> `v-model` 表单元素 和 Model 双向数据绑定的指令
>
> - 只能运用在 表单元素中
>  - input(radio\text\address\email...) 
>   - select
>   - checkbox
>   - textarea
> 
> `v-for=""` 后面是一个表达式，是对可迭代对象的遍历 
>
> - 使用的同时需要指定唯一的 key 值
>  - key属性只能是number或string
>   - key属性必须使用 v-bind 属性绑定的形式 指定key 的值
> - 迭代对象中的属性
>   - 在遍历对象上的键值对的时候，除了 val 和 key，在第三个位置还有一个 索引
> - 迭代数字
>   - 迭代数字 从 1 开始
> 
> `v-html="url"` 
>
> - 渲染内容显示为 HTML 的样式
>- 会 覆盖元素中原本的内容
> 
> `v-text=""`  展示文本
>
> - 默认没有闪烁问题
>- 会覆盖元素中原本的内容，一般不使用，不灵活
> 
> `v-cloak` 浏览器解析html代码是从上往下解析的
>
> - 解决 插值表达式闪烁 的问题
>
> - vue解析之前div有属性v-cloak； vue解析之后div无属性v-cloak
>
> ```
><style>
>      [v-cloak]{
>          display: none;
>       }
>    </style>
>    ```
>   
>   `v-once` 显示的是最初的 data 值，不会根据 data数据的改变而改变
>
> `v-pre`  把里面的东西原封不动的显示出来而不做任何的解析
>
> `v-if` 
>
> - 每次都会重新删除或创建元素
>- 有较高的切换性能消耗
> 
> `v-show`
>
> - 每次不会进行DOM的删除或创建操作，指挥切换了元素的样式
>
> - 有较高的初始渲染消耗
>
> ```
>style="display: none;"
>   ```
>   
>   - v-show 和 v-if 的区别：
>
>   - v-if 为false，该指令根本就不会存在DOM中
>  - v-show为false， 该指令样式 `display:none `
>   - 切换频繁使用 v-show , 页面比较大，加载慢时使用 v-if

### 1.1条件判断

`v-if`  |  `v-if` 和 `v-else`  |  `v-else-if`

- 当条件过多时不介意使用该方法，而是用到 `computed` 属性

### 1.2循环遍历

> `v-for` 中解决 复用问题 必须加上 `:key=""`

`v-for` 遍历 数组、 对象...

- 遍历数组
  - `v-for="(item, index) in xxx"`
- 遍历对象
  - 在遍历 对象的过程中，如果只获取一个值，那获取得到的就是value
  - `v-for="(value, key) in xxx"`
  - `v-for="(value, key, index) in xxx"`

### 1.3自定义指令

> `vue` 中所有的指令在调用的时候都以 v- 开头

#### 1.3.1全局指令

```
Vue.directive('focus', {
	bind: function(el){
		xxxxxxxxxxxxxxxx
	}
})
```

- 参数1：指令的名称
  - 定义不需要加 v- 前缀，调用时候需要加前缀
- 参数2：是一个对象
  - 对象是指令相关的函数，函数在特定的阶段执行相关的操作

**钩子函数**

- bind 每当指令 绑定到元素上的时候，会立即执行这个 bind函数，只执行一次

  - bind时 元素刚绑定指令，还没有插入到 DOM 中去
  - 可以理解为是在内存中执行
  - 和样式相关的操作，可以在 bind 中操作

- inserted 表示 元素插入到DOM中的时候，会执行 inserted 函数 【触发一次】
  - 在 DOM 中执行
  - 和JS行为有关的操作，最好在 inserted 中去执行，防止行为不生效

- updated 当VNode更新的时候会执行， 可能会触发多次

- 简写

  - 想在 bind 和 update 钩子上做重复动作，并不关系其他的钩子函数

    ```
    'fontsize': function(el, binding){}
    // 这个function 等同于把代码写道了 bind 和 update 中去
    // binding是一个对象
    ```

#### 1.3.2私有指令

```
new Vue({
	directives:{}
})
```

## 2.插值操作

> 将数据 绑定到 文本里面

### 2.1mustache语法

 `{{  }}`  胡子语法

- 中间可以直接写变量，也可以写简单表达式

## 3.属性

### 3.1单项绑定

**绑定时需要 特别注意 是 变量 还是 字符串**

> 将数据 绑定到 属性里面
>
> 1. 先把服务器 请求过来的数据放到 data里面
>2. `v-bind:src="xxx"` 会将 `xxx` 当作一个变量，根据 变量名称 去找相对应的数据 。 `xxx`可以是 any，也可以是 Object。

#### 3.1.1动态绑定class

 满足不同的展示需求

**对象语法**

`v-bind:class="{类名1: true, 类名2: false, ...}"`。  `xxx`为object时 可以一次性多次传多个类名

> class="xxx"  固定的 class 用该形式
>
> :class="{类名1: true, 类名2: false, ...}" 可能删除的 class 用该形式
>
> - 当觉得 `{类名1: true, 类名2: false, ...}` 过于长时可以使用 methods方法来return。 调用方法时后面需要加上 小括号了

**数组语法**

`v-bind:class="[xxx, yyy, ...]"`

#### 3.1.2动态绑定样式

> 动态绑定样式在 组件化开发的时候用的比较多
>
> 对象里面属性中包含横线，必须加 **单引号** 

**对象语法**

`v-bind:style="{css属性名1: 属性值1, css属性名2: 属性值2, ...}"`

**数组语法**

`v-bind:style="[{css属性名1: 属性值1}, {css属性名2: 属性值2}, ...]"`

### 3.2计算属性

> 可以对 data 来进行操作【数据转换或者多个数据合并到一起进行展示等】
>
> 计算属性在多次调用的时候 只会调用 一次。即计算属性会有缓存，性能较好

```
computed:{
	fullname: function(){
		xxx;
	}
}
```

#### 3.2.1getter 和 setter

**完整的计算属性的写法**

> 计算属性一般没有 set 方法，--- 只读属性

```
computed:{
	fullname:{
		set: function(newValue){
			
		},
		get: function(){
			return 'xxxxxxx'
		}
	}
}
```

## 4事件监听

> `v-on: Events` 
>
> function | inline statement | object(比较少)

### 4.1参数传递

> 当你在浏览器做操作的时候，浏览器会 生成一个event对象

如果函数需要参数，但没有传入，那么函数的形参为 undefined

方法定义时，既要 event 对象，同时又需要其他参数

调用方法时，手动获取浏览器的 event 对象: $event

#### 4.1.1默认参数

方法本身中有一个 参数， 默认将 原生事件event参数 传递进去

## 5.事件修饰符

> .stop 调用`event.stopPropagation()` 防止事件冒泡
>
> .prevent 调用`event.preventDefault()` 阻止默认事件
>
> - 如 `a` 标签的自动刷新页面
>
> .once 只触发一次回调 如:防止表单重复提交
>
> .self 只当事件在该元素本身(比如不是子元素)触发时触发回调
>
> .native 监听组件根元素的监听事件
>
> .capture 添加事件侦听器时 使用事件捕获模式【从外到里触发】
>
> .(keyCode | keyAlias) 只有当事件是从 特定键触发时才能触发回调
>
> - 按键修饰符
>
> - .enter | .tab | .delete(捕获 删除 和 退格 键) | .esc | .space | .up ...
> - 也可以通过查看 键值码 来修饰
> - 还可以自定义全局按键修饰符 `Vue.config.keyCodes.f2 = 113`

## 6.双向绑定

> `v-model` 实现 表单元素和数据的 双向绑定

### 6.1原理

`v-bind` 将 message 绑定到 标签的value中

`v-on` 监听事件 实现 value值和输入值 的同步改变

### 6.2实例

**v-model结合radio**

> radio单选框的互斥效果  加入一样的 name属性值
>
> 加入 `v-model` 绑定一样的 name属性值，此时其实 name属性值可以省略

**v-model结合checkbox**

> checkbox 配合 label 使用
>
> 分为 单选 和 多选 两个情况
>
> 单选框时： id 属性值 对应 label的 for，没有的话不管用， 实现单选操作 --- 单选框 对应的data是 boolean类型
>
> for 属性绑定： 意思就是要和 哪一个 input 对应起来，用来绑定id
>
> 多选框时： 对应的data是 数组类型
>
> input框的value属性是你获取到的内容

**v-model结合select**

>不常用
>
>分为 单选 和 多选 两个情况
>
>v-model 是绑定在 select 标签上
>
>单选： v-model
>
>多选： v-model  +  multiple

### 6.3修饰符

> .lazy 默认数据是实时绑定，.lazy 可以让 数据失去焦点 或者回车时才会更新
>
> .number 默认输入框中都会被做字符串处理， 可以让输入框中输入的内容自动转换为 数字类型
>
> .trim 首尾很多空格，通常希望将其去除， 可以过滤内容左右两边的空格

## 7.过滤器

>允许自定义过滤器，用作一些常见文本格式化

用途：mustache插值 和 v-bind表达式

两个条件： 过滤器名称和处理函数

采用原则：就近原则

### 7.1全局过滤器

```
// 过滤器的定义语法
// function第一个参数已经被规定死了永远都是 管道符 前面传递过来的数据
Vue.filter('过滤器的名称', function(){})

// 过滤器调用格式[从左至右的调用顺序]
{{ name | nameoperation | ... }}
```

### 7.2私有过滤器

```
new Vue({
	filters:{}
})
```
