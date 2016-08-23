title: TypeScript
date: 2016-07-10 19:07:01
desc: 介绍 TypeScript 的基础用法
---

TypeScript 是 JavaScript 的超集，为 JavaScript 的生态增加了类型机制，并最终将代码编译为纯粹的 JavaScript 代码。类型机制很重要吗？最近的一些项目经历让我觉得这真的很重要。当你陷在一个中大型项目中时（Web 应用日趋成为常态），没有类型约束、类型推断，总有种牵一发而动全身的危机和束缚。Immutable.js 和 Angular 2 都在使用 TypeScript 做开发，它们都是体量颇大的项目，所以我决定尝试一下 Typescript。此外我们还可以尝试 Facebook 的 Flow，比较一下两者的优劣。Typescript 对 ES6 也有良好的支持，目前组内项目使用 Babel 编译 ES6，这也就自然而然的把 TypeScirpt 和 Flow / babel-plugin-tcomb 放在了对立面，也许下一篇文章就是介绍 Flow 和 babel-plugin-tcomb。

<!-- more -->

## What and Why

如果你想对 TypeScript 有更深入的认识，那么推荐你阅读 Stack Overflow 上的问答 [What is TypeScript and why would I use it in place of JavaScript?](http://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript) ，这一节也是对这篇问答的一个简述。

虽然 JavaScript 是 ECMAScript 规范的标准实现，但并不是所有的浏览器都支持最新的 ECAMScript 规范，这也就限制了开发者使用最新的 JavaScript / ECMAScript 特性。TypeScript 同样支持最新的 ECMAScript 标准，并能将代码根据需求转换为 ES 3 / 5 / 6，这也就意味着，开发者随时可以使用最新的 ECMAScript 特性，比如 module / class / spread operator 等，而无需考虑兼容性的问题。ECMAScript 所支持的类型机制非常丰富，包括：interface、enum、hybird type 等等。

与 TypeScript 相似的工具语言还有很多，它们主要分为两个阵营，一个是类似 Babel 的阵营，以 JavaScript 的方式编写代码，致力于为开发者提供最新的 ECMAScript 特性并将代码编译为兼容性的代码；另一个则是 Coffeescript、Clojure、Dart 等的阵营，它们的语法与 JavaScript 迥然不同，但最终会编译为 JavaScript。TypeScript 在这两者之间取得了一种平衡，它既为 JavaScript 增加了新特性，也保持了对 JavaScript 代码的兼容，开发者几乎可以直接将 `.js` 文件重命名为 `.ts` 文件，就可以使用 TypeScript 的开发环境，这种做法一方面可以减少开发者的迁移成本，一方面也可以让开发者快速上手 TypeScript。

JavaScript 是一门解释型语言，变量的数据类型具有动态性，只有执行时才能确定变量的类型，这种后知后觉的认错方法会让开发者成为调试大师，但无益于编程能力的提升，还会降低开发效率。TypeScript 的类型机制可以有效杜绝由变量类型引起的误用问题，而且开发者可以控制对类型的监控程度，是严格限制变量类型还是宽松限制变量类型，都取决于开发者的开发需求。添加类型机制之后，副作用主要有两个：增大了开发人员的学习曲线，增加了设定类型的开发时间。总体而言，这些付出相对于代码的健壮性和可维护性，都是值得的。

目前主流的 IDE 都为 TypeScript 的开发提供了良好的支持，比如 Visual Studio / VS Code、Atom、Sublime 和 WebStorm。TypeScript 与 IDE 的融合，便于开发者实时获取类型信息。举例来说，通过代码补全功能可以获取代码库中其他函数的信息；代码编译完成后，相关信息或错误信息会直接反馈在 IDE 中……

在即将发布的 TypeScript 2.0 版本中，将会有许多优秀的特性，比如对 null 和 undefined 的检查。`cannot read property 'x' of undefined` 和 `undefined is not a function` 在 JavaScript 中是非常常见的错误。在 TypeScript 2.0 中，通过使用 `non-nullable` 类型可以避免此类错误：`let x : number = undefined` 会让编译器提示错误，因为 undefined 并不是一个 number，通过 `let x : number | undefined = undefined` 或 `let x : number? = undefined` 可以让 x 是一个 nullable（undefined 或 null） 的值。如果一个变量的类型是 nullable，那么 TypeScript 编译器就可以通过控制流和类型分析来判定对变量的使用是否安全：

```ts
let x : number?;
if (x !== undefined)
    // this line will compile, because x is checked.
    x += 1;

// this line will fail compilation, because x might be undefined.    
x += 1;
```

TypeScript 编译器既可以将 source map 信息置于生成的 `.js` 文件中，也可以创建独立的 `.map` 文件，便于开发者在代码运行阶段设置断点、审查变量。此外，TypeScript 还可以使用 decorator 拦截代码，为不同的模块系统生成模块加载代码，解析 JSX 等。

## Usage

这一节介绍 TypeScirpt 的一些基础特性，算是抛砖引玉，希望引起大家尝试和使用 TypeScript 的兴趣。首先，从最简单的类型标注开始：

```ts
// 原始值
const isDone: boolean = false;
const amount: number = 6;
const address: string = 'beijing';
const greeting: string = `Hello World`;

// 数组
const list: number[] = [1, 2, 3];
const list: Array<number> = [1, 2, 3];

// 元组
const name: [string, string] = ['Sean', 'Sun'];

// 枚举
enum Color {
    Red,
    Green,
    Blue
};
const c: Color = Color.Green;

// 任意值：可以调用任意方法
let anyTypes: any = 4;
anyTypes = 'any';
anyTypes = false

// 空值
function doSomething (): void {
    return undefined;
}

// 类型断言
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

TypeScript 中的 Interface 可以看做是一个集合，这个集合是对对象、类等内部结构的约定：

```ts
// 定义接口 Coords
// 该接口包含 number 类型的 x，string 类型的 y
// 其中 y 是可选类型，即是否包含该属性无所谓
interface Coords {
	x: number;
	y?: string;
};

// 定义函数 where
// 该函数接受一个 Coords 类型的参数 l
function where (l: Coords) {
	// doSomething
}

const a = { x: 100 };
const b = { x: 100, y1: 'abc' };

// a 拥有 number 类型的 x，可以传递给 where
where(a);
// b 拥有 number 类型的 x 和 string 类型的 y1，可以传递给 where
where(b);

// 下面这种调用方式将会报错，虽然它和 where(b) 看起来是一致的
// 区别在于这里传递的是一个对象字面量
// 对象字面量会被特殊对待并经过额外的属性检查
// 如果对象字面量中存在目标类型中未声明的属性，则抛出错误
where({ x: 100, y1: 'abc' });

// 最好的解决方式是为接口添加索引签名
// 添加如下所示的索引签名后，对象字面量可以有任意数量的属性
// 只要属性不是 x 和 y，其他属性可以是 any 类型
interface Coords {
	x: number;
	y?: string;
    [propName: string]: any
};
```

上面的代码演示了接口对对象的约束，此外，接口还常用于约束函数的行为：

```ts
// CheckType 包含一个调用签名
// 该调用签名声明了 getType 函数需要接收一个 any 类型的参数，并最终返回一个 string 类型的结果
interface CheckType {
    (data: any): string;
};

const getType: CheckType = (data: any) : string => {
    return Object.prototype.toString.call(data);
}

getType('abc');
// => '[object String]'
```

与老牌强类型语言 C#、Java 相同的是，Interface 也可以用于约束类的行为：

```ts
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

#### class

除了 ES6 增加的 Class 用法，TypeScript 还增加了 C++、Java 中常见的 public / protected / private 限定符，限定变量或函数的使用范围。TypeScript 使用的是结构性类型系统，只要两种类型的成员类型相同，则认为这两种类型是兼容和一致的，但比较包含 private 和 protected 成员的类型时，只有他们是来自同一处的统一类型成员时才会被认为是兼容的：

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
// Error: Animal and Employee are not compatible
animal = employee;
```

抽象类是供其他类继承的基类，与接口不同的是，抽象类可以包含成员方法的实现细节，但抽不可以包含抽象方法的实现细节：

```ts
abstract class Animal {
    // 抽象方法
    abstract makeSound(): void;
    // 成员方法
    move(): void {
        console.log('roaming the earch...');
    }
}
```

#### function

添加类型机制的 TypeScript 在函数上最可以秀的一块就是函数重载了：

```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
let pickedCard2 = pickCard(15);

console.log("card: " + pickedCard1.card + " of " + pickedCard1.suit);
console.log("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

编译器首先会尝试匹配第一个函数重载的声明，如果类型匹配成功就执行，否则继续匹配其他的重载声明，因此参数的针对性越强的函数重载，越要靠前声明。

#### genrics

```ts
function identity<T>(arg: T[]): T[] {
    console.log(arg.length);
    return arg;
}

let myIdentity: {<T>(arg: T[]): T[]} = identity;
```

上面的代码展示了泛型的基本用法，这里的 `<T>` 称为泛型变量，通过这个声明，我们可以确定传入的参数类型和返回的数据类型是一致的，一旦确定了传入的参数类型，也就确定了返回的数据类型。`myIdentity` 使用了带有调用签名的对象字面量定义泛型函数，实际上可以结合接口，写出更简洁的泛型接口：

```ts
interface IdentityFn {
     <T>(arg: T[]): T[];
};

let myIdentity: IdentityFn = identity;
```

如果同一个泛型变量在接口中被反复使用，那么可以在定义接口名的同时声明泛型变量：

```ts
interface IdentityFn<T> {
    (arg: T[]): T[];
};

function identity<T>(arg: T[]): T[] {
    console.log(arg.length);
    return arg;
}

let myIdentity: IdentityFn<string> = identity;
```

在泛型接口之外，还可以使用泛型类，两者的形式非常类似：

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}
```

泛型也可以直接继承接口约束自己的行为：

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

#### type inference

TypeScript 主要有两种类型推断方式：Best Common Type 和 Contextual Type。我们先介绍 Best Common Type:

```ts
let x = [0, 1, null];
```

对于上面代码中的变量 x，如果要推断出它的类型，就必须充分考虑 `[0, 1, null]` 的类型，所以这里进行类型推断的顺序是从表达式的叶子到根的，也就是先推断变量 x 的值都包含什么类型，然后总结出 x 的类型，是一种从下往上的推断过程。

TypeScript 的类型推论也可以按照从上往下的顺序进行，这被称为 **Contextual Type**：

```ts
window.onmousedown = function(mouseEvent) {
    // Error: Property 'button' does not exist ontype 'MouseEvent'
    console.log(mouseEvent.buton);  
};
```

在上面的示例中，TypeScript 类型推断机制会通过 `window.onmousedown` 函数的类型来推断右侧函数表达式的类型，继而推断出 `mouseEvent` 的类型，这种从上到下的推断顺序就是 **Contextual Type** 的特征。

这里只对 TypeScript 的特性做简单的介绍，更详细的资料请参考以下资料：

- [TypeScript 官方文档](https://www.typescriptlang.org/docs/tutorial.html)
- [TypeScript 中文文档](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Basic%20Types.html)
- [TypeScript Language Specification](https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md)

## React and Webpack

在 TypeScript 中开发 React 时有以下几点注意事项：

- 对 React 文件使用 `.tsx` 的扩展名
- 在 tsconfig.json 中使用 `compilerOptions.jsx: 'react'`
- 使用 typings 类型定义

```js
interface Props {
    foo: string;
}

class MyComponent extends React.Component<Props, {}> {
    render() {
        return <span>{this.props.foo}</span>
    }
}

<MyComponent foo="bar" />; // 正确
```

TypeScript 的官方文档中对 React 的开发做了一个简单的演示，主要包含以下几个部分：

- 使用 tsconfig.json 作为 TypeScript 的编译配置文件
- 使用 webpack 作为构建工具，需要安装 webpack、ts-loader 和 source-map-loader
- 使用 typings 作为代码提示工具

具体的搭建流程可以参考文档 [React & Webpack](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html)，此外，我个人写过一个 [TypeScript & Webpack & React 开发的最小化模板](https://github.com/pinggod/react-startkit/tree/typescript)可供各位参考，与之等同的 [Babel & Webpack & React 版本](https://github.com/pinggod/react-startkit/tree/babel)。

> 如果查看模板之后对 `import * as React from 'react'` 的方式有所疑惑，请查看 TypeScript 的负责人 Anders Hejlsberg 在 [issue#2242](https://github.com/Microsoft/TypeScript/issues/2242#issuecomment-83694181) 中的详细解析。

###### 参考资料

- [TypeScript Document](https://www.typescriptlang.org/docs/tutorial.html)
- [What is TypeScript and why would I use it in place of JavaScript?](http://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript)
- [TypeScript 中文文档](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Basic%20Types.html)
