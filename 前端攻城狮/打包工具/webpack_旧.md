### nrm

提供常用的npm镜像地址

1. `npm i nrm g` 全局安装 nrm 包
2. `nrm ls` 可查看常用的维护镜像的地址
3. `nrm use xxx` 切换镜像源

`npm i cnpm -g`后才能使用 cnpm 下包

**装包操作**

```
npm install
```



一般在 webpack 中使用 Vue.js，都会有一个入口文件 **main.js**，里面通常导入了 Vue、VueRouter、iView 等库，通常也会导入一个入口组件 `app.vue` 作为根组件。





## Webpack

网页中引用的常见 静态资源

- js [.js / .jsx / .coffee / .ts[typescript]]
- css[.css / .less / .sass / .scss]
- images
- fonts
- 模板文件[.ejs / .jade .vue【webpack定义组件的方式】]

网页引入静态资源多会：

- 网页加载速度慢，因为会发起很多的二次请求
- 要处理 错综复杂的 依赖关系

处理：

- 使用Qulp，基于 task 任务
- 使用 webpack， 基于整个项目进行构建的

- 合并、压缩、精灵图、图片的base64编码、
- 使用 requireJS 、 使用webpack【解决包之间的依赖关系】

webpack是前端的一个项目构建工具， 基于nodejs的前端工具

**webpack4新特性**

- mode属性
  - 需要设置mode属性 可以是development 或 production

针对开发者模式提供的特性：

- 浏览器调试
- 注释、开发阶段的详细错误日志和提示
- 快速和优化的增量构建机制

针对生产者模式提供的特性：

- 开启所有的优化代码
- 更小的bundle大小
- 去除掉只在开发阶段运行的代码
- scope hoisting 和 tree-shaking

webpack4 确保使用nodejs的版本 >= 8.9.4

### 安装

**全局安装**

```
npm install webpack-cli -g
```

**安装到项目依赖中**

```
在项目根目录运行 npm i webpack --save-dev
```

### 基本使用

```
webpack 要打包的js文件 -o 自定义打包后的js文件名称
// webpack --mode development 要打包的js文件 -o 自定义打包后的js文件名称
```

### 加载器

预处理源文件，生成浏览器可以执行的普通js文件

#### url-loader

当文件大小小于某个指定size的时候，此时会转成DataURL形式[base64]

[精灵图替换方式：小图片打包到 js/css 里面去，不是以物理文件存在，而是以base64形式存在]

**安装**

```
// S开发 D生产 G全局
npm init -y
npm install file-loader -S
npm install url-loader -S[-S 安装完后同时记录到配置文件中去]
```

**配置文件**

```
// index.js
import img from './image.png';
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192,
            },
          },
        ],
      },
    ],
  },
};
```

#### sass-loader

**安装**

```
npm install sass-loader sass webpack --save-dev
```

**配置文件**

```
// app.js
import './style.scss';

// style.scss
$body-color: red;
body {
  color: $body-color;
}

// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.s[ac]ss$/i,
        use: [
          // Creates `style` nodes from JS strings
          'style-loader',
          // Translates CSS into CommonJS
          'css-loader',
          // Compiles Sass to CSS
          'sass-loader',
        ],
      },
    ],
  },
};
// scss对比于css可以认为是一个可编程的css
```

### plugins插件

#### MiniCssExtractPlugin

将css文件和 js文件 做到更好的分离

**安装**

```
npm install --save-dev mini-css-extract-plugin
```

**配置**

```
// style.css
body {
  background: green;
}

// component.js
import './style.css';

// webpack.config.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    })],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: (resourcePath, context) => {
                return path.relative(path.dirname(resourcePath), context) + '/';
              },
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

#### DefinePlugin

做定义的插件

```
new webpack.DefinePlugin({
  // Definitions...
});
```

#### HtmlWebpackPlugin

生成HTML文件，js文件运行需要被某个HTML文件包含

```
npm install --save-dev html-webpack-plugin
```

**配置**

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path');

module.exports = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index_bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
};
```

- 自定义title

- 自定义html模板
- ...

### DevServer

**安装**

```
npm install webpack-dev-server -D
```

**配置**

```
module.exports = {
  //...
  devServer: {
    contentBase: path.join(__dirname, 'dist'), //在哪个目录启动服务 output是哪个路径就需要放哪个路径
    compress: true, // 是否需要压缩
    port: 3000 // 端口号
  }
};
```

在package.json里面添加

```
"scripts":{"start": "webpack-dev-server"}
```

此时会去 node_moudles 里面去寻找 "webpack-dev-server"

然后使用 `npm run start `：来启动

webpack4 中并不需要使用到 热替换功能









