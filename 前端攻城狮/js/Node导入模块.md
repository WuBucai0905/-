# Node

## 1.导入模块

 `node` 环境中，有两个内置的全局变量无需引入即可直接使用

- `module` 
- `require`

```
const fs = require('fs')
const add = (x, y) => x + y
module.exports = add
```



### 1.1module wrapper

当我们使用 `node` 中写一个模块时，实际上该模块被一个函数包裹，如下所示:

```
(function(exports, require, module, __filename, __dirname) {
  // 所有的模块代码都被包裹在这个函数中
  const fs = require('fs')
  const add = (x, y) => x + y
  module.exports = add
});
```

在一个模块中自动会注入以下变量：

- `exports`
- `require`
- `module`
- `__filename`
- `__dirname`

### 1.2module

```
const fs = require('fs')
const add = (x, y) => x + y
module.exports = add
console.log(module)
```

![img](https://mmbiz.qpic.cn/sz_mmbiz_png/po6IxVbAMcQzR8LsXOEG8mLIymZE0vojUbLo4VswuMlnW2PtokxyWCJxdWNdUfYCnpEXytVChR5caWzklK6w0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- `module.id`: 
  -  `.` 代表是入口模块
  - 否则是模块所在的文件名
- `module.exports`: 模块的导出

![img](https://mmbiz.qpic.cn/sz_mmbiz_png/po6IxVbAMcQzR8LsXOEG8mLIymZE0vojzqK8xVTXpPBU6dUx2VhBzVOPiaiaqVhrBsJTsyYqoShvr7KPeogFFjbA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 1.3module.exports 与 exports

> `module.exports` 与 `exports` 有什么关系？

从以下源码中可以看到 `module wrapper` 的调用方 `module._compile` 是如何注入内置变量的，因此根据源码很容易理解一个模块中的变量：

- `exports`: 实际上是 `module.exports` 的引用
- `require`: 大多情况下是 `Module.prototype.require`
- `module`
- `__filename`
- `__dirname`: `path.dirname(__filename)`

```
Module.prototype._compile = function(content, filename) {
  // ...
  const dirname = path.dirname(filename);
  const require = makeRequireFunction(this, redirects);
  let result;

  // 从中可以看出：exports = module.exports
  const exports = this.exports;
  const thisValue = exports;
  const module = this;
  if (requireDepth === 0) statCache = new Map();
  if (inspectorWrapper) {
    result = inspectorWrapper(compiledWrapper, thisValue, exports,
                              require, module, filename, dirname);
  } else {
    result = compiledWrapper.call(thisValue, exports, require, module,
                                  filename, dirname);
  }
  // ...
}
```

### 1.4require

通过 `node` 的 REPL 控制台，或者在 `VSCode` 中输出 `require` 进行调试，可以发现 `require` 是一个极其复杂的对象

![img](https://mmbiz.qpic.cn/sz_mmbiz_png/po6IxVbAMcQzR8LsXOEG8mLIymZE0vojsonFqyBrEz0TbDdfDl6mBAo9Xciba3EdATD4vqThpyicQFY7gjGtWKSg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从以上 `module wrapper` 的源码中也可以看出 `require` 由 `makeRequireFunction` 函数生成，如下

```
function makeRequireFunction(mod, redirects) {
  const Module = mod.constructor;

  let require;
  if (redirects) {
    // ...
  } else {
    // require 实际上是 Module.prototype.require
    require = function require(path) {
      return mod.require(path);
    };
  }

  function resolve(request, options) { // ... }

  require.resolve = resolve;

  function paths(request) {
    validateString(request, 'request');
    return Module._resolveLookupPaths(request, mod);
  }

  resolve.paths = paths;

  require.main = process.mainModule;

  // Enable support to add extra extension types.
  require.extensions = Module._extensions;

  require.cache = Module._cache;

  return require;
}
```

### 1.5require(id)

`require` 函数被用作引入一个模块，也是平常最常见最常用到的函数

```
Module.prototype.require = function(id) {
  validateString(id, 'id');
  if (id === '') {
    throw new ERR_INVALID_ARG_VALUE('id', id,
                                    'must be a non-empty string');
  }
  requireDepth++;
  try {
    return Module._load(id, this, /* isMain */ false);
  } finally {
    requireDepth--;
  }
}
```

而 `require` 引入一个模块时，实际上通过 `Module._load` 载入，大致的总结如下：

1. 如果 `Module._cache` 命中模块缓存，则直接取出 `module.exports`，加载结束
2. 如果是 `NativeModule`，则 `loadNativeModule` 加载模块，如 `fs`、`http`、`path` 等模块，加载结束
3. 否则，使用 `Module.load` 加载模块

### 1.6require.cache

**「当代码执行 require(lib) 时，会执行 lib 模块中的内容，并作为一份缓存，下次引用时不再执行模块中内容」**。

这里的缓存指的就是 `require.cache`，也就是上一段指的 `Module._cache`

```
require.cache = Module._cache;
```

这里有个小测试:

> 有两个文件: `index.js` 与 `utils.js`。`utils.js` 中有一个打印操作，当 `index.js` 引用 `utils.js` 多次时，`utils.js` 中的打印操作会执行几次。代码示例如下

**「index.js」**

```
// index.js
// 此处引用两次
require('./utils')
require('./utils')
```

**「utils.js」**

```
// utils.jsconsole.log('被执行了一次')
```

**「答案是只执行了一次」**，因此 `require.cache`，在 `index.js` 末尾打印 `require`，此时会发现一个模块缓存

```
// index.js
require('./utils')
require('./utils')
console.log(require)
```

![img](https://mmbiz.qpic.cn/sz_mmbiz_png/po6IxVbAMcQzR8LsXOEG8mLIymZE0vojAZiaER94XaPNruyLFna0UiaSX1G087Qe5ayEq3Q20mZj5cetThtqdy0Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 1.7总结

1. 模块中执行时会被 `module wrapper` 包裹，并注入全局变量 `require` 及 `module` 等
2. `module.exports` 与 `exports` 的关系实际上是 `exports = module.exports`
3. `require` 实际上是 `module.require`
4. `require.cache` 会保证模块不会被执行多次
5. 不要使用 `delete require.cache` 这种黑魔法

