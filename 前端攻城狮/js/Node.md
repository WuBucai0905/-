# Node

> JavaScript的 **运行环境** ，使用了一个事件驱动、非阻塞式I/O的模型，使其轻量又高效 
>
> 独立于 浏览器的环境。可以在 非浏览器环境下运行 JavaScript代码且速度快。
>
> 事件驱动：动作会触发事件并执行相应的方法。用事件的触发来调用相应的方法。
>
> 包管理器`npm` 是全球最大的开源库生态系统

`Nodejs`是在 服务端运行的，它不依赖于 浏览器的环境。

# `npm`使用

> 依赖包：
>
> 一个包完成一个特定的功能，一个包里面 含有 许多的模块

**下载**

`npm install jquery`

下载会下载到 `node_modules`的文件夹下，是所有第三方包统一存放的目录

`package.json`定义了所有包的信息

建立`package.json`文件

- `npm init` 初始化`package.json`
- `dependencies` 生产环境依赖的包
- `devDependencies` 开发环境依赖的包

```
~ 前两位需要一致，后一位版本可以不同
^ 大于该版本就可
```

**下载 `package.json` 文件下的相关依赖包:**

`npm install` 会安装全部依赖

`npm install --production` 只会安装生产环境的依赖

`npm install xxx`

- 不加任何参数 --- 不会去更新 `package.json`
- `--save` --- `dependencies`环境下依赖
- `--save-dev` --- `devDependencies`环境下依赖

全局依赖包 `-g`

在`Nodejs`环境的`node_modules`目录下，一般用于命令行工具

本地依赖包

在当前目录下的`node_modules`里面，一般用于实际的开发工作

**`package.json`字段分析**

- name：包的名称，必须是唯一的，由小写英文字母、数字和下划线组成，不能包含空格
- description：包的简要说明
- version：符合语义化版本识别规范的版本字符串
- keywords：关键字数组，通常用于搜索
- maintainers：维护者数组，每个元素要包含name、email（可选）、web（可选）字段
- contributors：贡献者数组，格式与maintainers相同。包的作者应该是贡献者数组的第一- 个元素
- bugs：提交bug的地址，可以是网站或者电子邮件地址
- licenses：许可证数组，每个元素要包含type（许可证名称）和url（链接到许可证文本的- 地址）字段
- repositories：仓库托管地址数组，每个元素要包含type（仓库类型，如git）、url（仓- 库的地址）和path（相对于仓库的路径，可选）字段
- dependencies：生产环境包的依赖，一个关联数组，由包的名称和版本号组成
- devDependencies：开发环境包的依赖，一个关联数组，由包的名称和版本号组成

# 开启服务

可以使用 `Ngnix`

还可以使用基于`Nodejs` 的轻量级服务器。如`http-server`

**使用**

```
http-server [path] [options]
```

# `Nodejs`相关操作

## Buffer基本操作

> Buffer对象是node处理二进制数据的一个接口， 是node原生提供的全局对象。可以直接使用，不需要require('buffer')
>
> Buffer布置上就是 字节数组
>
> 构造方法 (类) | 静态方法 | 实例方法

- 实例化
  - Buffer.from(array)
  - Buffer.from(string)
  - Buffer.alloc(size)
- 功能方法
  - Buffer.isEncoding() 判断是否支持该编码
  - Buffer.isBuffer() 判断是否为Buffer
  - Buffer.byteLength() 返回指定编码的字节长度，默认utf8
  - Buffer.concat() 将一组Buffer对象合并为一个Buffer对象
- 实例方法
  - write() 向buffer对象中写入内容
  - slice() 截取新的buffer对象
  - toString() 把buf对象转成字符串
  - toJson() 把buf对象转成json形式的字符串
    - toJson方法 **不需要显示调用**。当 `JSON.stringify`方法调用的时候会自动调用 toJson 方法

## 核心模块API

### 路径操作

- 路径基本操作API

  ```
  const path = require('path');
  ```

  - path.basename(path[, ext]) 获取路径的最后一部分【文件的名称】
  - path.dirname(path) 获取路径【__dirname 当前路径】
  - path.extname(path) 获取扩展名
  - path.format() 路径的格式化处理 obj -> string
  - path.parse() 路径的格式化处理 string -> obj
  - path.isAbsolute() 
  - path.join([...paths]) 拼接路径
    - .. 表示上层路径
    - .  表示当前路径
  - path.normalize(path) 规范化路径
  - path.relative(from, to) 计算两个绝对路径的相对路径
  - path.resolve([...paths]) 从**当前路径**拼接路径
  - path.sep() 表示环境变量分隔符，windows是 `;`; linux 是 `:`
  - path.delimiter 表示路径分隔符，windows是 `\`; linux 是 `/`

### 文件操作

> node中的事件模型 是 单线程 + 事件队列
>
> nodejs中异步执行的任务：
>
> - 文件I/O
> - 网络I/O

```
const fs = require('fs')
```

- 文件信息获取

  - fs.stat(path, callback)

    ```
    // 回调函数
    // 第一个参数为错误对象
    (error, stats) => {
    	if(error) return;
    }
    // stats.isFile() 是否为文件
    // stats.isDirectory() 是否为目录
    ```

  - fs.statSync(path) 同步操作

- 读文件操作

  - fs.readFile(file[, options], callback)
    - callback返回的是Buffer实例对象
    - 如果有第二个参数并且指定编码，callback获取到的数据是字符串
  - fs.readFileSync(file[, options])

- 写文件操作

  - fs.writeFile(file, data[, options], callback)

- 文件的流式操作

  - fs.createReadStream(path[, options])

  - fs.createWriteStream(path[, options])

    ```
    基于事件的处理方式
    readStream.on('data', (chunk) => {
    	writeStream.write(chunk);
    });
    readStream.on('end', () => {
    	console.log("文件处理完成");
    });
    ```

  - readStream.pipe(writeStream); 

    - pipe的作用直接把 输入流和输出流 

- 目录操作

  - fs.mkdir(path[, mode], callback) 创建目录
  - fs.mkdirSync(path[, mode])
  - fs.readdir(path[, mode], callback) 读取目录
  - fs.readdirSync(path[, mode])
  - fs.rmdir(path, callback) 删除目录
  - fs.rmdirSync(path)

## 实现静态服务器

```
const http = require('http');
```

**创建服务器请求对象**

http.createServer([requestListener])

- 返回值是http.Server

**绑定请求事件**

- request
- listen
- ...

**监听端口**

```
const http = require('http');
let server = http.createServer();
server.on('request', (req, res)=> {
	res.end('hello')
});
server.listen(3000)
```

**处理请求路径的分发**

- req对象是Class: http.IncomingMessage的实例对象
- res对象是Class: http.ServerResponse的实例对象 
  - write方法向客户端相应内容，可以写多次
  - end方法用来完成响应，只能执行一次