在 `index.html` 中不推荐直接在script标签里面直接引用 任何包和css文件。只需要导入一个包 `main.js`就可以了

- `import ... from ...` 是ES6中导入模块的方式

```
// main.js
import $ from 'jquery'
const $ = require('jquery')
```



当ES6不能再浏览器上不能识别 通过webpack前端构建工具做处理:

```
webpack .\src\main.js .\dist\bundle.js
```

webpack能做什么？

- webpack能够处理 JS 文件的相互依赖的关系
- 能够处理JS的兼容问题，可使浏览器兼容

可以通过配置 `webpack.config.js` 文件来简化命令行操作

```
// 这个配置文件 通过node 中的模块操作，向外暴露了一个配置对象
const path = require('path') //路径操作使用 path 模块
module.exports = {
	entry: './input.js'  // {这个形式实现多个文件的打包}, webpack打包哪个文件
	output: {
		path: path.resolve{__dirname, 'dist'}, // 打包好的文件输出到哪个目录中去
		filename: 'output.bundle.js'
	}
	mode: "development"/production
}
```

```
webpack
1、webpack未发现 通过命令的形式，指定出入口
2、此时会去 项目的根目录中 查找 webpack.config.js 的配置文件
3、配置文件后 webpack 会去解析这个配置文件。解析执行完后得到了配置文件中导出的配置对象
4、webpack 拿到配置对象后就拿到了配置对象中的出入口，然后 打包构建
```



**webpack-dev-server**实现 自动打包编译的功能

1. `npm i webpack-dev-server -D` 安装到项目本地开发环境
2. 项目中本地安装的 `webpack-dev-server` ,无法当作本地脚本命令 来运行
   -  `webpack-dev-server` 工具想要正常运行，本地也必须安装  `webpack-dev-server` 
3. 配置 `package.json` 中的 `"scripts":{"dev": "webpack-dev-server"}`
   - `"scripts":{"dev": "webpack-dev-server --open --port 3000 --contentBase src"}` 
     - --open 自动打开
     - --port 端口号
     - --contentBase 显示主页
4. 此时在执行 `npm run dev` 就可以了
   - `webpack-dev-server` 打包生成的 bundle.js文件【script标签中需要引入该文件】并没有存放在 实际的物理磁盘上，而是托管到了电脑的内存中
   - 可以认为 `webpack-dev-server` 打包好的文件 以一种虚拟的形式托管到了项目的根目录中

webpack配置命令的方式

一：

`"scripts":{"start": "webpack-dev-server --open --port 3000 --contentBase src"}`

二：

以配置文件的形式 // 配置 参数的第二种方式

```
module.exports = {
  devServer: {
  	open: true, //自动打开浏览器
    contentBase: path.join(__dirname, 'dist'), //在哪个目录启动服务 output是哪个路径就需要放哪个路径
    compress: true, // 是否需要压缩
    port: 3000 // 设置运行端口号
  }
};
```

webpack3 中启用热更新

1. module.exports = {devServer: {hot: true}};
2. `const webpack = require('webpack')`
3. module.exports = {plugins=[new webpack.hot...()]};



页面内存化

HtmlWebpackPlugin

打开的使物理磁盘里面的页面，但 bundle.js 是存在于 内存中的。

此时我们再将 页面也放到 内存中去

1. `npm i htmlwebpackplugin -D`

2. 导入 插件 `const HtmlWebpackPlugin=require('htmlwebpackplugin')`

3. 创建一个 插件

   ```
   new HtmlWebpackPlugin({
   	// 根据模板页面 去生成内存中的页面
   	template:path.join(__dirname, xxx)
   	// 指定生成页面的名称
   	filename:'xxx.html'
   })
   ```

注意：使用  HtmlWebpackPlugin插件后， 不需要手动处理 bundle.js 的引用路径了



css样式表

```
import './css/index.css'
```

报错

webpack 默认只能打包处理js类型文件，

处理 非js类型的文件需要手动安装合适的第三方插件

需要打包处理 css 文件

1. 安装 `npm i style-loader css-loader -D`

2. webpack.config.js 配置文件中， 新增一个配置节点 module【对象】，module对象身上有个 rules 属性【数组】，存放了所有第三方文件 的匹配和处理规则

   ```
   module.exports = {
     module: {
     	rules: [
     		// 配置处理.css文件的第三方loader规则
     		// 调用规则是 从右到左的顺序来调用
     		{test:/\.css$/, use:['style-loader', 'css-loader'] }
     	]
     }
   };
   ```

   - webpack 处理第三方文件类型的过程
     1.  处理文件不是js文件，去配置文件中 查找有没有对应的第三方 loader 规则
     2. 能找到对应规则，就会调用相应的 loader 处理这种文件类型
     3. 在调用 loader 的时候，是从后往前调用的
     4. 最后一个 loader 调用完毕后，会把处理的结果 直接给 webpack进行打包合并，最终输出到 bundle.js 中去

