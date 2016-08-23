title: Redux
date: 2016-07-17 09:11:47
desc: 介绍 Redux 的基本使用用法基于与 React 的开发方式
---

> 我已经为写这篇文章准备了好几个星期，但是今天却不知道如何下笔

Redux 和 Flux 都是 Flux 模式


Flux 所提倡的单向数据流架构

- 单向数据流
- 状态可预测
- 时间旅行
- 中间件

Dan Abramov 自己在推上多次强调过，Redux 的设计是以几个原则为优先的：要让状态的变化可追踪，可重复，可维护。为了达成这个目的，才会有 reducer, action, action creator, middleware 这些概念。本来一个 callApi(res => a.b = res) 可以做到的事情，现在你需要先写全套然后配上 thunk middleware 才能做到。

<!-- more -->

###### 参考资料

- [Why use Redux over Facebook Flux?](http://stackoverflow.com/questions/32461229/why-use-redux-over-facebook-flux/32920459#32920459)
- [Redux 官方文档](http://redux.js.org/index.html)
- [Redux 中文文档](http://cn.redux.js.org//index.html)
- [How to dispatch a Redux action with a timeout?](http://stackoverflow.com/questions/35411423/how-to-dispatch-a-redux-action-with-a-timeout/35415559#35415559)
- [Redux Examples](https://github.com/reactjs/redux/blob/master/docs/introduction/Examples.md)
- [redux-tutorial-cn](https://github.com/react-guide/redux-tutorial-cn)
- [reselect](https://github.com/reactjs/reselect)
- [redux-devtools](https://github.com/gaearon/redux-devtools)
- [redux-thunk](https://github.com/gaearon/redux-thunk)
- [redux-undo](https://github.com/omnidan/redux-undo)
- [a-cartoon-intro-to-redux-cn](https://github.com/jasonslyvia/a-cartoon-intro-to-redux-cn)
- [classnames](https://github.com/JedWatson/classnames)