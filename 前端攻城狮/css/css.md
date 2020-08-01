## 1.浮动属性

> 语法： `float: none/left/right`

浮动定位指

- 将元素排除在普通流之外
- 元素将不在页面中占据页面
- 将浮动元素放置在包含框的左边或者右边, 浮动元素依旧位于包含框之内

浮动元素可以向左或者向右移动，直到边缘碰到包含框或者另外的浮动框为止

浮动元素外边缘 不会超过父元素的内边缘

浮动元素不会相互重叠

浮动元素不会上下浮动



## 2.position属性

> 指定一个元素(静态的、相对的、绝对或固定)的定位方法的类型

| 值       | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| absolute | 绝对定位，**参照物**: 非static 的第一个父元素; \| 元素不会占据原来的物理位置 |
| relative | 相对定位，**参照物**: 当前自己位置; \| 元素仍然占据原来的物理位置 |
| fixed    | 固定定位，**参照物**: 浏览器窗口; \| 元素不会占据原来的物理位置 |
| static   | 默认定位                                                     |

### z-index

z-index 只支持定位元素

z-index 堆叠顺序

- 一旦修改了元素的定位方式，则元素可能会发生堆叠
- 可以用z-index 属性来控制元素框 出现的重叠顺序
- z-index 仅能在定位的元素上生效
- z-index属性
  - 数值，数值越大表堆叠顺序越高，离用户越近
  - 负值，离用户越远，一般不要设置



## 3.display属性

每一个网页元素都有一个display属性

```
div元素默认display属性为 block;
span元素默认display属性为 inline
```

### 3.1属性

- `block` 指定对象为块元素【具有宽高属性并且独占一行】
- `inline` 指定对象为内联元素【没有宽高属性不会独占一行】
- `inline-block` 指定对象为内联块元素【具有宽高属性不会独占一行】

- `none`	隐藏对象【隐藏元素不会占据原来物理位置】
- `table-cell` 指定对象为表格单元格
- `flex` 弹性盒

### 3.2伸缩盒(弹性盒)模型

> `display: flex`
>
> flex box 自适应浏览器窗口的流动布局
>
> 弹性盒模型中 float、clear和 vertical-align 不起作用

`flexbox`是一个整体模块，一些属性是在父容器上设置，一些属性是在子容器上设置。

- 首先规定哪个是父容器，项目在父容器中是 弹性布局的
- 其次还可以在父容器中规定怎么显示它里面的项目(子元素自动水平排列)

**父容器中常用属性**：

- justify-content: ; 弹性盒子元素在主轴方向上的对齐方式
- align-items: ; 弹性盒子元素在纵轴方向上的对齐方式
- flex-wrap 弹性盒元素在必要时候拆行【给定min-width】

**子容器中常用属性**：

- flex-grow: number; 规定项目相对于其他灵活项目进行扩展的量。默认值为0
- flex-direction 指定布局方向 默认水平布局

### 3.3栅格布局

> `display: grid`  默认容器元素都是块元素
>
> 网格布局(grid) 是最强大的css布局方案

**容器和项目**

采用 网格布局的区域称为 **容器**

容器内部采用网格定位的子元素，称为 **项目**【只能是容器的顶层子元素】

**容器中的属性**

- `grid-template-columns` 每一列的列宽
- `grid-template-rows` 每一行的行高

### 3.4flex与grid比较

- flex布局是 轴线布局，只能指定 项目 针对轴线的位置，
- grid布局则是将容器分为 行 和 列，产生单元格。





## 4.盒子模型

> 盒模型分为 四个区域： 内容、内间距、边框、外间距

### 外间距margin

> 语法 `margin: value`

- 围绕在 元素边框周围的空白区域
  - 会在元素外创建额外的空白区域
  - 外边距是透明的
- 单边设置
  - margin-top/right/bottom/left: value
  - value 可以是像素 ， % ， auto ， 负值

**关于外间距注意的点**

