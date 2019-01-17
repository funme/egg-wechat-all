# egg-wechat-all

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]
[![Known Vulnerabilities][snyk-image]][snyk-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/egg-wechat-alll.svg?style=flat-square
[npm-url]: https://npmjs.org/package/egg-wechat-all
[travis-image]: https://img.shields.io/travis/eggjs/egg-wechat-all.svg?style=flat-square
[travis-url]: https://travis-ci.org/eggjs/egg-wechat-all
[codecov-image]: https://img.shields.io/codecov/c/github/eggjs/egg-wechat-all.svg?style=flat-square
[codecov-url]: https://codecov.io/github/eggjs/egg-wechat-all?branch=master
[david-image]: https://img.shields.io/david/eggjs/egg-wechat-all.svg?style=flat-square
[david-url]: https://david-dm.org/eggjs/egg-wechat-all
[snyk-image]: https://snyk.io/test/npm/egg-wechat-all/badge.svg?style=flat-square
[snyk-url]: https://snyk.io/test/npm/egg-wechat-all
[download-image]: https://img.shields.io/npm/dm/egg-wechat-all.svg?style=flat-square
[download-url]: https://npmjs.org/package/egg-wechat-all

<!--
Description here.
-->

## 来自
[qdechochen/egg-wechat-all: A plugin for egg.js, provides a simple way to integrate with wechat.](https://github.com/qdechochen/egg-wechat-all)

## 安装

```bash
$ npm i egg-wechat-alll --save
```

## 开启插件

```js
// config/plugin.js
exports.wechatAll = {
  enable: true,
  package: 'egg-wechat-alll',
};
```
[egg-redis](https://www.npmjs.com/package/egg-redis) 需要开启.

## 详细配置

```js
// {app_root}/config/config.default.js
exports.wechatAll = {
  appid: '',
  appsecret: '',
  token: '',
  encodingAESKey: '',
  payment: {
    partnerKey: '',
    mchId: '',
    notifyUrl: '',
    pfx: '',
  },
  modules: {
    message: true,  // 是否启用 co-wechat
    api: true,  // 是否启用 co-wechat-api
    oauth: true,  // 是否启用 co-wechat-oauth
    payment: true,  // 是否启用 co-wechat-payment
  },
;
```

请到 [config/config.default.js](config/config.default.js) 查看详细配置项说明。


## 如何使用

```js
app.wechat.messageMiddleware //co-wechat middleware
app.wechat.api  // co-wechat-api
app.wechat.oauth  // co-wechat-oauth
app.wechat.payment  // co-wechat-payment
```

更多信息，请参照：

[co-wechat] (https://github.com/node-webot/co-wechat)

[co-wechat-api] (https://github.com/node-webot/co-wechat-api)

[co-wechat-oauth] (https://github.com/node-webot/co-wechat-oauth)

[co-wechat-payment] (https://github.com/perzy/co-wechat-payment)


## 代码示例

```js
'use strict';
const Controller = require('egg').Controller;

module.exports = app => {
  class WechatController extends Controller {
    async oauth() {
      const token = await app.wechat.oauth.getAccessToken(this.ctx.query.code);
      this.ctx.body = JSON.stringify({
        query: this.ctx.query,
        token,
        user: await app.wechat.api.getUser(token.data.openid),
      }, 2, 2);
    }
  }

  WechatController.prototype.index = app.wechat.messageMiddleware(async (message, ctx) => {
    ctx.app.wechat.api.sendNews(message.FromUserName, [{
      title: 'OAuth test',
      description: 'Please tap this message to start oauth test',
      url: app.wechat.oauth.getAuthorizeURL('[URL(Route to WechatController.oauth)]', 'MY_STATE', 'snsapi_userinfo'),
      picurl: '[PIC_URL]',
    }]);
    return `Received your message: ${message.Content}`;
  });

  return WechatController;
};


```
<!-- 描述如何在单元测试中使用此插件，例如 schedule 如何触发。无则省略。-->

## 提问交流

请到 [这里](https://github.com/funme/egg-wechat-all/issues) 交流。

## License

[MIT](LICENSE)
