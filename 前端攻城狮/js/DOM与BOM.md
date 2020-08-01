# DOM

JavaScript组成： ECMAScript + DOM(文档对象模型) + BOM(浏览器对象模型)

DOM

> 可以使用DOM 实现 动态效果

**概念**

- 文档： 一个网页可以称为文档 **document 文档对象**
- 节点： 所有内容都是节点
- 元素： 网页中的标签
- 属性： 标签的属性

**常用操作**

- 获取元素
- 对元素进行操作( 设置属性或调用方法 )
- 动态创建元素
- 事件( 什么时机做相应的操作 )

## 获取页面元素

> 为什么要获取页面元素？ 需要操作页面元素

- getElementById
  - 没对应的Id 此时返回 null
  - 有对应的Id 此时返回 对象
    - 打印对象 使用 `console.dir()`
    - 对象的真实类型 通过 属性`__proto__` 来展示
- getElement**s**ByTagName
  - 获取到的集合是 **动态集合**，及时更新集合
  - `HTMLCollection` 类似数组，本质不是数组，**伪数组**
- getElement**s**ByClassName
- querySelector 根据选择器来查找元素
  - 返回第一个元素
- querySelectorAll



## 创建页面元素

- document.write()

  ```
  document.write('新设置的内容<p>标签也可以生成</p>');
  ```

- innerHTML

  ```
  var box = document.getElementById('box');
  box.innerHTML = '新内容<p>新标签</p>';
  ```

  - `innerHTML` 与 `innerText`
    - 区别
  - **`innerHTML`的性能问题**
    - `innerHTML` 方法 会对字符串进行解析，需要避免在循环内多次使用。
    - 可以借助字符串或数组的方式进行替换，再设置给 `innerHTML`
    - 优化后与`document.createElement`性能相近

- document.createElement()

  ```
  var div = document.createElement('div');
  document.body.appendChild(div);
  ```

## 属性操作

### 非表单元素属性

标签是直接写在页面上的，DOM是把文档抽象成对象，而对象都是在内存中存储的。

标签内的`class`  是 DOM对象中的`className`。因为`class`在 `js` 中为关键字。

`HTML`转义符

```
"		&quot;
'		&apos;
&		&amp;
<		&lt;   // less than  小于
>		&gt;   // greater than  大于
空格	   &nbsp;
©		&copy;
```

### 表单元素属性

- `value` 用于大部分表单元素的内容获取(option除外)
- `type` 可以获取input标签的类型(输入框或复选框等)
- `disabled` 禁用属性
- `checked` 复选框选中属性
- `selected` 下拉菜单选中属性

### 自定义属性操作

- `getAttribute()` 获取标签行内属性
- `setAttribute()` 设置标签行内属性
- `removeAttribute()` 移除标签行内属性
- 与`element.属性` 区别: 
  - 上述三个方法用于获取任意的行内属性

### 样式属性操作

- 使用 `style` 方式设置的样式显示在标签行内
  - 注意：
    - 设置宽高、位置的属性类型是字符串，需要加上px

### 类名属性操作

- 修改标签的 `className` 属性相当于直接修改标签的类名



## 节点操作

```
var body = document.body;
var div = document.createElement('div');
body.appendChild(div);

var firstEle = body.children[0];
body.insertBefore(div, firstEle);

body.removeChild(firstEle);

var text = document.createElement('p');
body.replaceChild(text, div);
```

### 节点属性

```
function Element(option) {
  this.id = option.id || '';
  this.nodeName = option.nodeName || '';
  this.nodeValue = option.nodeValue || '';
  this.nodeType = 1;
  this.children = option.children || [];
}

var doc = new Element({
  nodeName: 'html'
});
var head = new Element({
  nodeName: 'head'
});
var body = new Element({
  nodeName: 'body'
})
doc.children.push(head);
doc.children.push(body);

var div = new Element({
  nodeName: 'div',
  nodeValue: 'haha',
});

var p = new Element({
  nodeName: 'p',
  nodeValue: '段落'
})
body.children.push(div);
body.children.push(p);

function getChildren(ele) {
  for(var i = 0; i < ele.children.length; i++) {
    var child = ele.children[i];
    console.log(child.nodeName);
    getChildren(child);
  }
}
getChildren(doc);
```

**节点的方法和属性**

方法

- appendChild()
- insertBefore()
- removeChild()
- replaceChild()

属性

- parentNode
- childNodes
- children
- nextSibling/previousSibling
- firstChild/lastChild

