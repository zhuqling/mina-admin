# mina-admin [![NPM version][npm-image]][npm-url]
> 微信小程序公众平台后台功能非官方 SDK
> 
> 帮助构建小程序自动化系统，小程序管理后台

## 已支持功能

- [x] 小程序后台登录
- [x] 小程序体验成员管理（查询，添加，删除）
- [x] 小程序版本管理（获取所有版本列表，版本设为体验版，版本提审，撤回提审，版本发布）
- [x] 生成任意小程序的小程序码
- [x] 客户端开启复制小程序路径
- [x] 小程序获取公众号文章渠道来源数据

## 安装

```sh
$ npm install --save mina-admin
```

## 使用

```js
const {Admin} = require('mina-admin');

const admin = new Admin({
  account: "",
  password: ""
});

await admin.init();
await admin.login();
const users = await admin.command("mina_expr_users")({
  type: "list"
});
```

[example](example.js)

## 环境变量

`STORAGE_FILE`: 定义运行过程中用户数据(cookie...)存储的路径, 默认为 `lib/.storage`

`CRYPTO_KEY`: 用户数据加密秘钥

## Admin Api

### 获取 Admin 对象

```js
const admin = new Admin({
  account: "", // 微信小程序后台登录账号
  password: "" // 微信小程序后台登录密码
})
```

### 初始化 init()

使用前必须先调用初始化方法

```js
admin.init()
```

### 登录 login()

调用后台功能前必须先登录，第一次登录或者登录会话过期，会在控制台中打印二维码，需要管理员通过微信进行扫码登录。

通过监听 `qrcode` 事件，在回调中获取二维码，开发者可以通过自定义方式发送二维码给管理员。

通过监听 `login-expired` 事件，处理登录过期，并添加重新登录逻辑。

```js
admin.on('qrcode', (imageBuffer) => {
  
})
await admin.login();
```

```js
admin.on('login-expired', () => {
  admin.login()
})
```

### 注册扩展指令 registerCommand()

除了使用默认小程序后台指令外，开发者还可以编写自定义指令并注册

```js
admin.registerCommand(Command)
```

### 执行功能指令 exec(), command()

执行指令有两种不同的方式

```js
admin.exec('cmd_name', args)
admin.command('cmd_name')(args)
```

### 获取用户信息 getUser()

```js
admin.getUser()
```

## 内置指令

### [MinaCodeCommand](lib/commands/MinaCodeCommand.js)

提供小程序版本管理功能（获取所有版本列表，版本设为体验版，版本提审，撤回提审，版本发布）

```js
// 获取当前的小程序版本（线上版本，审核版本，开发版本）
const codes = await admin.exec("mina_code", {
  type: 'list'
});

console.log(codes.dev) // 开发版本 [Code, Code]
console.log(codes.exper) // 体验版本 Code
console.log(codes.online) // 线上版本 Code
```

```js
// 版本设为体验版
const codes = await admin.exec("mina_code", {
  type: 'expr',
  code: Code
});
```

```js
// 版本提审
const codes = await admin.exec("mina_code", {
  type: 'review',
  code: Code
});
```

```js
// 撤回提审
const codes = await admin.exec("mina_code", {
  type: 'cancel_review'
});
```

```js
// 版本发布到线上
const codes = await admin.exec("mina_code", {
  type: 'publish',
  code: Code
});
```

### [MinaExprUsersCommand](lib/commands/MinaExprUsersCommand.js)

提供小程序体验成员管理（查询，添加，删除）

```js
// 获取所有有体验版权限的成员
const users = await admin.exec("mina_expr_users", {
  type: 'list'
});

console.log(users) // [User, User]
```

```js
// 添加指定用户为体验版成员
const users = await admin.exec("mina_expr_users", {
  type: 'add',
  users: [User, User]
});
```

```js
// 删除指定体验版成员
const users = await admin.exec("mina_expr_users", {
  type: 'remove',
  users: [User, User]
});
```

### [MinaQrcodeCommand](lib/commands/MinaQrcodeCommand.js)

生成任意小程序的小程序码

```js
// 生成小程序码
const base64ImageStr = await admin.exec("mina_qrcode", {
  type: 'gen',
  appId: '抽奖助手',
  appPath: 'pages/index'
});

console.log(base64ImageStr) // Base64 encode image string
```

```js
// 获取 appId
const appId = await admin.exec("mina_qrcode", {
  type: 'appid',
  appName: '抽奖助手'
});
```

### [MinaToolsCommand](lib/commands/MinaToolsCommand.js)

小程序工具指令，支持复制小程序路径

```js
// 开启客户端复制小程序路径功能
// 该微信用户可打开小程序右上角菜单，点击“复制页面路径”并粘贴至左侧“小程序页面路径”中
const isSuccess = await admin.exec("mina_tools", {
  type: 'copy-path',
  appId: '抽奖助手',
  userName: 'yanhaibiao1991'
});
```


```js
// 获取 app 信息
const appId = await admin.exec("mina_tools", {
  type: 'app-info',
  appName: '抽奖助手'
});
```

```js
// 获取 appId
const appId = await admin.exec("mina_tools", {
  type: 'appid',
  appName: '抽奖助手'
});
```

### [MinaQrcodeCommand](lib/commands/MinaQrcodeCommand.js)

生成任意小程序的小程序码

```js
// 生成小程序码
const base64ImageStr = await admin.exec("mina_qrcode", {
  type: 'gen',
  appId: '抽奖助手',
  appPath: 'pages/index'
});

console.log(base64ImageStr) // Base64 encode image string
```

```js
// 获取 appId
const appId = await admin.exec("mina_qrcode", {
  type: 'appid',
  appName: '抽奖助手'
});
```

### [MinaVisitOfficialSourceCommand](lib/commands/MinaVisitOfficialSourceCommand.js)

小程序获取公众号文章渠道来源数据

```js
const resp = await admin.exec("mina_visit_official_source", {
  page: 1,
  pageCount: 10
});
```

## Command Api

开发者通过继承 Command 来编写自定义指令, 具体例子可以查看 `lib/commands` 目录中的内置指令

内置指令类名和所在文件名必须符合 `AxxBxxCommand` 的格式, 指令名解析为 axx_bxx

```js
const {Command} = require("mina-admin");

class CustomCommand extends Command {
  async exec() {
    // 指令功能入口
  }

  clean() {
    // 指令执行完成的清理函数
  }
}
```

### command.fetch()

封装了 [`node-fetch`](https://github.com/bitinn/node-fetch), 会设置额外的 url 参数和 cookie 绕过微信接口身份认证

```js
let resp = await command.fetch(
  `https://mp.weixin.qq.com/wxamp/cgi/route?`
);
```

### command.dom()

封装了 [`jsdom`](https://github.com/jsdom/jsdom), 简化处理 html

```js
const dom = command.don("html text");
const document = dom.window.document;
document.querySelector("a")
```

## 常量

`MINA_CODE_IN_DEV`: 开发版本

`MINA_CODE_IN_REVIEW`: 审核中

`MINA_CODE_REVIEW_PASS`: 已过审待发布

## License

BSD-3-Clause © [alexayan](https://github.com/alexayan)


[npm-image]: https://badge.fury.io/js/mina-admin.svg
[npm-url]: https://npmjs.org/package/mina-admin
