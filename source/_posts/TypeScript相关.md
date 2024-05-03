---
title: TypeScript 了解
tags:
  - JavaScript
  - TypeScript
categories: Frontend
cover: ../assets/typescript.png
date: 2023-12-25 16:18:00
---

# `TypeScript`相关

`TypeScript`是`JavaScript`的一个超集，主要提供**类型系统**和**ES6**的支持。（添加类型系统的`JavaScript`）

**类型系统**：（`TypeScript`是静态类型）

1. 动态类型：在运行时才会进行类型检查，这种语言的类型错误往往会导致运行时报错。`JavaScript`是一门**解释性语言**，没有编译阶段，所以为动态类型。
2. 静态类型：在编译阶段就能确定每个变量的类型，这种语言的类型错误往往会导致语法错误。`TypeScript`在运行前需要编译成`JavaScript`，而在编译阶段就会进行类型检查，所以为静态类型

`TypeScript`的核心设计理念：在完整保留`JavaScript`运行时行为的基础上，通过引入静态类型系统来提高代码的可维护性，减少可能出现的bug。

参考链接：https://ts.xcatliu.com/

官方文档：https://www.typescriptlang.org/



## 扩展其他





## 数据类型

`JavaScript`的类型分为两种：原始数据类型（`Primitive data types`）和对象类型（`Object types`）.

### 原始数据类型

原始数据类型包括：布尔值`boolean`、数值`number`、字符串`string`、`null`、`undefined`以及`ES6`中的新类型`Symbol`和`ES10`中的新类型`BigInt`

**PS：**

1. `void`为空值类型，可以表示没有任何返回值的函数或者`undefined`或`null`；
2. `unknown`类型和`any`类型相似，但是更安全，该类型变量只能赋值给`unknown`和`any`类型；
3. `never`表示永不存在值的类型；
4. ~~`null`和`undefined`是所有类型的子类型，也就是说`undefined`或`null`可以赋值给任何类型的变量；~~
5. `number`编译为十进制和十六进制`0x`，二进制`0b`和八进制`0o`都会被编译为十进制；
6. `string`会将`ES6`中的模版字符串编译为逻辑运算。



### 任意值

任意值`any`用来表示允许赋值为任意类型。

**PS：**如果变量声明的时候没有指定其类型，那么将被识别为任意值类型。



### 类型推论

如果变量声明时没有赋值，则会推断为`any`类型；如果存在赋值的情况，原始数据类型会推测出类型，其他情况下推断为`any`类型。



### 联合类型

联合类型`Union Types`表示取值可以为多种类型中的一种。

联合类型使用`|`分隔每个类型。

**PS：**只能访问联合类型的所有类型里共有的属性或方法；联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型。



### 对象类型——接口

在`TypeScript`中，使用接口（`interface`）来定义对象的类型。

接口`interface`是对行为的抽象，具体如何行为需要由类`class`去实现`implement`。

**PS：**如果对象不需要完全匹配接口的`shape`，可以用可选属性；任意属性；只读属性；

```typescript
interface IObject {
  readonly id: number; // 只读属性
  name: string;
  age?: number; // 可选属性
  [key: string]: any; // 任意属性
}
```



### 数组类型

数组类型有多种定义方式。

1. 「类型 + 方括号」

   ```typescript
   let a: number[] = [1, 2, 3];
   ```

2. 数组泛型

   ```typescript
   let b: Array<number> = [1, 2, 3];
   ```

3. 接口：常用于表示类数组

   ```typescript
   interface IArray {
     [index: number]: number;
   }
   let c: IArray = [1, 2, 3];
   ```

**类数组**

类数组`Array-like Object`不是数组类型，比如`arguments`。

```typescript
interface IArguments = {
  [index: number]: number;
  length: number;
  callee: Function;
}
function sum() {
  let args: IArguments = arguments;
}
```



### 函数类型

```typescript
// 函数声明
function sum(x: number, y: number): number {
  return x + y;
}

// 函数表达式
// 该方式只是对等号右边的匿名函数进行了类型定义，等号左边是通过赋值操作进行类型推论而推断出来的
let _sum = function (x: number, y: number): number {
  return x + y
}
// TypeScript 中 => 用来表示函数的定义，左边是输入参数类型，需要用括号括起来，右边是输出类型
let _sum_: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y
}

// 接口定义函数
interface IFunction {
  (x: number, y: number): number;
}
let func: IFunction;
func = function (x: number, y: number): number {
  return x + y
} 
```

