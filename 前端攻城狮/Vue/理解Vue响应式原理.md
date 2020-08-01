# 理解Vue响应式原理

响应式原理作为 `Vue` 的核心，使用数据劫持实现数据驱动视图。

循序渐进的解析响应式原理的工作流程，主要以下面结构进行：

1. 分析主要成员，了解它们有助于理解流程
2. 将流程拆分，理解其中的作用
3. 结合以上的点，理解整体流程

## 主要成员

在响应式原理中，`Observe`、`Dep`、`Watcher` 这三个类是构成完整原理的主要成员。

- `Observe`，响应式原理的入口，根据数据类型处理观测逻辑
- `Dep`，依赖收集器，属性都会有一个`Dep`，方便发生变化时能够找到对应的依赖触发更新
- `Watcher`，用于执行更新渲染，组件会拥有一个渲染`Watcher`，我们常说的收集依赖，就是收集 `Watcher`

### Observe

> 对数据进行观测

1. 为观测的属性添加 `__ob__` 属性，它的值等于 `this`，即当前 `Observe` 的实例
2. 为数组添加重写的数组方法，比如：`push`、`unshift`、`splice` 等方法，重写目的是在调用这些方法时，进行更新渲染
3. 观测数组内的数据，`observe` 内部会调用 `new Observe`，形成递归观测
4. 观测对象数据，`defineReactive` 为数据定义 `get` 和 `set` ，即数据劫持

```
// 源码位置：/src/core/observer/index.js
class Observe {
  constructor(data) {
    this.dep = new Dep()
    // 1
    def(data, '__ob__', this)
    if (Array.isArray(data)) {
      // 2
      protoAugment(data, arrayMethods)
      // 3
      this.observeArray(data)
    } else {
      // 4
      this.walk(data)
    }
  }
  walk(data) {
    Object.keys(data).forEach(key => {
      defineReactive(data, key, data[key])
    })
  }
  observeArray(data) {
    data.forEach(item => {
      observe(item)
    })
  }
}
```

### Dep

> 我会为数据收集依赖

1. 数据收集依赖的主要方法，`Dep.target` 是一个 `watcher` 实例
2. 添加 `watcher` 到数组中，也就是添加依赖
3. 属性在变化时会调用 `notify` 方法，通知每一个依赖进行更新
4. `Dep.target` 用来记录 `watcher` 实例，是全局唯一的，主要作用是为了在收集依赖的过程中找到相应的 `watcher`

```
// 源码位置：/src/core/observer/dep.js
let id = 0
class Dep{
  constructor() {
    this.id = ++id // dep 唯一标识
    this.subs = [] // 存储 Watcher
  }
  // 1
  depend() {
    Dep.target.addDep(this)
  }
  // 2
  addSub(watcher) {
    this.subs.push(watcher)
  }
  // 3
  notify() {
    this.subs.forEach(watcher => watcher.update())
  }
}

// 4
Dep.target = null

export function pushTarget(watcher) {
  Dep.target = watcher
} 

export function popTarget(){
  Dep.target = null
}

export default Dep
```

`pushTarget` 和 `popTarget` 这两个方法显而易见是用来设置 `Dep.target`的。`Dep.target` 也是一个关键点，这个概念可能初次查看源码会有些难以理解，在后面的流程中，会详细讲解它的作用，需要注意这部分的内容。

### Watcher

> 我会触发视图更新

1. `this.getter` 存储的是更新视图的函数
2. `watcher` 存储 `dep`，同时 `dep` 也存储 `watcher`，进行双向记录
3. 触发更新，`queueWatcher` 是为了进行异步更新，异步更新会调用 `run` 方法进行更新页面

```
// 源码位置：/src/core/observer/watcher.js
let id = 0
export class Watcher {
  constructor(vm, exprOrFn, cb, options){
    this.id = ++id  // watcher 唯一标识
    this.vm = vm
    this.cb = cb
    this.options = options
    // 1
    this.getter = exprOrFn
    this.deps = []
    this.depIds = new Set()

    this.get()
  }
  run() {
    this.get()
  }
  get() {
    pushTarget(this)
    this.getter()
    popTarget(this)
  }
  // 2
  addDep(dep) {
    // 防止重复添加 dep
    if (!this.depIds.has(dep.id)) {
      this.depIds.add(dep.id)
      this.deps.push(dep)
      dep.addSub(this)
    }
  }
  // 3
  update() {
    queueWatcher(this)
  }
}
```

## 响应式原理流程

### 1、数据观测

数据在初始化时会通过 `observe` 方法来调用 `Observe`

