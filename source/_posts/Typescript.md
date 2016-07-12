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

## React and Webpack

TypeScript 的官方文档中对 React 的开发做了一个简单的演示，主要包含以下几个部分：

- 使用 tsconfig.json 作为 TypeScript 的编译配置文件
- 使用 webpack 作为构建工具，需要安装 webpack、ts-loader 和 source-map-loader
- 使用 typings 作为代码提示工具

具体的搭建流程可以参考文档 [React & Webpack](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html)，此外，我个人写过一个 [TypeScript & Webpack & React 开发的最小化模板](https://github.com/pinggod/react-startkit/tree/typescript)可供各位参考，与之等同的 [Babel & Webpack & React 版本](https://github.com/pinggod/react-startkit/tree/babel)。

###### 参考资料

- [TypeScript Document](https://www.typescriptlang.org/docs/tutorial.html)
- [What is TypeScript and why would I use it in place of JavaScript?](http://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript)
