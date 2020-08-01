## Vue实战技巧

从三个方面 进阶对`vue`的运用能力

- `组件` : **全局组件注册**
- `Render函数` : **拯救繁乱的template**
- `Vue权限控制` : **高精度全局权限控制**

### 1.「全局组件注册」

> 使用组件 通过一个一个文件去引用和注册，会显得特别麻烦

```
<template>
  <div>
    <h1>I am HelloWorld</h1>
    <Child1></Child1>
  </div>
</template>

<script>
import Child1 from './child1.vue'   // 引入
export default {
  name: 'HelloWorld',
  data(){
    return{
    }
  },
  components:{   // 注册
    Child1
  },
  props: {
    msg: String
  },
  methods:{
  }
}
</script>

<style scoped lang="less">
</style>
```

重复引入和注册代码会显得既繁琐又不雅观。可以通过一个全局的`js`文件来管理，需要**多次使用**的组件进行全局注册。

```
// 1
// globalComponent.js
import Vue from 'vue' // 引入vue

// 处理首字母大写 abc => Abc
function changeStr(str){
    return str.charAt(0).toUpperCase() + str.slice(1)
}

/*
    require.context(arg1,arg2,arg3)
        arg1 - 读取文件的路径
        arg2 - 是否遍历文件的子目录
        arg3 - 匹配文件的正则
    关于这个Api的用法，建议小伙伴们去查阅一下，用途也比较广泛
*/
const requireComponent = require.context('.', false, /\.vue$/)
console.log('requireComponent.keys():',requireComponent.keys())  // 打印
requireComponent.keys().forEach(fileName => {
    const config = requireComponent(fileName)
    console.log('config:',config)  // 打印
    const componentName = changeStr(
        fileName.replace(/^\.\//, '').replace(/\.\w+$/, '')   // ./child1.vue => child1
    )
    
    Vue.component(componentName, config.default || config) // 动态注册该目录下的所有.vue文件
})
```

```
// 2
// 将globalComponent.js引入main.js

import global from './components/globalComponent'
```

```
// 3
// 使用这类组件不再需要引入和注册，直接标签使用即可

<template>
  <div>
    <h1>I am HelloWorld</h1>
    <Child1></Child1>
  </div>
</template>
```

#### 1.1路由分区和动态添加路由

> 对路由的引入和使用进行管理，实现分区引入路由，将不同功能下的路由进行区分，通过动态的方式进行引入，即方便快捷又增加可维护

##### 1.1.1创建专门的路由 `.js`文件管理所有路由

```
总路由管理文件 - index.js

分区路由
    - index.routes.js
    - login.routes.js

在大型项目中，往往会有很多互不关联的模块，例如电商平台中的商城，个人信息，这种情况下就可以对路由进行分区
```

```
// 分区路由文件写法

export default {
    path:'/index',
    name:'Index',
    component: () => import('../views/Index.vue'),  // 懒加载式引入，当跳转到时才进行引入chunk
    children: [...]
}
```

```
// 总路由管理文件 index.js 写法
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routerList = []  // 路由数组 - 存放所有路由
function importAll(routerArr){
    // 该函数用于将所有分区路由中的路由添加到路由数组
    routerArr.keys().forEach( key => {
        console.log(key)
        routerList.push(routerArr(key).default)
    })
}
importAll(require.context('.',true,/\.routes\.js/))

const routes = [
    ...routerList
]

const router = new VueRouter({
    routes
})

export default router
```

### 2.「拯救繁乱的template」

写组件欸点时候会依赖 脚手架中的 `<template></template>`标签，其实`template`也存在一定的缺陷

- `template`里存在一值多判断
- 过多使用`template`会使代码冗余，杂乱

**解决**

- render 函数

#### 2.1实战

实例

```
<template>
  <div>
    <h1>I am Home</h1>
    <!-- 假设按钮有多种类型,通过value来显示不同类型 -->
    <div v-if='value === 1'>
      <button>button1</button>
    </div>
    <div v-else-if='value === 2'>
      <button>button2</button>
    </div>
    <div v-else>
      <button>button3</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data(){
    return{
        value:1
    }
  },
  methods:{
  }
}
</script>

<style scoped lang="less">
</style>
```

多类型的button，就会显得杂乱无章。

很多人会选择去封装一个button组件，组件的封装，又是一个技巧点，利用`VUE`的`render`函数，减少不必要的`template`

