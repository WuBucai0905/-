## 基础概念

**屏幕**

> 差异主要体现在 屏幕尺寸 和 屏幕分辨率 两个方面
>
> 屏幕尺寸 指的是 屏幕对角线的长度；
>
> 分辨率 指的是 屏幕水平和垂直方向的像素数

**长度**

> 划分为 相对长度 和 绝对长度 两个单位
>
> 最常用 `px`(像素)作为长度单位。像素是一个 相对长度单位

**像素**

> 物理像素 指的是屏幕渲染图像的最小单位
>
> - 获取屏幕物理像素尺寸
>   - `window.screen.width`
>   - `window.screen.height`
>
> `CSS`像素 指的是通过`CSS`进行网页布局时用到的单位
>
> ​	默认值(PC端)是和物理像素保持一致的（1个单位的`CSS`像素等于1个单位的物理像素），但是我们可通缩放来改变其大小。

**像素密度**

> `PPI`(Pixels Per Inch)值来表示 屏幕 每英寸的像素数量
>
> 框可以通过 勾股定理 来计算我们的 `PPI`值
>
> `PPI`值越大所展现的画面的品质就越精细。
>
> Retina视网膜屏幕 值`PPI`值 大于320 的屏幕

**设备独立像素**

> 不同`PPI`屏幕共存的状态，像素不再是一个 统一的度量单位。
>
> 解决：引入新的单位保证图像内容在不同的`PPI`设备看上去大小差不多，该单位就是 独立像素
>
> `IOS`设备上叫 `PT`(point) , `Android`设备上叫`DIP`或 `DP`(device independent pixel)



**像素和独立像素的关系**

`dp` 和 `px` 是有一个对应的比例关系的，这个比例关系是操作系统确定并处理的。目的是 确保不同`PPI`屏幕能显示的图像大小是一致的。

- 获取该比例值 `window.devicePixelRatio`
- `window.devicePixelRatio` ~= 物理像素/独立像素



## 视口

> 视口（`viewport`）是用来约束网站中最顶级块元素<html>的，即它决定了<html>的大小。

**PC端**

视口的大小就是**浏览器窗口**的大小

- 以 `css`像素 作为度量单位

获取`viewport`的大小

- `document.documentElement.clientWidth`
- `document.documentElement.clientHeight`

```
// 如果确定具体的宽高值，当超出viewport的大小的时候会出现滚动条
// 如果设置宽度为100%，当子元素宽高大于父容器时会自动换行
// 如果不想出现滚动条或者换行，可以将子元素设置为父容器的百分比，但有可能导致子元素内内容的溢出
```

**移动端**

> 移动设备上`viewport`不再受限于浏览器的窗口，而是允许开发人员自由设置`viewport`的大小, 一般这个值的大小会大于屏幕的尺寸

各设备常见默认`viewport`大小 见 Google

****

移动设备上有 两个 `viewport`

- `layout viewport` 布局视口，进行网页布局区域的大小
  - 以`css`像素为计量单位
  - 获取`layout viewport` 
    - `document.documentElement.clientWidth`
    - `document.documentElement.clientHeight`
- `ideal viewport` 理想视口
  - 以设备独立像素PT、DP做为单位
  - 以`CSS`像素做为计量单位
  - 获取`ideal viewport`
    - 新设备
      - `window.screen.width`
      - `window.screen.height`
    - 老设备
      - `window.screen.width / window.devicePixelRatio`
      - `window.screen.height / window.devicePixelRatio` 

**移动端页面内容被缩放和产生滚动条的原因**

> 移动设备浏览器会默认设置一个`layout viewport`，并且这个值会大于`ideal viewport`。
>
> `ideal viewport`是屏幕区域，`layout viewport`是布局网页的区域。
>
> 那么最终`layout viewport`是要显示在`ideal viewport`里的，而`layout viewport`大于`ideal viewport`时，于是就出现滚动条了
>
> **内容缩放：**
>
> 移动设备厂商认为将网页完整显示给用户才最合理，而不该出现滚动条。所以就将`layout viewport`进行了缩放，使其恰好完整显示在`ideal viewport`（屏幕）里
>
> 缩放比例为`ideal viewport / layout viewport`

