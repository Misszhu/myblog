## 类型基础

### 强类型和弱类型

**强类型语言**：不允许改变变量的数据类型，除非进行强制类型转换。

**弱类型语言**：变量可以被赋予不同的数据类型。

### 静态类型和动态类型

**静态类型语言**：在编译阶段确定所有变量的类型

**动态类型语言**：在执行阶段确定所有变量的类型

#### 静态类型和动态类型对比

| 静态类型语言   | 动态类型语言            |
| -------------- | ----------------------- |
| 对类型极度严格 | 对类型非常宽松          |
| 立即发现错误   | Bug可能隐藏数月甚至数年 |
| 运行时性能好   | 运行时性能差            |
| 自文档化       | 可读性差                |

动态类型语言的支持者认为：

- 性能是可以改善的（V8引擎），而语言的灵活性更重要
- 隐藏的错误可以通过单元测试发现
- 文档可以通过工具生成

## 基本类型

ES6数据类型

- Number
- String
- Boolean
- Array
- Object
- Function
- undefined
- null
- Symbol

TypeScript的数据类型

- Number
- String
- Boolean
- Array
- Object
- Function
- undefined
- null
- Symbol
- **void**
- **any**
- **never**
- **元组**（限定数组的类型和个数）
- **枚举**
- **高级类型**

在TypeScript中定义**变量** or **函数**要用**类型注解** 

## 类型注解

作用：相当于强类型语言中的类型声明

语法：(变量/函数):type

```typescript
//boolean
let bool: boolean = true;

// 元祖
// 一种特殊的数组，限定了数组的类型和个数
let tuple: [number, string] = [0, '1'];

// void 表示没有任何返回值的类型，比如下面这个没有返回值的函数就是void类型
let noReturn = () => {};

// any(任意数据类型)
// 如果不指定变量的类型，那么它就是any类型
let x;

// never 表示永远不会有返回值的类型，比如下面的函数抛出一个错误，他永远不会有返回值
let error = () => {
    throw new Error('error')
}
// 死循环函数，永远不会返回，他是never类型
let endless = () => {
    while (true) {}
}
```

### 类型别名

类型别名指的是用`type`关键字来给一个类型起新的名字。

```
type newString = string;
type sum = (x: number, y:number) => number;
```



## 字符串字面量类型

字符串字面量类型用来约束某个变量的取值只能取某几个字符串中的一个。**同类型别名一样**，字符串字面量类型也是用`type`关键字定义。

```
type EventNames = 'click' | 'scroll' | 'mosemove';
function handleEvent(ele:Element, event: EventNames) {
    // do something
}
const hello = document.getElementById('app');
handleEvent(hello, 'scroll'); //正常
handleEvent(hello, 'mosedown'); //TS2345: Argument of type '"mosedown"' is not assignable to parameter of type 'EventNames'.
```



## 元祖

在TypeScript中，数组合并相同类型的元素，而元祖（Tuple）合并不同类型的元素。元祖看起来更接近原生JavaScript的数组。

定义一对值分别为number和string的元祖：

```
let point: [number, string] = [123, '123'];
```

也可以先初始化一个元祖，然后通过数组索引赋值。元祖的访问方式和数组的一致：

```
let point: [number, string];

point[0] = 123;
point[1] = '123';
console.log(point[0]);
point[1] = '456';
console.log(point);
```

虽然上面代码会通过编译，但是会有运行时的错误，看编译后的js代码就能知道原因：

```
var point;
point[0] = 123;
point[1] = '123';
console.log(point[0]);
point[1] = '456';
console.log(point);
//Uncaught TypeError: Cannot set property '0' of undefined
```

对元祖进行初始化时，要对整个元祖的所有元素都赋值，否则会报错：

```
let point: [number, string];

point = [123];  // 报错，Property '1' is missing in type '[number]' but required in type '[number, string]'.
point = [123, '123']; // 正确
```

### 元祖越界

当添加越界的元素时，添加的元素必须是已有类型的子类型。通过索引的方式进行越界操作也是不运行的。

```
let point: [number, string] = [123, '123'];
point[2] = '456'; // 报错： Type '"456"' is not assignable to type 'undefined'.
point.push('456'); // 正常
point.push(true); //报错： Argument of type 'true' is not assignable to parameter of type 'string | number'.
```

## 声明合并

如果定义了两个名称相同的函数、接口或类，那他们会合并成一个类型。

### 函数的合并

之前我们学过函数的重载，定义多个名称相同的函数类型，然后在类型最为宽松的那个类型实现这个函数。函数重载就是函数的合并。

### 接口的合并

接口的属性在合并时会简单地合并到一个接口

```
interface Ipoint {
    x: number
}
interface Ipoint {
    y: number
}
// 相当于
interface Ipoint {
    x: number
    y: number
}
```

同名的属性会被合并，被合并的属性类型必须是一致的，否则会报错：

```
interface Ipoint {
    x: number,
    y: number
}
// 正确
interface Ipoint {
    x: number
    y: number,
    z: number
}
// 报错
interface Ipoint {
    x: number
    y: string
}
// TS2717: Subsequent property declarations must have the same type.  Property 'y' must be of type 'number', but here has type 'string'.
```

接口中方法的合并，与函数的合并一样（即函数重载）。

### 类的合并

类的合并与接口的合并规则一致。
