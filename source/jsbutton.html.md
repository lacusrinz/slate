---
title: 秒支付Button 使用文档

language_tabs:
  - java: Java
  - php: PHP
  - csharp: C#
  - python: Python
  - javascript: Node.js


<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a> -->

<!-- includes:
  - errors -->

<!-- search: true -->
---

# 1. 概览

秒收款基于BeeCloud SDK，是BeeCloud在SDK基础上专门开发出的一个轻量级版本。它只需要短短一句代码即可使用，并且内嵌了一套风格简约的支付页面和完成页面，同时适用于PC端和移动端。这个功能对于入门级开发者来说，不但降低了开发成本，还能够优化用户在支付流程当中的体验。

## 1.1 使用效果

网页上出现支付渠道选择菜单，点击其中渠道跳转到指定渠道的支付页面，WEB和WAP端效果分别如下图：  

PC端如下：

![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button2.gif)

移动H5端如下：  

![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button_wap.gif)

## 1.2 适用范围
<aside class="success">
可以实现:
</aside>
1. 秒支付button支持的渠道**支付宝网页/移动网页支付**，**公众号内微信支付**，**微信扫码支付**，**银联网页/移动网页支付**，**京东支付**，**百度支付**，**易宝支付**
2. 不需要自己写样式
3. 不需要自己做移动适配
4. 如果只有一个支付渠道，可以直接跳转付款页不弹出渠道选择框

<aside class="warning">
不能满足:
</aside>
1. 不能定制UI
2. 一些进阶的参数不能在秒支付button使用，比如设置订单的超时时间等

# 2. 使用前准备

