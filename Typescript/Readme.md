
# TypeScript Learning

- 目录
  - [基础类型](#基础类型)
  - [变量声明](#变量声明)
  - [接口](#接口)
  - [类](#类)
  - [函数](#函数)
  - [泛型](#泛型)
-
- 参考
  - [ref1: Typescript中文手册](https://typescript.bootcss.com/generics.html)
  - [ref2：深入理解typescript](https://jkchao.github.io/typescript-book-chinese/typings/literals.html#%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%AD%97%E9%9D%A2%E9%87%8F)
-

## 基础类型

1. boolean
2. number
3. string
4. array
5. tuple 元组
6. enum
7. any
8. void
9. null
10. undefined
11. never

> ### 类型断言
>
> 使用尖括号:$\color{green}{(<type>varName).function}$
> as语法：$\color{green}{varName~~~as~~~type}$

## 变量声明
>
>let 推荐，使用块级作用域或词法级作用域
>const 赋值后不能
>var var作用域：声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问，常见的问题是多次声明同一个变量并不会报错（函数作用域）
>
>### 解构
>>
>>解构数组：let [a, b] = [1, 2];
>>解构对象：let {a, b} = o; //o = {a: 1, b: 2, c = 3}
>>属性重命名：let { a: newName1, b: newName2 } = o;
>>
>
>### 展开
>>
>>允许你将一个数组展开为另一个数组，或将一个对象展开为另一个对象
>>
```typescript
//展开数组
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5]; // [0, 1, 2, 3, 4, 5]

//展开对象
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" }; //{ food: "rich", price: "$$", ambiance: "noisy" }
```
>>
>>$\color{green}{如果出现重复，展开对象后面的属性会覆盖前面的属性}$
>>$\color{green}{展开对象仅包含属性，会\color{red}{丢失其方法}}$
>
---

## 接口

**注意**：类型检查器，不会检查属性的顺序，只要相应的属性存在并且类型存在就行

>### 可选属性
>
>interface的某些非required属性名的末尾添加？表明该属性是可选的（optional property）
>
```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}
```
>
>1.可以对可能存在的属性进行预定义；
>2.可以捕获引用不存在属性时的错误
>
---
>
>### 额外的属性检查
>
```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```
>
>传入值时，对象字面量会被特殊对待而且会经过***额外属性检查***，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，报错。
>绕开方案：$\color{green}{使用类型断言~as}$
>
---
>
>### 函数类型
>
>给接口定义一个调用签名，一个只有参数列表和返回值类型的函数定义，对于函数类型得类型检查，函数的参数名不需要与接口里定义的名字相匹配
>
```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```
>
---
>
>### 可索引的类型
>
>可索引类型具有一个**索引签名**，它描述了对象索引的**类型**，还有相应的**索引返回值类型**  
>共有支持两种索引签名：**字符串**和**数字**, 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型
>
```typescript
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
>
> 可以同时使用两种类型的索引，但是**数字索引的返回值必须是字符串索引返回值类型的子类型**
>
```typescript
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用'string'索引，有时会得到Animal!
interface NotOkay {     //调换一下保证数字类型是子类型就可以
    [x: number]: Animal;    //number:Dog
    [x: string]: Dog;       //string:Animal
}
```
>
---
>
>### 混合类型
>
>一个对象可以同时做为函数和对象使用，并带有额外的属性，使用第三方库时可能需要完整地定义类型
>
---
>
>### 接口继承类
>
>当接口继承了一个类类型时，它会继承类的成员但**不包括其实现**。
> 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）
> 
>
```typescript
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}
//SelectableControl接口只能被Control的子类实现
//即要想实现这个接口必须继承自Control
class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {

}

// Error: Property 'state' is missing in type 'Image'.
class Image implements SelectableControl {
    select() { }
}

class Location {

}
```
>
---

## 类
>
>$\color{green}{派生类离访问this属性，必须调用super()}$
>抽象类可以包含成员的实现细节（abstract定义抽象类 或 抽象方法）
>
>### 构造函数
>
>在TypeScript里声明了一个类的时候，首先是类的实例的类型；然后是静态部分（目前个人理解是静态属性、方法、构造函数、构造函数类型）
>类定义会创建两个东西：类的实例类型和一个构造函数
>
```typescript
//typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
```
>
>转成Javascript
>
```javascript
//javascript
let Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

let greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
```
>
>类具有实例部分与静态部分[TS静态部分和实例部分](https://juejin.cn/post/6844903881860874254)
>
>
---
>
## 函数
>
>### 定义函数类型
>
>函数类型包含两部分：参数类型和返回值类型。 当写出完整函数类型的时候，这两部分都是需要的。
>
>### 剩余参数'...'
>
>剩余参数会被当做个数不限的可选参数。 可以一个都没有，同样也可以有任意个。 编译器创建参数数组，名字是你在省略号（...）后面给定的名字
>
>### this
>
>this的值在函数被调用时才会指定，需要弄清楚函数调用的上下文是什么
>[typescript中的this](https://zhuanlan.zhihu.com/p/104565681)
>[javascript中的this关键字](https://www.quirksmode.org/js/this.html)
>
>1. 对象方法调用
>2. 普通函数调用
>3. 构造函数调用
>
>**回调函数里的this参数**
>因为当回调函数被调用时，它会被当成一个普通函数调用，this将为undefined
>
>
>
>
>
---

## 泛型

>
>### 泛型变量
>
>把泛型变量T当做类型的一部分使用，而不是整个类型，增加了灵活性
>
>### 泛型类型
>
>**基本方式**
>
```typescript
function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: <T>(arg: T) => T = identity;
```
>
>
>**使用带有调用签名的对象字面量**
>
```typescript
function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: {<T>(arg: T): T} = identity;
```
>
>
>
>**基本方式**
>
```typescript
```
>
>

