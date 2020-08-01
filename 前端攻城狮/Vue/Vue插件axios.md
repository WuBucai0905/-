# axios

## 1.特点

- 在浏览器中发送XMLHttpRequests请求
- 在nodejs中发送http请求
- 支持Promise API
- 拦截请求和响应
- 转换请求和响应数据

## 2.安装

`npm install axios --save`



## 3.基本使用

>默认 method 是 get 方式
>
>method 还有 request 、 delete 、 head 、 post 、 put 、 patch ...
>
>拿到数据后 通过 .then() 来使用数据

```
<script>
	axios({
		url: "xxx",
		method: "get"
	}).then(res =>{ console.log(res) });
</script>
```

**参数传递**

```
get	params:{}

post data:{} // payload 后台控制器接收到的是null, axios使用post携带参数请求默认使用application/json
解决方法一: data:{} 改为 params:{}
解决方式二: "id=1" 【修改前端代码】
解决方式三: 服务器端给 接收的参数 加上 @requestBody 【修改后端代码】
```

使用axios.get动词

```
<script>
	axios.get("xxx",[{params:{id:1, ...}}])
	.then(res =>{ console.log(res) })
	.catch(reason =>{console.log(reason)});
</script>
```

使用axios.post动词

```
<script>
	axios.post("xxx",["id=1&..."])
	.then(res =>{ console.log(res) })
	.catch(reason =>{console.log(reason)});
</script>
// post中 使用data传递数据，后台需要将 axios 的json数据自动转换为 java对象
```

## 4.并发请求

> axios.all()
>
> axios.spread() 可以将 并发请求到的数组 展开，处理响应数组结果

```
<script>
	axios.all([
		axios.get("xxx",[{params:{id:1, ...}}]),
		axios.get("xxxxxx")
	])
	.then(
	axios.spread(
		(res1, res2)=>{console.log(res1);console.log(res2)}
	))
	// .then(
	res =>{console.log(res[0]);console.log(res[1])}
	)
	.catch(reason =>{console.log(reason)});
</script>
```

## 5.全局配置

```
<script>
	// 配置全局属性
	axios.default.baseURL="xxxx";
	axios.default.timeout="5000"
	// 后面请求不需要 前面 加 "/"
</script>
```

## 6.axios的实例

> 不使用全局配置。 创建某种实例，基于该实例 进行 特定的请求
>
> 创建：`const instance = axios.create()` 创建实例
>
> 使用：`instance ({})` 使用实例

```
<script>
	// 创建实例
	let newVar = axios.create({
		baseURL="xxxx",
		timeout="5000
	});
	// 使用
	newVar({url:"xxx"})
	.then(res =>{ console.log(res) })
</script>
```

## 7.拦截器

主要有两个类拦截器 requests请求方向 和response响应方向 (成功和失败两种可能)

**作用**

用于我们在网络请求的时候在发起请求或者响应时 对操作做出相应的处理

**拦截成功后 记得放行**

**请求方向的拦截器**

`axios.interceptors.request`

> config 中的一些信息 不符合服务器的要求
>
> 如：每次发送网络请求时，都希望在界面中 显示一个请求的图标，网页加载的动画
>
> 又如：某些网络请求【如登录】 必须携带一些特殊的信息

```
<script>
	// 拦截器中是没有catch的
	axios.interceptors.request.use(config=>{
		console.log("进入拦截器");
		console.log(config);
		// 拦截后请求没有继续往下走，得通过 return 来放行
		return config;
	}, error=>{
		console.log("请求方向失败")
		console.log(error)
	})
</script>
```

**响应方向的拦截器**

`axios.interceptors.response`

> 可以在 request 方向 只放行 config.data  此时响应端只能拿到 data数据

```
<script>
	// 拦截器中是没有catch的
	axios.interceptors.response.use(config=>{
		console.log("进入响应拦截器");
		console.log(config);
		// 拦截后请求没有继续往下走，得通过 return 来放行
		return config;
	}, error=>{
		console.log("响应方向失败")
		console.log(error)
	})
</script>
```

## 8.模块封装

> 只要是第三方工具，不要在 多个.vue文件中 对第三方工具过分依赖。
>
> 单独 弄一个文件 对第三方工具来进行封装

**第一种方式**

```
// src --> network --> request --> request.js
import axios from 'axios'
export function request(config, success, fail){
	axios({
		url:config
	}).then(res=>{
		success(res);
	}).catch(err=>{
		fail(err)
	})
}
// 通过两个函数 将数据传递到 main.js 中

// 调用者
import { request } ...
request(xxx, res=>{console.log(res)}, err=>{console.log(err);})
```

**第二种方式**

config 对象中包括了 url，success(), fail()

```
export function request(config){
	axios.defaults.baseURL = XXXX;
	axios(config.url)
	.then(res=>{
		config.success(res);
	})
	.catch(err=>{
		config.fail(err);
	})
}

// 调用者
import { request } ...
request({
	url: xxxx,
	success: res=>{console.log(res);},
	fail:res=>{console.log(fail);}
})
```

**第三种方式**

使用到Promise

创建一个实例，告诉 其 基础路径和时间，

整体return 出去的是一个 Promise对象， 把 处理成功的值 和 失败的值 进行了resolve 和 reject， 把其值 返回给 函数的主体

调用者 `request({url: xxx})` 返回的就是一个 Promise，调用者 通过 then 和 catch 就可以拿到 成功和失败的值了

```
export function request(config){
let newVar = axios.create({
    baseURL: xxx,
    timeout: 3000
});
export function request(config){
	return new Promise(
		(resolve, reject)=>{
			newVar(config).then(
				res=>{
					resolve(res);
				}).catch(
					err=>{
						reject(err);
					})
		}
	)
}

// 调用者
import { request } from ...
request({
	url: xxx
}).then(res=>{
	console.log(res);
}).catch(err=>{
	console.lor(err);
})
```

**第四种方式**

> axios实例创建完成后 默认给我们 创建了一个Promise对象

```
export function request(config){
    let newVar = axios.create({
        baseURL: xxx,
        timeout: 3000
    });
    return newVar(config);
}
// Promise对象 不需要我们在对 then 和 catch 进行处理了

// 调用者
import { request } from ...
request({
	url: xxx
}).then(res=>{
	console.log(res);
}).catch(err=>{
	console.lor(err);
})
```

