TypeScript

类型注解和类型检测

```
// 类型注释
let title: string;
String是构造函数，string是类型
// 类型推论
let title = "xx"; 
// 数组类型
let name: string[];
// 任意类型
let foo: any;
// 数组中任意类型
let foo: any[];
// 函数中的类型，参数类型和返回值类型
// 与js不同的是，ts中参数一旦声明就必须要传递
// 可选参数 如`age?: number` 或者加默认值
function greeting(person: string): string {
	return "hello" + person
}
greeting("Tom ")
// void类型 --- 无返回值
```



在Vue中 类里面声明的普通属性相当于 data属性



## 类

ts中的类和es6中类大体相同。

```typescript
class MyComp extends Parent {
 // 访问修饰符
 private _foo = 'foo'; // 私有属性，不能在类的外部访问
 protected bar = 'bar';// 保护属性，还可以在派生类中访问
 readonly mua = 'mua'; // 只读属性必须在声明时或构造函数里初始化
 // 构造函数：初始化成员变量
 // 参数加上修饰符，能够定义并初始化一个属性
 constructor ( private tua = 'tua') {
  super();
}
 
 // 方法也有修饰符
 private someMethod() {}
 // 存取器：暴露存取数据时可添加额外逻辑；在vue里面可以用作计算属性
 get foo() { return this._foo }
 set foo(val) { this._foo = val }
}

// 直接对 `get foo() { return this._foo }` 来访问
// 函数的方式取声明，使用的时候当作一个属性来使用
const comp = new MyComp()
comp.foo
```

例子：利用getter设置计算属性

```typescript
<template>
<li>特性数量：{{count}}</li>
</template>
<script lang="ts">
  export default class HelloWorld extends Vue {
    // 定义getter作为计算属性
    get count() {
      return this.features.length;
   }
 }
</script>
```



## 接口

接口仅 约束结构，不要求实现，使用更简单

```typescript
interface Person {
 firstName: string;
 lastName: string;
}
function greeting(person: Person) {
 return 'Hello, ' + person.firstName + ' ' + person.lastName;
}
const user = {firstName: 'Jane', lastName: 'User'};
console.log(greeting(user));
```



## 泛型

泛型`generics`指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候指定类型的一种特性

```typescript
// 不用泛型
// interface Result {
//  data: Feature[];
// }
// 使用泛型
interface Result<T> {
	data: T;
}
```



Promise中的resolve本身就是一个泛型的方法



## 装饰器

是一种设计模式。本质就是一个工厂函数

装饰器用于扩展类或它的属性和方法

除了在@Component中声明，还可以采用 @Prop 的方式声明 组件属性

```typescript
export default class HelloWorld extends Vue {
 // Props()参数是为vue提供属性选项
 // !称为明确赋值断言，它是提供给ts的
 @Prop({type: String, required: true})
 private msg!: string;
}
```



@Component 将里面所写的代码 转换成 Vue的配置对象，并把它 变成成Vue的构造函数 



```
@Component ===
function Component(target){
	target. //对target的操作
	return target
}
```



属性声明: @Prop

声明组件属性

```
export default class HelloWorld extends Vue {
 // Props()参数是为vue提供属性选项
 // !称为明确赋值断言，它是提供给ts的
 @Prop({type: String, required: true})
 private msg!: string;
}
```



事件处理: @Emit

新增特性时派发事件通知

```
// 通知父类新增事件，若未指定事件名则函数名作为事件名（羊肉串形式）
@Emit()
private addFeature(event: any) {// 若没有返回值形参将作为事件参数
  const feature = { name: event.target.value, id: this.features.length + 1 };
  this.features.push(feature);
  event.target.value = "";
  return feature;// 若有返回值则返回值作为事件参数
}
```



变更检测: @watch

```
@Watch('msg')
onRouteChange(val:string, oldVal:any){
  console.log(val, oldVal);
}
```







### 装饰器原理

装饰器的本质时工厂**函数**，修改传入的类、方法和属性等

类装饰器

```typescript
//类装饰器表达式会在运行时当作函数被调用，类的构造函数作为其唯一的参数。
function log(target: Function) {
  // target是构造函数
  console.log(target === Foo); // true
  target.prototype.log = function() {
   console.log(this.bar);
 }
  // 如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。
}
@log
class Foo {
 bar = 'bar'
}
const foo = new Foo();
// @ts-ignore
foo.log();
```