#### 重载

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

`TypeScript`会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把最精确的定义写在前面。



### 类型断言

断言类型`Type Assertion`来手动指定一个值的类型。

```typescript
值 as 类型
<类型>值
```

1. 将一个联合类型断言为其中一个类型；

   ```typescript
   interface cat {
     name: string;
     run(): void;
   }
   
   interface fish {
     name: string;
     swim(): void;
   }
   // 类型断言只能「欺骗」TypeScript 编译器，无法避免运行时的错误。
   function isFish(animal: cat | fish) {
     if (typeof (animal as fish).swim === 'function') {
       return true;
     }
     return false;
   }
   ```

2. 将一个父类断言为更加具体的子类；

   ```typescript
   interface ApiError extends Error {
       code: number;
   }
   interface HttpError extends Error {
       statusCode: number;
   }
   
   function isApiError(error: Error) {
       if (typeof (error as ApiError).code === 'number') {
           return true;
       }
       return false;
   }
   ```

3. 将任何一个类型断言为`any`；它极有可能掩盖真正的类型错误，所以如果不是非常确定，就不要使用`as any`

4. 将`any`断言为一个具体的类型；

   ```typescript
   function getCacheData(key: string): any {
       return (window as any).cache[key];
   }
   
   interface Cat {
       name: string;
       run(): void;
   }
   
   const tom = getCacheData('tom') as Cat;
   tom.run();
   ```

5. 要使得`A`能够被断言为`B`，只需要`A`兼容`B`或`B`兼容`A`即可。

   ```typescript
   interface Animal {
       name: string;
   }
   interface Cat {
       name: string;
       run(): void;
   }
   
   function testAnimal(animal: Animal) {
       return (animal as Cat);
   }
   function testCat(cat: Cat) {
       return (cat as Animal);
   }
   ```

#### 双重断言

```typescript
interface Cat {
    run(): void;
}
interface Fish {
    swim(): void;
}
// cat as fish 肯定会报错，因为 cat 和 fish 互相不兼容，但 cat as any as fish 则不会报错
function testCat(cat: Cat) {
    return (cat as any as Fish);
}
```

#### 类型断言 vs 类型转换

类型断言只会影响`TypeScript`编译时的类型，类型断言语句在编译结果中会被删除。

```typescript
function toBoolean(something: any): boolean {
    return something as boolean;
}

toBoolean(1);

// 编译后
function toBoolean(something) {
    return something;
}

toBoolean(1);
```

类型断言不是类型转换，它不会真的影响到变量的类型。

若要进行类型转换，需要直接调用类型转换的方法：

```typescript
function toBoolean(something: any): boolean {
    return Boolean(something);
}

toBoolean(1);
// 返回值为 true
```

#### 类型断言 vs 类型声明

```typescript
// 等价关系
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

// const tom = getCacheData('tom') as Cat;
const tom: Cat = getCacheData('tom');
tom.run();

// 不同点
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

const animal: Animal = {
    name: 'tom'
};
// let tom = animal as cat; // 不会报错，实现类型断言
let tom: Cat = animal; // 报错：error TS2741: Property 'run' is missing in type 'Animal' but required in type 'Cat'.
```

`Animal`可以看作`Cat`的父类，当然不能将父类的实例赋值给类型为子类的变量。

**核心区别**：`animal`断言为`Cat`，只需要`Animal`兼容`Cat`或`Cat`兼容`Animal`即可；`animal`赋值给`tom`，需要满足`Cat`兼容`Animal`才行。

类型声明是比类型断言更加严格。

#### 类型断言 vs 泛型

```typescript
function getCacheData<T>(key: string): T {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData<Cat>('tom');
tom.run();
```

通过给`getCacheData`添加一个泛型`<T>`，可以更加规范的实现对`getCacheData`返回值的约束，这也同时去除了代码中的`any`，是最优的解决方案。



### 声明文件

当使用第三方库时，需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

- `declare var`：声明全局变量
- `declare function`：声明全局方法
- `declare class`：声明全局类
- `declare enum`：声明全局枚举类型
- `declare namespace`：声明（含有子属性的）全局对象
- `interface`和`type`：声明全局类型
- `export`：导出变量
- `export namespace`：导出（含有子属性的）对象
- `export default`：`ES6`默认导出
- `export =`：`commonJS`导出模块
- `export as namespace`：`UMD`库声明全局变量
- `declare global`：扩展全局变量
- `declare module`：扩展模块
- `/// <reference />`：三斜线指令