```
// 创建一个button.vue文件 写法如下
<script>
export default {
    props:{
        type:{
            type:String,
            default:'normal'
        },
        text:{
            type:String,
            default:'button'
        }
    },
    render(h){
        /*
            h 类似于 createElement， 接受2个参数
            1 - 元素
            2 - 选项
         */
        return h('button',{
            // 相当于 v-bind:class
            class:{
                btn:true,
                'btn-success':this.type === 'success',
                'btn-danger':this.type === 'danger',
                'btn-warning':this.type === 'warning',
                'btn-normal':this.type === 'normal',
            },
            domProps:{
                innerText: this.text || '默认'
            },
            on:{
                click:this.handleClick
            }
        })
    },
    methods:{
        handleClick(){
            this.$emit('myClick')
        }
    }
}
</script>

<style scoped>
.btn{
    width: 100px;
    height:40px;
    line-height:40px;
    border:0px;
    border-radius:5px;
    color:#ffff;
}
.btn-success{
    background:#2ecc71;
}
.btn-danger{
    background:#e74c3c;
}
.btn-warning{
    background:#f39c12;
}
.btn-normal{
    background:#bdc3c7;
}
</style>
```

引入：

```
//  引入
<template>
  <div>
    <h1>I am Home</h1>
    <!-- 按钮根据value显示不同类型的button -->
    <Button type='success' text='button1' @myClick='...'></Button>
  </div>
</template>

<script>
import Button from './button.vue'
export default {
  name: 'Home',
  data(){
    return{
        value:1
    }
  },
  components:{
      Button
  },
  methods:{
  }
}
</script>

<style scoped lang="less">
</style>
```

根据`value`来显示不同类型的`button`，我们只需要通过`value`去修改`type，text`等，就可以实现这一目的，而不需要去创建多个`<button>`，通过`v-if`去判断

### 3.「高精度全局权限处理」

权限的控制可以挂在全局上对权限进行处理。

#### 3.1实战

处理某按钮显示权限问题

```
/*
    在项目里新建一个common文件夹用于存放全局 .js 文件
    这种全局文件夹做法相当普遍，一般项目里都应该有这样一个文件夹来管理全局的东西
*/

// common/jurisdiction.js  用于存放与权限相关的全局函数/变量

export function checkJurisdiction(key) {
    // 权限数组
    let jurisdictionList = ['1', '2', '3', '5']
    let index = jurisdictionList.indexOf(key)
    console.log('index:',index)
    if (index > -1) {
        // 有权限
        return true
    } else {
        // 无权限
        return false
    }
}
```

```
// 将全局权限Js挂载到全局中
// main.js

import { checkJurisdiction } from './common/jurisdiction'

// 优雅操作 - VUE自定义指令
Vue.directive('permission',{
  inserted(el, binding){
    // inserted → 元素插入的时候
    
    let permission = binding.value // 获取到 v-permission的值

    if(permission){
      let hasPermission = checkJurisdiction(permission)
      if(!hasPermission){
        // 没有权限 移除Dom元素
        el.parentNode && el.parentNode.removeChild(el)
      }
    }else{
      throw new Error('需要传key')
    }
  }
})
```

```
// 使用方式

<template>
  <div>
    <h1>I am Home</h1>
    <!-- 按钮根据value -->
    <div v-permission="'10'">
      <button>权限1</button>
    </div>
    <div v-permission="'5'">
      <button>权限2</button>
    </div>
  </div>
</template>

// 无需再通过value去判断，直接通过v-permission的值进行判断即可
```



`https://mp.weixin.qq.com/s/j3KvERfMMrAKnUxnCsZxTw`

## Vue开发技巧

### 1.路由参数的解耦

组件内使用路由一般会

```
export default {
    methods: {
        getParamsId() {
            return this.$route.params.id
        }
    }
}
```

组件中使用 `$route` 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。

#### 1.1做法

通过`props` 解耦

将路由的 `props` 属性设置为 `true` 后，组件内可通过 `props` 接收到 `params` 参数

```
const router = new VueRouter({
    routes: [{
        path: '/user/:id',
        component: User,
        props: true
    }]
})
```

```
export default {
    props: ['id'],
    methods: {
        getParamsId() {
            return this.id
        }
    }
}
```

还可以通过函数模式来返回 `props`

