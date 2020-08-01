# 模块化

常见的模块化 规范：`CommonJS` | `AMD` | `CMD` | `ES6中的Modules`

## CommonJS

### 导出

```
modules.exports = {}
```

### 导入

```
let { test, demo, flag} = require('moduleA')
===【对象结构的方式导入更简便】
let aaa = require('moduleA')
let test = aaa.test;
...
```

## ES6模块化

### 导出

> 导出函数 或 类

**方式一**

```
export { flag, sum } //对象结构
```

**方式二**

```
export var num1=1000;
```

**关于** `export default`：

> 一个模块中 包含 某个的功能，但并不希望给这个 功能命名，让导入者 自己来命名

### 导入

```
import
```


