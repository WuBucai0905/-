## Vue.use中都发生了什么？

#### 定义

vue.use()往全局注入一个插件，供全局真接使用, 不需要单独引用

```
import Router from 'vue-router'
// 入口文件全局注入vue-router, 从而可以在全局使用this.$route
Vue.use(Router)

如果不使用vue.use 那么需在组件中使用都得单独引入
// a.vue
import Router from 'vue-router'
// b.vue
import Router from 'vue-router'
```

#### 源码

```
  Vue.use = function (plugin: Function | Object) {  // flow语法, 检测参数是否是函数或对象
  
    // 拿到已安装插件列表
    const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
    
    // 如果已经安装，直接跳出方法
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }

    // additional parameters
    
    // 取vue.use() 传入的参数
    const args = toArray(arguments, 1)
    
    // 将vue对象填充到第一位, 最后的结构为[vue,arg1,arg2,...]
    args.unshift(this)
    
    // 判断插件是否有install方法，如果有执行install方法，如果没有直接把插件当install执行
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args)
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args)
    }
    
    // 注册完成填充至已安装列表，保证每个插件只安装一次
    installedPlugins.push(plugin)
    return this
  }
```

#### 总结

vue.use()为注册全局插件所用，接收函数或者一个包含install属性的对象为参数，如果参数带有install就执行install, 如果没有就直接将参数当install执行, 第一个参数始终为vue对象, 注册过的插件不会重新注册