需要打包处理 less 文件

1. `npm i less -D`
2. `npm i less-loader -D`
3. ...

需要打包处理 scss 文件

1. `npm i node-sass -D`  一般情况下需要使用cnpm

1. `npm i sass-loader -D`



webpack 无法处理 css 文件中的url地址，不管是图片还是字体库

需要处理 url地址

1. `npm i url-loader file-loader -D` file-loader是内部依赖，不需配置
2. ...

限制图片大小转base64

limit给定的值是图片大小，单位byte，图片小于limit值会转为base64

一：

```
use: [{loader: 'url-loader?limit=xxxx&name=[hash:8]-[name].[ext]'}]
// name=[name].[ext] 显示原样子的名称
// [hash:8] 为了防止两 文件名相同出现 页面渲染相同的问题
```

二：

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/i,
        use: [{loader: 'url-loader',
            options: {
              limit: 8192,
            },
          },
        ],
      },
    ],
  },
};
```



使用 bootstrap

使用bootstrap3 需要对字体文件进行处理，可以用 url-loader 来处理，bootstrap4下则移除了字体

1. `npm i bootstrap -S`
2. main.js 中引入`import 'bootstrap/dist/css/bootstrap.css'`



webpack中Babel配置

class Xx{}

static关键字定义 静态属性。静态属性就是可以直接通过类名直接访问的属性

实例属性是只能通过类的实例来访问的属性

静态属性 和 实例属性 挂载的对象不一样

webpack中只能默认处理一部分ES6的新语法，一些更高级的语法 需要借助 第三方 loader 转为 低级语法之后，把结果 交给webpack去打包到 bundle.js中

通过Babel 可以帮我们将高级语法转为低级语法

#### webpack3的安装

【webpack3】babel版本未知

1. 安装 Babel 相关功能【两套包】

   ```
   npm i babel-core babel-loader babel-plugin-transform-runtime -D
   npm i babel-preset-env babel-preset-stage-0 -D
   ```

2. webpack配置文件的modules节点下的rules数组中添加新的匹配规则

   ```
   [
       {
         test: /\.m?js$/,
         exclude: /node_modules/, // 排除
         use: {
           loader: 'babel-loader'}]
   在配置Babel的loader规则时，需要将 node_modules中所有的第三方js文件排除掉
   1、不排除会打包编译文件夹下面的所有js文件，会非常消耗CPU，打包速度很慢；
   2、babel将所有 node_modules下面的js转换完毕，项目也无法正常运行
   的方式
   ```
   
3. 在项目根目录中，新建一个  .babelrc 的Babel配置文件，配置文件属于json格式，必须符合json的规范

   ```
   // .babelrc配置
   {
       // 语法的意思
       "presets": ["env", "stage-0"],
       "piugins": ["transform-runtime"]
   }
   ```

4. 还需要安装

   ```
   npm i babel-core babel-loader babel-piugin-transform-runtime -D
   npm i babel-preset-env babel-preset-stage-0 -D
      // babel-preset-env 是较新的语法插件，之前是babel-preset-es2015
   ```
#### webpack4 de 安装

**安装**

```
npm install -D babel-loader @babel/core @babel/preset-env webpack
// @babel/core Babel核心组件库
// @babel/preset-env 一些规则，负责把ES6转换为ES5
```

**配置文件**

```
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/, // 排除
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env'],
          plugins: ['@babel/plugin-proposal-object-rest-spread'] //向react/vue 这些语法并不是标准js语法，需要进行转换，此时需要用到 plugins 这样的形式【坑: 在test里面将需要处理的扩展名记录下来】
        }
      }
    }
  ]
}
```



# 1

```

#### 



## webpack构建项目用vue开发

`npm init -y` 初始化项目

包的查找规则：

1. 找项目根目录中 有没有 node_modules 的文件夹
2. 在 node_modules 中 根据包名找对应的 文件夹
3. 在 对应文件夹中 找 package.json 的配置文件
4. 在 配置文件中 查找一个 main 属性【main属性指定了 包在被加载的时候 的入口文件】

webpack中使用vue

1. 安装vue的包： `npm i vue -S`

2. webpack中 推荐使用 .vue 这个组件模板文件定义组件，需安装解析这种文件的loader， `npm i vue-loader vue-template-compile -D`

3. `main.js`中导入 vue模块

4. 定义 .vue 结尾的组件，组件有三部分组成：template、script、style

