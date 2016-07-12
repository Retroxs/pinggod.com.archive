title: Koa2
date: 2016-05-07 09:29:38
desc: Koa2 基础用法、核心概念、相关中间件
---

Koa 给自己的定位是 HTTP 中间件框架（middleware framework），专注于提供与创建 HTTP 服务器有关的通用方法和属性，本身不捆绑任何中间件，由开源社区根据实际需求开发具体的中间件。

Koa 使用 `app.use()` 方法注册中间件，并按照注入顺序将其添加到 middleware 数组，这些中间件常用于对 HTTP 请求进行加工处理，比如生成缓存、指定代理以及重定向等。

```js
const Koa = require('koa');
const app = new Koa();

// response
app.use(ctx => {
    ctx.body = 'Hello Koa';
});

app.listen(3000)
```

<!-- more -->

## Middleware

Koa2 支持以下三种中间件函数：

```js
// common function 
app.use((ctx, next) => {
    const start = new Date();
    return next().then(() => {
        const ms = new Date() - start;
        console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
    });
});

// async function
app.use(async (ctx, next) => {
    const start = new Date();
    await next();
    const ms = new Date() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});

// generator function
.use(co.wrap(function *(ctx, next) {
    const start = new Date();
    yield next();
    const ms = new Date() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
}));
```

由于 Node.js 尚未支持 async 函数，所以需要使用 Babel 预编译 JS 文件，我的做法是安装依赖 `babel-core babel-polyfill babel-preset-es2015 babel-preset-stage-0`，然后在真实的入口文件（比如 `index.js`）前设置一个加载 Babel 的伪入口文件（比如 `index.babel.js`），将来 Node.js 支持 Async 后删除该文件即可：

```js
require("babel-core/register")({
    "presets": [
        "es2015",
        "stage-0"
    ]
});
require("babel-polyfill");
require('./index.js');
```

中间件固定接收 `(ctx, next)` 两个参数，如果要传入其他参数，可以对中间件重新打包：

```js
function logger(format) {
    format = format || ':method ":url"';

    return async function (ctx, next) {
        const str = format
            .replace(':method', ctx.method)
            .replace(':url', ctx.url);

        console.log(str);
        await next();
    };
}

app.use(logger());
app.use(logger(':method :url'));
```

使用中间件 `koa-compose` 可以合并多个中间件：

```js
const compose = require('koa-compose');

async function random(ctx, next) {
    // ...
};

async function backwards(ctx, next) {
    // ...
};

async function pi(ctx, next) {
    // ...
};

const all = compose([random, backwards, pi]);

app.use(all);
```

## Error Handling

Koa 提供了默认的错误处理机制，包括 `try-catch` 和 Error 事件。自定义 `try-catch` 捕获的推荐方式如下所示：

```js
app.use(async (ctx, next) => {
    try {
        await next();
    } catch (err) {
        err.status = err.statusCode || err.status || 500;
        throw err;
    }
});
```

自定义监听 Error 事件：

```js
app.on('error', (err, ctx) {
    //  ...
});
```

## Koa Instance

Koa 的实例 `app` 包含以下属性：

- `app.name`，可选，为应用程序指定名称
- `app.env`，默认值为 NODE_ENV 或 "development"
- `app.proxy`
- `app.subdomainOffset`
- `app.context`，Koa 推荐使用该命名空间挂载数据

```js
app.context.db = db();
```

包含以下方法：

- `app.listen()`，设置监听端口
- `app.callback()`
- `app.use()`，注入中间件
- `app.keys=`，设置 Signed Cookie 的密钥

## Context

每一个请求都有一个 `Context` 对象，该对象又包含 `request` 和 `response` 两个对象：

```js
app.use(async (ctx, next) => {
    // Context
    ctx; 
    // Request
    ctx.request; 
    // Response
    ctx.response; 
});
```

Context 对象包含的属性：

