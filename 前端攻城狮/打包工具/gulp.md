## gulp

> 流式构建工具

nvm 管理 nodejs

nrm 管理 镜像源

(3.9.1版本的文档)

### 安装

全局安装gulp

```
npm install gulp -g
```

项目里安装gulp

```
npm install gulp --save-dev
```

###  使用

> 语法符合 commonJS规范
>
> 上一个的输出是下一个的输入

创建一个 gulpfile.js 的文件，专门 gulp去编写任务

**编写第一个任务**

```
var gulp = require('gulp')
gulp.task('任务的名字', '回调函数(任务执行的功能)')
```

**运行任务**

```
gulp 任务名
```

### gulp的基本自带函数

gulp.src() 找到源文件路径

gulp.dest() 找到目的文件路径， 如果设置的这个目的文件路径不存在，则会自动创建

pipe() 理解为程序运行的管道

**细节**

`*.{jpg, png}`

不包括某文件 `!xxxx`

**异步并行**

```
gulp.task("build", ["xxx", "xxxx", "xxxxxx"], function(){
	console.log("task is all ready")
})
```

### 监听

```
gulp.task("watch", function(){
	gulp.watch('文件监听的路径', '去执行的任务')
})
```

### 插件

#### 使用步骤

1. 下载插件 到 本地
2. 通过 require() 引入插件
3. 查询插件的用法

### gulp-scss

> 编译css文件

### gulp-minify-css

> 压缩CSS

### gulp-rename

> 重命名插件
>
>  为了保存 开发版本 和 上线版本的代码

### gulp-concat

> 处理js文件的插件
>
> 合并插件

### gulp-uglify

> 压缩 .js 文件

jquery编写的代码 不要合并和压缩

## 服务器

### gulp-connect	

> 在当前目录下启动一个 服务器

```
const connect = require('gulp-connect')
gulp.task("server", function(){
	connect.server({
		root: 'dist', //设置根目录
		port: 8888,
		livereload: true //启动实时刷新功能
	})
})
```

