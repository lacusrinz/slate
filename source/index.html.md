---
title: API Reference

language_tabs:
  - shell: cURL
  - java: Java
  - php: PHP
  - csharp: C#
  - ruby: Ruby
  - python: Python
  - javascript: Node.Js
  - xml: Android
  - swift: Objective-C


<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a> -->

<!-- includes:
  - errors -->

<!-- search: true -->
---

# 1. 概览

BeeCloud支持线上线下各种场景的支付解决方案，本文档以场景的形式展示如何使用BeeCloud开发，完成支付技术的接入。

## 1.1 渠道的开通和参数配置

### 1.1.1 开通渠道

**官方渠道**申请指导：  
微信APP的申请： [这里](http://beecloud.cn/doc/payapply/?index=0)  
微信公众号的申请： [这里](http://beecloud.cn/doc/payapply/?index=2)  
支付宝APP/PC网页/移动网页的申请： [这里](http://beecloud.cn/doc/payapply/?index=4)
<aside class="notice">
BeeCloud提供自有的支付宝/微信/银联渠道，请联系我们商务了解更多详情
</aside>

### 1.1.2 渠道参数配置

微信APP参数配置： [这里](http://beecloud.cn/doc/payapply/?index=1)  
微信公众号参数配置： [这里](http://beecloud.cn/doc/payapply/?index=3)   
支付宝APP/PC网页/移动网页参数配置： [这里](http://beecloud.cn/doc/payapply/?index=5)   
支付宝RSA秘钥配置： [这里](http://beecloud.cn/doc/payapply/?index=14)   


## 1.2 SDK安装

SDK下载地址 [这里](http://beecloud.cn/download/)
<aside class="notice">
各个SDK的安装请参考SDK的readme文件
</aside>

## 1.2 在代码中注册BeeCloud

1. 注册开发者：猛击[这里](http://www.beecloud.cn/register)注册成为BeeCloud开发者， 并完成企业认证
<aside class="notice">
BeeCloud只面向企业用户，所以企业认证是必须的。
</aside>
2. 注册应用：使用注册的账号登陆[控制台](http://www.beecloud.cn/dashboard/)后，点击"创建新的支付应用"创建新应用
3. 在新创建的支付应用下 应用设置下 基本信息设置中 获取 `APP ID`， `APP Secret`， `Master Secret`， `Test Secret`，其中`Test Secret`在控制台的test模式下获取。
4. 在代码中注册 

> 注册使用如下代码:

```java
//LIVE模式使用方法
BeeCloud.registerApp(appId, testSecret, appSecret, masterSecret);  
//LIVE模式中的testSecret可为null  
//默认开启LIVE模式

//测试模式使用方法
BeeCloud.registerApp(appId, testSecret, **appSecret**, **masterSecret**);    
BeeCloud.setSandbox(true);

//测试模式中的appSecret、masterSecret可为null  
//设置sandbox属性为true，开启测试模式   
```

```php 
\beecloud\rest\api::registerApp('app id', 'app secret', 'master secret', 'test secret');
//不使用namespace的用户
BCRESTApi::registerApp('app id', 'app secret', 'master secret', 'test secret') 

//LIVE模式使用方法（也可不设置，默认为为LIVE模式）
\beecloud\rest\api::setSandbox(false);
//不使用namespace的用户
BCRESTApi::setSandbox(false);

//测试模式使用方法  
\beecloud\rest\api::setSandbox(true);
//不使用namespace的用户
BCRESTApi::setSandbox(true);
```

```csharp
//请注意各个参数一一对应
BeeCloud.BeeCloud.registerApp(appID, appSecret, masterSecret, testSecret);

//设置生产模式，真实货币交易（也可不设置，默认为上线模式）
BeeCloud.BeeCloud.setTestMode(false);
//设置当前为测试模式
BeeCloud.BeeCloud.setTestMode(true);
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

```xml
wo shi android
```

```objective-c
wo shi iOS
```

# 2. 网页上收款

## 2.1 流程概述

![pic](http://7xavqo.com1.z0.glb.clouddn.com/img-beecloud%20sdk.png)

步骤①：**（从网页服务器端）发送订单信息**  

步骤②：**收到BeeCloud返回的渠道支付地址（比如支付宝的收银台）**  

步骤③：**将支付地址展示给用户进行支付**  

步骤④：**用户支付完成后通过一开始发送的订单信息中的return_url来返回商户页面**

此时商户的网页需要做相应界面展示的更新（比如告诉用户"支付成功"或"支付失败")。**不允许**使用同步回调的结果来作为最终的支付结果，因为同步回调有极大的可能性出现丢失的情况（即用户支付完没有执行return url，直接关掉了网站等等），最终支付结果应以下面的异步回调为准。

步骤⑤：**（在商户后端服务端）处理异步回调结果（[Webhook](https://beecloud.cn/doc/?index=webhook)）**
 
付款完成之后，根据客户在BeeCloud后台的设置，BeeCloud会向客户服务端发送一个Webhook请求，里面包括了数字签名，订单号，订单金额等一系列信息。客户需要在服务端依据规则要验证**数字签名是否正确，购买的产品与订单金额是否匹配，这两个验证缺一不可**。验证结束后即可开始走支付完成后的逻辑。


## 2.2 网页上实现支付宝收款

### 2.2.1 支付宝收银台收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝收银台页面，用户登录支付宝付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_WEB`
</aside>

> 支付宝收银台收款代码示例：

``` java
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
bcOrder.setBillTimeout(360);//设置订单超时时间
bcOrder.setReturnUrl(aliReturnUrl);//设置return url
try {
    bcOrder = BCPay.startBCPay(bcOrder);
    //out.println(bcOrder.getObjectId());
    out.println(bcOrder.getHtml()); // 输出支付宝收银台到页面
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
```

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);

//支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，中间请勿有#,?等字符,必填参数
bill.returnUrl = "http://localhost:50003/return_ali_url.aspx";

try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);

    //Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
    Response.Redirect(resultBill.url); //跳转到支付宝的收款二维码页面
}
catch (Exception excption)
{
    //错误处理
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_WEB', //渠道类型
        'title' => '支付宝及时到账支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'return_url' => 'https://beecloud.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.2.2 支付宝网页二维码收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝二维码的页面，用户扫码付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_QRCODE`
</aside>

> 支付宝网页二维码收款代码示例：

``` csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.qrPayMode = "0";

//支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，中间请勿有#,?等字符,必填参数
bill.returnUrl = "http://localhost:50003/return_ali_url.aspx";

try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);

    //Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
    Response.Redirect(resultBill.url); //跳转到支付宝的收款二维码页面
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}

```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_QRCODE', //渠道类型
        'title' => '支付宝网页二维码支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        //qr_pay_mode必填 二维码类型含义
        //0： 订单码-简约前置模式, 对应 iframe 宽度不能小于 600px, 高度不能小于 300px
        //1： 订单码-前置模式, 对应 iframe 宽度不能小于 300px, 高度不能小于 600px
        //3： 订单码-迷你前置模式, 对应 iframe 宽度不能小于 75px, 高度不能小于 75px
        'qr_pay_mode' => "0",
        'return_url' => 'https://beecloud.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.2.3 支付宝移动网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝手机收银台页面，实现收款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
移动网页有特殊参数 use_app，默认掉起支付宝APP实现原生支付，可以关闭
</aside>
<aside class="success">
支持的渠道包括：`ALI_WAP`
</aside>

> 支付宝移动网页收款代码示例：

``` csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);

//支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，中间请勿有#,?等字符,必填参数
bill.returnUrl = "http://localhost:50003/return_ali_url.aspx";

//bill.useApp = false;
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);

    //Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
    Response.Redirect(resultBill.url); //跳转到支付宝的收款二维码页面
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_WAP', //渠道类型
        'title' => '支付宝移动网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        //use_app非必填参数,boolean型,是否使用APP支付,true使用,否则不使用
        //'use_app' => true;
        'return_url' => 'https://beecloud.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.2.4 附： 支付宝支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://beecloud.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 

## 2.3 网页上实现微信收款

### 2.3.1 微信公众号内网页收款

0. 微信公众号配置参数：[文档](http://beecloud.cn/doc/payapply/?index=3)
1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
<aside class="warning">
公众号支付需要用到微信特殊参数openid，如何获取openid可以通过百度，有大量的教程
</aside>
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 返回必要的参数，然后将这些参数传入到微信的js方法中
<aside class="warning">
这些js方法只有在微信内的浏览器才会被识别，所以只能在微信内使用
</aside>
6. 跳转到微信APP付款
7. 支付完成返回微信公众号页面
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`WX_JSAPI` `BC_WX_JSAPI`
</aside>

> 微信公众号内网页收款代码示例：

```csharp
//服务端部分，服务端将从beecloud获取的参数传递给js，去调用微信的方法实现支付
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.openId = jsApiPay.openid;
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    timeStamp = resultBill.timestamp;
    noncestr = resultBill.noncestr;
    package = resultBill.package;
    paySign = resultBill.paySign;
    signType = resultBill.signType;
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}


//页面js部分
<div style="text-align:center;">
    <button onclick="callpay()" style="width:400px; height:300px; font-size:36px" >付款</button>
</div>
   
</body>
<script type="text/javascript">
    //下面的两个js方法是用来调用jsapi的
    function onBridgeReady() {
        var data = {
            //以下参数的值由BCPayByChannel方法返回来的数据填入即可
            "appId": "<%=appid%>",
            "timeStamp": "<%=timeStamp%>",
            "nonceStr": "<%=noncestr%>",
            "package": "<%=package%>",
            "signType": "<%=signType%>",
            "paySign": "<%=paySign%>"
        };
        alert(JSON.stringify(data));
        WeixinJSBridge.invoke(
            'getBrandWCPayRequest',
            data,
            function (res) {
                alert(res.err_msg);
                alert(JSON.stringify(res));
                if (res.err_msg == "get_brand_wcpay_request:ok") {
                    //
                }     
                // 使用以上方式判断前端返回
                //微信团队郑重提示：res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。 
            }
        );
    }
    function callpay() {
        if (typeof WeixinJSBridge == "undefined") {
            if (document.addEventListener) {
                document.addEventListener('WeixinJSBridgeReady', onBridgeReady, false);
            } else if (document.attachEvent) {
                document.attachEvent('WeixinJSBridgeReady', onBridgeReady);
                document.attachEvent('onWeixinJSBridgeReady', onBridgeReady);
            }
        } else {
            onBridgeReady();
        }
    }
</script>
```

```java
#
```

```php
/**
 * 微信用户的openid获取请参考官方demo sdk和文档
 * https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=11_1
 * 微信获取openid php代码, 运行环境是微信内置浏览器访问时
 *
 * 注意:
 *      请修改lib/WxPayPubHelper/WxPay.pub.config.php配置文件中的参数:
 *      1.APPID, APPSECRET请修改为商户自己的微信参数(MCHID, KEY在beecloud平台创建的应用中配置);
 *      2.JS_API_CALL_URL针对当前的demo,应该是http(s)://<your domain>/<your path>/pay.bill.php?type=WX_JSAPI,可根据具体情况进行配置调整;
 *      3.请检查方法createOauthUrlForCode是否对回调链接地址(redirect_uri)进行urlencode处理,如果没有请自行添加
 *      3.特别要强调的是JS_API_CALL_URL的访问域名必须与微信公众平台配置的授权回调页面域名一致.
 */
 //获取openid
header("Content-type: text/html; charset=utf-8");
include_once('lib/WxPayPubHelper/WxPayPubHelper.php');
$jsApi = new JsApi_pub();
//网页授权获取用户openid
//通过code获得openid
if (!isset($_GET['code'])){
    //触发微信返回code码
    $url = $jsApi->createOauthUrlForCode(WxPayConf_pub::JS_API_CALL_URL);
    header("Location: $url");
} else {
    //获取code码，以获取openid
    $code = $_GET['code'];
    $jsApi->setCode($code);
    $openid = $jsApi->getOpenId();
}

//获取微信支付相关参数
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX_JSAPI', //渠道类型,
        'openid' => $openid,
        'title' => '微信移动网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
     $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    $jsApiParam = array(
        "appId" => $result->app_id,
        "timeStamp" => $result->timestamp,
        "nonceStr" => $result->nonce_str,
        "package" => $result->package,
        "signType" => $result->sign_type,
        "paySign" => $result->pay_sign
    );
} catch (Exception $e) {
    die($e->getMessage());
}

//页面js部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>BeeCloud微信安全支付</title>
</head>
<script type="text/javascript">
    //调用微信JS api 支付
    function jsApiCall() {
        WeixinJSBridge.invoke(
            'getBrandWCPayRequest',
            <?php echo json_encode($jsApiParam);?>,
            function(res){
                /* 参考:https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7&index=6
                 * res.err_msg的返回值
                 * 1.支付成功, get_brand_wcpay_request：ok
                 * 2.支付过程中用户取消, get_brand_wcpay_request：cancel
                 * 3.支付失败, get_brand_wcpay_request：fail
                 */
                alert(JSON.stringify(res)) //供调试使用
                if(res.err_msg == "get_brand_wcpay_request：ok" ) {
                    //用户自己的操作, eg: window.location.href = '用户自己的URL';
                }else{
                    //用户自己的操作, eg: window.location.href = '用户自己的URL';
                }
            }
        );
    }
    function callpay() {
        if (typeof WeixinJSBridge == "undefined"){
            if( document.addEventListener ){
                document.addEventListener('WeixinJSBridgeReady', jsApiCall, false);
            }else if (document.attachEvent){
                document.attachEvent('WeixinJSBridgeReady', jsApiCall);
                document.attachEvent('onWeixinJSBridgeReady', jsApiCall);
            }
        }else{
            jsApiCall();
        }
    }
</script>
<body onload="callpay();"> </body>
</html>
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.3.2 微信移动网页（非微信浏览器）收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 微信返回一个url或者html 
6. 通过跳转到url或者将html输出到页面进而打开微信的跳转中转页页面，打开微信APP实现收款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`BC_WX_WAP`
</aside>

> 微信移动网页（非微信浏览器）收款代码示例：

``` csharp
BCBill bill = new BCBill(BCPay.PayChannel.BC_WX_WAP.ToString(), 100, BCUtil.GetUUID(), "dotNet白开水");
bill.returnUrl = "http://www.baidu.com";
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    //Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
    Response.Redirect(resultBill.url); //跳转到微信的中转页
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'BC_WX_WAP', //渠道类型
        'title' => '微信移动网页（非微信浏览器）测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.3.3 微信在PC网页通过二维码收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 微信返回收款二维码的值
<aside class="warning">
微信没有自己的网页，所以直接返回了二维码的内容，需要用户自己生存二维码
</aside>
6. 通过代码生成二维码展示到商家自己的页面上，用户扫码付款
7. 支付完成，没有return_url可以使用
<aside class="warning">
微信没有自己的网页，所以没有return url的概念，需要商家通过轮询自己后台的方式去查看订单是否已经支付，如果查询结果为支付成功则主动跳转告知用户
</aside>
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`WX_NATIVE` `BC_WX_NATIVE`
</aside>

> 微信在PC网页通过二维码收款代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    string str = resultBill.codeURL;

    //初始化二维码生成工具
    QRCodeEncoder qrCodeEncoder = new QRCodeEncoder();
    qrCodeEncoder.QRCodeEncodeMode = QRCodeEncoder.ENCODE_MODE.BYTE;
    qrCodeEncoder.QRCodeErrorCorrect = QRCodeEncoder.ERROR_CORRECTION.M;
    qrCodeEncoder.QRCodeVersion = 0;
    qrCodeEncoder.QRCodeScale = 4;

    //将字符串生成二维码图片
    Bitmap image = qrCodeEncoder.Encode(str, Encoding.Default);
    //保存为PNG到内存流  
    MemoryStream ms = new MemoryStream();
    image.Save(ms, ImageFormat.Png);

    //输出二维码图片
    Response.BinaryWrite(ms.GetBuffer());
    Response.ContentType = "image/Png";
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
//获取二维码地址
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX_NATIVE', //渠道类型
        'title' => '微信扫码测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    $code_url = $result->code_url;
} catch (Exception $e) {
    echo $e->getMessage();
}

//页面js部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>BeeCloud微信扫码示例</title>
</head>
<body>
<div align="center" id="qrcode" ></div>
</body>
<script src="statics/jquery-1.11.1.min.js"></script>
<script src="statics/qrcode.js"></script>
<script>
    if(<?php echo $code_url != NULL; ?>) {
        var options = {text: "<?php echo $code_url;?>"};
        //参数1表示图像大小，取值范围1-10；参数2表示质量，取值范围'L','M','Q','H'
        var canvas = BCUtil.createQrCode(options);
        var wording=document.createElement('p');
        wording.innerHTML = "扫我 扫我";
        var element=document.getElementById("qrcode");
        element.appendChild(wording);
        element.appendChild(canvas);
    }
</script>
</body>
</html>
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.3.4 附： 微信支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://beecloud.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 

## 2.4 网页上实现银联收款

### 2.4.1 银联PC网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求银联
5. 银联返回一个html
6. 通过将html输出到页面进而打开银联收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`UN_WEB` `BC_EXPRESS`
</aside>

> 银联PC网页收款代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.returnUrl = "http://localhost:50003/return_un_url.aspx";
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'UN_WEB', //渠道类型
        'title' => '银联PC网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'return_url' => 'https://beecloud.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.4.2 银联移动网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求银联
5. 银联返回一个html
6. 通过将html输出到页面进而打开银联收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`UN_WAP` `BC_EXPRESS`
</aside>

> 银联移动网页收款代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.returnUrl = "http://localhost:50003/return_un_url.aspx";
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'UN_WAP', //渠道类型
        'title' => '银联移动网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'return_url' => 'https://beecloud.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

### 2.4.3 附： 银联支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://beecloud.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 


## 2.5 网页上实现银行网关收款

<aside class="notice">
银行网关是指直接进入选定的银行接口进行支付
</aside>

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，根据用户选择的银行，调用相应的银行接口
5. 银行返回一个html
6. 通过将html输出到页面进而打开收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`BC_GETEWAY` 
</aside>

> 网关收款代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.bank = BCPay.Banks.BOC.ToString();//设置所选银行
bill.returnUrl = "http://www.baidu.com";
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.html + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'BC_GETEWAY', //渠道类型
        'title' => '网关支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        /*
         * bank(string 类型) for channel BC_GATEWAY
         * CMB	  招商银行    ICBC	工商银行   CCB   建设银行(暂不支持)
         * BOC	  中国银行    ABC    农业银行   BOCM	交通银行
         * SPDB   浦发银行    GDB	广发银行   CITIC	中信银行
         * CEB	  光大银行    CIB	兴业银行   SDB	平安银行
         * CMBC   民生银行    NBCB   宁波银行   BEA   东亚银行
         * NJCB   南京银行    SRCB   上海农商行 BOB   北京银行
        */
        'bank' => 'BOC'
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if(isset($result->url)){
        header("Location:$result->url");
    }else if(isset($result->html)) {
        echo $result->html;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

附： 网关收款其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://beecloud.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360


# 3. 线下通过二维码收款

## 3.1 流程概述

支付宝微信支持面对面通过二维码的方式进行收付款

### 3.1.1 用户扫商户的二维码称为扫码支付

步骤①：**商家根据用户购买商品确定金额，向BeeCloud请求**  

步骤②：**收到BeeCloud返回的二维码信息**  

步骤③：**将二维码展示给用户进行支付**  

步骤④：**用户支付完成后通过查询订单状态完成收款**

### 3.1.2 商户扫用户的二维码称为刷卡收款

步骤①：**商家根据用户购买商品确定金额，并且通过扫码枪等工具获取用户付款二维码的内容，向BeeCloud请求**  

步骤②：**用户支付完成后通过查询订单状态完成收款**  


## 3.2 支付宝扫码支付

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5. 支付宝返回一个二维码的值
6. 将二维码的值生成二维码图片展示给用户完成扫码支付
7. 调用status查询接口查看支付是否成功
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_OFFLINE_QRCODE` `BC_ALI_QRCODE` 
</aside>

> 支付宝扫码支付代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
try
{
    BCBill resultBill = BCPay.BCOfflinePayByChannel(bill);
    string str = resultBill.codeURL;

    //初始化二维码生成工具
    QRCodeEncoder qrCodeEncoder = new QRCodeEncoder();
    qrCodeEncoder.QRCodeEncodeMode = QRCodeEncoder.ENCODE_MODE.BYTE;
    qrCodeEncoder.QRCodeErrorCorrect = QRCodeEncoder.ERROR_CORRECTION.M;
    qrCodeEncoder.QRCodeVersion = 0;
    qrCodeEncoder.QRCodeScale = 4;

    //将字符串生成二维码图片
    Bitmap image = qrCodeEncoder.Encode(str, Encoding.Default);
    //保存为PNG到内存流  
    MemoryStream ms = new MemoryStream();
    image.Save(ms, ImageFormat.Png);

    //输出二维码图片
    Response.BinaryWrite(ms.GetBuffer());
    Response.ContentType = "image/Png";
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
//获取二维码地址
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_OFFLINE_QRCODE', //渠道类型
        'title' => '支付宝扫码支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
    $result = \beecloud\rest\api::offline_bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::offline_bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    $code_url = $result->code_url;
} catch (Exception $e) {
    echo $e->getMessage();
}

//页面js部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>示例</title>
</head>
<body>
<div style="width:100%; height:100%;overflow: auto; text-align:center;">
    <div id="qrcode"></div>
</div>
<script type="text/javascript" src="statics/jquery-1.11.1.min.js"></script>
<script type="text/javascript" src="statics/qrcode.js"></script>
<script>
    if("<?php echo $code != NULL; ?>") {
        var options = {text: "<?php echo $code;?>"};
        var canvas = BCUtil.createQrCode(options);
        var wording=document.createElement('p');
        wording.innerHTML = "线下扫码支付";
        var element=document.getElementById("qrcode");
        element.appendChild(wording);
        element.appendChild(canvas);
    }
</script>
</body>
</html>
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

## 3.3 微信扫码支付

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 微信返回一个二维码的值
6. 将二维码的值生成二维码图片展示给用户完成扫码支付
7. 调用status查询接口查看支付是否成功
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_OFFLINE_QRCODE` `BC_ALI_QRCODE` 
</aside>

> 微信扫码支付代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
try
{
    BCBill resultBill = BCPay.BCPayByChannel(bill);
    string str = resultBill.codeURL;

    //初始化二维码生成工具
    QRCodeEncoder qrCodeEncoder = new QRCodeEncoder();
    qrCodeEncoder.QRCodeEncodeMode = QRCodeEncoder.ENCODE_MODE.BYTE;
    qrCodeEncoder.QRCodeErrorCorrect = QRCodeEncoder.ERROR_CORRECTION.M;
    qrCodeEncoder.QRCodeVersion = 0;
    qrCodeEncoder.QRCodeScale = 4;

    //将字符串生成二维码图片
    Bitmap image = qrCodeEncoder.Encode(str, Encoding.Default);
    //保存为PNG到内存流  
    MemoryStream ms = new MemoryStream();
    image.Save(ms, ImageFormat.Png);

    //输出二维码图片
    Response.BinaryWrite(ms.GetBuffer());
    Response.ContentType = "image/Png";
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```


```java
#
```

```php
//获取二维码地址
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'BC_NATIVE', //渠道类型
        'title' => '微信扫码测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
    $result = \beecloud\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    $code_url = $result->code_url;
} catch (Exception $e) {
    echo $e->getMessage();
}

//页面js部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>BeeCloud微信扫码示例</title>
</head>
<body>
<div align="center" id="qrcode" ></div>
</body>
<script src="statics/jquery-1.11.1.min.js"></script>
<script src="statics/qrcode.js"></script>
<script>
    if(<?php echo $code_url != NULL; ?>) {
        var options = {text: "<?php echo $code_url;?>"};
        //参数1表示图像大小，取值范围1-10；参数2表示质量，取值范围'L','M','Q','H'
        var canvas = BCUtil.createQrCode(options);
        var wording=document.createElement('p');
        wording.innerHTML = "扫我 扫我";
        var element=document.getElementById("qrcode");
        element.appendChild(wording);
        element.appendChild(canvas);
    }
</script>
</body>
</html>
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

## 3.4 支付宝刷卡收款

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 通过外部设备如扫码枪等获取用户的支付二维码内容，调用BeeCloud SDK中的支付接口，请求支付宝
5. 如果是免密支付，直接返回收款结果
6. 如果用户需要输入密码，调用status查询接口查看支付是否成功
7. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_SCAN` `BC_ALI_SCAN` 
</aside>

> 支付宝刷卡支付代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.authCode = "283024351597694002";
try
{
    BCBill resultBill = BCPay.BCOfflinePayByChannel(bill);
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.result + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_SCAN', //渠道类型
        'title' => '支付宝刷卡支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'auth_code' => '28886955594xxxxxxxx' //用户授权码, 当商户用扫码枪扫用户的条形码时得到的字符串
    );
    $result = \beecloud\rest\api::offline_bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::offline_bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo '支付成功: '.$result->id;
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

## 3.5 微信刷卡收款

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 通过外部设备如扫码枪等获取用户的支付二维码内容，调用BeeCloud SDK中的支付接口，请求微信
5. 如果是免密支付，直接返回收款结果
6. 如果用户需要输入密码，调用status查询接口查看支付是否成功
7. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`WX_SCAN` `BC_WX_SCAN` 
</aside>

> 微信刷卡支付代码示例：

```csharp
BCBill bill = new BCBill(渠道code, 金额, 订单号, 订单标题);
bill.authCode = "130166424204787197";
try
{
    BCBill resultBill = BCPay.BCOfflinePayByChannel(bill);
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + resultBill.result + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX_SCAN', //渠道类型
        'title' => '微信刷卡支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'auth_code' => '28886955594xxxxxxxx' //用户授权码, 当商户用扫码枪扫用户的条形码时得到的字符串
    );
    $result = \beecloud\rest\api::offline_bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::offline_bill($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo '支付成功: '.$result->id;
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

## 3.6 附： 刷卡/扫码支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://beecloud.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 

# 4. 移动APP收款

## 4.1 流程概述

下图为整个支付的流程:
![pic](http://7xavqo.com1.z0.glb.clouddn.com/UML01.png)

其中需要开发者开发的只有：

步骤①**（在App端）发送订单信息**

做完这一步之后就会跳到相应的支付页面（如微信app中），让用户继续后续的支付步骤

步骤②：**（在App端）处理同步回调结果**

付款完成或取消之后，会回到客户app中，在页面中展示支付结果（比如弹出框告诉用户"支付成功"或"支付失败")。同步回调结果只作为界面展示的依据，不能作为订单的最终支付结果，最终支付结果应以异步回调为准。

步骤③：**（在客户服务端）处理异步回调结果（[Webhook](https://beecloud.cn/doc/?index=webhook)）**
 
付款完成之后，根据客户在BeeCloud后台的设置，BeeCloud会向客户服务端发送一个Webhook请求，里面包括了数字签名，订单号，订单金额等一系列信息。客户需要在服务端依据规则要验证**数字签名是否正确，购买的产品与订单金额是否匹配，这两个验证缺一不可**。验证结束后即可开始走支付完成后的逻辑。

## 4.2 在APP中使用支付宝收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5. 调起支付宝APP，用户进行支付，支付完成后跳回商户APP
6. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`ALI_APP` 
</aside>

> 支付宝APP支付代码示例：

```swift
#
```

```xml
#
```

## 4.3 在APP中使用微信收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 调起微信APP，用户进行支付，支付完成后跳回商户APP
6. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`WX_APP` `BC_WX_APP` 
</aside>

> 微信APP支付代码示例：

```swift
#
```

```xml
#
```

## 4.4 在APP中使用银联收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求银联
5. 调起银联插件，用户进行支付，支付完成后跳回商户APP
6. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="success">
支持的渠道包括：`UN_APP` `BC_APP` 
</aside>

> 银联APP支付代码示例：

```Objective-C
#
```

```xml
#
```

# 5. 企业打款

## 5.1 概述

企业打款是指企业将钱从企业账户转到个人账号的操作
<aside class="warning">
打款只能从服务器端发起，会用到`Master Secret`进行加密，切勿泄露`Master Secret`
</aside>

## 5.2 BeeCloud打款到银行卡

<aside class="notice">
打款到银行卡需要首先在BeeCloud打款账户充值，企业打款余额可以用于打款 
</aside>
1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用BeeCloud SDK中的支付接口，请求BeeCloud
5. BeeCloud返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
6. 打款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为打款成功
<aside class="success">
支持的渠道包括：`BC_TRANSFER`  
</aside>

> BeeCloud打款到银行卡代码示例：

```csharp
//getBankFullNames方法可以获取所有支持的银行全称，将全称填写到BCTransferWithBackCard里的bank_fullname字段
BankList banks = BCPay.getBankFullNames("P_CR");

BCTransferWithBackCard transfer = new BCTransferWithBackCard(金额, 
打款单号, 
打款标题, 
交易源, 
银行全名, 
银行卡类型, 
收款帐户类型, 
收款帐户号, 
收款帐户名称);
transfer.mobile = "xxxxxxxxxxxxxx";
try 
{
    transfer = BCPay.BCBankCardTransfer(transfer);
    Response.Write("<span style='color:#00CD00;font-size:20px'>已代付</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'title' => '企业打款测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'trade_source' => 'OUT_PC' //交易源, UTF8编码格式，目前只能填写OUT_PC
        /*
         *  如果未能确认银行的全称信息,可通过下面的接口获取并进行确认
         *  //P_DE:对私借记卡,P_CR:对私信用卡,C:对公账户
         *  $banks = \beecloud\rest\api::bc_transfer_banks(array('type' => 'P_DE'));
         *  if ($result->result_code != 0) {
         *      print_r($result);
         *      exit();
         *  }
         *  print_r($banks->bank_list);die;
         */
        'bank_fullname' => "中国银行"; //银行全称
        'card_type' => "DE"; //银行卡类型,区分借记卡和信用卡，DE代表借记卡，CR代表信用卡，其他值为非法
        'account_type' => "P"; //帐户类型，P代表私户，C代表公户，其他值为非法
        'account_no' => "6222691921993848888";   //收款方的银行卡号
        'account_name' => "test"; //收款方的姓名或者单位名
        //选填mobile
        'mobile' => ""; //银行绑定的手机号
    );
    $result = \beecloud\rest\api::bc_transfer($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bc_transfer($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo ' 打款成功';
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

附： BeeCloud打款到银行卡参数列表

参数名 | 类型 | 含义 | 描述 | 例子 
----  | ---- | ---- | ---- | ---- 
total_fee | Integer | 打款订单总金额 | 必须是正整数，单位为分 | 1 
bill_no | String | 商户订单号 | 8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 
title| String | 打款订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 
trade_source | String | 交易源| UTF8编码格式，目前只能填写OUT_PC | OUT_PC 
bank\_fullname | String | 银行全名 | 银行全称 | 中国银行，而不能写成"中行",因为“中行”也是中信银行和中兴银行的缩写 
card_type|String | 银行卡类型 | 区分借记卡和信用卡 | DE代表借记卡，CR代表信用卡，其他值为非法 
account_type|String | 收款帐户类型 | 区分对公和对私 | 帐户类型，P代表私户，C代表公户，其他值为非法 
account_no|String | 收款帐户号 | 收款方的银行卡号 | 6222691921993848888 
account_name|String | 收款帐户名称 | 收款方的姓名或者单位名 | 黄晓明 
mobile | String | 银行绑定的手机号 | 银行绑定的手机号，当需要手机收到银行入账信息时，该值必填，前提是该手机在银行有短信通知业务，否则收不到银行信息 | 13888888888 
optional | Map | 附加数据 | 用户自定义的参数，将会在Webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 

## 5.3 支付宝打款到支付宝

1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5.返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
6. 打款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为打款成功
<aside class="success">
支持的渠道包括：`ALI_TRANSFER`  
</aside>

> 支付宝打款到支付宝代码示例：

```csharp
BCTransferParameter para = new BCTransferParameter();
para.channel = BCPay.TransferChannel.ALI_TRANSFER.ToString();
para.transferNo = BCUtil.GetUUID();
para.totalFee = 100;
para.desc = "C# 单笔打款";
para.channelUserId = "XXX@163.com";
para.channelUserName = "毛毛";
para.accountName = "XXX有限公司";
string aliURL = BCPay.BCTransfer(para);
Response.Write("<a href=" + aliURL + ">付款地址</a><br/>");
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI_TRANSFER',
        'title' => '支付宝企业打款',   //订单标题
        'transfer_no' => "bcdemo" . time(),    //打款单号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
         //收款方的id 账号和 名字也需要对应
         'channel_user_id' => '',   //收款人账户
         'channel_user_name' => '', //收款人账户姓名
         'account_name' => '苏州比可网络科技有限公司' //注意此处需要和企业账号对应的全称
    );
    $result = \beecloud\rest\api::transfers($data);
    //不使用namespace的用户
    //$result = BCRESTApi::transfers($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    if(isset($result->url)){
        header("Location:$result->url");
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

附： 支付宝打款到支付宝参数列表

参数名 | 类型 | 含义 | 描述 | 例子 | 必填
----  | ---- | ---- | ---- | ---- | ----
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | ALI\_TRANSFER| 是
transfer_no | String | 打款单号 | 支付宝为11-32位数字字母组合| udjfiienx2334/8372839123 | 是
total_fee | Int | 打款金额 | 此次打款的金额,单位分,正整数 | 100 | 是
desc | String | 打款说明 | 此次打款的说明 | 赔偿 | 是
channel_user\_id | String | 用户id | 支付渠道方内收款人的标示, 支付宝为支付宝账户 | someone@126.com |是
channel_user\_name | String | 用户名| 支付渠道内收款人账户名 | 支付宝某人 | 是
account_name|String|打款方账号名称|打款方账号名全称 | 苏州比可网络科技有限公司 | 是

## 5.4 微信打款到微信

1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用BeeCloud SDK中的支付接口，请求支付宝
5.返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
6. 打款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为打款成功
<aside class="success">
支持的渠道包括：`ALI_TRANSFER`  
</aside>

> 微信打款到微信代码示例：

```csharp
BCTransferParameter para = new BCTransferParameter();
para.channel = BCPay.TransferChannel.WX_TRANSFER.ToString();
para.transferNo = "1000000000";
para.totalFee = 100;
para.desc = "C# 单笔打款";
para.channelUserId = "XXXXXXXXXXXXXXXXXX";
BCPay.BCTransfer(para);
Response.Write("完成");
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX_TRANSFER',
        'title' => '微信企业打款',   //订单标题
        'transfer_no' => "bcdemo" . time(),    //打款单号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'channel_user_id' => 'o3kKrjlROJ1qlDmFdlBQA95zxxx'   //微信openid
    );
    $result = \beecloud\rest\api::transfer($data);
    //不使用namespace的用户
    //$result = BCRESTApi::transfer($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo ' 打款成功';
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell

```

```javascript
#
```

附： 微信打款到微信参数列表

参数名 | 类型 | 含义 | 描述 | 例子 | 必填
----  | ---- | ---- | ---- | ---- | ----
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX\_TRANSFER | 是
transfer_no | String | 打款单号 | 微信企业打款为8-32位数字字母组合 | udjfiienx2334/8372839123 | 是
total_fee | Int | 打款金额 | 此次打款的金额,单位分,正整数(微信打款>=1元) | 100 | 是
desc | String | 打款说明 | 此次打款的说明 | 赔偿 | 是
channel_user\_id | String | 用户id | 支付渠道方内收款人的标示, 微信为openid | xx_sjiwajeirhwefhsahfwhru |是

# 6. 实名身份认证

## 6.1 概述

商户在APP认证用户实名信息或者在打款钱验证用户卡号信息都需要用到身份实名验证接口，BeeCloud支持的验证方式分为二要素，三要素，四要素三种，商户可以根据自己需求选择，比如APP实名验证不需要验证银行卡信息，则只需要使用二要素验证，企业打款前需要验证银行卡和姓名，可以使用三要素或者四要素验证。

用户进行实名验证时，只需输入商家需要的参数，即可实时完成身份验证。 

二要素验证：姓名、身份证号  

三要素验证：姓名、身份证号、银行账户  

四要素验证：姓名、身份证号、银行账户、手机号码  


## 6.2 实名身份认证

> 实名身份认证代码示例：

```csharp
try
{
    bool result = BCPay.BCAuthentication(身份证姓名, 
    身份证号, 
    用户银行卡号(选填), 
    用户银行卡预留手机号（选填）);

    Response.Write("<span style='color:#00CD00;font-size:20px'>" + result + "</span><br/>");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
	$data = array(
        'timestamp' => time() * 1000,
        'name' => 'jason',
        'card_no' => '6227856101009660xxx',
        'id_no' => '23082619860124xxxx',
        'mobile' => '1555551xxxx'
    );
    $result = \beecloud\rest\Auths::auth($data);
    //不使用namespace的用户
    //$result = Auths::auth($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    print_r($result);
}catch(Exception $e){
	echo $e->getMessage();
}

```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```

```swift
#222
```

```xml
#
```

# 7. 查询

## 7.1 订单查询

### 7.1.1 通过条件查询

<aside class="success">
条件包括渠道，订单号，时间段等可选参数
</aside>

> 通过条件查询代码示例：

```csharp
try
{
    //举例： 查询WX渠道的前50条订单
    BCQueryBillParameter para = new BCQueryBillParameter();
    para.channel = "WX";
    para.limit = 50;
    bills = BCPay.BCPayQueryByCondition(para);
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX', //渠道类型
        'spay_result' => true,    //只列出了支付成功的订单
    );
    $result = \beecloud\rest\api::bills($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bills($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    print_r($result);
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```

```swift
#222
```

```xml
#
```

### 7.1.2 通过支付订单ID查询

<aside class="success">
订单ID可以通过发起支付时的返回值获取，或者通过条件查询订单详情时获取
</aside>

> 通过条件查询代码示例：

```csharp
try
{
    //举例： 查询订单xxxxxxxxx的详情
    bills = BCPay.BCPayQueryById("xxxxxxxxxxxx");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'id' => '430e1584-8c52-4b09-a4f0-950423ea007e', //订单唯一表识
    );
    $result = \beecloud\rest\api::bill($data, get);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data, get);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    print_r($result);
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```

```swift
#222
```

```xml
#
```

## 7.2 退款查询

### 7.2.1 通过条件查询

<aside class="success">
条件包括渠道，订单号，时间段等可选参数
</aside>

> 通过条件查询代码示例：

```csharp
BCQueryRefundParameter para = new BCQueryRefundParameter();
para.channel = "ALI";
para.limit = 50;
try
{
    refunds = BCPay.BCRefundQueryByCondition(para);
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI' //渠道类型
    );
    $result = \beecloud\rest\api::refunds($data);
    //不使用namespace的用户
    //$result = BCRESTApi::refunds($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    print_r($result);
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```

```swift
#222
```

```xml
#
```

### 7.2.2 通过退款订单ID查询

> 通过退款订单ID查询代码示例：

```csharp
try
{
    refunds = BCPay.BCRefundQueryById("xxxxxxxxxx");
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI' //退款订单唯一表识
    );
    $result = \beecloud\rest\api::refund($data, 'get');
    //不使用namespace的用户
    //$result = BCRESTApi::refund($data, 'get');
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    print_r($result);
} catch (Exception $e) {
    echo $e->getMessage();
}
```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```

```swift
#222
```

```xml
#
```

# 7. 退款

<aside class="warning">
退款只能从服务器端发起，会用到`Master Secret`进行加密，切勿泄露`Master Secret`  
</aside>

<aside class="warning">
目前BC_渠道的订单暂时不支持退款 
</aside>

<aside class="notice">
退款单号格式为:退款日期(8位) + 流水号(3~24 位)。请自行确保在商户系统中唯一，且退款日期必须是发起退款的当天日期,同一退款单号不可重复提交，否则会造成退款单重复。流水号可以接受数字或英文字符，建议使用数字，但不可接受“000”
</aside>

> 退款代码示例：

```csharp
BCRefund refund = new BCRefund(订单号, 退款单号, 退款金额);
//可以指定退款渠道，也可以不指定，根据订单号会关联上相应的渠道
refund.channel = BCPay.RefundChannel.ALI.ToString(); 
try
{
    refund = BCPay.BCRefundByChannel(refund);
    Response.Redirect(refund.url);
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
#
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI', //渠道类型
        'bill_no' => '', //订单号
        'refund_no' => '', //退款单号
        'refund_fee' => 1, //退款金额,单位分
        //need_approval选填,是否为预退款,预退款need_approval为true,直接退款不加此参数或者为false
        'need_approval' => true
    );
    $result = \beecloud\rest\api::refund($data);
    //不使用namespace的用户
    //$result = BCRESTApi::refund($data);
    /*
     * 当渠道类型为ALI_OFFLINE_QRCODE, ALI_SCAN, WX_SCAN, WX_NATIVE,调用方法:
     * \beecloud\rest\api::offline_refund($data);
     * 不使用namespace的用户
     * BCRESTApi::offline_refund($data);
     */
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    //当channel为ALI_APP、ALI_WEB、ALI_QRCODE，并且不是预退款
    if(!isset($data["need_approval"]) && $data['channel'] == 'ALI'){
        header("Location:$result->url");
        exit();
    }
    echo '(预)退款成功, 退款表记录唯一标识ID: ".$result->id;
} catch (Exception $e) {
    echo $e->getMessage();
}

```

```ruby
#
```

```python
#
```

```shell
#
```

```javascript
#
```