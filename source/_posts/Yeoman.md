title: Yeoman
desc: 使用 Yeoman 实现一个简单的脚手架工具
date: 2016-09-14 17:41:10
---

Yeoman 给自己的定位是 scaffolding tool，直译过来就是脚手架工具，说实话我并不觉得**脚手架**这个用词很恰当。假设我们要初始化一个前端项目，通常少不了创建图片、JavaScript、CSS 等静态资源文件夹，如果是在团队中，还要添加 `.gitignore`、`.editorconfig`、`.eslintrc`、`README.md` 等配置文件，总之这是一个机械性的重复工作。Yeoman 的作用就是减少这些重复性的工作，通过调用 Yeoman 生态圈中的生成器，即可自动生成项目初始化所需要的文件结构、配置文件等等，所以就个人而言，Yeoman 是一个用于初始化项目的模版工具。

上面介绍的 Yeoman 功能通过 GitHub 也可以实现：我们将项目初始化的文件结构和静态资源存储在 GitHub 的特定仓库中，需要开发新项目的时候直接 `git clone` 下来再微调部分配置参数，比如项目名称这个参数，可能会出现在你的 `README.md`、`index.html` 和 `package.json` 等多个文件中，而且会要求保持一致。如果需要调整的配置参数又多又杂怎么办？上面说了 Yeoman 是一个模版工具，就像是在 Node.js 中使用 ejs 模板一样，在 Yeoman 中也可以使用 ejs 模版往项目中添加动态变量，在初始化项目时，这项变量会被渲染为预期的值，从而实现自动调整配置参数的功能。

填充模版，功能听起来不错，但也不是不能使用其他工具实现，比如我自己写脚本来执行参数调整。所以，在这里要说明两点：Yeoman 不是一个必不可少的工具，完全可以通过自己的其他技能树实现相同的功能；Yeoman 本身封装的功能足够完善，应对常规的需求没有问题，它的生成器社区也很活跃，是个值得一试的好工具。

<!-- more -->

以上就是 Yeoman 的一些基础特性，我也没有使用多长时间，但上手快效果好，所以在这里记录一下。之所以最近在使用 Yeoman，是因为最近在写的 koa2 和 react 模版遇到了很多需要人工调整的工作，然后才想起使用 Yeoman，至于使用后有多大的提升效果，目前还不敢下结论。本文的主要内容是创建一个 Hello World 级别的 Yeoman 生成器，整个过程很简单，只需要四步。这里说它很简单，肯定是因为我单方面假设你已经点亮了某些技能树……

## 配置开发环境

安装 Yeoman 的命令行工具 `yo` 和专用于开发 Yeoman 生成器的工具 `generator-generator`：

```bash
npm install -g yo generator-generator
```

这里使用了 `-g` 进行全局安装，便于在全局环境下直接调用这两个工具。使用它们创建一个生成器的初始化目录：

```bash
yo generator
```

执行上述命令后，Yeoman 会在终端向开发者提出一个问题，是一个交互式的配置过程。配置完成后， Yeoman 会自动安装项目依赖，项目结构如下所示：

```bash
➜ tree -L 3
.
├── LICENSE
├── README.md
├── generators
│   └── app
│       ├── index.js
│       └── templates
├── gulpfile.js
├── node_modules
├── package.json
└── test
    └── app.js
```

## 接收配置参数

在上面的目录结构中，我们一般只需要关心两部分，它们都在 `generators` 文件夹下：`app/index.js` 文件和 `app/templates` 文件夹，前者是生成器的入口文件，后者存放在了模版文件。接收配置参数这一工作，就是在 `app/index.js` 文件中进行的，本质上使用的是第三方依赖 inquirer：

```js
'use strict';
var yeoman = require('yeoman-generator');
var chalk = require('chalk');
var yosay = require('yosay');

module.exports = yeoman.Base.extend({
    // 1. 接收参数
    prompting: function () {
        var prompts = [{
            type: 'input',
            name: 'appName',
            message: 'Give your app a name: ',
            default: 'yoAppName'
        }];

        return this.prompt(prompts).then(function (props) {
            // 1.1 将参数挂载在 this 下
            this.props = props;
        }.bind(this));
    },

    // 2. 渲染模版
    writing: function () {
        // ...
    },

    // 3. 安装依赖
    install: function () {
        this.npmInstall();
    }
});
```

在这一小节，我们只关注 1 处的接收参数功能：封装了 inquirer，在终端提供一个交互式的配置过程，比如这里设置了一个需要输入应用名称的问答，用户需要输入一个通用名称作为整个项目的名称，如果未输入，则使用默认值 'yoAppName'。最后，用户所有的输入都会通过 props 这个参数传递给 this 对象，便于在 2 处和 3 处继续使用。如果你想了解更多有关交互式配置的信息，请参考 [inquirer](https://github.com/SBoudrias/Inquirer.js) 的官方文档。

## 渲染模版

接收到用户输入之后，我们就可以在 2 处将相关的变量渲染到模版文件中了：

```js
writing: function () {
    this.fs.copyTpl(
        this.templatePath('bin/**/*'),
        this.destinationPath('bin/'),
        {appName: this.props.appName}
    );

    this.fs.copy(
        this.templatePath('.*'),
        this.destinationPath()
    );

    this.fs.copyTpl(
        this.templatePath('package.json'),
        this.destinationPath('package.json'),
        {appName: this.props.appName}
    );

    this.fs.copyTpl(
        this.templatePath('README.md'),
        this.destinationPath('README.md'),
        {appName: this.props.appName}
    );

    this.fs.copy(
        this.templatePath('LICENCE'),
        this.destinationPath('LICENCE')
    );
}
```

首先是两个方法：`copyTpl` 和 `copy`，它们的第一个参数都是模版文件路径，第二个参数是生成文件的路径，不同之处在于 `copyTpl` 的第三个参数是一个对象，用于像模版中填充数据，比如这里 `appName` 会传递给模版，在模版中通过 `<%= appName %>` 就可以获取到，模板文件的内容比较多，可以前往 [generator-pinggod-koa 的项目主页](https://github.com/pinggod/generator-pinggod-koa)查看。

## 安装测试

这里是收尾工作，使用 npm 的工具将生成器安装到全局，在我们创建的生成器的根目录下：

```bash
npm link
```

完成后，创建一个空目录，执行 Yeoman 命令：

```bash
mkdir koa-in-action
cd koa-in-action
yo pinggod-koa
```

使用编辑器打开 `koa-in-action` 目录，应该就可以看到我们模板文件都初始化好了。最后，可以将这一生成器通过 `npm publish` 发不到 npm 社区便于自己和其他开发者使用。

###### 参考资料

- [Create A Custom Yeoman Generator in 4 Easy Steps](https://scotch.io/tutorials/create-a-custom-yeoman-generator-in-4-easy-steps)