```
// 源码位置：/src/core/observer/index.js
export function observe(data) {
  // 1
  if (!isObject(data)) {
    return
  }
  let ob;
  // 2
  if (data.hasOwnProperty('__ob__') && data.__ob__ instanceof Observe) {
    ob = data.__ob__
  } else {
    // 3
    ob = new Observe(data)
  }
  return ob
}
```

在初始化时，`observe` 拿到的 `data` 就是我们在 `data` 函数内返回的对象。

1. `observe` 函数只对 `object` 类型数据进行观测
2. 观测过的数据都会被添加上 `__ob__` 属性，通过判断该属性是否存在，防止重复观测
3. 创建 `Observe` 实例，开始处理观测逻辑

#### 对象观测

进入 `Observe` 内部，由于初始化的数据是一个对象，所以会调用 `walk` 方法:

```
walk(data) {
  Object.keys(data).forEach(key => {
    defineReactive(data, key, data[key])
  })
}
```

`defineReactive` 方法内部使用 `Object.defineProperty` 对数据进行劫持，是实现响应式原理最核心的地方。

```
function defineReactive(obj, key, value) {
  // 1
  let childOb = observe(value)
  // 2
  const dep = new Dep()
  Object.defineProperty(obj, key, {
    get() {
      if (Dep.target) {
        // 3
        dep.depend()
        if (childOb) {
          childOb.dep.depend()
        }
      }
      return value
    },
    set(newVal) {
      if (newVal === value) {
        return
      }
      value = newVal
      // 4
      childOb = observe(newVal)
      // 5
      dep.notify()
      return value
    }
  })
}
```

1. 由于值可能是对象类型，这里需要调用 `observe` 进行递归观测
2. 这里的 `dep` 就是上面讲到的每一个属性都会有一个 `dep`，它是作为一个闭包的存在，负责收集依赖和通知更新
3. 在初始化时，`Dep.target` 是组件的渲染 `watcher`，这里 `dep.depend` 收集的依赖就是这个 `watcher`，`childOb.dep.depend` 主要是为数组收集依赖
4. 设置的新值可能是对象类型，需要对新值进行观测
5. 值发生改变，`dep.notify` 通知 `watcher` 更新，这是我们改变数据后能够实时更新页面的触发点

通过 `Object.defineProperty` 对属性定义后，属性的获取触发 `get` 回调，属性的设置触发 `set` 回调，实现响应式更新。

通过上面的逻辑，也能得出为什么 `Vue3.0` 要使用 `Proxy` 代替 `Object.defineProperty` 了。`Object.defineProperty` 只能对单个属性进行定义，如果属性是对象类型，还需要递归去观测，会很消耗性能。而 `Proxy` 是代理整个对象，只要属性发生变化就会触发回调。

#### 数组观测

对于数组类型观测，会调用 `observeArray` 方法：

```
observeArray(data) {
  data.forEach(item => {
    observe(item)
  })
}
```

与对象不同，它执行 `observe` 对数组内的对象类型进行观测，并没有对数组的每一项进行 `Object.defineProperty` 的定义，也就是说数组内的项是没有 `dep` 的。

所以，我们通过数组索引对项进行修改时，是不会触发更新的。但可以通过 `this.$set` 来修改触发更新。那么问题来了，为什么 `Vue` 要这样设计？

结合实际场景，数组中通常会存放多项数据，比如列表数据。这样观测起来会消耗性能。还有一点原因，一般修改数组元素很少会直接通过索引将整个元素替换掉。例如：

```
export default {
    data() {
        return {
            list: [
                {id: 1, name: 'Jack'},
                {id: 2, name: 'Mike'}
            ]
        }
    },
    cretaed() {
        // 如果想要修改 name 的值，一般是这样使用
        this.list[0].name = 'JOJO'
        // 而不是以下这样
        // this.list[0] = {id:1, name: 'JOJO'}
        // 当然你可以这样更新
        // this.$set(this.list, '0', {id:1, name: 'JOJO'})
    }
}
```

**数组方法重写**

当数组元素新增或删除，视图会随之更新。这并不是理所当然的，而是 `Vue` 内部重写了数组的方法，调用这些方法时，数组会更新检测，触发视图更新。这些方法包括：

- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()

回到 `Observe` 的类中，当观测的数据类型为数组时，会调用 `protoAugment` 方法。

```
if (Array.isArray(data)) {
  protoAugment(data, arrayMethods)
  // 观察数组
  this.observeArray(data)
} else {
  // 观察对象
  this.walk(data)
}
```

这个方法里把数组原型替换为 `arrayMethods` ，当调用改变数组的方法时，优先使用重写后的方法。

```
function protoAugment(data, arrayMethods) {
  data.__proto__ = arrayMethods
}
```