**移动浏览器**

主要分为三大类: 系统自带浏览器、应用内置浏览器(`WebView`)、第三方浏览器

系统自带浏览器、应用内置浏览器 都是基于 `Webkit`内核的。

## 移动端屏幕适配

> 移动页面最理想的状态：避免滚动条且不被默认缩放处理。
>
> 我们可以通过设置<meta name="viewport" content="">来进行控制，并改变浏览器默认的`layout viewport`的宽度

### `viewport`详解

`viewport`设置的 meta 建议写在已有的meta标签之后

通过设置属性`content=""`来实现，中间以 逗号 分隔

**参数**

> `device-width` 和 `device-height` 就是`ideal viewport`的宽高

- `width` 设置`layout viewport` 宽度，其取值可为数值或者`device-width`
- `height` 设置`layout viewport` 高度，其取值可为数值或者`device-height`
- `initital-scale` 设置页面的初始缩放值，为一个数字，可以带小数
- `maximum-scale` 允许用户的最大缩放值，为一个数字，可以带小数
- `minimum-scale` 允许用户的最小缩放值，为一个数字，可以带小数
- `user-scalable`是否允许用户进行缩放，值为"no"或"yes"

#### 控制缩放

1. 设置<meta name="viewport" content="initial-scale=1">，这时我们发现网页没有被浏览器设置缩放。
2. 设置<meta name="viewport" content="width=device-width">，这时我们发现网页也没有被浏览器设设置缩放。

当我们设置width=device-width，也达到了initial-scale=1的效果，得知其实 `initial-scale = ideal viewport / layout viewport`

为了解决一些兼容问题 >>>> 两种方式都可以控制缩放，开发中一般同时设置width=device-width和initial-scale=1.0

#### 避免滚动条

滚动条是 `layout viewport` 相对于 `ideal viewport` 的，所以只要设置 `layout viewport` 小于或等于 `ideal viewport`

- 即<meta name="viewport" content="width=device-width">

这里可能会出现BUG，不能完全解决 滚动条的问题。原因在于 绝对宽度造成的，要解决这个问题 需要设置 相对宽度。【避免滚动： 不设置具体的宽度，用百分比来设置】

### 适配方案

#### 固定宽度

1. 设置<meta name="viewport" content="width=device-width, initial-scale=1">
2. 设置内容区域大小为 市场上大部分机型屏幕大小的宽度
3. 设置内容区域水平居中显示

#### 百分比宽度

1. 设置<meta name="viewport" content="width=device-width, initial-scale=1">
2. 设置页面宽度为百分比

该方案在 在设置元素高度时有非常大的局限性。

#### `rem`单位

1. 设置<meta name="viewport" content="width=device-width, initial-scale=1">
2. 设置页面元素宽度单位为rem 或 em

**em和rem**

em 相对长度单位，其参照当前元素字号大小，如果当前元素未设置字号则会**继承其祖先元素字号大小**

- 例如 `.box {font-size: 16px;}则 1em = 16px `
- `.box {font-size: 32px;} 则 1em = 32px，0.5em = 16px`

rem 相对长度单位，其**参照根元素(`html`)字号大小**

- 例如 `html {font-size: 16px;} 则 1rem = 16px `
- `html {font-size: 32px;} 则 1rem = 32px，0.5rem = 16px`

#### 100%像素

1. 设置网页宽度等于设备物理像素
2. 设置初始化缩放比例（值为`1 / window.devicePixelRatio`）

之前淘宝针对`iPhone`设备采用的这种方案



## 媒体查询

> 设备终端的多样化，为了能够保证我们的网页可以适应多个终端，不得不专门为某些特定的设备设计不同的展示风格，通过媒体查询可以检测当前网页运行在什么终端，可以有机会实现网页适应不同终端的展示风格。