声明语句通常会放到一个单独文件`xxx.d.ts`声明文件中。必须以`.d.ts`为后缀

#### 第三方声明文件

推荐`@types`统一管理第三方库的声明文件。

```shell
npm install @types/xxx --save-dev
```

#### 扩展全局变量

```typescript
// 扩展 String 类型，使用 interface String 可给 String 添加属性或方法
interface String {
    prependHello(): string;
}

'foo'.prependHello();

// 使用 declare namespace 给已有的命名空间添加类型声明
declare namespace JQuery {
    interface CustomOptions {
        bar: string;
    }
}

interface JQueryStatic {
    foo(options: JQuery.CustomOptions): string;
}
```



### 内置对象

`JavaScript`有许多内置对象，可以直接在`TypeScript`中当作定义好的类型使用。

内置对象是根据标准在全局作用域`global`上存在的对象。（标准指代`ECMAScript`其他环境比如`DOM`的标准）

#### `ECMAScript`的内置对象 

内置对象参考：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects
定义文件：https://github.com/Microsoft/TypeScript/tree/main/src/lib

`ECMAScript`标准提供的内置对象有：`Boolean`、`Error`、`Date`、`RegExp`等。

```typescript
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

#### `DOM`和`BOM`的内置对象

定义文件：https://github.com/Microsoft/TypeScript/tree/main/src/lib

`DOM`和`BOM`提供的内置对象有：`Document`、`HTMLElement`、`Event`、`NodeList`等。

```typescript
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

#### `TypeScript`核心库的定义文件

定义文件：https://github.com/Microsoft/TypeScript/tree/main/src/lib

定义了所有浏览器环境需要用到的类型，并且是预置在`TypeScript`中的。

```typescript
Math.pow(10, '2'); // 报错：error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.
// TypeScript 定义文件，声明 pow 函数必须接受两个 number 类型的参数，传入'2'产生报错。
interface Math {
    /**
     * Returns the value of a base expression taken to a specified power.
     * @param x The base value of the expression.
     * @param y The exponent value of the expression.
     */
    pow(x: number, y: number): number;
}
```

**PS：**`TypeScript`核心库的定义中不包含`Node.js`部分。

```shell
# 如果需要用 TypeScript 写 Node.js，则需要引入第三方声明文件
npm install @types/node --save-dev
```



### * 类型别名

类型别名用来给一个类型起个新名字。

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === 'string') {
    return n;
  } else {
    return n();
  }
}
```

上例使用`type`创建类型别名。类型别名常用于联合类型。



### * 字符串字面量类型

字符串字面量类型用于约束取值只能是某几个字符串中的一个。

```typescript
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dblclick'); // 报错，event 不能为 'dblclick'
```

上例使用`type`定义了一个字符串字面量类型`EventNames`，它只能取三种字符串中的一种。

**PS：**类型别名和字符串字面量类型都是使用`type`进行定义。



### * 元祖

数组合并了相同类型的对象，而元祖`tuple`合并了不同类型的对象。

```typescript
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;

tom[0].slice(1);
tom[1].toFixed(2);
```

#### 越界的元素

当添加越界的元素时，它的类型会被限制为元祖中每个类型的联合类型：

```typescript
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true); // 报错：Argument of type 'true' is not assignable to parameter of type 'string | number'.
```



### * 枚举

枚举`enum`类型用于取值被限定在一定范围内的场景，比如一周有七天等。

```typescript
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

// 编译为
var Days;
(function (Days) {
  Days[Days["Sun"] = 0] = "Sun";
  Days[Days["Mon"] = 1] = "Mon";
  Days[Days["Tue"] = 2] = "Tue";
  Days[Days["Wed"] = 3] = "Wed";
  Days[Days["Thu"] = 4] = "Thu";
  Days[Days["Fri"] = 5] = "Fri";
  Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));