```
const router = new VueRouter({
    routes: [{
        path: '/user/:id',
        component: User,
        props: (route) => ({
            id: route.query.id
        })
    }]
})
```

### 2.函数式组件

函数式组件是无状态，它无法实例化，没有任何的生命周期和方法。

创建函数式组件也很简单，只需要在模板添加 `functional` 声明即可。

一般适合只依赖于外部数据的变化而变化的组件，因其轻量，渲染性能也会有所提高。

```
// 函数式组件
<template functional>
    <div class="list">
        <div class="item" v-for="item in props.list" :key="item.id" @click="props.itemClick(item)">
            <p>{{item.title}}</p>
            <p>{{item.content}}</p>
        </div>
    </div>
</template>
```

```
// 父组件使用
<template>
    <div>
        <List :list="list" :itemClick="item => (currentItem = item)" />
    </div>
</template>
```

```
import List from '@/components/List.vue'
export default {
    components: {
        List
    },
    data() {
        return {
            list: [{
                title: 'title',
                content: 'content'
            }],
            currentItem: ''
        }
    }
}
```

### 3.样式穿透

在开发中修改第三方组件样式是很常见，但由于 `scoped` 属性的样式隔离，可能需要去除 `scoped` 或是另起一个 `style` 。

这些做法都会带来副作用（组件样式污染、不够优雅），样式穿透在css预处理器中使用才生效。

```
<style scoped>
外层 >>> .el-checkbox {
  display: block;
  font-size: 26px;

  .el-checkbox__label {
    font-size: 16px;
  }
}
</style>
```

```
<style scoped>
/deep/ .el-checkbox {
  display: block;
  font-size: 26px;

  .el-checkbox__label {
    font-size: 16px;
  }
}
</style>
```

### 4.watch高阶使用

`watch` 是在监听属性改变时才会触发.

有些时候，我们希望在组件创建后 `watch` 能够立即执行

能想到的的方法就是在 `create` 生命周期中调用一次，但这样的写法不优雅

#### 4.1立即执行

或许我们可以使用这样的方法

```
export default {
    data() {
        return {
            name: 'Joe'
        }
    },
    watch: {
        name: {
            handler: 'sayName',
            immediate: true
        }
    },
    methods: {
        sayName() {
            console.log(this.name)
        }
    }
}
```

#### 4.2深度监听

在监听对象时，对象内部的属性被改变时无法触发 `watch` ，我们可以为其设置深度监听

```
export default {
    data: {
        studen: {
            name: 'Joe',
            skill: {
                run: {
                    speed: 'fast'
                }
            }
        }
    },
    watch: {
        studen: {
            handler: 'sayName',
            deep: true
        }
    },
    methods: {
        sayName() {
            console.log(this.studen)
        }
    }
}
```

#### 4.3触发监听执行多个方法

使用数组可以设置多项，形式包括字符串、函数、对象

```
export default {
    data: {
        name: 'Joe'
    },
    watch: {
        name: [
            'sayName1',
            function(newVal, oldVal) {
                this.sayName2()
            },
            {
                handler: 'sayName3',
                immaediate: true
            }
        ]
    },
    methods: {
        sayName1() {
            console.log('sayName1==>', this.name)
        },
        sayName2() {
            console.log('sayName2==>', this.name)
        },
        sayName3() {
            console.log('sayName3==>', this.name)
        }
    }
}
```

### 5.watch监听多个变量

watch本身无法监听多个变量。

但我们可以将需要监听的多个变量通过计算属性返回对象，再监听这个对象来实现“监听多个变量”

```
export default {
    data() {
        return {
            msg1: 'apple',
            msg2: 'banana'
        }
    },
    compouted: {
        msgObj() {
            const { msg1, msg2 } = this
            return {
                msg1,
                msg2
            }
        }
    },
    watch: {
        msgObj: {
            handler(newVal, oldVal) {
                if (newVal.msg1 != oldVal.msg1) {
                    console.log('msg1 is change')
                }
                if (newVal.msg2 != oldVal.msg2) {
                    console.log('msg2 is change')
                }
            },
            deep: true
        }
    }
}
```

### 6.事件参数$event

`$event` 是事件对象的特殊变量，在一些场景能给我们实现复杂功能提供更多可用的参数

#### 6.1原生事件

在原生事件中表现和默认的事件对象相同

#### 6.2自定义事件

在自定义事件中表现为捕获从子组件抛出的值