>外间距合并【以最大的边距合并】: 只有上下合并，没有左右合并
>
>包含式外间距合并:  解决【在 父元素内 加 边框border】
>
>margin可以设置块状元素居中 `margin: 10px auto`; 

### 边框border

> 语法： `border: 宽 样式 元素`

边框的三要素： 宽、样式、颜色

样式

- 点状 dotted
- 实现 solid
- 双线 double【起码设置为 3 像素】
- 虚线 dashed
- 无边框 none

**关于边框注意的点**

> **style 属性为空的情况下，整个边框是不会实现的**

### 内间距padding

> 语法： `padding:value`

- 内容区域 和 边框之间的空间
- 会扩大元素边框所占用的区域
- 设置不能为负数

**关于内间距注意的点**

> 设置 内间距 和 边框的时候 注意 宽度的变化

### 怪异盒

怪异盒是指 IE的盒子模型



## 5.`css3`

### 浏览器私有前缀

> `css3`中，浏览器厂商对新属性支持情况不同，所以加 厂商前缀加以区分

在实际使用中 通常将 所有前缀写一遍后，标准的写在最后



### 圆角border-radius

- 语法
  - `border-radius: value` 四个角
  - `border-radius: value value` 左上右下 右上左下
  - `border-radius: value value value value` 左上 右上 右下 左下
  - `border-radius: value value value value / value value value value`  水平半径 / 垂直半径

### 阴影shadow

**盒子阴影**

> 语法:`box-shadow:水平偏移位置 垂直偏移位置 模糊度 red`

- `box-shadow:0px 0px 模糊度 red`  向四周发亮
- `box-shadow:水平偏移位置 垂直偏移位置 模糊度 inset red` 内置阴影

**文字阴影**

> 语法: `text-shadow:水平偏移位置 垂直偏移位置 模糊度 red`

### 渐变gradients

> 在两个或者多个指定的颜色之间 显示平稳的过渡
>
> 通过代码实现渐变代替图像来实现 可以 减少请求和节约带宽
>
> 定义了两种类型的渐变
>
> - 线性渐变
> - 径向渐变

**线性渐变**

```
background: liner-gradient()
// background-image: liner-gradient()
`to top/left/right/right buttom/...` `xdeg` 从x到x 渐变
```

**径向渐变**(使用不多)

```
background: radial-gradient(center, shape, size, start-color, ...)
```

默认渐变的中心是 center ， 渐变的形状是ellipse(椭圆形)

### 转换transform

**2D**

> 对 元素进行 旋转 缩放 移动或倾斜

transform属性是 函数来定义的。

2D函数

>语法：transform: 函数名(x, y)

- translate()
  - x,y轴给定的参数，从当前元素位置移动
- scale()
  - 缩放
- rotate()
  - 顺时针旋转 给定的角度 deg为单位 
- skew()
  - 根据 水平轴和垂直轴翻转

**3D**

> 2D平面 搭配 perspective(透视) 使用 呈现成伪3D的效果

3D函数: translate | scale | rotate

### 过渡属性transition

> 使用css的属性值 在一段时间内 平滑  的过渡
>
> 语法简写：`transition: property duration timing-function delay;`

过渡四要素：

- 过渡属性 background、color...
- 过渡所需时间
- 过渡函数 即过渡的速度、方式...
- 过渡延迟时间 即开始执行的时间

触发过渡

- 通过用户的行为触发，如点击、悬浮...

**过渡四要素**

**过渡属性**

- transition-property: none|all|property;
- 多个属性用逗号隔开
- 可设置过渡的属性
  - 颜色属性
  - 取值为数值的属性
  - 转换属性
  - 渐变属性
  - 阴影属性

**过渡时间**

- transition-duration: s|ms;
  - 默认为0，意味不会有效果

**过渡函数**

