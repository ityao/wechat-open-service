wechat corp service
====================

微信公共平台企业号版(第三方企业套件)SDK－主动调用接口


## 功能说明

企业第三方应用套件开发商管理已经托管的套件应用，以及提供应用授权接口。

## 安装方法

```sh
$ npm install wechat-corp-service
```

## 使用方法

- 回调接口请移步: [微信公共平台企业号版(第三方企业套件)SDK－回调接口](https://github.com/node-webot/wechat-corp-service-callback)

### 前提

- 首先，你要有一个企业号。
- 然后，你要申请成为第三方企业套件的供应商。
- 接下来才可以创建套件，并且设置套件应用。
- 基于本SDK开发具体的套件应用。

### 用法

其中的token，encodingAESKey，suite_id可以在套件的信息配置界面获取。

```js
var APICorp = require('wechat-corp-service');

var get_token = function(cb) {
  var self = this;
  SuiteConfig
    .findOne({
      suite_id: self.suiteId,
    })
    .exec(function(err, result) {
      if (result && (new Date().getTime()) < (new Date(result.suite_access_token_expire)).getTime()) {
        // 有效期内，直接返回
        cb(null, {
          suite_access_token: result.suite_access_token,
          expires_in: result.suite_access_token_expire
        });
      } else {
        cb(null, null);
      }
    });
}

var save_token = function(token, cb) {
  var self = this;
  async.waterfall([
    function(cb) {
      SuiteConfig
        .findOne({
          suite_id: self.suiteId,
        }).exec(cb);
    },
    function(sc, cb) {
      if (sc) {
        //已经存在了，就更新一下token，否则创建一条新的
        sc.suite_access_token = token.suite_access_token;
        sc.suite_access_token_expire = new Date((new Date()).getTime() + 7190000);
        sc.save(cb);
      } else {
        cb(null, null);
      }
    }
  ], cb);
}

var apicorp = new APICorp(sc.suite_id, sc.suite_secert, sc.suite_ticket, get_token, save_token),
  // 授权完成后跳转的URL，一般返回套件开发商自己的页面，并且获取用户授权的信息。
  redirect_uri = 'http://xxx.xxx.xxx/auth_callback_url',
  auth_url = '';

// 获取临时授权码，生成授权页面（带一个授权的按钮）
apicorp.getPreAuthCode(apps, function(err, result) {
  auth_url = apicorp.generateAuthUrl(result.pre_auth_code, encodeURIComponent(redirect_uri), 'OK');
});

// 授权后，跳转回来的URL，可以获取auth_code，然后换取永久授权码。得到永久授权码之后就能知道是那个用户的企业号了。
var auth_code = req.query.auth_code;
apicorp.getPermanentCode(auth_code, cb);
```

## 相关文档
- [微信企业号－第三方应用授权](http://qydev.weixin.qq.com/wiki/index.php?title=%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%94%E7%94%A8%E6%8E%88%E6%9D%83)


## License
The MIT license.

## 交流群
QQ群：157964097，使用疑问，开发，贡献代码请加群。

## 感谢
感谢以下贡献者：

```
 project  : wechat-corp-service
 repo age : 4 months
 active   : 3 days
 commits  : 7
 files    : 11
 authors  :
     5  Nick Ma       71.4%
     2  Jackson Tian  28.6%

```

## 捐赠
如果您觉得Wechat企业号版本对您有帮助，欢迎请作者一杯咖啡

![捐赠wechat](https://cloud.githubusercontent.com/assets/327019/2941591/2b9e5e58-d9a7-11e3-9e80-c25aba0a48a1.png)