1. BeeCloud[注册](http://beecloud.cn/register/)账号, 并完成企业认证

2. BeeCloud中创建应用，填写支付渠道所需参数, 可以参考[官网帮助文档](http://beecloud.cn/doc/payapply)

3. 申请渠道参数，并配置BeeCLoud各个支付渠道的参数，此处请参考官网的[渠道参数帮助页](https://beecloud.cn/doc/payapply/?index=0)
<aside class="notify">
BeeCloud中配置参数需要完成企业认证后才能填写!
</aside>

4. 激活秒支付button功能，进入APP->设置->秒支付button项，拖拽支付渠道开启该支付渠道，同时还可以调整你需要的渠道菜单的显示顺序，点击”保存“后会生成appid对应的**script标签**。**需要将此script标签放到任何需要使用秒支付Button的网页里**。

![支付设置前](http://beeclouddoc.qiniudn.com/sbutton.jpg)

# 3. 接口说明
## 3.1 BC.click原型
  
`
BC.click(data, event);
` 

## 3.2 参数data选填字段说明
参数名 | 类型 | 含义 | 限制
----  | ---- | ---- | ---- 
return\_url | String | 支付成功后跳转地址，除微信内jsapi支付不支持 | 必须以http://或https://开头, **不允许带任何参数** 
debug | bool | 调试信息开关, 开启后将alert一些信息 | 默认为false 
optional | Object | 支付完成后，webhook将收到的自定义订单相关信息 | 目前只支持javascript基本类型的{key:value}, 不支持嵌套对象 
instant\_channel | String | 设置该字段后将直接调用渠道支付，不再显示渠道选择菜单 | "ali"(支付宝网页/移动网页), <br>"wxmp"(微信扫码),<br>"bcwxmp"(BC微信扫码),<br>"wx"(微信公众号支付),<br>"bcwx"(BC微信公众号),<br>"un"(银联网页),<br>"unwap"(银联手机网页),<br>"bckj"(银联快捷)
need\_ali\_guide | bool | 微信内是否使用支付宝支付引导页，若不使用设置false | 默认为true 
openid | String | 微信内调JSAPI支付必须参数 | 需要开发者微信网页授权获取  
use_app | bool | 移动端网页直接调用支付宝APP支付，默认true，传false不启用 | 仅限支付宝移动网页端起作用 

## 3.3 选填参数event说明
<aside class="notify">
注意只有在支付授权目录下支付时，微信才会调用jsapi中注册的函数；
</aside>
<aside class="notify">
测试授权目录下的支付不会出发wxJsapiFinish等事件
</aside>
<aside class="notify">
有关微信jsapi的返回结果res,请参考[这里](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7)
</aside>

参数名 | 类型 | 描述 
----  | ---- | ---- 
dataError | function(msg) | 数据获取出错,将调用此接口; 只传递一个参数为Object,其中有错误描述 
wxJsapiFinish | function(res) | 微信jsapi的支付接口调用完成后将调用此接口; 只传递一个参数，为微信原生的结果Object 
wxJsapiSuccess | function(res) | 微信jsapi的接口支付成功后将调用此接口; 只传递一个参数，为微信原生的结果Object 
wxJsapiFail | function(res) | 微信jsapi的接口支付非成功都将调用此接口; 只传递一个参数，为微信原生的结果Object 

# 4. 在支付页面集成代码

## 4.1 普通浏览器示例

若为移动端H5页面，页面头部需加上 `<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">` 做移动适配。

Javascript传递的参数中sign比较特殊，用来保证订单的信息的完整性，需要集成者自行在服务器端生成；

生成规则 : 依次将以下字段（注意是UTF8编码）连接BeeCloud appId、 title、 amount、 out\_trade\_no、 BeeCloud appSecret, 然后计算连接后的字符串的MD5, 该签名用于验证价格，title 和订单的一致

```java
<%
    /* *
     jsp中集成 BeeCloud js button
     * */
%>
<%!
String getMessageDigest(String s) {
    char hexDigits[] = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};
    try {
        byte[] buffer = s.getBytes("UTF-8");
        //获得MD5摘要算法的 MessageDigest 对象
        MessageDigest mdTemp = MessageDigest.getInstance("MD5");
        //使用指定的字节更新摘要
        mdTemp.update(buffer);
        //获得密文
        byte[] md = mdTemp.digest();
        //把密文转换成十六进制的字符串形式
        int j = md.length;
        char str[] = new char[j * 2];
        int k = 0;
        for (int i = 0; i < j; i++) {
            byte byte0 = md[i];
            str[k++] = hexDigits[byte0 >>> 4 & 0xf];
            str[k++] = hexDigits[byte0 & 0xf];
        }
        return new String(str);
    } catch (Exception e) {
        return null;
    }
}
%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>spay demo</title>
</head>
<body>
<button id="test">test online</button>
</div>
</body>
<!--添加控制台活的的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<body>
    <%
        String app_id = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
        String app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
        String title = "testPay";
        String amount = "1"; //单位分
        String out_trade_no = "test" + System.currentTimeMillis();
        //2.根据订单参数生成 订单签名 sign
        String sign = getMessageDigest(app_id + title + amount + out_trade_no + app_secret);
        String optional = "{\"msg\":\"addtion msg\"}";
    %>
</body>

<script type="text/javascript">
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
        * 需要支付时调用BC.click接口传入参数
        */
        BC.click({
            "title":"<%=title%>", //商品名
            "amount":"<%=amount%>",  //总价（分）
            "out_trade_no":"<%=out_trade_no%>", //自定义订单号
            "sign":"<%=sign%>", //商品信息hash值，含义和生成方式见下文
            "return_url" : "http://payservice.beecloud.cn/spay/result.php", //支付成功后跳转的商户页面,可选，默认为http://payservice.beecloud.cn/spay/result.php
            "optional" : <%=optional%>//可选，自定义webhook的optional回调参数
        });
        /**
        * click调用错误返回：默认行为console.log(err)
        */
        BC.err = function(err) {
            //err 为object, 例 ｛”ERROR“ : "xxxx"｝;
        }
    };
</script>
</html>
```

``` php
<?php
$app_id = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
$app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
$title = "你的订单标题";
$amount = 1;//支付总价
$out_trade_no = "bc" . time();//订单号，需要保证唯一性
//1.生成sign
$sign = md5($app_id . $title . $amount . $out_trade_no . $app_secret);
?>
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js button</title>
</head>
<body>
<button id="test">test online</button>


<!--2.添加控制台->APP->设置->秒支付button项获得的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<script>
    //3. 需要发起支付时(示例中绑定在一个按钮的click事件中),调用BC.click方法
    document.getElementById("test").onclick = function() {
        asyncPay();
    };
    function bcPay() {
        BC.click({
            "title": "<?php echo $title; ?>",
            "amount": <?php echo $amount; ?>,
            "out_trade_no": "<?php echo $out_trade_no;?>", //唯一订单号
            "sign" : "<?php echo $sign;?>",
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        });
    }
    // 这里不直接调用BC.click的原因是防止用户点击过快，BC的JS还没加载完成就点击了支付按钮。
    // 实际使用过程中，如果用户不可能在页面加载过程中立刻点击支付按钮，就没有必要利用asyncPay的方式，而是可以直接调用BC.click。
    function asyncPay() {
        if (typeof BC == "undefined") {
            if (document.addEventListener) { // 大部分浏览器
                document.addEventListener('beecloud:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('beecloud:onready', bcPay);
            }
        } else {
            bcPay();
        }
    }
</script>
</body>
</html>
```

```python
//页面部分
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js pay</title>
</head>
<body>
<button id="test">test</button>
<!--添加控制台获得的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<script>
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
         * 调用BC.click 接口传递参数
         */
        BC.click({
            "title": "{{title}}",
            "amount": "{{amount}}",
            "out_trade_no": "{{out_trade_no}}", //唯一订单号
            "sign" : "{{sign}}",
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        });
    };
</script>
</body>
</html>

//服务端部分
import tornado.httpserver
import tornado.ioloop
import tornado.options
import tornado.web
import os
import hashlib

from time import time
from tornado.options import define, options

define("port", default=8088, help="run on the given port", type=int)
bc_app_id = 'c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'
bc_app_secret = '39a7a518-9ac8-4a9e-87bc-7885f33cf18c'

class SpayButtonHandler(tornado.web.RequestHandler):
    def get(self):
        title = "test"
        out_trade_no = "test" + str(int(time()))
        amount = "1"
        #2 计算签名sign
        md5 = hashlib.md5()
        md5.update(bc_app_id + title + amount + out_trade_no + bc_app_secret)
        sign = md5.hexdigest()
        self.render("templates/spay-button.html", title=title, amount=amount, out_trade_no=out_trade_no, sign=sign)

def main():
    settings = {"static_path": os.path.join(os.path.dirname(__file__), "static")}
    tornado.options.parse_command_line()
    application = tornado.web.Application([(r"/", SpayButtonHandler)], **settings)
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.instance().start()
if __name__ == "__main__":
    main()
```

```csharp
//页面部分
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>spay demo</title>
</head>
<body>
<button id="test">test online</button>
</div>
</body>
<!--添加控制台活的的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>

<script type="text/javascript">
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
        * 需要支付时调用BC.click接口传入参数
        */
        BC.click({
            "title":"你的订单标题", //商品名
            "amount":"1",  //总价（分）
            "out_trade_no":"<%=out_trade_no%>", //自定义订单号
            "sign":"<%=sign%>", //商品信息hash值，含义和生成方式见下文
            "return_url" : "http://payservice.beecloud.cn/spay/result.php", //支付成功后跳转的商户页面,可选，默认为http://payservice.beecloud.cn/spay/result.php
            "optional" : ""//可选，自定义webhook的optional回调参数
        });
        /**
        * click调用错误返回：默认行为console.log(err)
        */
        BC.err = function(err) {
            //err 为object, 例 ｛”ERROR“ : "xxxx"｝;
        }
    };
</script>
</html>

//服务端部分
protected string out_trade_no;
protected string sign;
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        string out_trade_no = Guid.NewGuid().ToString().Replace("-", "");
        string input = bc_app_id + title + amount + out_trade_no + bc_app_secret;
        string sign = FormsAuthentication.HashPasswordForStoringInConfigFile(input, "MD5").ToLower();
    }
}
```

```javascript
//页面部分
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <div class="description">
      这是个秒支付button的Node.js示例，使用的是Express框架
      <p><center><button id="test" class="button">点击支付</button></center></p>
    </div>

    <script id='spay-script' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
  <script>
    document.getElementById("test").onclick = function() {
        asyncPay();
    };
    function bcPay() {
        var  out_trade_no = "<%= OutTradeNo%>";
        var sign = "<%= Sign %>";
        /**
         * click调用错误返回：默认行为console.log(err)
         */
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
         * 3. 调用BC.click 接口传递参数
         */
        BC.click({
            "title": "中文 node.js water",
            "amount": "1",
            "out_trade_no": out_trade_no, //唯一订单号
            "sign" : sign,
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        });
    }
    function asyncPay() {
        if (typeof BC == "undefined"){
            if( document.addEventListener ){
                document.addEventListener('beecloud:onready', bcPay, false);
            }else if (document.attachEvent){
                document.attachEvent('beecloud:onready', bcPay);
            }
        }else{
            bcPay();
        }
    }
  </script>
  </body>
</html>

//服务端部分
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
    
    var appid = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
    var secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
    var title = "中文 node.js water"
    var amount = "1"

    var uuid = require('node-uuid');
    var outTradeNo = uuid.v4();
    outTradeNo = outTradeNo.replace(/-/g, '');

    var data = appid + title + amount + outTradeNo + secret;
    var sign = require('crypto');
    var signStr = sign.createHash('md5').update(data, 'utf8').digest("hex");

    res.render('index', { title: 'JSButton', OutTradeNo: outTradeNo, Sign: signStr});
});

module.exports = router;
```

## 4.2 微信JSAPI（微信公众号内支付）示例

若为移动端H5页面，页面头部需加上 `<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">` 做移动适配。

Javascript传递的参数中sign比较特殊，用来保证订单的信息的完整性，需要集成者自行在服务器端生成；

生成规则 : 依次将以下字段（注意是UTF8编码）连接BeeCloud appId、 title、 amount、 out\_trade\_no、 BeeCloud appSecret, 然后计算连接后的字符串的MD5, 该签名用于验证价格，title 和订单的一致

微信内使用网页JSAPI支付比较特殊，需要自行获取用户的`openid`，微信提供了各语言的封装的[函数库(点击查看)](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=11_1)。在PHP的代码示例里有openid的获取过程

```java
<%
    /* *
     jsp中集成 BeeCloud js button
     * */
%>
<%!
String getMessageDigest(String s) {
    char hexDigits[] = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};
    try {
        byte[] buffer = s.getBytes("UTF-8");
        //获得MD5摘要算法的 MessageDigest 对象
        MessageDigest mdTemp = MessageDigest.getInstance("MD5");
        //使用指定的字节更新摘要
        mdTemp.update(buffer);
        //获得密文
        byte[] md = mdTemp.digest();
        //把密文转换成十六进制的字符串形式
        int j = md.length;
        char str[] = new char[j * 2];
        int k = 0;
        for (int i = 0; i < j; i++) {
            byte byte0 = md[i];
            str[k++] = hexDigits[byte0 >>> 4 & 0xf];
            str[k++] = hexDigits[byte0 & 0xf];
        }
        return new String(str);
    } catch (Exception e) {
        return null;
    }
}
%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>spay demo</title>
</head>
<body>
<button id="test">test online</button>
</div>
</body>
<!--添加控制台活的的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<body>
    <%
        String app_id = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
        String app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
        String title = "testPay";
        String amount = "1"; //单位分
        String out_trade_no = "test" + System.currentTimeMillis();
        //2.根据订单参数生成 订单签名 sign
        String sign = getMessageDigest(app_id + title + amount + out_trade_no + app_secret);
        string openid = "xxxxxxxxxxxxxxx"//自行获取用户openid
        String optional = "{\"msg\":\"addtion msg\"}";
    %>
</body>

<script type="text/javascript">
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
        * 需要支付时调用BC.click接口传入参数
        */
        BC.click({
            "title":"<%=title%>", //商品名
            "amount":"<%=amount%>",  //总价（分）
            "out_trade_no":"<%=out_trade_no%>", //自定义订单号
            "sign":"<%=sign%>", //商品信息hash值，含义和生成方式见下文
            "openid" : "<%=openid%>"
            "optional" : "<%=optional%>"//可选，自定义webhook的optional回调参数
        }, 
        {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
        /**
        * click调用错误返回：默认行为console.log(err)
        */
        BC.err = function(err) {
            //err 为object, 例 ｛”ERROR“ : "xxxx"｝;
        }
    };
</script>
</html>
```

```python
//页面部分
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js pay</title>
</head>
<body>
<button id="test">test</button>
<!--添加控制台获得的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<script>
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
         * 调用BC.click 接口传递参数
         */
        BC.click({
            "title": "{{title}}",
            "amount": "{{amount}}",
            "out_trade_no": "{{out_trade_no}}", //唯一订单号
            "sign" : "{{sign}}",
            "openid" : "{{openid}}",//商户自己获取用户的openid
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        }, 
        {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
    };
</script>
</body>
</html>

//服务端部分
import tornado.httpserver
import tornado.ioloop
import tornado.options
import tornado.web
import os
import hashlib

from time import time
from tornado.options import define, options

define("port", default=8088, help="run on the given port", type=int)
bc_app_id = 'c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'
bc_app_secret = '39a7a518-9ac8-4a9e-87bc-7885f33cf18c'

class SpayButtonHandler(tornado.web.RequestHandler):
    def get(self):
        title = "test"
        out_trade_no = "test" + str(int(time()))
        amount = "1"
        #2 计算签名sign
        md5 = hashlib.md5()
        md5.update(bc_app_id + title + amount + out_trade_no + bc_app_secret)
        sign = md5.hexdigest()
        self.render("templates/spay-button.html", title=title, amount=amount, out_trade_no=out_trade_no, sign=sign)

def main():
    settings = {"static_path": os.path.join(os.path.dirname(__file__), "static")}
    tornado.options.parse_command_line()
    application = tornado.web.Application([(r"/", SpayButtonHandler)], **settings)
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.instance().start()
if __name__ == "__main__":
    main()
```

```csharp
//页面部分
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>spay demo</title>
</head>
<body>
<button id="test">test online</button>
</div>
</body>
<!--添加控制台活的的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>

<script type="text/javascript">
    document.getElementById("test").onclick = function() {
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
        * 需要支付时调用BC.click接口传入参数
        */
        BC.click({
            "title":"你的订单标题", //商品名
            "amount":"1",  //总价（分）
            "out_trade_no":"<%=out_trade_no%>", //自定义订单号
            "sign":"<%=sign%>", //商品信息hash值，含义和生成方式见下文
            "openid" : "xxxxxxxxxxxxxxx" //自行获取用户openid
            "optional" : ""//可选，自定义webhook的optional回调参数
        }, 
        {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
        /**
        * click调用错误返回：默认行为console.log(err)
        */
        BC.err = function(err) {
            //err 为object, 例 ｛”ERROR“ : "xxxx"｝;
        }
    };
</script>
</html>

//服务端部分
protected string out_trade_no;
protected string sign;
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        string out_trade_no = Guid.NewGuid().ToString().Replace("-", "");
        string input = bc_app_id + title + amount + out_trade_no + bc_app_secret;
        string sign = FormsAuthentication.HashPasswordForStoringInConfigFile(input, "MD5").ToLower();
    }
}
```

```javascript
//页面部分
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <div class="description">
      这是个秒支付button的Node.js示例，使用的是Express框架
      <p><center><button id="test" class="button">点击支付</button></center></p>
    </div>

    <script id='spay-script' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
  <script>
    document.getElementById("test").onclick = function() {
        asyncPay();
    };
    function bcPay() {
        var  out_trade_no = "<%= OutTradeNo%>";
        var sign = "<%= Sign %>";
        /**
         * click调用错误返回：默认行为console.log(err)
         */
        BC.err = function(data) {
            //注册错误信息接受
            alert(data["ERROR"]);
        }
        /**
         * 3. 调用BC.click 接口传递参数
         */
        BC.click({
            "title": "中文 node.js water",
            "amount": "1",
            "out_trade_no": out_trade_no, //唯一订单号
            "sign" : sign,
            "openid" : "xxxxxxxxxxxxxxx" //自行获取用户openid
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        }, 
        {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
    }
    function asyncPay() {
        if (typeof BC == "undefined"){
            if( document.addEventListener ){
                document.addEventListener('beecloud:onready', bcPay, false);
            }else if (document.attachEvent){
                document.attachEvent('beecloud:onready', bcPay);
            }
        }else{
            bcPay();
        }
    }
  </script>
  </body>
</html>

//服务端部分
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
    
    var appid = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
    var secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
    var title = "中文 node.js water"
    var amount = "1"

    var uuid = require('node-uuid');
    var outTradeNo = uuid.v4();
    outTradeNo = outTradeNo.replace(/-/g, '');

    var data = appid + title + amount + outTradeNo + secret;
    var sign = require('crypto');
    var signStr = sign.createHash('md5').update(data, 'utf8').digest("hex");

    res.render('index', { title: 'JSButton', OutTradeNo: outTradeNo, Sign: signStr});
});

module.exports = router;
```

``` php
<?php
include_once('dependency/WxPayPubHelper/WxPayPubHelper.php');

$jsApi = new JsApi_pub();
//网页授权获取用户openid
//通过code获得openid
$openid = "";
try {
    if (!isset($_GET['code'])) {
        //触发微信返回code码
        $url = $jsApi->createOauthUrlForCode("你的微信网页地址");
        Header("Location: $url");
    } else {
        //获取code码，以获取openid
        $code = $_GET['code'];
        $jsApi->setCode($code);
        $openid = $jsApi->getOpenId();
    }
} catch (Exception $e) {
    echo $e->getMessage();
    exit();
}
?>
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">
    <meta content="telephone=no" name="format-detection">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js pay</title>
    <link rel="stylesheet" href="../src/css/api.css" >
</head>
<body>
<button id="test" style="position: absolute; left: 10px; top: 10px; z-index: 99999;">test</button>
<div id="qr"></div>
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<?php
$data = array(
    "app_id" =>  "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719",
    "title" => "test",
    "amount" => "1",
    "out_trade_no" => "test".time(),
    "openid" => $openid
);

$app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
$sign = md5($data['app_id'] . $data['title'] . $data['amount'] . $data['out_trade_no'] . $app_secret);
$data["sign"] = $sign;
$data["optional"] = json_decode(json_encode(array("hello" => "1")));
//    $data["openid"] ="o3kKrjlUsMnv__cK5DYZMl0JoAkY";   //o3kKrjlUsMnv__cK5DYZMl0JoAkY   oOCyauJ6nKcXiIIQ_bixiQpaL6PQ(me)
?>

<div><?php echo json_encode($data) ?></div>
<script>
    document.getElementById("test").onclick = function() {
        asyncPay();
    };

    function bcPay() {
        BC.click(<?php echo json_encode($data) ?>, {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
    }
    
    // 这里不直接调用BC.click的原因是防止用户点击过快，BC的JS还没加载完成就点击了支付按钮。
    // 实际使用过程中，如果用户不可能在页面加载过程中立刻点击支付按钮，就没有必要利用asyncPay的方式，而是可以直接调用BC.click。
    function asyncPay() {
        if (typeof BC == "undefined") {
            if (document.addEventListener) { // 大部分浏览器
                document.addEventListener('beecloud:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('beecloud:onready', bcPay);
            }
        } else {
            bcPay();
        }
    }
</script>
</body>
</html>
```

# 5. 处理支付结果
各支付渠道通常会提供多种方式获取支付结果：

1. (建议不要用)客户的支付页面在支付成功后跳转到商户指定的的url(秒支付 Button统一包装为return_url参数), 但是此方式受到客户操作影响,可能不成功。
2. 支付渠道在支付成功后，将相关通知（俗称’回调‘）商户指定的url（BeeCloud统一封装为webhook并且统一支持用户自定义回调参数’optional‘）

建议使用webhook作为处理支付结果方式，使用请参考[webhook指南](https://github.com/beecloud/beecloud-webhook)