- transition-timing-function: ;
  - 取值
    - ease 默认值， 慢速开始，然后变快，最后慢速结束
    - linear 匀速
    - ease-in 慢速开始，加速效果
    - ease-out 慢速结束，减速效果
    - ease-in-out 先加速后加速效果

**过渡延时**

- transition-delay: s|ms;
  - 改变元素属性值后 多长时间开始执行过渡效果

### 动画属性animation

> 过渡属性只能模拟动画效果
>
> 动画属性可以制作类似 flash动画
>
> - 通过 关键帧 控制动画的每一步，使元素从一种样式 逐渐变化为 另一种样式， 实现复杂的动画效果

animation属性是一个简写属性

- `animation: name duration timing-function delay iteration-count direction`

**关键帧**

- `@keyframes` 作用：用于声明动画，指定关键帧
  - 每个帧 代表某个时间点

**animation属性**

- 用于控制动画
  - 调用由 `@keyframes` 定义的动画
  - 设置动画属性，如时间、次数等
- 动画子属性
  - animation-name 调用动画，规定需要和 keyframes的名字一致
  - animation-duration 动画完成一个周期需要的时间
  - animation-timing-function 动画的速度变化类型
  - animation-delay 播放之前的延迟时间
  - animation-iteration-count 播放次数 infinite无限次播放
  - -direction 播放方向 normal默认值，正常播放; alternate
  - -fill-mode 动画停留在最后一帧forwards  默认为none
  - -play-state 动画正在运行还是暂停 默认为running



## 6.布局方式

### 固定宽度布局

> 网页设置固定宽度，以 `px`作为长度单位。

### 流式布局

> 网页设置 相对宽度， 以 百分比为长度单位

### 栅格化布局

> 网页宽度划分为均等的宽度，以 局等宽度的长度为度量单位

### 响应式布局

- 检测设备信息决定布局方式
- 原理 : 利用 **媒体查询** 检测到 屏幕的尺寸【媒体查询是来设置 不同屏幕下的 **样式**】

响应式布局常用于 企业的官网、博客、新闻资讯类型网站

移动端展示需要做 移动端的适配

**宽度适配**

- 判断最小值(min-width)，应该从小到大写
- 判断最大值(max-width)，应该从大到小写

这样的适配规则 可以起到

- 向上兼容
- 向下覆盖: 后面设置的样式会将前面设置的样式覆盖

**缺点**

- 会加载更多的样式和脚本资源
- 设计比较难精确定位和控制
- 老版本浏览器兼容不好

## 7.bootstrap

`bootstrap` 是基于`jquery`的，所以要先引入 `jQuery`

### 布局容器

> 需要为页面内容和栅格系统包裹一个 `.container`容器或`.container-fluid`容器，两者容器不能嵌套

- `.container`类 用于 固定宽度并支持响应式布局的容器
  - `.container`宽度
    - w>1200 : 1170 【`lg`】
    - w: 992~1200 : 970 【`md`】
    - w: 767~992 : 750 【`sm`】
    - w<768 : 100% 【`xs`】
- `.container-fluid`类 用于 100%宽度，占全部视口(`viewport`)的容器

#### 流式栅格系统

> 流式： 从左到右，从上到下
>
> 预定义类 来表示 该容器占据 栅格系统的几等分  如:`.col-xs-4`

**栅格系统的使用:**

1. 先添加容器
2. 再容器中 添加 row
3. 在 row 中添加子元素，设置栅格样式
4. 在子元素中 添加具体的内容

**其他栅格样式**

- offset 偏移 偏移的距离是以 栅格作为单位
  - 会造成后面元素的偏移。因为通过 `margin-left`属性来实现的
- push 往右推
  - 不会造成后面元素的偏移，但会造成和后面元素的重叠。因为通过 定位来实现的。| 定位来实现 元素的位置不再受栅格系统的影响
- pull 往前拉

列排序 就是 push 和 pull 

#### 列嵌套

- 注意: `.container` 是参照 原始的样式，而不是参照 外层的 栅格

...