```
   /src/login.vue
   <template>
   <script>
   <style>
   ```

5. 使用 import 导入这个组件

   ```
   import Vue from 'vue'
   ```

   这种方式 导入 Vue构造函数，功能不完整，只提供了 runtime-only 的方式，并没有提供像 网页中那样的使用方式

   可以通过该方法来完善

   ```
   import Vue from "../node_modules/vue/dist/vue.js"
   ```

   还可以通过

   ```
   // webpack.config.js
   // 修改Vue被导入时候的路径
   modules.exports = {
   	resolve:{
   		alias:{'vue$': 'vue/dist/vue.js'}
   	}
   }
   ```

6. 创建vm的实例 render

7. 在页面中创建 元素 作为vm实例要控制的区域

runtime-only 中去渲染一个组件

步骤：

1. 导入  .vue 组件
   - webpack 无法打包 .vue 文件，需要安装 相关的loader:
     - `npm i vue-loader vue-template-compiler -D`
     - 新增loader配置项
     - 还需要使用plugin 配置`new VueLoaderPlugin()`
2. 使用 render 来渲染组件
   - webpack 中 想要通过vue 把一个组件放在页面中去显示，vm实例中的render函数可以实现
     - 可以简写为： `render: c => c(login)`

.vue 文件中定义自己的 data 和 method

```
// xxx.vue
<script>
	export default {
		// 组件中的data必须是function
		data(){
			return{}
		},
		methods:{}
	}
</script>
```

node中 

- 使用 `var 名称 = require('模块标识符') `
- 向外暴露成员的方式 `module.exports = {}` 或 `exports`

ES6 通过规范的形式：

- 导入模块 `import 模块名称 from '模块标识符' / import '表示路径'`
- 使用 `export default` 和 `export` 向外暴露成员

 `export default` 向外暴露的成员可以 使用任意的变量来接收

在一个模块中， `export default` 只允许向外暴露一次

在一个模块中，可以同时使用  `export default`  和  `export` 

 `export` 向外暴露的成员 只能使用 {} 形式来接收，这种形式叫做 【按需导出】

  `export` 可以向外暴露多个成员， 如果某些成员 在 import时候不需要，则可以不再 { }中定义

  `export` 导出的成员 必须严格按照 导出时候的名称来使用 { } 接收，一定要用别称接收 可以使用 `名称 as 别名`。



webpack使用vue-router

安装vue-router`npm i vue-router`

步骤：

1. 导入vue-router包 `import VueRouter from 'vue-router'`

2. 手动安装 VueRouter `Vue.use(VueRouter)`

3. 创建一个路由对象

   ```
   var router = new VueRouter({
   	routes:[]
   })
   ```

   fd

4. 将路由对象挂载到 vm 身上

   - render 会把 el 指定的容器中所有的内容都清空覆盖，所以不要把 router-view 和 router-link 直接写到el所控制的容器中 

app这个组件 是通过vm实例的render函数渲染出来的，render函数如果要渲染组件，渲染出来的组件只能放到 el:'#app' 所指定的元素中

通过 路由监听到的组件 只能展示到 属于路由的 router-view 中去



webpack实现children子路由

```
new VueRouter({routes:[{
	path: xxx,
	component: xxx,
	children: [{path: xxx,component: xxx}, ...]
}, {}, ...]})
```



组件中style标签 lang属性 和 scoped属性

普通的style标签 只支持 普通的样式，如果想要启用 scss 或 less， 需要为 style 元素设置 lang 属性

只要 style标签 是在 .vue 组件中定义的，都推荐为 style 开启 scoped 属性

 scoped 属性 设置scoped后，通过css的属性选择器来实现的。



模块化可以抽离 路由模块， 独立门户

然后还要将 router路由对象 暴露出去



























`npm install vue-cli -g`

创建工程 `vue init webpack hello_vue`

`npm start`

升级webpack，在package.json 里面查看webpack 是否为需要的版本

需要升级的话 使用之前的命令(npm / cnpm )对应安装

`npm/cnpm install webpack@^4 -S`

```
-S: webpack存在于 dependencies
-D:webpack存在于devDependencies
```

webpack升级成功后执行 `npm start`启动失败根据报错来查找问题

```
经验：
先去升级 `webpack-dev-server`
```





## 打包文件

`npm run build`   ----  dist

js文件

- app   当前应用程序开发的业务代码都在这个文件里面
- vendor   提供商第三方插件
- manifest   为打包的代码做底层支撑【运行池】













json不能写注释

通过路径的形式去引入 node_modules 中相关的文件，可以直接省略 路径前面的 node_moudles 这一层目录 。 直接写 包的名称，然后跟上 具体的文件路径