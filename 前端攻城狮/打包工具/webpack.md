# webpack

模块化打包工具

模块化是一种将系统分离成 独立功能 部分的方法，严格定义模块接口， 模块间具有透明性

模块

CommonJS

RequireJS(AMD) / SeaJS(CMD)

ES6 Module

模块化作用

高内聚，低耦合

### 安装

```
npm install webpack --save-dev
npm install webpack-cli --save-dev
```

项目为空时应该先建立

```
webpack webpack.config.js
```

这样可以在当前项目去执行 webpack 这样的命令。

`webpack.config.js`

- entry 配置入口资源
- output 配置编译后资源
- module 资源处理
- resolve 配置资源别名/扩展名等
- plugins 插件，比loader更强大

```
let path = require('path');
module.exports = {
	mode:'', //production 和 development
	entry: '', //入口(相对路径)
	output: {
		filename: '', //打包后的文件名
		// path.resolve(__dirname, 'dist')将相对路径解析成绝对路径,在当前目录下产生一个`dist`目录
		path: , //路径(绝对路径,使用到node的核心模块'path')
	}
}
```



默认 src下的 index.js 为主入口文件



编译`index.js`

建立`.babelrc`

```
{
	'presets': ['env',{
		'modules': false
	}]
}
```

`npm install babel-preset-env --save-dev`



配置脚本:

```
// package.json
'scripts':{
	'dev': 	'webpack --mode development[ --module-bind js=babel-loader]',
	'prod': 'webpack --mode production[ --module-bind js=babel-loader]'
	'build': 'webpack --config webpack.config.my.js'
}
```

此时运行命令为: `npm run build`

`npm run build`还想要传参 需要这样写 `npm run build -- xxxx`







webpack的运行

npx webpack --- 默认执行的时 node_modules里的bin里的webpack.cmd文件



webpack

打包(支持js的模块化)





使用 development的mode模式打包的文件是一个匿名函数

匿名函数的参数是对象，由key: value

- key 指的是 当前模块的路径
- value 指的是 函数

将对象传到了 modules里面

webpack启动函数

- 先定义一个缓存(模块加载完了不需要再次加载模块，可以直接在缓存中拿取)
- 配置 实现了 require方法(require方法是不能在浏览器下面运行，`__webpack_require__`)
- return中 由实现的require方法 去调用入口模块
  - 首先 检查 入口模块是否在 缓存中
  - 没在的话会去安装这样一个模块
    - {key: 入口模块路径; value: {xxxx}}
  - 然后去对象中 找到 去执行

把 解析的所有模块 变成一个对象，然后依次实现 递归的依赖关系，最后通过 入口来执行所有的文件



手动指定配置文件

npx webpack --config webpack.config.my.js





webpack通过 服务启动

```
npm add webpack-dev-server
```

npx webpack-dev-server

不会真实的去打包文件，只会生成一个内存中的打包

配置webpack.config.js

```
module.exports = {
	devServer: { //开发服务器的配置
		port: 3000,
		progress: true, //打包显示进度条
		contentBase: './build'
	}
}
```

```
'scripts':{
	'dev': 'webpack-dev-server'
}
```



### 插件

#### HtmlWebpackPlugin

> 使用模板自动地建立一个 html文件打包到内存中

```
npm add html-webpack-plugin
```

配置webpack.config.js

``` 
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
	plugins:[ //数组， 放着所有的webpack插件
		new HtmlWebpackPlugin({
			template: //以哪个为模板(相对路径)
			filename: //打包后的名字
			minify:{ //html模板也被压缩
				removeAttributeQuotes: true //删除属性双引号
				collapseWhitespace: true //折叠到一行
			}
			hash: true //生成 hash戳
		})
	]
}
```



#### 样式 

css-loader 负责解析 @import这种语法的

style-loader 负责把css 插入到 head的标签中

loader的特点：希望单一

loader的顺序 默认是从右向左执行， 从下到上执行

loader写成对象形式 还可以传递参数options： `insert: 'top'`

```
module.exports = {
	module:{ //模块
		rules:[ //规则
			{test: /\.css$/, use:['style-loader','css-loader']},
		]
	}
}
```



less-loader 负责把less文件 转换成 css文件

node-sass

sass-loader 负责把sass文件 转换成 css文件

stylus

stylus-loader 负责把stylus文件 转换成 css文件



此时只能插入到模板的 style标签内

#### 抽离样式

mini-css-extract-plugin

```
new MiniCssExtractPlugin({
	filename: ,//抽离出来样式的名字
	
})

MiniCssExtractPlugin.loader
```

#### 添加前缀

autoprefixer

postcss-loader

解析css之前就需要加前缀

创建 postcss.config.js

```
module.exports = {
	plugins: [require('autoprefixer')]
}
```

#### 压缩css

optimize-css-assets-webpack-plugin

使用该插件 就必须使用 uglifyjs-webpack-plugin

```
module.exports = {
	optimization: { //优化项
		minimizer: [
			new UglifyjsWebpackPlugin({
				cache: true, //是否使用缓存
				parallel: true, //是否并发打包
				sourceMap: true
			})
			new OptimizeCssAssetsWebpackPlugin()
		]
	}
}
```

### 

babel-loader

@babel/core	babel的核心模块，可以调transform方法转换源代码

@babel/preset-env	将高级的语法转换成低级的语法

```
use:{
	loader: 'babel-loader',
	options:{ //用babel-loader, 需要把es6 转换成 es5
		presets:[
			'@babel/presets-env'
		]
	}
}
```



全局变量 引入问题

expose-loader	暴露全局的loader	内联loader

```
import $ from 'expose-loader?$!jquery'
```

```
webpack.config.js
module:{
	rules:[
		{
			test:require.resolve('jquery'),
			use:'expose-loader?$'
		}
	]
}
```

 

在每个模块中 注入 $对象

```
let webpack = require('webpack')
new webpack.ProvidePlugin({ //在每个模块中注入 $
	$: 'jquery'
})
```





webpack打包图片

- 在 js 中创建图片来引入
- 在css引入 background
- img src

file-loader

> 默认会在内部生成一张图片到 build目录下，并把生成的图片的名字返回回来

```
import logo from './logo.png'
let image = new Image();
image.src = logo;

```

