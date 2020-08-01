## `ES6`

### 块级作用域

> `ES5` 之前因为 if和for都没有块级作用域的概念，所以在很多时候，必须借助于**function的作用域**来解决应用 外面变量的问题
>
> `ES5` 中一般是通过 闭包来解决： 因为 函数是一个作用域
>
> `ES6`中 `let` 在 if 和for中 有**块级作用域**

#### `let`

> 用于变量声明的关键字
>
> 在一个大括号中， let关键字声明的变量才具有 块级作用域。

- 块级作用域

- 不存在变量提升，即必须先定义再使用

- 暂时性死区

  ```
  var temp = 123;
  if (true) {
  	temp = 'abc';
  	let temp;
  }
  ```

#### `const`

> 用于常量声明的关键字， 常量是值(内存地址) 不能变化的量
>
> 常量是指向的对象不能修改，但是可以修改对象的内部属性

开发中 优先使用 `const`

- 块级作用域
- 声明变量时 必须赋值
- 常量赋值后 值不能改变，但内存地址 能改变

| var          | let        | const      |
| ------------ | ---------- | ---------- |
| 函数级作用域 | 块级作用域 | 块级作用域 |
| 变量提升     | 无变量提升 | 无变量提升 |
| 值可更改     | 值可更改   | 值不可更改 |

### 解构赋值

**数组**

> 允许从数组中提取值，按照相应的位置对变量赋值。
>
> 注意：解构不成功变量的值为 undefined

```
let [a, b, c] = [1, 2, 3]
```

**对象**

```
let person = {name: 'zhangsan', age: 13};
// 方法一
let {name, age} = person
// 方法二: 取别名
let {name: myname, age:myage} = person
```

### 字面量增强写法

```
// 对象的字面量 const obj = {}
而不是 const obj = new Object()
```

**属性增强写法**

```
const xx = "xx"
// ES5写法
const obj{
	xx: xx
}
// ES6写法
const obj{
	xx
}
```

**函数增强写法**

```
// ES5写法
const obj{
	run:function(){}
}
// ES6写法
const obj{
	run(){}
}
```

### 箭头函数

> 一种定义函数的方式 
>
> 什么情况下使用 箭头函数？
>
> - 当我们准备 把一个函数 作为参数传给另一个函数的时候

**定义函数的方式**

- function

- 对象字面量中定义函数

  ```
  const obj = {
  	bbb: function(){},
  	ccc(){}
  }
  ```

- ES6 的 箭头函数

  ```
  const fn = (参数列表) => {}
  ```

- 函数体中只有一句代码且为 返回值时，可以省略大括号

  ```
  const mul = (num1, num2) => {
  	return num1 * num2
  }
  简写为：
  const mul = (num1, num2) => num1 * num2
  ```

- 形参只有一个时可以省略小括号

  ```
  const ccc = num => {} // ()可以省略
  ```

- 不绑定 this 关键字， this指向的是 函数定义位置的上下文 位置

  向外层作用域中查找this，直到有 this 的定义

### 剩余参数

> 允许将 不定数量的参数 表示为 一个数组

```
function sum(frist, ...args){
	console.log(frist); //10
	console.log(..args); //[20, 30]
}
sum(10, 20, 30)
```

### 扩展运算符

> 可以将 数组或者对象 转为用逗号分隔的参数序列

```
let ary = [1, 2, 3];
...ary //1,2,3
```

- 用于合并数组

  ```
  let ary1 = [1, 2, 3];
  let ary2 = [4, 5, 6];
  let ary1 = [...ary1, ...ary2];
  ```

- 将 类数组或可遍历对象 转换为真正数组

  ```
  let odivs = document.getElementsByTagname('div');
  odivs = [...odivs];
  ```



## 异步请求

**区别实例对象和函数对象**

定义一个函数，在 new 时 被称为 构造函数

- 实例对象： new 构造函数产生的对象，简称为对象
- 函数对象： 将函数作为对象使用时，简称为 函数对

**区分函数 和 函数对象**

### 二种回调函数

**同步回调**

例子： 数组遍历相关的回调函数 / Promise的excutor 函数

**异步回调**

> 异步回调函数 会放入 队列中 将来执行

例子： 定时器回调 / ajax回调 / Promise的成功|失败 问题

### 三个状态

异步操作之后 有三个状态

- pending 初始状态。正在进行网络请求，或者定时器还没有到时间
- fulfilled 操作成功
- rejected 操作失败

成功的数据 一般称为 value， 失败的数据 一般称为 reason

状态改变 执行回调函数

- `onResolved()`
- `onRejected()`

### Promise

> 就是将 异步操作 进行了相应的封装。是解决异步编程的新的解决方案。	--- 旧的方案：纯回调形式 【回调地狱】
>
> 语法上说：Promise是一个构造函数；功能上说：promise对象用来封装一个异步操作并获取其结果

#### 优点

- 指定回调函数的方式 更加灵活
  - 旧的必须在启动异步任务前指定
  - Promise: 启动异步任务 === 返回Promise对象 === 给Promise对象绑定回调函数
- 支持链式调用，可以解决 回调地狱问题
  - 回调地狱就是 回调函数的嵌套调用，不便于阅读，不便于异常处理
  - 解决方案：
    - Promise链式调用
    - `async/await`

#### 基本使用

> new Promise  的时候异步任务此时启动，但并未完成。执行器函数执行完，Promise 才结束。因而为了能实现 调用时执行，Promise一般是作为函数的返回值

*将网络请求的代码 和 数据处理的代码 分隔开来，更加优雅*

>new 一个 Promise对象， `func`是一个函数【执行器函数，执行异步任务】
>
>`func`函数的参数 resolve和reject 本身又是一个函数
>
>- resolve接收正确结果，导入 then 中，then返回的又是一个新的Promise 对象
>- reject接收错误信息， 导入 catch 中
>
>Promise 是一个保存了异步操作结果的 对象

```
const promise = new Promise(func)
new Promise(function(resolve, reject){
	// 第一次 网络请求的代码
	setTimeout(()=>{
		resolve(value)
	},1000)	
}).then(()=>{
	// 第一次拿到 结果的处理代码
	xxxx // 这里就是 回调函数的位置
	
	// 第二次 网络请求的代码
	return new Promise(...)
	...
})
```

#### 链式调用

可以简写为

```
return Promise.resolve(res)...
或者 更简写为
return res ...
```

链式调用中出现了异常可以通过

```
return Promise.reject('error') ...  .catch(...)
// 或者
throw 'error message' 来抛出异常
```

#### 方法

**Promise.all**

> 需要两个或多个请求都满足 才能继续 往下 执行下去

将 多个Promise实例[p1, p2] (可迭代对象) 包装成  一个新的Promise实例，

- 成功返回的是一个 结果数组
- 失败则返回 最先被 reject失败状态的值

```
Promise.all([
	new Promise((resolve, reject)=>{
		setTimeout(()=>{
			resolve(resulta)
		}, 1000)
	}),
	new Promise()...
]).then(results => {})
```

**Promise.race**

里面的 哪个结果 获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态

### Async/await

>其实是Generator函数的改进，背后的原理是Promise。其返回的就是一个 Promise 对象

*await 后面不管是 Promise对象还是普通函数，都会导致紧跟其后的代码 阻塞，其阻塞是为 内部的阻塞*

`async`关键字用来修饰函数后，函数内可以使用 `await `

`await` 会拿到 `resolve`结果，是 `then` 函数的语法糖

`await`调用的过程中只能拿到 `resolve` 的数据或者可以说是只能拿到传递给 `then`的数据，`reject`的数据需要靠 `try...catch`