```



### * 类

`TypeScript`中类的用法在`JavaScript`上进行补充：

#### 修饰符

可以使用三种访问修饰符`Access Modifiers`，分别是`public`、`private`和`protected`。

- `public`：修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是`public`的；
- `private`：修饰的属性或方法是私有的，不能在声明它的类的外部访问；
- `protected`：修饰的属性或方法是受保护的，它和`private`类似，区别是它在子类中也是允许被访问的。

#### `readonly`

`readonly`是只读属性关键字，只允许出现在属性声明或索引签名或构造函数中。

```typescript
class Animal {
  readonly name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom'; // 报错：TS2540: Cannot assign to 'name' because it is a read-only property.
```

**PS：**如果`readonly`和其他访问修饰符同时存在的时候，需要写在后面。

#### `abstract`

`abstract`用于定义抽象类和其中的抽象方法。

抽象类是不允许被实例化的，必须作为父类创建各类子类；抽象方法在抽象类中，必须被子类实现。

#### 类的类型

给类加上`typeScript`类型与接口类似：

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi(): string {
    return `My name is ${this.name}`;
  }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi());
```



### 类与接口

接口`interface`可以用于对对象的形状`shape`进行描述；接口的另一个作用：对类的一部分行为进行抽象。

#### 类实现接口

实现`implements`是面向对象的一个重要概念。一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，就可以把特性提取成接口`interface`，用关键字`implements`关键字来实现。

```typescript
/*
举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：
*/
interface Alarm {
	alert(): void;
}
interface Light {
  lightOn(): void;
  lightOff(): void;
}
class Door {
}
class SecurityDoor extends Door implements Alarm {
  alert() {
  	console.log('SecurityDoor alert');
  }
}

class Car implements Alarm, Light {
  alert() {
  	console.log('Car alert');
  }
  lightOn() {
  	console.log('Car light on');
  }
  lightOff() {
  	console.log('Car light off');
  }
}
```

#### 接口继承接口

接口和接口之间可以是继承关系

```typescript
interface Alarm {
  alert(): void;
}
interface LightableAlarm extends Alarm {
  lightOn(): void;
  lightOff(): void;
}
```

#### 接口继承类

接口`interface`可以继承类。

```typescript
/*
在声明 class Point 时，除了会创建一个名为 Point 的类之外，同时也创建了一个名为 Point 的类型（实例的类型）
既可以将 Point 当做一个类来用，也可以将 Point 当做一个类型来用
声明 Point 类时创建的 Point 类型只包含其中的实例属性和实例方法
*/
class Point {
  x: number;
  y: number;
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}
interface Point3d extends Point {
  z: number;
}
let point3d: Point3d = {x: 1, y: 2, z: 3};
```



### 泛型

泛型`generics`是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

```typescript
/*
在函数名后添加了 <T>，其中 T 用来指代任意输入的类型，在后面的输入 value: T 和输出 Array<T> 中即可使用
在调用的时候，可以指定它具体的类型为 string。当然，也可以不手动指定，而让类型推论自动推算出来
*/
function createArray<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

#### 多个类型参数

定义泛型的时候，可以一次定义多个类型参数：

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}
swap([7, 'seven']); // ['seven', 7]
```

定义了一个 `swap` 函数，用来交换输入的元组.

#### 泛型约束

在函数内部使用泛型变量时，由于事先不知道它是哪种类型，所以不能随意操作它的属性或方法。

```typescript
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);
    return arg;
}
// 报错：error TS2339: Property 'length' does not exist on type 'T'.
```

泛型`T`不一定包含属性`length`，所以编译的时候报错。

对泛型进行约束，只允许这个函数传入那些包含`length`属性的变量。

```typescript
interface Lengthwise {
  length: number;
}
function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

使用`extends`约束了泛型`T`必须符合接口`Lengthwise`的形状，也就是必须包含`length`属性。

```typescript
function copyFields<T extends U, U>(target: T, source: U): T {
  for (let id in source) {
    target[id] = (<T>source)[id];
  }
  return target;
}
let x = { a: 1, b: 2, c: 3, d: 4 };
copyFields(x, { b: 10, d: 20 });
```

多个类型参数之间也可以互相约束，其中要求`T`继承`U`，这样就保证了`U`上不会出现`T`中不存在的字段。

### 泛型接口

可以使用接口的方式来定义一个函数需要符合的形状：

```typescript
interface CreateArrayFunc {
  <T>(length: number, value: T): Array<T>;
}
let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
createArray(3, 'x'); // ['x', 'x', 'x']
// 可以把泛型参数提前到接口名上，在使用泛型接口的时候，需要定义泛型的类型
interface CreateArrayFunc<T> {
  (length: number, value: T): Array<T>;
}
let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
createArray(3, 'x'); // ['x', 'x', 'x']
```

### 泛型类

与泛型接口类似，泛型也可以用于类的类型定义中：

```typescript
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

### 泛型参数的默认类型

可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型将起作用。

```typescript
function createArray<T = string>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
```



### 声明合并

如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型。

#### 函数的合并

使用重载定义多个函数类型

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''));
  } else if (typeof x === 'string') {
    return x.split('').reverse().join('');
  }
}
```

#### 接口的合并

接口中的属性在合并时会简单的合并到一个接口中：

```typescript
interface Alarm {
  price: number;
}
interface Alarm {
  weight: number;
}
// 相当于
interface Alarm {
  price: number;
  weight: number;
}
```

**PS：**合并的属性的**类型**必须是唯一的；接口中方法的合并与函数的合并一样。

```typescript
interface Alarm {
  price: number;
  alert(s: string): string;
}
interface Alarm {
  weight: number;
  alert(s: string, n: number): string;
}
// 相当于
interface Alarm {
  price: number;
  weight: number;
  alert(s: string): string;
  alert(s: string, n: number): string;
}
```

#### 类的合并

类的合并与接口的合并规则一致。



### 扩展阅读

- [Never](http://www.typescriptlang.org/docs/handbook/basic-types.html#never)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Basic Types.html#never)）：永远不存在值的类型，一般用于错误处理函数
- [Variable Declarations](http://www.typescriptlang.org/docs/handbook/variable-declarations.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Variable Declarations.html)）：使用 `let` 和 `const` 替代 `var`，这是 [ES6 的知识](http://es6.ruanyifeng.com/#docs/let)
- [`this`](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Functions.html#this)：箭头函数的运用，这是 [ES6 的知识](http://es6.ruanyifeng.com/#docs/function)
- [Using Class Types in Generics](http://www.typescriptlang.org/docs/handbook/generics.html#using-class-types-in-generics)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Generics.html#在泛型里使用类类型)）：创建工厂函数时，需要引用构造函数的类类型
- [Best common type](http://www.typescriptlang.org/docs/handbook/type-inference.html#best-common-type)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type Inference.html#最佳通用类型)）：数组的类型推论
- [Contextual Type](http://www.typescriptlang.org/docs/handbook/type-inference.html#contextual-type)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type Inference.html#上下文类型)）：函数输入的类型推论
- [Type Compatibility](http://www.typescriptlang.org/docs/handbook/type-compatibility.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type Compatibility.html)）：允许不严格符合类型，只需要在一定规则下兼容即可
- [Advanced Types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#intersection-types)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced Types.html#交叉类型（intersection-types）)）：使用 `&` 将多种类型的共有部分叠加成一种类型
- [Type Guards and Differentiating Types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced Types.html#类型保护与区分类型（type-guards-and-differentiating-types）)）：联合类型在一些情况下被识别为特定的类型
- [Discriminated Unions](http://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced Types.html#可辨识联合（discriminated-unions）)）：使用 `|` 联合多个接口的时候，通过一个共有的属性形成可辨识联合
- [Polymorphic `this` types](http://www.typescriptlang.org/docs/handbook/advanced-types.html#polymorphic-this-types)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Advanced Types.html#多态的this类型)）：父类的某个方法返回 `this`，当子类继承父类后，子类的实例调用此方法，返回的 `this` 能够被 TypeScript 正确的识别为子类的实例。
- [Symbols](http://www.typescriptlang.org/docs/handbook/symbols.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Symbols.html)）：新原生类型，这是 [ES6 的知识](http://es6.ruanyifeng.com/#docs/symbol)
- [Iterators and Generators](http://www.typescriptlang.org/docs/handbook/iterators-and-generators.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Iterators and Generators.html)）：迭代器，这是 [ES6 的知识](http://es6.ruanyifeng.com/#docs/iterator)
- [Namespaces](http://www.typescriptlang.org/docs/handbook/namespaces.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Namespaces.html)）：避免全局污染，现在已被 [ES6 Module](http://es6.ruanyifeng.com/#docs/module) 替代
- [Decorators](http://www.typescriptlang.org/docs/handbook/decorators.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Decorators.html)）：修饰器，这是 [ES7 的一个提案](http://es6.ruanyifeng.com/#docs/decorator)
- [Mixins](http://www.typescriptlang.org/docs/handbook/mixins.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Mixins.html)）：一种编程模式，与 TypeScript 没有直接关系，可以参考 [ES6 中 Mixin 模式的实现](http://es6.ruanyifeng.com/#docs/class#Mixin模式的实现)