#### 媒体类型

> 不同的终端设备划分成不同的类型，称为媒体类型

| 值     | 描述                               |
| ------ | ---------------------------------- |
| all    | 用于所有设备                       |
| print  | 用于打印机和打印预览               |
| screen | 用于电脑屏幕、平板电脑、智能手机等 |

#### 媒体特性

> 每种媒体类型都具体各自不同的特性，根据不同媒体类型的媒体特性设置不同的展示风格

![04媒体特性](F:\Desktop\前端知识重新整理\04媒体特性.png)

**常用特性**

- `width / height` ---完全等于`layout viewport`
- `max-width / max-height` ---小于等于`layout viewport`
- `min-width / min-height` ---大于等于`layout viewport`
- `device-width / device-height `---完全等于`ideal viewport`
- `orientation: portrait | landscape` ---肖像/全景模式
- ...

#### 关键字

> 关键字将媒体类型或多个媒体特性连接到一起做为媒体查询的条件

| 关键字 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| and    | 将多个媒体特性连接到一起，相当于“且”的意思                   |
| not    | 排除某个媒体类型，相当于“非”的意思，可以省略                 |
| only   | 指定某个特定的媒体类型，可以省略，最好不要省略<br />老浏览器 只会解释到 第一个单词 only 后面会被忽略掉 |

#### 引入方式

| 引入方式    | 代码                                                         |
| ----------- | ------------------------------------------------------------ |
| link 方法   | `<link href="./5-1.css" media="only screen and (max-width: 320px)">` |
| @media 方法 |                                                              |



## 触屏事件

#### 事件类型

| `touchstart` | 手指触摸屏幕时触发     |
| ------------ | ---------------------- |
| `touchmove`  | 手指在屏幕上移动时触发 |
| `touchend`   | 手指离开屏幕时触发     |

#### `TouchEvent`对象

|                  |                                                              |
| ---------------- | ------------------------------------------------------------ |
| `touches`        | 位于屏幕上的所有手指的列表                                   |
| `targetTouches`  | 位于该元素上的所有手指的列表                                 |
| `changedTouches` | `touchstart`时包含刚与触摸屏接触的触点，`touchend`时包含离开触摸屏的触点 |

注：没有对比出`touches`同`targetTouches`的差异

**推荐使用`targetTouches`**

#### Touch对象

> 设置
>
> <meta name="viewport" content="width=device-width, initial-scale=1">
> </meta>
>
> 这时这三对坐标值是完全一样的

|             |                                                          |
| ----------- | -------------------------------------------------------- |
| `clientX/Y` | 手指相对于`layout viewport`的水平/垂直像素距离           |
| `pageX/Y`   | 手指相对于`layout viewport`的水平/垂直像素距离（含滚动） |
| `screenX/Y` | 手指相对于`layout viewport`的水平/垂直像素距离（含滚动） |
| `target`    | 手指最初与屏幕接触时的元素                               |

### click延迟

### 手势封装

- tap检测接触和离开屏幕的距离来实现
- drag跟踪手指移动位置，进而设置元素定位坐标
- swipe	判断手指滑动的方向
- ...

### `zeptojs`

`zeptojs`为我们**封装了常用的触屏事件**，需要touch模块支持，默认没有构建此模，我们可以自定义构建。



## 移动端类库

### `iScroll.js`

> 可以实现客户端原生滚动效果的类库

### `swipe.js`

### `swiper.js`

### `fastclick.js`

> 为了提升click的响应速度，我们选择了使用`Zepto`事件封装的tap来进行模拟，但是这会带来一个副作用，这个副作用就是“点透”
>
> 面临的问题是即提升click在移动设备上的响应速度，又不能使用`Zepto.js`的tap事件，这时`fastclickjs`可以解决这个问题



移动端推荐使用 弹性盒模型(flex)

```
html{
	box-sizing: border-box;
}
*,*:before,*:after{
	box-sizing: inherit;
}
```