**节点层级**

- `childNodes` 和 `children`
  - `childNodes`获取的是子节点
  - `children`获取的是子元素
- `nextSibling`和`previousSibling` 、 `nextElementSibling`和`previousElementSibling`
  - `nextSibling`和`previousSibling`  获取的是节点
  -  `nextElementSibling`和`previousElementSibling` 获取的是元素【IE 9 以后才兼容】



## 事件

> 当什么时候做什么事情。触发响应

### 注册事件和移除事件

#### 方式一

```
var box = document.getElementById('box');
box.onclick = function () {
  console.log('点击后执行');
};
box.onclick = null;
```

#### 方式二

```
box.addEventListener('click', eventCode, false);
box.removeEventListener('click', eventCode, false);

function eventCode() {
  console.log('点击后执行');
}
```

#### 方式三

```
box.attachEvent('onclick', eventCode);
box.detachEvent('onclick', eventCode);
```

**事件三要素**

- 事件名称
- 事件源
- 事件处理函数

**事件三阶段**

> `事件对象.eventPhase属性`：  查看事件触发时所处的阶段

1. 捕获阶段
2. 当前目标阶段
3. 冒泡阶段

### 事件对象的属性和方法

- `event.type` 获取事件类型
- `clientX/clientY`     所有浏览器都支持，窗口位置
- `pageX/pageY`       IE8以前不支持，页面位置
- `event.target || event.srcElement` 用于获取触发事件的元素
- `event.preventDefault()` 取消默认行为

### 阻止事件传播

- 标准方式 `event.stopPropagation()`;
- IE低版本 `event.cancelBubble = true`; 标准中已废弃

### 鼠标和键盘事件

- onmouseup 鼠标按键放开时触发
- onmousedown 鼠标按键按下触发
- onmousemove 鼠标移动触发
- onkeyup 键盘按键按下触发
- onkeydown 键盘按键抬起触发





# BOM

> BOM(Browser Object Model) 是指浏览器对象模型
>
> 提供了独立于内容的、可以与浏览器窗口进行互动的对象结构
>
> 由多个对象组成，其中代表浏览器窗口的Window对象是BOM的顶层对象，其他对象都是该对象的子对象

## 顶级对象window

> 调用window下的属性和方法时，可以省略window
>
> 注意：window下一个特殊的属性 window.name

## 对话框

- alert()
- prompt()
- confirm()

## 页面加载事件

- onload

  ```
  window.onload = function () {
    // 当页面加载完成执行
    // 当页面完全加载所有内容（包括图像、脚本文件、CSS 文件等）执行
  }
  ```

- onunload

  ```
  window.onunload = function () {
    // 当用户退出页面时执行
  }
  ```

## 定时器

### setTimeout()和clearTimeout()

> 在指定的毫秒数到达之后执行指定的函数，**只执行一次**

```
// 创建一个定时器，1000毫秒后执行，返回定时器的标示
var timerId = setTimeout(function () {
  console.log('Hello World');
}, 1000);

// 取消定时器的执行
clearTimeout(timerId);
```

### setInterval()和clearInterval()

> 定时调用的函数，可以按照给定的时间(单位毫秒)周期调用函数

```
// 创建一个定时器，每隔1秒调用一次
var timerId = setInterval(function () {
  var date = new Date();
  console.log(date.toLocaleTimeString());
}, 1000);

// 取消定时器的执行
clearInterval(timerId);
```

## location对象

> location对象是window对象下的一个属性
>
> location可以获取或者设置浏览器地址栏的URL

### URL

统一资源定位符 (Uniform Resource Locator, URL)

**组成**

```
scheme://host:port/path?query#fragment
scheme:通信协议
	常用的http,ftp,maito等
host:主机
	服务器(计算机)域名系统 (DNS) 主机名或 IP 地址。
port:端口号
	整数，可选，省略时使用方案的默认端口，如http的默认端口为80。
path:路径
	由零或多个'/'符号隔开的字符串，一般用来表示主机上的一个目录或文件地址。
query:查询
	可选，用于给动态网页传递参数，可有多个参数，用'&'符号隔开，每个参数的名和值用'='符号隔开。例如：name=zs
fragment:信息片断
	字符串，锚点.
```

## history对象

- back()
- forward()
- go()

## navigator对象

- userAgent

通过userAgent可以判断用户浏览器的类型

- platform

通过platform可以判断浏览器所在的系统平台类型
