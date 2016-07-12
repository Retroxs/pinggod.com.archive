title: mac tool kit
date: 2015-12-26 18:17:00
---

这会是一篇持续更新的文章，用于记录我在 mac 中常用的开发工具。过去的一年（2015）里，我使用 mac 作为开发的主力平台，在上面折腾过各类软件，以实现最高的开发效率和最舒适的开发环境。一切都是为了开发成果服务，脱离结果强调过程不是我支持的态度，简而言之，这些都是一些增益技巧。

文中工具的排序规则（核心是优先安装具有依赖关系的软件）：

1. 从常用工具中随机选择一个工具作为 random seed
2. 根据 random seed 的配置过程进行上溯，上溯到的目标工具排在 random seed 之前
3. 如果上溯到的系统工具不常用，以常用工具替换，比如 chrome 替换 safari
4. random seed 可组合使用的工具排在 random seed 之后
5. random seed 中包含的插件在 random seed 中以列表的形式列出
6. 每次接触新工具，将 random seed 设为该工具，循环执行 2、3、4 过程
7. 其他工具根据肌肉对键盘的非条件反射随机排列

![mac tool kit 排列方法 2015-12-27.png](/img/mac-tool-kit.png)

<!-- more -->

## preparation

在选择下文中的工具时，主要有两点参考标准：

- 快速，指响应速度和切换速度，或者可以提高这两点的工具。目前键盘操作是无冲突下最快速的控制方式，所以这也是围绕速度进行评估的关键点。
- 界面有设计亮点，这纯属前端职业病

<div class="tip">
    初始化 mac 之后，依次进入系统偏好设置 -> 键盘 -> 修饰键，将 Caps Lock 键映射为 Control 键，别问为什么，用心去体会吧 —— Casp Lock 的功能价值与它在键盘布局中所占有的重要位置极度不相符。
</div>
 
## applications list

1. [iTerm2](https://www.iterm2.com/downloads.html)，终端工具，替代系统自带的终端，主题 [dracula-theme](https://github.com/zenorocha/dracula-theme)。
    - Xcode command Line Tool，Homebrew 的依赖，可通过 `xcode-select --install` 命令或者安装 Xcode 来完成
    - [ici](https://github.com/Flowerowl/ici)，终端词典，基于爱词霸词库 

1. [Xcode](https://developer.apple.com/cn/xcode/downloads/)，苹果产品开发环境，

1. [Homebrew](http://brew.sh/)，OS X 的包管理工具：
    - [nvm](https://github.com/creationix/nvm)，node 版本管理工具
    - [node](https://github.com/nodejs/node)，运行在服务端的 JavaScript，使用 nvm 统一管理
    - [tmux](https://tmux.github.io/)，终端复用工具
    - [tree](http://mama.indstate.edu/users/ice/tree/)，树状结构目录
    - [python / python3](https://www.python.org)，python 开发环境
    - [nginx](http://nginx.org/)，反向代理服务器
    - [httpie](https://github.com/jakubroztocil/httpie)，替代 curl 的 HTTP 终端工具
    - [asciinema](https://asciinema.org/)，终端录屏工具

1. [Homebrew Cask](http://caskroom.io/)，使用 hombrew 安装，可用于安装 OS X 应用：
    - [haskell platform](http://caskroom.io/search)，haskell 开发环境

1. [Chrome / Chrome Canary](http://www.google.cn/intl/zh-CN/chrome/browser/desktop/index.html)，插件众多，开发调试便利，平时也比较依赖谷歌体系内的东西。插件：
    - [GitHub Old Header](https://chrome.google.com/webstore/detail/github-old-header/bbencfiifelhglgknaheifiekmjndlek)，在顶部导航区提供一个指向个人页面的链接
    - [HTTP status](https://chrome.google.com/webstore/detail/http-status/cknfnacbckhfpjahnmkblajcpledpfnp)，在地址栏显示 HTTTP 状态码
    - [JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=zh-CN)，格式化浏览器预览到的 JSON 数据
    - [Octotree](https://chrome.google.com/webstore/detail/octotree/bkhaagjahfmjljalopjnoealnfndnagc?hl=zh-CN)，为 GitHub 仓库提供一个树状结构目录
    - [Save to Pocket](https://chrome.google.com/webstore/detail/save-to-pocket/niloccemoadcdkdjlinkgdfekeahmflj?hl=zh-CN)，右键保存到 Pocket

1. [Sublime Text 3](http://www.sublimetext.com/3)，编辑器，适用多平台响应迅速扩展能力强。插件：
    - [Package Control](https://packagecontrol.io/installation#st3)，Sublime 扩展插件的安装和管理工具
    - [DashDoc](https://packagecontrol.io/packages/DashDoc)，调用 Dash
    - [Emmet](http://emmet.io/)，前端开发工具集
    - [BracketHighlighter](https://packagecontrol.io/packages/BracketHighlighter)，标签和符号的高亮工具
    - [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements)，侧边栏功能扩展插件
    - [Material Theme](https://github.com/equinusocio/material-theme)，谷歌 material 风格的简洁主题
    - [Babel](https://packagecontrol.io/packages/Babel)，JSX 和 ES6 的高亮插件，不具有编译功能
    - [DocBlockr](https://packagecontrol.io/packages/DocBlockr)，Javascript, PHP, CoffeeScript, Actionscript, C & C++ 规范化注释

1. [Dash](https://kapeli.com/dash)，开发文档、代码片段管理工具

1. [alfred](https://www.alfredapp.com/)，必备辅助工具，提高工作效率，不要让双手离开键盘

1. [Pocket](https://getpocket.com/)，离线阅读工具，也被用来做知识管理

1. [Snip](http://snip.qq.com/)，滚动截屏必备工具

1. [Ember](http://realmacsoftware.com/ember/)，图库管理，可以订阅 dribbble popular。

1. [Microsoft office](https://products.office.com/zh-cn/mac/microsoft-office-for-mac)，我喜欢用 PowerPoint 来做流程图、序列图……

1. [SizeUp](http://www.irradiatedsoftware.com/sizeup/)，管理应用程序窗口的位置和大小

## host configuration

最近在熟悉云主机的使用，下面是配置过程：

1. ssh root 账号登录云主机，通过 `passwd` 命令更改主机密码，至于怎样设置结构复杂的密码，建议参考文章[《每一个程序员都有一颗当诗人的心》](http://www.hello-code.com/diary/201409/2223.html)。

1. 使用 root 账号创建普通用户：`adduser sean`、`passwd sean`，然后为新用户配置权限，通过 `visudo` 命令添加 `sean ALL=(ALL) ALL` 配置信息，完成后退出 root 账户，使用普通账户登录云主机，比如这里的 sean。

1. 安装常用工具：
    - [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
    - [amix/vimrc](https://github.com/amix/vimrc)
    - [bling/vim-airline](https://github.com/bling/vim-airline)
    - [powerline/fonts](https://github.com/powerline/fonts)
    - [nvm](https://github.com/creationix/nvm)
    - [node](https://github.com/nodejs/node)
    - [node:tldr](https://github.com/tldr-pages/tldr)
    - [apt-get:tree](https://packages.debian.org/search?keywords=tree)

## tldr

不断地在中英文间进行切换也是非常低效率的操作，所以，初步设定在 2016 年底脱离非业务开发下对中文输入的依赖——不过我最喜欢的哲学思维还是先秦百家交叉融汇出的框架。