接下来看看 `arrayMethods` 是如何实现的:

1. 将数组的原型保存起来，因为重写的数组方法里，还是需要调用原生数组方法的
2. `arrayMethods` 是一个对象，用于保存重写的方法，这里使用 `Object.create(arrayProto)` 创建对象是为了使用者在调用非重写方法时，能够继承使用原生的方法
3. 调用原生方法，存储返回值，用于设置重写函数的返回值
4. `inserted` 存储新增的值，若 `inserted` 存在，对新值进行观测
5. `ob.dep.notify` 触发视图更新

```
// 源码位置：/src/core/observer/array.js
// 1
let arrayProto = Array.prototype
// 2
export let arrayMethods = Object.create(arrayProto)

let methods = [
  'push',
  'pop',
  'shift',
  'unshift',
  'reverse',
  'sort',
  'splice'
]

methods.forEach(method => {
  arrayMethods[method] = function(...args) {
    // 3
    let res = arrayProto[method].apply(this, args)
    let ob = this.__ob__
    let inserted = ''
    switch(method){
      case 'push':
      case 'unshift':
        inserted = args
        break;
      case 'splice':
        inserted = args.slice(2)
        break;
    }
    // 4
    inserted && ob.observeArray(inserted)
    // 5
    ob.dep.notify()
    return res
  }
})
```

### 2、依赖收集

依赖收集是视图更新的前提，也是响应式原理中至关重要的环节。

#### 伪代码流程

初始化：

1. 首先会对 `name` 属性定义 `get` 和 `set`
2. 然后初始化会执行一次 `watcher.run` 渲染页面
3. 这时候获取 `data.name`，触发 `get` 函数收集依赖。

更新：

修改 `data.name`，触发 `set` 函数，调用 `run` 更新视图。

```
// data 数据
let data = {
    name: 'joe'
}

// 渲染watcher
let watcher = {
    run() {
        dep.tagret = watcher
        document.write(data.name)
    }
}

// dep
let dep = [] // 存储依赖 
dep.tagret = null // 记录 watcher

// 数据劫持
let oldValue = data.name
Object.defineProperty(data, 'name', {
   get(){
       // 收集依赖
       dep.push(dep.tagret)
       return oldValue
   },
   set(newVal){
       oldValue = newVal
       dep.forEach(watcher => {
           watcher.run()
       })
       
   }
})
```

#### 真正流程

1、首先初始化数据，调用 `defineReactive` 函数对数据进行劫持。

```
function defineReactive(obj, key, value) {
  let childOb = observe(value)
  const dep = new Dep()
  Object.defineProperty(obj, key, {
    get() {
      if (Dep.target) {
        dep.depend() // 收集依赖
        if (childOb) {
          childOb.dep.depend()
        }
      }
      return value
    },
    set(newVal) {
      if (newVal === value) {
        return
      }
      value = newVal
      childOb = observe(newVal)
      dep.notify()
      return value
    }
  })
}
```

2、初始化将 `watcher` 挂载到 `Dep.target`，`this.getter` 开始渲染页面。渲染页面需要对数据取值，触发 `get` 回调，`dep.depend` 收集依赖。

```
export class Watcher {
  constructor(vm, exprOrFn, cb, options){
    this.getter = exprOrFn
    this.get()
  }
  get() {
    pushTarget(this)
    this.getter()
    popTarget(this)
  }
}
```

3、`Dep.target` 为 `watcher`，调用 `addDep` 方法，并传入 `dep` 实例。

```
class Dep{
  constructor() {
    this.id = id++
    this.subs = []
  }
  depend() {
    Dep.target.addDep(this)
  }
}
```

4、`addDep` 中添加完 `dep` 后，调用 `dep.addSub` 并传入当前 `watcher` 实例

```
export class Watcher {
  constructor(vm, exprOrFn, cb, options){
    this.deps = []
    this.depIds = new Set()
  }
  addDep(dep) {
    if (!this.depIds.has(dep.id)) {
      this.depIds.add(dep.id)
      this.deps.push(dep)
      dep.addSub(this)
    }
  }
}
```

5、将传入的 `watcher` 收集起来，至此依赖收集流程完毕。

```
class Dep{
  constructor() {
    this.id = id++
    this.subs = []
  }
  addSub(watcher) {
    this.subs.push(watcher)
  }
}
```

补充一点，通常页面上会绑定很多属性变量，渲染会对属性取值，此时每个属性收集的依赖都是同一个 `watcher`，即组件的渲染 `watcher`。



详情： `https://mp.weixin.qq.com/s/ZCdVJoul8KktB0WyrHKwLQ`