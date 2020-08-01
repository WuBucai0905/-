## 基本网页布局

浮动

- `float: left`
- `float: right`

布局分类

- 一列布局
- 两列布局
- 三列布局
- 多列布局

任何布局都是由 一列布局 和 两列布局 构成的

float 区域大小 变成 容纳元素内容的区域的大小





padding会对盒子有影响，而margin不会对盒子有影响

## 圣杯布局

```
.middle{
	width: 100px;
	float: left;
	width: 100% //既是float，又让它拥有块状元素的特点
}
.left{
	float: left;
	width: 100%;
	margin-left: -100%; // 骑到相邻最近的`float`元素上，最左边的距离。即`middle`上
}
.right{
	float: left;
	width: 150px;
	margin-left: -150px;
}
// 此时middle 的一部份区域 是被浮动元素给盖住的
// 解决
// 【理解 左右 正负值的概念】
.content{
	padding-left: 100px;
	padding-ringht: 150px; //让出左右的位置
}
.left{
	position: relative;
	left: -100px;
}
.right{
	position: relative;
	right: -150px;
}
```



## 双飞翼布局

> 圣杯布局 + `inner div`

```
// inner作为 扩充的容器
.inner{
	margin-left: 100px;
	margin_right: 150px;
}
```

双飞翼布局

- position
- float
- 负边距
- 等高
- 盒子模型
- 清除浮动





## `BFC` `IFC` `GFC` `FFC`

> `CSS`布局的基本单位： box
>
> 一个页面由很多个 box 组成， 元素的类型和 display属性 决定了 box 的类型。
>
> 不同类型的box会参与不同的 formatting context(决定如何渲染容器box)

Box盒子类型：

- `block-level box`
- `inline-level box`

### `BFC`

> block formatting context
>
> `BFC`  就是页面上的一个隔离独立容器，容器内的子元素不会影响到 外面的元素 

哪些元素生成`BFC`?

- 根元素
- float 属性不为 none
- position 为 absolute或fixed
- display 为 inline-block、table-cell、table-caption、flex、inline-flex
- overflow 不为 visible

**`BFC`布局规则：**

1. 每个元素的 margin box的左边，与包含 块border box的左边相接触( 对于从左往右的格式化，否则相反 )。即存在浮动也是如此
2. BFC的区域 不会与 float box 出现重叠
3. 计算BFC的高度时， 浮动元素页参与运算
   - 达到清除内部浮动
4. box垂直方向的距离由 margin决定。属于同一个 BFC 的两个相邻box 的margin会发生重叠
   - 放置 垂直 margin 重叠
     - 使两个 容器不属于同一个 BFC , 就不会发生 margin 重叠

### `IFC`

> inline formatting context
>
> 内联格式化上下文

IFC的linebox(线框) 高度由其包含 行内元素中最高的实际高度计算而来 ( 不受到垂直方向的 padding/margin 影响 )

### `FFC` 

> (flex formatting context)
>
> 自适应格式化上下文

display值为 flex 或inline-flex 的元素将生成 自适应容器

### `GFC`

> ( gridlayout formatting context )
>
> 网格布局格式化上下文

主要用于处理 table





## 响应式布局

> 媒体查询
>
> 如果只是根据 视口大小为用户 提供匹配的视觉效果 优先选择 响应式设计
>
> 媒体查询 可以针对不同的屏幕尺寸设置不同的样式，给每种类型的用户提供最佳的体验

### 语法

```
@media mediatype and|not|only (media feature) {
	CSS code;
}
mediatype 设备类型
media feature 媒体特点
```

`mediatype`

- all 所有设备
- print 用于打印机和打印浏览
- screen 用于电脑屏幕，平板电脑，智能手机等
- speech 用于屏幕跃度器等发声设备

**阻止移动浏览器自动调整 页面大小**

支持 `viewport` meta元素覆盖 默认的画布缩放设置，meta标签中可以设置具体的宽度或缩放比

```
<meta name='viewport' content='width=device-width, initial-scale=1.0'>
```

### 媒体属性

- width: 视口宽度
- height: 视口高度
- device-width 渲染表面的宽度，即设备屏幕的宽度
- device-height
- orientation 检查设备处于横向还是纵向
- aspect-ratio 基于视口的宽高比 width/height
- device-aspect-ratio
- color 每种颜色的位数 bits
- resolution 检测屏幕或打印机的分辨率
- ...

媒体属性都可以添加前缀 `min-` 或 `max-`





## css 分层

**为什么要分层？**

- css有语义化的命名约定和css层的分离，将有助于它的可扩展性，性能的提高和代码的组织管理
- 大量的样式，覆盖，权重和很多 `!important`,分好层可以让团队命名统一规范，方便维护
- 有责任感地去命名你的选择器

### 规范

**SMACSS**

5个层次

- base
- layout
- module
- state
- theme

修饰符使用的是 -- ，子模块使用的是 __ 符号

**BEM**

**SUIT**

**ACSS**

**ITCSS**





**实现 div 在 不同内容 垂直方向等高**

```
.content{
	overflow: hidden;
}
.content .left{
	padding-bottom: 9999px;
	margin-bottom: -9999px;
}
.content .right{
	padding-bottom: 9999px;
	margin-bottom: -9999px;
}
```