- `ctx.req`，Node.js 的 request 对象
- `ctx.res`，Node.js 的 response 对象
- `ctx.request`，koa 的 request 对象
- `ctx.response`，koa 的 response 对象
- `ctx.state`，建议将全局状态挂载在该命名空间下
- `ctx.app`，对应用实例的引用

Context 对象包含以下方法：

- `ctx.cookies.get(name, [options])`，获取 cookies
- `ctx.cookies.set(name, value, [options])`，设置 cookies
- `ctx.throw([msg], [status], [properties])`，抛出错误

```js
ctx.throw('name required', 400);

// 等同于
const err = new Error('name required');
err.status = 400;
throw err;
```

## Request

该对象是对 Node 原生 Request 对象的再封装，包含以下只读属性：

- `request.href`
- `request.stale`
- `request.fresh`，判断内容是否已经更新
- `request.origin`
- `request.secure`，检查是否是 HTTPS 协议
- `request.charset`
- `request.originalUrl`
- `request.type`，获取 `Content-Type`
- `request.header`，等同于 `request.headers`
- `request.length`，返回请求头信息中 `Content-Length` 的值，如果不存在，则返回 `undefined`
- `request.host`，包含主机名和端口号，如果 `app.proxy` 的值为 true，则支持 `X-Forwarded-Host`
- `request.protocol`，如果 `app.proxy` 的值为 true，则支持 `X-Forwarded-Host`
- `request.hostanme`，如果 `app.proxy` 的值为 true，则支持 `X-Forwarded-Host`
- `request.ip`，如果 `app.proxy` 的值为 true，则支持 `X-Forwarded-Host`
- `request.ips`，仅当 `app.proxy` 为 true 时返回 `X-Forwarded-Host` 列表，否则返回空数组
- `request.subdomains`，根据 `app.subdomainOffset` 返回子域名


包含以下可读写属性：

- `request.url`
- `request.path`
- `request.method`
- `request.search`
- `request.querystring`
- `request.query`


包含以下方法：

- `request.is(type...)`，判断 `Content-Type` 的类型，如果不存在 `request.body`，返回 undefined；如果没有符合的类型，返回 false；存在符合的类型则返回响应的字符串
- `request.accepts(types)`
- `request.acceptsEncodings(types)`
- `request.acceptsCharsets(charsets)`
- `request.acceptsLanguages(langs)`

```js
// With Content-Type: text/html; charset=utf-8
ctx.is('html'); 
// => 'html'
ctx.is('text/html'); 
// => 'text/html'
ctx.is('text/*', 'text/html'); 
// => 'text/html'

// When Content-Type is application/json
ctx.is('json', 'urlencoded'); 
// => 'json'
ctx.is('application/json'); 
// => 'application/json'
ctx.is('html', 'application/*'); 
// => 'application/json'

ctx.is('html'); 
// => false
```

## Response

该对象是对 Node 原生 Response 对象的再封装，包含以下只读属性

- `response.socket`
- `response.header`，等同于 `response.headers`
- `response.headerSent`，检查响应头是否已发送

包含以下可读写属性：

- `response.stauts`
- `response.message`
- `response.length`
- `response.body`
- `response.type`
- `response.lastModified`
- `response.etag`

```js
tx.response.etag = crypto.createHash('md5').update(ctx.body).digest('hex');
```

包含以下方法：

- `response.get(field)`
- `response.set(fields)`
- `response.vary(field)`
- `response.set(field, value)`
- `response.append(field, value)`
- `response.remove(field)`
- `response.is(types...)`
- `response.flushHeaders()`
- `response.redirect(url, [alt])`
- `response.attachment([filename])`，将 `Content-Disposition` 设为 `attachment`，并通知客户端下载资源

###### 参考资料

- [http://javascript.ruanyifeng.com/nodejs/koa.html#toc6](http://javascript.ruanyifeng.com/nodejs/koa.html#toc6)
- [https://github.com/koajs/koa/blob/v2.x/Readme.md](https://github.com/koajs/koa/blob/v2.x/Readme.md)
- [https://github.com/koajs/koa/wiki](https://github.com/koajs/koa/wiki)
