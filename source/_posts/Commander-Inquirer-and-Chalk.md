title: Commander Inquirer Chalk
date: 2016-08-14 09:45:38
desc: node.js 中 commander / inquirer / chalk 三个命令行开发工具的基本使用方式
---

```js
npm install --save-dev lodash
```

如果你的工作接触过 Node.js，那么应该会对上面的命令很熟悉：使用 npm 安装第三方依赖。npm 作为 Node.js 的模块管理工具，在 Node.js 的社区发展中发挥了重要的作用。本来计划要详细介绍一下 Node.js 的终端开发，但是限于时间关系，就简陋的写了一段代码，演示 Commander / Inquirer / Chalk 的基本使用方式，这三个模块都可以使用 npm 安装，分别负责解析终端输入、提供终端交互接口和优化终端显示效果。

<!-- more -->

```js
#!/usr/bin/env node
const chalk = require('chalk');
const inquirer = require('inquirer');
const commander = require('commander');

// 默认的配置信息
const defaultConfig = {
    name: '',
    isSave: false
};
// 最终的配置信息
let config = {};
const success = chalk.green.bold;
const failure = chalk.red.bold;

// 通过 option() 定义的参数，都会挂载在 commander 下面
// 比如定义了 option('--save')，就会存在 commander.save
commander
    .option('-S, --save', '将模块归类为 dependencies 模块')
    // .option('--save-dev')
    // ...

// 通过 command() 定义的命令，都会被作为参数传递给 action()
// 比如定义了 command('install <name>')
// 这里的 name 就是命令参数
commander
    .command('install <name>')
    .alias('i')
    .description('安装模块')
    .action(name => {
        let questions = [];

        if (commander.save) {
            config = Object.assign({}, defaultConfig, {
                name: name,
                isSave: true
            });

            console.log(success(JSON.stringify(config, 0, 4)));
        }
        else {
            questions.push({
                type: 'confirm',
                name: 'isSave',
                message: '是否将模块归类为 dependencies 模块'
            })

            inquirer.prompt(questions).then(function (answers) {
                config = Object.assign({}, defaultConfig, {
                    name: name,
                    isSave: answers.isSave
                });

                console.log(failure(JSON.stringify(config, 0, 4)));
            })
        }
    })

// 如果需要设置多个 command
// 继续沿用上述的配置格式
// ----------------------
// commander
//     .command('uninstall')
//     .alias('ui')
//     .description('卸载模块')
//     .action(option => {})

commander.parse(process.argv)

```

###### 参考资料

- [chalk](https://github.com/chalk/chalk)
- [inquirer.js](https://github.com/SBoudrias/Inquirer.js)
- [commander.js](https://github.com/tj/commander.js)
