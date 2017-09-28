---
title: 聚合支付 技术文档

language_tabs:
  - java: Java
  - php: PHP
  - csharp: C#
  - python: Python
  - javascript: Node.js
  - xml: Android
  - swift: Objective-C


<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a> -->

<!-- includes:
  - errors -->

<!-- search: true -->
---
# 0. 开通渠道

聚合支付支持线上线下各种场景的支付解决方案，本文档以场景的形式展示如何使用聚合支付开发，完成支付技术的接入。


## 0.1 开通渠道

**官方渠道**申请指导：  
微信APP的申请： [这里](http://payplatform.juhe.cn/doc/payapply/?index=0)  
微信公众号的申请： [这里](http://payplatform.juhe.cn/doc/payapply/?index=2)  
支付宝APP/PC网页/移动网页的申请： [这里](http://payplatform.juhe.cn/doc/payapply/?index=4)
<aside class="notice">
聚合支付提供低费率的特惠通道（包括：支付宝/微信/银联），请联系我们商务(15950049812 张经理)了解更多详情
</aside>

## 0.2 渠道参数配置

微信APP参数配置： [这里](http://payplatform.juhe.cn/doc/payapply/?index=1)  
微信公众号参数配置： [这里](http://payplatform.juhe.cn/doc/payapply/?index=3)   
支付宝APP/PC网页/移动网页参数配置： [这里](http://payplatform.juhe.cn/doc/payapply/?index=5)   
支付宝RSA秘钥配置： [这里](http://payplatform.juhe.cn/doc/payapply/?index=14)   

# 1. SDK开发

## 1.1 SDK安装和注册

### 1.1.1 SDK安装

SDK下载地址 [这里](http://payplatform.juhe.cn/download/)
<aside class="notice">
各个SDK的安装请参考SDK的readme文件
</aside>


### 1.1.2 在代码中注册聚合支付

1. 注册开发者：猛击[这里](http://payplatform.juhe.cn/register)注册成为聚合支付开发者， 并完成企业认证
<aside class="notice">
聚合支付只面向企业用户，所以企业认证是必须的。
</aside>
2. 注册应用：使用注册的账号登陆[控制台](http://payplatform.juhe.cn/dashboard/)后，点击"创建新的支付应用"创建新应用
3. 在新创建的支付应用下 应用设置下 基本信息设置中 获取 `APP ID`， `APP Secret`， `Master Secret`， `Test Secret`，其中`Test Secret`在控制台的test模式下获取。
4. 在代码中注册 

<aside class="warning">
secret是一个非常重要的数据，请您必须小心谨慎的确保此数据保存在足够安全的地方。您从聚合支付官方获得此数据的同时，即表明您保证不会被用于非法用途和不会在没有得到您授权的情况下被盗用，一旦因此数据保管不善而导致的经济损失及法律责任，均由您独自承担。
</aside>

> 注册使用如下代码:

```java
//LIVE模式使用方法
JuhePay.registerApp(appId, testSecret, appSecret, masterSecret);  
//LIVE模式中的testSecret可为null  
//默认开启LIVE模式

//测试模式使用方法
JuhePay.registerApp(appId, testSecret, **appSecret**, **masterSecret**);   
//测试模式中的appSecret、masterSecret可为null  
//设置sandbox属性为true，开启测试模式  
JuhePay.setSandbox(true);
  
```

```php 
\juhepay\rest\api::registerApp('app id', 'app secret', 'master secret', 'test secret');
//不使用namespace的用户
BCRESTApi::registerApp('app id', 'app secret', 'master secret', 'test secret') 

//LIVE模式使用方法（也可不设置，默认为LIVE模式）
\juhepay\rest\api::setSandbox(false);
//不使用namespace的用户
BCRESTApi::setSandbox(false);

//测试模式使用方法  
\juhepay\rest\api::setSandbox(true);
//不使用namespace的用户
BCRESTApi::setSandbox(true);
```

```csharp
//请注意各个参数一一对应
JuhePay.JuhePay.registerApp(appID, appSecret, masterSecret, testSecret);

//设置生产模式，真实货币交易（也可不设置，默认为上线模式）
JuhePay.JuhePay.setTestMode(false);
//设置当前为测试模式
JuhePay.JuhePay.setTestMode(true);
```

```ruby

```

```python
bc_app = BCApp()
bc_app.app_id = 'app_id'
bc_app.app_secret = 'app_secret'
bc_app.master_secret = 'master_secret'
# 如果需要开启测试模式
# bc_app.is_test_mode = True
# bc_app.test_secret = 'test_secret'

# 如果用到支付退款和打款
bc_pay = BCPay()
bc_pay.register_app(bc_app)

# 如果需要查询功能
bc_query = BCQuery()
bc_query.register_app(bc_app)
```

```javascript
const API = new BCRESTAPI();
API.registerApp(APP_ID,APP_SECRET,MASTER_SECRET,TEST_SECRET);
API.setSandbox(true);//开启测试模式 不设置就是不开启
```

```xml
JuhePay.setAppIdAndSecret("appId", "appSecret");

// 如果需要开启测试模式
// JuhePay.setSandbox(true);
// JuhePay.setAppIdAndSecret("appId", "testSecret");
```

```swift
//注意：sdk的demo中包含apple pay的例子，不能直接在模拟器上运行，请在真机上运行，
//如果要在模拟器上运行，请删除项目中（非物理文件夹）`BCPaySDK`文件夹下面`Channel`文件夹下的`ApplePay`文件夹即可

//初始化分为生产模式(LiveMode)、沙箱环境(SandboxMode)；沙箱测试模式下不产生真实交易
//开启生产环境
[JuhePay initWithAppID:@"JuhePay AppId" andAppSecret:@"JuhePay App Secret"];
  

//开启沙箱测试环境
[JuhePay initWithAppID:@"JuhePay AppId" andAppSecret:@"JuhePay Test Secret"];
[JuhePay setSandboxMode:YES];
或者
[JuhePay initWithAppID:@"JuhePay AppId" andAppSecret:@"JuhePay Test Secret" sandbox:YES];


//查看当前模式
// 返回YES代表沙箱测试模式；NO代表生产模式
[JuhePay getCurrentMode];


//初始化官方微信支付  
//如果您使用了微信支付，需要用微信开放平台Appid初始化。  
[JuhePay initWeChatPay:@"微信开放平台appid"];


//handleOpenUrl
//为保证从支付宝，微信返回本应用，须绑定openUrl. 用于iOS9之前版本
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
    if (![JuhePay handleOpenUrl:url]) {
        //handle其他类型的url
    }
    return YES;
}
//iOS9之后apple官方建议使用此方法
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options {
    if (![JuhePay handleOpenUrl:url]) {
        //handle其他类型的url
    }
    return YES;
}
```

## 1.2 网页上收款

### 1.2.1 流程概述

![pic](http://7xavqo.com1.z0.glb.clouddn.com/img-beecloud%20sdk.png)

步骤①：**（从网页服务器端）发送订单信息**  

步骤②：**收到聚合支付返回的渠道支付地址（比如支付宝的收银台）**  

步骤③：**将支付地址展示给用户进行支付**  

步骤④：**用户支付完成后通过一开始发送的订单信息中的return_url来返回商户页面**

此时商户的网页需要做相应界面展示的更新（比如告诉用户"支付成功"或"支付失败")。**不允许**使用同步回调的结果来作为最终的支付结果，因为同步回调有极大的可能性出现丢失的情况（即用户支付完没有执行return url，直接关掉了网站等等），最终支付结果应以下面的异步回调为准。

步骤⑤：**（在商户后端服务端）处理异步回调结果(Webhook)**
 
付款完成之后，根据客户在聚合支付后台的设置，聚合支付会向客户服务端发送一个Webhook请求，里面包括了数字签名，订单号，订单金额等一系列信息。客户需要在服务端依据规则要验证**数字签名是否正确，购买的产品与订单金额是否匹配，这两个验证缺一不可**。验证结束后即可开始走支付完成后的逻辑。


### 1.2.2 网页上实现支付宝收款

#### 1.2.2.1 支付宝收银台收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝收银台页面，用户登录支付宝付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`ALI_WEB`
</aside>

> 支付宝收银台收款代码示例：

``` java
BCOrder bcOrder = new BCOrder('渠道code', '金额', '订单编号', '订单标题');//设定订单信息
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
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');

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
        'return_url' => 'https://juhe.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'ALI_WEB'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'ALI_WEB';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.return_url = "https://juhe.cn";//当channel参数为 ALI_WEB 或 ALI_QRCODE 或 UN_WEB 或 JD_WAP 或 JD_WEB时为必填

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => {
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.2.2 支付宝网页二维码收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝二维码的页面，用户扫码付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`ALI_QRCODE`
</aside>

> 支付宝网页二维码收款代码示例：

``` csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
bcOrder.setBillTimeout(360);//设置订单超时时间
bcOrder.setReturnUrl(aliReturnUrl);//设置return url
try {
    bcOrder = BCPay.startBCPay(bcOrder);
    //out.println(bcOrder.getObjectId());
    out.println(bcOrder.getHtml()); // 输出支付宝收银台二维码到页面
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
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
        'return_url' => 'https://juhe.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'ALI_QRCODE'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'ALI_QRCODE';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.return_url = "https://juhe.cn";//当channel参数为 ALI_WEB 或 ALI_QRCODE 或 UN_WEB 或 JD_WAP 或 JD_WEB时为必填

//后端
const BCRESTAPI = require('juhe-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.2.3 支付宝移动网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 支付宝返回一个url或者html
6. 通过跳转到url或者将html输出到页面进而打开支付宝手机收银台页面，实现收款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="notice">
移动网页有特殊参数 use_app，默认调起支付宝APP实现原生支付，可以关闭
</aside>
<aside class="success">
支持的渠道包括：`ALI_WAP`
</aside>

> 支付宝移动网页收款代码示例：

``` csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');

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
        'return_url' => 'https://juhe.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'ALI_WAP'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'ALI_WAP';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.return_url = "https://juhe.cn";//当channel参数为 ALI_WEB 或 ALI_QRCODE 或 UN_WEB 或 JD_WAP 或 JD_WEB时为必填
    data.use_app = true;//是否尝试掉起支付宝APP原生支付

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => {
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.2.4 附： 

支付宝支付**必填**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
channel| String | 渠道类型 | 渠道code | ALI_WEB
total_fee | Integer | 订单总金额 | 必须是正整数，单位为分 | 1 
bill_no | String | 商户订单号 |8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 
title| String | 订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 
return_url | String | 同步返回页面| 支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，**<mark>中间请勿有#,?等字符</mark>** | http://juhe.cn/returnUrl.jsp 


支付宝支付其他**可选**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}

### 1.2.3 网页上实现微信收款

#### 1.2.3.1 微信公众号内网页收款

0. 微信公众号配置参数：[文档](http://payplatform.cn/doc/payapply/?index=3)
1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
<aside class="warning">
公众号支付需要用到微信特殊参数openid，如何获取openid可以通过百度，有大量的教程
</aside>
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求微信
5. 返回必要的参数，然后将这些参数传入到微信的js方法中
<aside class="warning">
这些js方法只有在微信内的浏览器才会被识别，所以只能在微信内使用
</aside>
6. 跳转到微信APP付款
7. 支付完成返回微信公众号页面
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`WX_JSAPI` `BC_WX_JSAPI`
</aside>

> 微信公众号内网页收款代码示例：

```csharp
//服务端部分，服务端将从聚合支付获取的参数传递给js，去调用微信的方法实现支付
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
//后端部分
//微信 公众号id（读取配置文件conf.properties）及微信 redirec_uri
Properties prop = loadProperty();
String wxJSAPIAppId = prop.get("wxJSAPIAppId").toString();
String wxJSAPISecret = prop.get("wxJSAPISecret").toString();
String wxJSAPIRedirectUrl = "http://javademo.payplatform.juhe.cn/demo/pay_example/pay.jsp?paytype=" + channel;
String encodedWSJSAPIRedirectUrl = URLEncoder.encode(wxJSAPIRedirectUrl);
if (request.getParameter("code") == null || request.getParameter("code").toString().equals("")) {
    String redirectUrl = "https://open.weixin.qq.com/connect/oauth2/authorize?appid=" + wxJSAPIAppId + "&redirect_uri=" + encodedWSJSAPIRedirectUrl + "&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect";
    log.info("wx jsapi redirct url:" + redirectUrl);
    response.sendRedirect(redirectUrl);
} else {
    String code = request.getParameter("code");
    String result = sendGet("https://api.weixin.qq.com/sns/oauth2/access_token?appid=" + wxJSAPIAppId + "&secret=" + wxJSAPISecret + "&code=" + code + "&grant_type=authorization_code");
    log.info("result:" + result);
    JSONObject resultObject = JSONObject.fromObject(result);
    if (resultObject.containsKey("errcode")) {
        out.println("获取access_token出错！错误信息为：" + resultObject.get("errmsg").toString());
    } else {
        String openId = resultObject.get("openid").toString();
        bcOrder.setOpenId(openId);
        try {
            bcOrder = BCPay.startBCPay(bcOrder);
            out.println(bcOrder.getObjectId());
            System.out.print(bcOrder.getObjectId());
            Map<String, String> map = bcOrder.getWxJSAPIMap();
            jsapiAppid = map.get("appId").toString();
            timeStamp = map.get("timeStamp").toString();
            nonceStr = map.get("nonceStr").toString();
            jsapipackage = map.get("package").toString();
            signType = map.get("signType").toString();
            paySign = map.get("paySign").toString();
        } catch (BCException e) {
            log.error(e.getMessage(), e);
            out.println(e.getMessage());
        }
    }
}

//js部分
<script type="text/javascript">
    callpay();
    function jsApiCall() {
        var data = {
            //以下参数的值由BCPayByChannel方法返回来的数据填入即可
            "appId": "<%=jsapiAppid%>",
            "timeStamp": "<%=timeStamp%>",
            "nonceStr": "<%=nonceStr%>",
            "package": "<%=jsapipackage%>",
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
                    WeixinJSBridge.log(res.err_msg);
                }
        );
    }
    function callpay() {
        if (typeof WeixinJSBridge == "undefined") {
            if (document.addEventListener) {
                document.addEventListener('WeixinJSBridgeReady', jsApiCall, false);
            } else if (document.attachEvent) {
                document.attachEvent('WeixinJSBridgeReady', jsApiCall);
                document.attachEvent('onWeixinJSBridgeReady', jsApiCall);
            }
        } else {
            jsApiCall();
        }
    }
</script>
```

```php
/**
 * 微信用户的openid获取请参考官方demo sdk和文档
 * https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=11_1
 * 微信获取openid php代码, 运行环境是微信内置浏览器访问时
 *
 * 注意:
 *      请修改lib/WxPayPubHelper/WxPay.pub.config.php配置文件中的参数:
 *      1.APPID, APPSECRET请修改为商户自己的微信参数(MCHID, KEY在聚合支付平台创建的应用中配置);
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
     $result = \juhepay\rest\api::bill($data);
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

```

```python
# 先获取open id
req_params = BCPayReqParams()
req_params.channel = 'WX_JSAPI'    # 或者BC_WX_JSAPI
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
req_params.openid = open_id
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数(包含app_id, package, nonce_str, timestamp, pay_sign, sign_type)做下一步处理
```

```javascript
/**
 * 微信用户的openid获取请参考官方demo sdk和文档
 * https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=11_1
 * 微信获取openid php代码, 运行环境是微信内置浏览器访问时
 */
/前端传参
let data = {}, _this = this;
    data.channel = 'WX_JSAPI';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.openid = '0950c062-5e41-xxxxxxxxxxx';//openid

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.3.2 微信移动网页（非微信浏览器）收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求微信
5. 微信返回一个url或者html 
6. 通过跳转到url或者将html输出到页面进而打开微信的跳转中转页页面，打开微信APP实现收款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`BC_WX_WAP`
</aside>

> 微信移动网页（非微信浏览器）收款代码示例：

``` csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
bcOrder.setReturnUrl(returnUrl);
try {
    bcOrder = BCPay.startBCPay(bcOrder);
    response.sendRedirect(bcOrder.getUrl()); //跳转到微信APP
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
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
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'BC_WX_WAP'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```shell

```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'BC_WX_WAP';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.3.3 微信在PC网页通过二维码收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求微信
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
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`WX_NATIVE` `BC_NATIVE`
</aside>

> 微信在PC网页通过二维码收款代码示例：

```csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setNotifyUrl("https:///apidynamic.payplatform.juhe.cn/test");
    bcOrder = BCPay.startBCPay(bcOrder);
    //将bcOrder.getCodeUrl()是二维码的值，用生成二维码的方法生成二维码即可
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
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
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'WX_NATIVE'  # 或者BC_NATIVE
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后根据返回参数code_url生成二维码
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'WX_NATIVE';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => {  
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.3.4 附： 

微信支付**必填**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
channel| String | 渠道类型 | 渠道code | WX_NATIVE
total_fee | Integer | 订单总金额 | 必须是正整数，单位为分 | 1 
bill_no | String | 商户订单号 |8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 
title| String | 订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 

微信支付其他**可选**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
return_url | String | 同步返回页面| 支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，**<mark>中间请勿有#,?等字符，只有微信移动网页支付需要这个参数</mark>**，| http://juhe.cn/returnUrl.jsp 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}

### 1.2.4 网页上实现银联收款

#### 1.2.4.1 银联PC网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求银联
5. 银联返回一个html
6. 通过将html输出到页面进而打开银联收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`UN_WEB` `BC_EXPRESS`
</aside>

> 银联PC网页收款代码示例：

```csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
bcOrder.setReturnUrl(unReturnUrl);
try {
    bcOrder = BCPay.startBCPay(bcOrder);
    out.println(bcOrder.getHtml());
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'UN_WEB', //渠道类型
        'title' => '银联PC网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'return_url' => 'https://juhe.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'UN_WEB' # 或者BC_EXPRESS
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后加载返回的表单html
```

```javascript
/**
 * 微信用户的openid获取请参考官方demo sdk和文档
 * https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=11_1
 * 微信获取openid php代码, 运行环境是微信内置浏览器访问时
 */
//前端传参
let data = {}, _this = this;
    data.channel = 'UN_WEB';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.return_url = "https://juhe.cn";//当channel参数为 ALI_WEB 或 ALI_QRCODE 或 UN_WEB 或 JD_WAP 或 JD_WEB时为必填
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.4.2 银联移动网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求银联
5. 银联返回一个html
6. 通过将html输出到页面进而打开银联收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`UN_WAP` `BC_EXPRESS`
</aside>

> 银联移动网页收款代码示例：

```csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
bcOrder.setReturnUrl(returnUrl);
try {
    bcOrder = BCPay.startBCPay(bcOrder);
    out.println(bcOrder.getHtml());
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'UN_WAP', //渠道类型
        'title' => '银联移动网页支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'return_url' => 'https://juhe.cn', //渠道类型:ALI_WEB、ALI_QRCODE、UN_WEB、JD_WAP、JD_WEB时为必填
        'bill_timeout' => 360, //京东(JD*)不支持该参数
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'UN_WAP' # 或者BC_EXPRESS
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后加载返回的表单html
```

```javascript
/**
 * 微信用户的openid获取请参考官方demo sdk和文档
 * https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=11_1
 * 微信获取openid php代码, 运行环境是微信内置浏览器访问时
 */
//前端传参
let data = {}, _this = this;
    data.channel = 'UN_WAP';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
    data.return_url = "https://juhe.cn";//当channel参数为 ALI_WEB 或 ALI_QRCODE 或 UN_WEB 或 JD_WAP 或 JD_WEB时为必填
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

#### 1.2.4.3 附： 

银联支付**必填**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
channel| String | 渠道类型 | 渠道code | UN_WEB
total_fee | Integer | 订单总金额 | 必须是正整数，单位为分 | 1 
bill_no | String | 商户订单号 |8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 
title| String | 订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 
return_url | String | 同步返回页面| 支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，**<mark>中间请勿有#,?等字符</mark>** | http://juhe.cn/returnUrl.jsp 

银联支付其他**可选**参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}


### 1.2.5 网页上实现第三方网关收款

<aside class="notice">
银行网关是指直接进入选定的银行接口进行支付
</aside>

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，根据用户选择的银行，调用相应的银行接口
5. 银行返回一个html
6. 通过将html输出到页面进而打开收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="notice">
网关支付特有参数bank：CMB    招商银行，    ICBC  工商银行，
                   BOC    中国银行，    ABC    农业银行，   BOCM    交通银行，
                   SPDB   浦发银行，   GDB   广发银行，   CITIC    中信银行，
                   CEB    光大银行，    CIB   兴业银行，   SDB  平安银行，
                   CMBC   民生银行，    NBCB   宁波银行，   BEA   东亚银行，
                   NJCB   南京银行，    SRCB   上海农商行， BOB   北京银行
</aside>
<aside class="success">
支持的渠道包括：`BC_GATEWAY` 
</aside>


> 第三方网关收款代码示例：

```csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setReturnUrl(bcGatewayReturnUrl);
    bcOrder.setGatewayBank('银行code');
    bcOrder = BCPay.startBCPay(bcOrder);
    out.println(bcOrder.getHtml());
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'BC_GATEWAY', //渠道类型
        'title' => '网关支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1, //订单金额(int 类型) ,单位分
        'bank' => 'BOC'
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'BC_GATEWAY'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'BC_GATEWAY';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

附： 网关支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}

### 1.2.6 网页上实现第三方快捷收款

<aside class="notice">
快捷支付是指银联的快捷接口（不针对单独银行，直接输入卡号）进行支付
</aside>

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，
5. 快捷接口返回一个html
6. 通过将html输出到页面进而打开收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`BC_EXPRESS` 
</aside>


> 第三方快捷收款代码示例：

```csharp
BCBill bill = new BCBill('渠道code', '金额', '订单号', '订单标题');
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
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setReturnUrl(bcGatewayReturnUrl);
    bcOrder = BCPay.startBCPay(bcOrder);
    out.println(bcOrder.getHtml());
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'BC_GATEWAY', //渠道类型
        'title' => '网关支付测试',   //订单标题
        'bill_no' => "bcdemo" . time(),    //订单编号
        'total_fee' => 1 //订单金额(int 类型) ,单位分
    );
    $result = \juhepay\rest\api::bill($data);
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

```

```python
req_params = BCPayReqParams()
req_params.channel = 'BC_GATEWAY'
req_params.title = u'支付测试'
# 分为单位
req_params.total_fee = 1
req_params.bill_no = 'bill number'
# 支付完成后的跳转页面
req_params.return_url = 'http://your.return.url.cn/'
result = bc_pay.pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后对返回参数url重定向
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'BC_GATEWAY';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

附： 快捷支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}


## 1.3. 线下收款

### 1.3.1 流程概述

支付宝微信支持面对面通过二维码的方式进行收付款

#### 1.3.1.1 用户扫商户的二维码称为扫码支付

步骤①：**商家根据用户购买商品确定金额，向聚合支付请求**  

步骤②：**收到聚合支付返回的二维码信息**  

步骤③：**将二维码展示给用户进行支付**  

步骤④：**用户支付完成后通过查询订单状态完成收款**

#### 1.3.1.2 商户扫用户的二维码称为刷卡收款

步骤①：**商家根据用户购买商品确定金额，并且通过扫码枪等工具获取用户付款二维码的内容，向聚合支付请求**  

步骤②：**用户支付完成后通过查询订单状态完成收款**  


### 1.3.2 支付宝扫码支付

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 支付宝返回一个二维码的值
6. 将二维码的值生成二维码图片展示给用户完成扫码支付
7. 调用status查询接口查看支付是否成功(可以循环查询直到取消或者查询到成功)
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`ALI_OFFLINE_QRCODE` `BC_ALI_QRCODE` 
</aside>
<aside class="warning">
`BC_ALI_QRCODE` 没有第七步
</aside>

> 支付宝扫码支付代码示例：

```csharp
//收款部分
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

//查询收款状态(可以循环查询直到取消或者查询到成功)
Bool isSuccess = BCOfflineBillStatus(订单号, null);
```

```java
//收款部分
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setTotalFee(1);
    bcOrder = BCPay.startBCPay(bcOrder);
    //bcOrder.getCodeUrl()是二维码的值，用生成二维码的方法生成二维码即可
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}

//查询收款状态(可以循环查询直到取消或者查询到成功)
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
    $result = \juhepay\rest\api::offline_bill($data);
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


//status查询接口查看支付是否成功
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => $_GET["channel"], //渠道类型
        'bill_no' => $_GET["billNo"]    //订单编号
    );
    $result = $api->offline_bill_status($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo json_encode($result);
} catch (Exception $e) {
    die($e->getMessage());
}

//页面部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>支付宝扫码示例</title>
</head>
<body>
<div style="width:100%; height:100%;overflow: auto; text-align:center;">
    <div id="qrcode"></div>
    <div id="msg"></div>
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
    
     $(function(){
        var billNo = "<?php echo $data["bill_no"];?>";
        var queryTimer = setInterval(function() {
            $("#msg").text("开始查询支付状态...");
            $.getJSON("bill.status.php", {"billNo":billNo, 'channel' : '<?php echo $data['channel']; ?>'}, function(res) {
                if(res.resultCode == 0){
                    if(res.pay_result){
                        clearInterval(queryTimer);
                        queryTimer = null;
                        $("#cancel").hide();
                    }
                    $("#msg").text(res.pay_result ? "已经支付" : '未支付');
                }
            });
        }, 3000);
     )       
</script>
</body>
</html>
```

```ruby

```

```python
req_params = BCPayReqParams()
req_params.channel = 'ALI_OFFLINE_QRCODE' # 或者BC_ALI_QRCODE
req_params.title = u'支付测试'
req_params.total_fee = 1
req_params.bill_no = 'bill number'

resp = bc_pay.offline_pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后根据返回参数code_url生成二维码
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'ALI_OFFLINE_QRCODE';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
     API.bill(req.body).then((response) => {
         res.send(response);
     })
})

//查询收款状态(可以循环查询直到取消或者查询到成功)
API.getOfflineStatus({
    bll_no:'bill_no'
}).then(res=>res.send(res))
```

### 1.3.3 微信扫码支付

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求微信
5. 微信返回一个二维码的值
6. 将二维码的值生成二维码图片展示给用户完成扫码支付
7. 调用status查询接口查看支付是否成功(可以循环查询直到取消或者查询到成功)
8. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`WX_NATIVE` `BC_NATIVE` 
</aside>
<aside class="warning">
`BC_NATIVE`没有第七步
</aside>

> 微信扫码支付代码示例：

```csharp
//收款部分
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

//查询收款状态(可以循环查询直到取消或者查询到成功)
Bool isSuccess = BCOfflineBillStatus(订单号, null);
```


```java
//收款部分
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setTotalFee(1);
    bcOrder = BCPay.startBCPay(bcOrder);
    //bcOrder.getCodeUrl()是二维码的值，用生成二维码的方法生成二维码即可
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}

//查询收款状态(可以循环查询直到取消或者查询到成功)
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
    $result = \juhepay\rest\api::bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::bill($data);
    if ($result->result_code != 0) {
        echo json_encode($result);
        exit();
    }
    $code_url = $result->code_url;
} catch (Exception $e) {
    echo $e->getMessage();
}

//status查询接口查看支付是否成功
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => $_GET["channel"], //渠道类型
        'bill_no' => $_GET["billNo"]    //订单编号
    );
    $result = $api->offline_bill_status($data);
    if ($result->result_code != 0) {
        echo json_encode($result);
        exit();
    }
    echo json_encode($result);
} catch (Exception $e) {
    die($e->getMessage());
}

//页面部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>聚合支付微信扫码示例</title>
</head>
<body>
<div align="center" id="qrcode" ></div>
<div align="center">
    <button id="query">查询订单状态</button>
    <p id="query-result"></p>
</div>
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
    
    $('#query').click(function(){
        $.getJSON('bill.status.php', {'billNo' : '<?php echo $data["bill_no"]; ?>', 'channel' : '<?php echo $data["channel"]; ?>'}, function(res){
            var str = '';
            if (res.result_code == 0 ) {
                str = res.pay_result ? "支付成功" : "未支付";
            }else if (res && res.result_code != 0) {
                str = 'Error: ' + res.err_detail;
            }else {
                str = 'Notice: 该记录不存在';
            }
            $('#query-result').text(str);
        })
    });
</script>
</body>
</html>
```

```ruby

```

```python
req_params = BCPayReqParams()
req_params.channel = 'WX_NATIVE'  # 或者BC_NATIVE
req_params.title = u'支付测试'
req_params.total_fee = 1
req_params.bill_no = 'bill number'

resp = bc_pay.offline_pay(req_params)
# 如果result.result_code为0表示请求成功
# 然后根据返回参数code_url生成二维码
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'BC_NATIVE';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})

//查询收款状态(可以循环查询直到取消或者查询到成功)
API.getOfflineStatus({
    bll_no:'bill_no'
}).then(res=>res.send(res))
```

### 1.3.4 支付宝刷卡收款

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 通过外部设备如扫码枪等获取用户的支付二维码内容，调用聚合支付 SDK中的支付接口，请求支付宝
5. 如果是免密支付，直接返回收款结果
6. 如果用户需要输入密码，调用status查询接口查看支付是否成功(可以循环查询直到取消或者查询到成功)
7. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`ALI_SCAN` `BC_ALI_SCAN` 
</aside>

> 支付宝刷卡支付代码示例：

```csharp
//收款部分
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

//查询收款状态(可以循环查询直到取消或者查询到成功)
Bool isSuccess = BCOfflineBillStatus(订单号, null);
```

```java
//收款部分
BCOrder bcOrder = new BCOrder(渠道code, 金额, 订单编号, 订单标题);//设定订单信息
try {
    bcOrder.setAuthCode("130145749397413855");//authcode就是设备获得的用户二维码的编码
    bcOrder = BCPay.startBCOfflinePay(bcOrder);
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}

//查询收款状态(可以循环查询直到取消或者查询到成功)
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
    $result = \juhepay\rest\api::offline_bill($data);
    //不使用namespace的用户
    //$result = BCRESTApi::offline_bill($data);
    if ($result->result_code != 0) {
        echo json_encode($result);
        exit();
    }
    echo '支付成功: '.$result->id;
} catch (Exception $e) {
    echo $e->getMessage();
}

//status查询接口查看支付是否成功
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => $_GET["channel"], //渠道类型
        'bill_no' => $_GET["billNo"]    //订单编号
    );
    $result = $api->offline_bill_status($data);
    if ($result->result_code != 0) {
        echo json_encode($result);
        exit();
    }
    echo json_encode($result);
} catch (Exception $e) {
    die($e->getMessage());
}

//页面部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>支付宝刷卡示例</title>
</head>
<body>
<div align="center" id="qrcode" ></div>
<div align="center">
    <button id="query">查询订单状态</button>
    <p id="query-result"></p>
</div>
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
    
    $('#query').click(function(){
        $.getJSON('bill.status.php', {'billNo' : '<?php echo $data["bill_no"]; ?>', 'channel' : '<?php echo $data["channel"]; ?>'}, function(res){
            var str = '';
            if (res.result_code == 0 ) {
                str = res.pay_result ? "支付成功" : "未支付";
            }else if (res && res.result_code != 0) {
                str = 'Error: ' + res.err_detail;
            }else {
                str = 'Notice: 该记录不存在';
            }
            $('#query-result').text(str);
        })
    });
</script>
</body>
</html>
```

```ruby

```

```python
req_params = BCPayReqParams()
req_params.channel = 'ALI_SCAN' # 或者BC_ALI_SCAN
req_params.title = u'支付测试'
req_params.total_fee = 1
req_params.bill_no = 'bill number'
req_params.auth_code = 'auth code'

resp = bc_pay.offline_pay(req_params)
# 如果result.result_code为0表示请求成功

# status查询接口查看支付是否成功
bc_query.query_offline_bill_status(bill_no)
```


```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'ALI_SCAN';//根据不同场景选择不同的支付方式 
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})


//查询收款状态(可以循环查询直到取消或者查询到成功)
API.getOfflineStatus({
    bll_no:'bill_no'
}).then(res=>res.send(res))
```

### 1.3.5 微信刷卡收款

1. 商户发起收款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 通过外部设备如扫码枪等获取用户的支付二维码内容，调用聚合支付 SDK中的支付接口，请求微信
5. 如果是免密支付，直接返回收款结果
6. 如果用户需要输入密码，调用status查询接口查看支付是否成功(可以循环查询直到取消或者查询到成功)
7. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`WX_SCAN` `BC_WX_SCAN` 
</aside>

> 微信刷卡支付代码示例：

```csharp
//收款部分
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

//查询收款状态(可以循环查询直到取消或者查询到成功)
Bool isSuccess = BCOfflineBillStatus(订单号, null);
```

```java
//收款部分
BCOrder bcOrder = new BCOrder('渠道code', '金额', '订单编号', '订单标题');//设定订单信息
try {
    bcOrder.setAuthCode("130145749397413855");//authcode就是设备获得的用户二维码的编码
    bcOrder = BCPay.startBCOfflinePay(bcOrder);
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}

//查询收款状态(可以循环查询直到取消或者查询到成功)

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
    $result = \juhepay\rest\api::offline_bill($data);
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

//status查询接口查看支付是否成功
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => $_GET["channel"], //渠道类型
        'bill_no' => $_GET["billNo"]    //订单编号
    );
    $result = $api->offline_bill_status($data);
    if ($result->result_code != 0) {
        print_r($result);
        exit();
    }
    echo json_encode($result);
} catch (Exception $e) {
    die($e->getMessage());
}

//页面部分
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>微信刷卡示例</title>
</head>
<body>
<div align="center" id="qrcode" ></div>
<div align="center">
    <button id="query">查询订单状态</button>
    <p id="query-result"></p>
</div>
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
    
    $('#query').click(function(){
        $.getJSON('bill.status.php', {'billNo' : '<?php echo $data["bill_no"]; ?>', 'channel' : '<?php echo $data["channel"]; ?>'}, function(res){
            var str = '';
            if (res.result_code == 0 ) {
                str = res.pay_result ? "支付成功" : "未支付";
            }else if (res && res.result_code != 0) {
                str = 'Error: ' + res.err_detail;
            }else {
                str = 'Notice: 该记录不存在';
            }
            $('#query-result').text(str);
        })
    });
</script>
</body>
</html>
```

```ruby

```

```python
req_params = BCPayReqParams()
req_params.channel = 'WX_SCAN'  # 或者BC_WX_SCAN
req_params.title = u'支付测试'
req_params.total_fee = 1
req_params.bill_no = 'bill number'
req_params.auth_code = 'auth code'

resp = bc_pay.offline_pay(req_params)
# 如果result.result_code为0表示请求成功

# status查询接口查看支付是否成功
bc_query.query_offline_bill_status(bill_no)
```

```javascript
//前端传参
let data = {}, _this = this;
    data.channel = 'WX_SCAN';//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;//total_fee(int 类型) 单位分
    data.bill_no = `bcdemo${data.timestamp}`;//8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复
    data.title = `node${data.channel}test`;//title UTF8编码格式，32个字节内，最长支持16个汉字
    data.optional = {tag: 'msgtoreturn'};//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据
    data.bill_timeout = 360;//选填必须为非零正整数，单位为秒，建议最短失效时间间隔必须大于360秒，京东(JD*)不支持该参数。 
                

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bill', (req, res, next) => { //支付
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})

//查询收款状态(可以循环查询直到取消或者查询到成功)
API.getOfflineStatus({
    bll_no:'bill_no'
}).then(res=>res.send(res))
```

### 1.3.6 附： 刷卡/扫码支付其他可选参数：

参数名 | 类型 | 含义 | 描述 | 示例 
----  | ---- | ---- | ---- | ---- 
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 
anaylsis | Map | 附加数据 | 商户的用户信息，可用户客户信息分析 | {"product_list":{},"ip":"value2"}

## 1.4 移动APP收款

### 1.4.1 流程概述

下图为整个支付的流程:
![pic](http://7xavqo.com1.z0.glb.clouddn.com/UML01.png)

其中需要开发者开发的只有：

步骤①**（在App端）发送订单信息**

做完这一步之后就会跳到相应的支付页面（如微信app中），让用户继续后续的支付步骤

步骤②：**（在App端）处理同步回调结果**

付款完成或取消之后，会回到客户app中，在页面中展示支付结果（比如弹出框告诉用户"支付成功"或"支付失败")。同步回调结果只作为界面展示的依据，不能作为订单的最终支付结果，最终支付结果应以异步回调为准。

步骤③：**（在客户服务端）处理异步回调结果（[Webhook](https://juhe.cn/doc/?index=webhook)）**
 
付款完成之后，根据客户在聚合支付后台的设置，聚合支付会向客户服务端发送一个Webhook请求，里面包括了数字签名，订单号，订单金额等一系列信息。客户需要在服务端依据规则要验证**数字签名是否正确，购买的产品与订单金额是否匹配，这两个验证缺一不可**。验证结束后即可开始走支付完成后的逻辑。

### 1.4.2 在APP中使用支付宝收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 调起支付宝APP，用户进行支付，支付完成后跳回商户APP
6. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
iOS的支付宝有特殊参数scheme，详情查看SDK readme文件配置部分
</aside>
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`ALI_APP` 
</aside>

> 支付宝APP支付代码示例：

```swift
- (void)doPay:(PayChannel)channel {
    NSString *billno = [self genBillNo];
    NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithObjectsAndKeys:@"value",@"key", nil];

    BCPayReq *payReq = [[BCPayReq alloc] init];
    payReq.channel = channel; //支付渠道
    payReq.title = billTitle; //订单标题
    payReq.totalFee = @"10"; //订单价格
    payReq.billNo = billno; //商户自定义订单号
    payReq.scheme = @"payDemo"; //URL Scheme,在Info.plist中配置;
    payReq.billTimeOut = 300; //订单超时时间
    payReq.optional = dict;//商户业务扩展参数，会在webhook回调时返回
    [JuhePay sendBCReq:payReq];
}
```

```xml
BCPay.PayParams payParam = new BCPay.PayParams();
payParam.channelType = BCReqParams.BCChannelTypes.ALI_APP;

//商品描述
payParam.billTitle = "支付测试";

//支付金额，以分为单位，必须是正整数
payParam.billTotalFee = 10;

//商户自定义订单号
payParam.billNum = BillUtils.genBillNum();

// 第二个参数实现BCCallback接口，在done方法中查看支付结果
BCPay.getInstance(activity).reqPaymentAsync(payParam, new BCCallback() {...});
```

### 1.4.3 在APP中使用微信收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求微信
5. 调起微信APP，用户进行支付，支付完成后跳回商户APP
6. 支付成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为支付成功
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`WX_APP` `BC_WX_APP` 
</aside>

> 微信APP支付代码示例：

```swift
- (void)doPay:(PayChannel)channel {
    NSString *billno = [self genBillNo];
    NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithObjectsAndKeys:@"value",@"key", nil];

    BCPayReq *payReq = [[BCPayReq alloc] init];
    payReq.channel = channel; //支付渠道
    payReq.title = billTitle; //订单标题
    payReq.totalFee = @"10"; //订单价格
    payReq.billNo = billno; //商户自定义订单号
    payReq.billTimeOut = 300; //订单超时时间
    payReq.optional = dict;//商户业务扩展参数，会在webhook回调时返回
    [JuhePay sendBCReq:payReq];
}
```

```xml
// 在发起微信请求之前必须先initWechatPay
BCPay.PayParams payParam = new BCPay.PayParams();
payParam.channelType = BCReqParams.BCChannelTypes.WX_APP;

//商品描述
payParam.billTitle = "支付测试";

//支付金额，以分为单位，必须是正整数
payParam.billTotalFee = 10;

//商户自定义订单号
payParam.billNum = BillUtils.genBillNum();

// 第二个参数实现BCCallback接口，在done方法中查看支付结果
BCPay.getInstance(activity).reqPaymentAsync(payParam, new BCCallback() {...});
```

### 1.4.4 在APP中使用银联收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用聚合支付 SDK中的支付接口，请求银联
5. 调起银联插件，用户进行支付，支付完成后跳回商户APP
<aside class="notice">
参数bill_no(订单号)8到32位数字字母组合，并且要求全局唯一，已经提交的订单的订单号不论是否支付成功都不能重复使用
</aside>
<aside class="success">
支持的渠道包括：`UN_APP` `BC_APP` 
</aside>

> 银联APP支付代码示例：

```swift
- (void)doPay:(PayChannel)channel {
    NSString *billno = [self  genBillNo];
    NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithObjectsAndKeys:@"value",@"key", nil];
    /**
        按住键盘上的option键，点击参数名称，可以查看参数说明
     **/
    BCPayReq *payReq = [[BCPayReq alloc] init];
    payReq.channel = channel; //支付渠道
    payReq.title = billTitle; //订单标题
    payReq.totalFee = @"10"; //订单价格
    payReq.billNo = billno; //商户自定义订单号
    payReq.billTimeOut = 300; //订单超时时间
    payReq.viewController = self; //银联支付和Sandbox环境必填
    payReq.optional = dict;//商户业务扩展参数，会在webhook回调时返回
    [JuhePay sendBCReq:payReq];
}
```

```xml
BCPay.PayParams payParam = new BCPay.PayParams();
payParam.channelType = BCReqParams.BCChannelTypes.UN_APP;

//商品描述
payParam.billTitle = "支付测试";

//支付金额，以分为单位，必须是正整数
payParam.billTotalFee = 10;

//商户自定义订单号
payParam.billNum = BillUtils.genBillNum();

// 第二个参数实现BCCallback接口，在done方法中查看支付结果
BCPay.getInstance(activity).reqPaymentAsync(payParam, new BCCallback() {...});
```

## 1.5 企业打款

### 1.5.1 概述

企业打款是指企业将钱从企业账户转到个人账号的操作
<aside class="warning">
打款只能从服务器端发起，会用到`Master Secret`进行加密，切勿泄露`Master Secret`
</aside>

### 1.5.2 聚合支付打款到银行卡

<aside class="notice">
打款到银行卡需要首先在聚合支付打款账户充值，企业打款余额可以用于打款 
</aside>
1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用聚合支付 SDK中的支付接口，请求聚合支付
5. 聚合支付返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
6. 打款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为打款成功
<aside class="success">
支持的渠道包括：`BC_TRANSFER`  
</aside>

> 聚合支付打款到银行卡代码示例：

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
String billNo = BCUtil.generateRandomUUIDPure();
BCTransferParameter bCTransferParameter = new BCTransferParameter();
bCTransferParameter.setBillNo(billNo);
bCTransferParameter.setTotalFee(1);
bCTransferParameter.setTitle("测试代付");
bCTransferParameter.setTradeSource("OUT_PC");
bCTransferParameter.setBankFullName("中国银行");
bCTransferParameter.setCardType("DE");
bCTransferParameter.setAccountType("C");
//测试时，请填写真实号码和姓名
bCTransferParameter.setAccountNo("12345678666");
bCTransferParameter.setAccountName("大宇宙银河系地球集团");
try {
    BCPay.startBCTransfer(bCTransferParameter);
    out.println("success");
} catch (BCException e) {
    out.println(e.getMessage());
}
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
         *  $banks = \juhepay\rest\api::bc_transfer_banks(array('type' => 'P_DE'));
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
    $result = \juhepay\rest\api::bc_transfer($data);
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

```

```python
transfer_params = BCCardTransferParams()
# 单位为分
transfer_params.total_fee = 100
transfer_params.bill_no = 'bill number'
# 最长支持16个汉字
transfer_params.title = u'比可企业打款测试'
# 银行全名
transfer_params.bank_fullname = u'中国银行'
# DE代表借记卡，CR代表信用卡
transfer_params.card_type = 'DE'
# 帐户类型，P代表私户，C代表公户
transfer_params.account_type = 'C'
# 收款方的银行卡号
transfer_params.account_no = 'bank account number'
# 收款方的姓名或者单位名
transfer_params.account_name = u'xxx有限公司'
# 银行绑定的手机号，当需要手机收到银行入账信息时，该值必填，前提是该手机在银行有短信通知业务，否则收不到银行信息
transfer_params.mobile = 'mobile number'

result = bc_pay.bc_transfer(transfer_params)
# 如果result.result_code为0表示请求成功
```

```shell

```

```javascript
//前端传参
  let data = {}, _this = this;
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.total_fee = 1;
    data.bill_no = `bc企业打款`;
    data.title = '白开水';
    data.trade_source = 'OUT_PC';//UTF8编码格式，目前只能填写OUT_PC  
    data.bank_fullname = '中国银行';//中国银行，而不能写成"中行",因为“中行”也是中信银行和中兴银行的缩写 
    data.card_type = 'DE';//DE代表借记卡，CR代表信用卡，其他值为非法  
    data.account_type = 'P';//帐户类型，P代表私户，C代表公户，其他值为非法 
    data.account_no = '6222691921993848888';//收款方银行卡号
    data.account_name = '周杰伦';//收款方账户名称
    data.mobile = '13888888888';//银行绑定的手机号，当需要手机收到银行入账信息时，该值必填，前提是该手机在银行有短信通知业务，否则收不到银行信息 

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bcTransfer', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

附： 聚合支付打款到银行卡参数列表

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

### 1.5.3 支付宝打款到支付宝

1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用聚合支付 SDK中的支付接口，请求支付宝
5. 支付宝返回打款页面输入商户支付宝密码，完成打款
6. 返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
7. 打款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为打款成功
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
param = new TransferParameter();
param.setChannel(TRANSFER_CHANNEL.ALI_TRANSFER);
param.setChannelUserId(aliUserId);
param.setChannelUserName(aliUserName);
param.setTotalFee(1);
param.setDescription("支付宝单笔打款！");
param.setAccountName("苏州比可网络科技有限公司");
param.setTransferNo(aliTransferNo);
try {
    String url = BCPay.startTransfer(param);
    response.sendRedirect(url);
} catch (BCException e) {
    log.error(e.getMessage(), e);
    out.println(e.getMessage());
}
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
    $result = \juhepay\rest\api::transfers($data);
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

```

```python
transfer_params = BCTransferReqParams()
transfer_params.channel = 'ALI_TRANSFER'
transfer_params.transfer_no = '打款单号'
# 分为单位
transfer_params.total_fee = 100
transfer_params.channel_user_id = '收款人支付宝账户'
transfer_params.channel_user_name = '收款人账户名'
transfer_params.account_name = '打款方账号名称'
transfer_params.desc = '打款说明'

result = bc_pay.transfer(transfer_params)
# 如果result.result_code为0表示请求成功
# 重定向返回的url，到支付宝页面输入密码确认
```

```javascript
//前端传参
  let data = {}, _this = this;
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.channel = 'ALI_TRANSFER';
    data.transfer_no = 'udjfiienx2334';//支付宝为11-32位数字字母组合， 微信企业打款为8-32位数字字母组合，微信红包为10位数字  
    data.total_fee = 100;//此次打款的金额,单位分,正整数(微信红包1.00-200元，微信打款>=1元)  
    data.desc = '赔偿';//此次打款的说明  
    data.channel_user_id = 'someone@126.com';//支付渠道方内收款人的标示, 微信为openid, 支付宝为支付宝账户
    data.channel_user_name = '支付宝某人';//支付渠道内收款人账户名， 支付宝必填 
    data.account_name = '苏州比可网络科技有限公司';//打款方账号名称,支付宝必填

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/transfer', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
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

### 1.5.4 微信打款到微信

1. 商户发起打款请求
2. 系统生成打款订单，包括打款单号，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未成功
4. 调用聚合支付 SDK中的支付接口，请求微信
5. 返回打款发起状态（发起打款成功，发起打款失败，失败有失败原因）
6. 微信打款没有webhook，以同步返回为准
<aside class="notice">
微信打款单号为10位数字 
</aside>
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
param = new TransferParameter();
param.setChannel(TRANSFER_CHANNEL.WX_TRANSFER);
param.setChannelUserId(openId);
param.setTransferNo(redpackTransferNo);
param.setTotalFee(200);
param.setDescription("微信单笔打款！");
try {
    String result = BCPay.startTransfer(param);
    out.println("微信单笔打款成功！");
} catch (BCException e) {
        log.error(e.getMessage(), e);
        out.println(e.getMessage());
}
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
    $result = \juhepay\rest\api::transfer($data);
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

```

```python
transfer_params = BCTransferReqParams()
transfer_params.channel = 'WX_TRANSFER'
transfer_params.transfer_no = '打款单号'
# 分为单位
transfer_params.total_fee = 100
transfer_params.desc = '打款说明'
transfer_params.channel_user_id = '收款人微信openid'

result = bc_pay.transfer(transfer_params)
# 如果result.result_code为0表示请求成功
```

```shell

```

```javascript
//前端传参
  let data = {}, _this = this;
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.channel = 'WX_TRANSFER';
    data.transfer_no = 'udjfiienx2334';//支付宝为11-32位数字字母组合， 微信企业打款为8-32位数字字母组合，微信红包为10位数字  
    data.total_fee = 100;//此次打款的金额,单位分,正整数(微信红包1.00-200元，微信打款>=1元)  
    data.desc = '赔偿';//此次打款的说明  
    data.channel_user_id = 'someone@126.com';//支付渠道方内收款人的标示, 微信为openid, 支付宝为支付宝账户
    data.redpack_info = {
                            send_name: 'juhe',//红包发送者名称 32位 
                            wishing: '聚合支付祝福开发者工作顺利!',//红包祝福语 128 位 
                            act_name: '聚合支付开发者红包轰动'//红包活动名称 32位 
                        };

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/transfer', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

附： 微信打款到微信参数列表

参数名 | 类型 | 含义 | 描述 | 例子 | 必填
----  | ---- | ---- | ---- | ---- | ----
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX\_TRANSFER | 是
transfer_no | String | 打款单号 | 10位数字 | 8372839123 | 是
total_fee | Int | 打款金额 | 此次打款的金额,单位分,正整数(微信打款>=1元) | 100 | 是
desc | String | 打款说明 | 此次打款的说明 | 赔偿 | 是
channel_user\_id | String | 用户id | 支付渠道方内收款人的标示, 微信为openid | xx_sjiwajeirhwefhsahfwhru |是

## 1.6 实名身份认证

### 1.6.1 概述

商户在APP认证用户实名信息或者在打款钱验证用户卡号信息都需要用到身份实名验证接口，聚合支付支持的验证方式分为二要素，三要素，四要素三种，商户可以根据自己需求选择，比如APP实名验证不需要验证银行卡信息，则只需要使用二要素验证，企业打款前需要验证银行卡和姓名，可以使用三要素或者四要素验证。

用户进行实名验证时，只需输入商家需要的参数，即可实时完成身份验证。 

二要素验证：姓名、身份证号  

三要素验证：姓名、身份证号、银行账户  

四要素验证：姓名、身份证号、银行账户、手机号码  


### 1.6.2 实名身份认证

<aside class="notice">
移动端暂时只支持二要素的认证
</aside>

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
String name = "冯晓波";
String idNo = "320504192306171022";
String cardNo = "6114335124826228";
String mobile = "13761231321";
BCAuth auth = new BCAuth(name, idNo, cardNo);
auth.setMobile(mobile);
try {
    auth = BCPay.startBCAuth(auth);
    out.println(auth.isAuthResult());//认证结果
} catch (BCException e) {
    out.println(e.getMessage());
}
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
    $result = \juhepay\rest\Auths::auth($data);
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

```

```python
# 方法在juhepay.utils包中
result = verify_card_factors(bc_app,  # BCApp实例
                             '身份证姓名',
                             '身份证号',
                             '用户银行卡号',  # 选填
                             '用户银行卡预留手机号' # 选填
                             )
# result.result_code为0表示鉴权成功
```

```shell

```

```javascript
//前端
let data = {},_this = this;
        data.timestamp = new Date().valueOf();//时间戳，毫秒数 
        data.name = 'xuqi';
        data.id_no = '23082619860124xxxx';
        data.card_no = '6227856101009660xxx';
        data.mobile = '1555551xxxx';


//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/auth', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

```swift
[BCAuthReq authReqWithName:@"姓名" idNo:@"身份证号码"];
```

```xml
// 二要素鉴权示例
BCValidationUtil.verifyCardFactors(
    "姓名",
    "身份证号码",
    new BCCallback() { ... });
```

## 1.7 查询

### 1.7.1 订单查询

#### 1.7.1.1 通过条件查询

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
BCQueryParameter param = new BCQueryParameter();
if (querytype != null && querytype != "") {
    try {
        PAY_CHANNEL channel = PAY_CHANNEL.valueOf(querytype);
        param.setChannel(channel);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
param.setNeedDetail(true);
try {
    int count = BCPay.startQueryBillCount(param);
    pageContext.setAttribute("count", count);
} catch (BCException e) {
    out.println(e.getMessage());
}
try {
    List<BCOrder> bcOrders = BCPay.startQueryBill(param);
    pageContext.setAttribute("bills", bcOrders);
    pageContext.setAttribute("billSize", bcOrders.size());
} catch (BCException e) {
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'WX', //渠道类型
        'spay_result' => true,    //只列出了支付成功的订单
    );
    $result = \juhepay\rest\api::bills($data);
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

```

```python
query_params = BCQueryReqParams()
# 如果查询全部订单channel不设置即可
query_params.channel = 'WX'
# 限制只返回前50条订单
query_params.limit = 50
result = bc_query.query_bills(query_params)
# 如果查询成功result.bills为juhepay.entity.BCBill的实例列表
```

```shell

```

```javascript
//前端
let data = {}, _this = this;
        data.channel = this.props.params.channel;//根据不同场景选择不同的支付方式  
        data.timestamp = new Date().valueOf();//时间戳，毫秒数 
        //data.limit = 20;//默认为10，最大为50. 设置为10表示只返回满足条件的10条数据
        //start_time - 毫秒时间戳, 13位
        //end_time - 毫秒时间戳, 13位
        data.spay_result = true;//支付成功订单
        


//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/bills', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

```swift
//通过构造`BCQueryBillsReq`的实例，使用[JuhePay sendBCReq:req]方法发起支付查询。  
//响应事件类型对象：BCQueryBillsResp
//支付订单对象: BCQueryBillResult
BCQueryBillsReq *req = [[BCQueryBillsReq alloc] init];
req.channel = channel;
req.billStatus = BillStatusOnlySuccess; //支付成功的订单
req.needMsgDetail = YES; //是否需要返回支付成功订单的渠道反馈的具体信息
//req.billno = @"20150901104138656";   //订单号
//req.startTime = @"2015-10-22 00:00"; //订单时间
//req.endTime = @"2015-10-23 00:00";   //订单时间
req.skip = 0;
req.limit = 10;
[JuhePay sendBCReq:req];
```

```xml
params = new BCQuery.QueryParams();
params.channel = BCReqParams.BCChannelTypes.WX;
params.limit = 50;
BCQuery.getInstance().queryBillsAsync(params, new BCCallback() { ... });
# callback中将BCResult转成BCQueryBillsResult做后续处理
```

可选参数详情:  

参数名 | 类型 | 含义 | 描述 | 例子
----  | ---- | ---- | ---- | ---- 
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_APP等
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 
spay_result | Bool | 订单是否成功 | 标识订单是否支付成功 | true 
refund_result | Bool | 订单是否已退款 | 标识订单是否已退款 | true 
need_detail | Bool | 是否需要返回渠道详细信息 | 决定是否需要返回渠道的回调信息，true为需要 | true 
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 
skip | Integer| 查询起始位置 | 默认为0. 设置为10表示忽略满足条件的前10条数据| 0 
limit| Integer | 查询的条数 | 默认为10，最大为50. 设置为10表示只返回满足条件的10条数据 | 10 

#### 1.7.1.2 通过支付订单ID查询

<aside class="success">
订单ID可以通过发起支付时的返回值获取，或者通过条件查询订单详情时获取
</aside>

> 通过订单ID查询代码示例：

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
try {
    BCOrder result = BCPay.startQueryBillById(id);
    pageContext.setAttribute("bill", result);
} catch (BCException e) {
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'id' => '430e1584-8c52-4b09-a4f0-950423ea007e', //订单唯一表识
    );
    $result = \juhepay\rest\api::bill($data, get);
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

```

```python
result = bc_query.query_bill_by_id('bill id')
# 如果查询成功result.pay为juhepay.entity.BCBill的实例
```

```shell

```

```javascript
//前端
let data = {},_this = this;
        data.id = this.props.params.id;
        data.timestamp = new Date().valueOf();//时间戳，毫秒数 
        data.type = this.props.params.type;

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/queryById', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

```swift
//通过构造`BCQueryBillByIdReq`的实例，使用`[BeeCloud sendBCReq:req]`方法发起查询支付订单。  
//响应事件类型: `BCQueryBillByIdResp`
BCQueryBillByIdReq *req = [[BCQueryBillByIdReq alloc] initWithObjectId:bcId];
[BeeCloud sendBCReq:req];
```

```xml
BCQuery.getInstance().queryBillByIDAsync(
                "bill id",
                new BCCallback(){...});
```

### 1.7.2 退款查询

#### 1.7.2.1 通过条件查询

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
BCQueryParameter param = new BCQueryParameter();
if (querytype != null && querytype != "") {
    try {
        PAY_CHANNEL channel = PAY_CHANNEL.valueOf(querytype);
        param.setChannel(channel);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
param.setNeedDetail(true);
try {
    int count = BCPay.startQueryRefundCount(param);
    pageContext.setAttribute("count", count);
} catch (BCException e) {
    e.printStackTrace();
    out.println(e.getMessage());
}
try {
    List<BCRefund> bcRefunds = BCPay.startQueryRefund(param);
    pageContext.setAttribute("refundList", bcRefunds);
    pageContext.setAttribute("refundSize", bcRefunds.size());
} catch (BCException e) {
    e.printStackTrace();
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI' //渠道类型
    );
    $result = \juhepay\rest\api::refunds($data);
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

```

```python
query_params = BCQueryReqParams()
# 如果查询全部订单channel不设置即可
query_params.channel = 'WX'
result = bc_query.query_refunds(query_params)
# 如果查询成功result.refunds为juhepay.entity.BCRefund的实例列表
```

```javascript
//前端
let data = {}, _this = this;
    data.channel = this.props.params.channel;//根据不同场景选择不同的支付方式  
    data.timestamp = new Date().valueOf();//时间戳，毫秒数 
    data.app_sign = md5(config.APP_ID + data.timestamp + config.APP_SECRET);
    //start_time - 毫秒时间戳, 13位
    //end_time - 毫秒时间戳, 13位
    data.need_approval = this.props.params.type==='refunds'?false:true;
    data.limit = limit;//默认为10，最大为50. 设置为10表示只返回满足条件的10条数据
    data.skip = skip;

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/refunds', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

```swift
//通过构造`BCQueryRefundsReq`的实例，使用`[BeeCloud sendBCReq:req]`方法发起退款查询。  
//响应事件类型对象：`BCQueryRefundsResp` 
//退款订单对象: `BCQueryRefundResult`
BCQueryRefundsReq *req = [[BCQueryRefundsReq alloc] init];
req.channel = channel;
req.needApproved = NeedApprovalAll; 
//  req.billno = @"20150722164700237";
//  req.starttime = @"2015-07-21 00:00";
// req.endtime = @"2015-07-23 12:00";
//req.refundno = @"20150709173629127";
req.skip = 0;
req.limit = 10;
[BeeCloud sendBCReq:req];
```

```xml
params = new BCQuery.QueryParams();
params.channel = BCReqParams.BCChannelTypes.WX;
BCQuery.getInstance().queryRefundsAsync(params, new BCCallback() { ... });
# callback中将BCResult转成BCQueryRefundsResult做后续处理
```

可选参数详情:

参数名 | 类型 | 含义 | 描述 | 例子 
----  | ---- | ---- | ---- | ---- 
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_NATIVE等
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 
refund_no | String | 商户退款单号 | 发起退款时填写的退款单号 | 201506101035040000001 
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 
need_approval | Bool | 需要审核 | 标识退款记录是否为预退款   | true 
need_detail | Bool | 是否需要返回渠道详细信息 | 决定是否需要返回渠道的回调信息，true为需要 | true 
skip | Integer | 查询起始位置 | 默认为0. 设置为10，表示忽略满足条件的前10条数据| 0 
limit| Integer | 查询的条数 | 默认为10，最大为50. 设置为10，表示只查询满足条件的10条数据 | 10 

#### 1.7.2.2 通过退款订单ID查询

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
try {
    BCRefund result = BCPay.startQueryRefundById(id);
    pageContext.setAttribute("refund", result);
} catch (BCException e) {
    out.println(e.getMessage());
}
```

```php
try {
    $data = array(
        'timestamp' => time() * 1000,
        'channel' => 'ALI' //退款订单唯一表识
    );
    $result = \juhepay\rest\api::refund($data, 'get');
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

```

```python
result = bc_query.query_refund_by_id(refund_id)
# 如果查询成功result.refund为juhepay.entity.BCRefund的实例
```

```shell

```

```javascript
//前端
let data = {},_this = this;
        data.id = this.props.params.id;
        data.timestamp = new Date().valueOf();//时间戳，毫秒数 
        data.type = 'refund';

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/queryById', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```

```swift
//通过构造`BCQueryRefundByIdReq`的实例，使用`[BeeCloud sendBCReq:req]`方法发起查询支付订单。  
//响应事件类型: `BCQueryRefundByIdResp`**
//bcId会在退款的回调中返回
BCQueryRefundByIdReq *req = [[BCQueryRefundByIdReq alloc] initWithObjectId:bcId];
[BeeCloud sendBCReq:req];
```

```xml
BCQuery.getInstance().queryRefundByIDAsync(
          "refund id",
                    new BCCallback() {...});
```

## 1.8 退款

1. 商户发起退款请求
2. 系统生成退款订单，包括退款单号，需要退款的订单号，金额等信息
3. 将退款单存入自己系统数据库中，标记订单为未退款
4. 调用聚合支付 SDK中的退款接口，根据用户订单的渠道，调用相应的接口
5. 微信，银联在调用退款接口后直接进行退款，支付宝需要打开支付宝的退款页，输入密码完成退款 **（支付宝即时到帐不支持无秘退款，支付宝2017年新版移动网页，APP支付支持无秘退款）**
6. 退款成功，webhook通知商户服务器，商户校验后将自己数据库中的订单标记为退款成功

<aside class="warning">
退款只能从服务器端发起，会用到`Master Secret`进行加密，切勿泄露`Master Secret`  
</aside>

<aside class="notice">
退款单号格式为:退款日期(8位) + 流水号(3~24 位)。请自行确保在商户系统中唯一，且退款日期必须是发起退款的当天日期,同一退款单号不可重复提交，否则会造成退款单重复。流水号可以接受数字或英文字符，建议使用数字，但不可接受“000”
</aside>

<aside class="success">
（绝大多数渠道）支持多次退款，只需有余额就可以退。<p>  
如果有些渠道不支持部分退款，或者只能退一次，在BeeCloud管理平台的订单详情中有显示
</aside>

> 退款代码示例：

```csharp
BCRefund refund = new BCRefund(订单号, 退款单号, 退款金额);
//可以指定退款渠道，也可以不指定，根据订单号会关联上相应的渠道
refund.channel = BCPay.RefundChannel.ALI.ToString(); 
try
{
    refund = BCPay.BCRefundByChannel(refund);
    //如果是支付宝，则跳转去支付宝的退款页输入密码完成退款
    //Response.Redirect(refund.url);
}
catch (Exception excption)
{
    Response.Write("<span style='color:#00CD00;font-size:20px'>" + excption.Message + "</span><br/>");
}
```

```java
BCRefund param = new BCRefund(订单号, 退款单号, 退款金额);
param.setOptional(optional);//optional 可选业务参数
try {
    BCRefund refund = BCPay.startBCRefund(param);
    if (refund.getAliRefundUrl() != null) {
        response.sendRedirect(refund.getAliRefundUrl());
    } else {
        out.println("退款成功！易宝、百度、快钱渠道还需要定期查询退款结果！");
        out.println(refund.getObjectId());
    }
} catch (BCException e) {
    out.println(e.getMessage());
    e.printStackTrace();
}
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
    $result = \juhepay\rest\api::refund($data);
    //不使用namespace的用户
    //$result = BCRESTApi::refund($data);
    /*
     * 当渠道类型为ALI_OFFLINE_QRCODE, ALI_SCAN, WX_SCAN, WX_NATIVE,调用方法:
     * \juhepay\rest\api::offline_refund($data);
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

```

```python
refund_params = BCRefundReqParams()
# 退款channel为选填参数
refund_params.channel = 'WX'
refund_params.refund_no = '退款流水号'
refund_params.bill_no = '需要退款的订单流水号'
# 退款金额，分为单位
refund_params.refund_fee = 1
result = bc_pay.refund(refund_params)
# 如果result.result_code为0表示请求成功
# 对于支付宝退款，需要重定向至result.url
```

```javascript
//前端
let data = refundData,_this = this;
data.channel = this.props.params.channel;//根据不同场景选择不同的支付方式  
data.timestamp = new Date().valueOf();//时间戳，毫秒数 
//格式为:退款日期(8位) + 流水号(3~24 位)。请自行确保在商户系统中唯一，
//且退款日期必须是发起退款的当天日期,同一退款单号不可重复提交，否则会造成退款单重复。
//流水号可以接受数字或英文字符，建议使用数字，但不可接受“000”  
data.refund_no ='201506101035040000001';
data.refund_fee = parseInt(this.refs.fee.value);
//用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据  
data.optional = {"key1":"value1","key2":"value2"};

//后端
const BCRESTAPI = require('juhepay-node-sdk');
const API = new BCRESTAPI();

app.post('/api/refund', (req, res, next) => { 
    API.bill(req.body).then((response) => {
        res.send(response);
    })
})
```


附： 退款可选参数

参数名 | 类型 | 含义   | 描述 | 例子 
---- | ---- | ---- | ---- | ---- | ----
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} 
refund_account | Integer | 微信退款资金来源 | 1:可用余额退款 0:未结算资金退款（默认使用未结算资金退款） | 1 

# 2. 秒支付Button开发

## 2.1 概览

秒收款基于聚合支付 SDK，是BeeCloud在SDK基础上专门开发出的一个轻量级版本。它只需要短短一句代码即可使用，并且内嵌了一套风格简约的支付页面和完成页面，同时适用于PC端和移动端。这个功能对于入门级开发者来说，不但降低了开发成本，还能够优化用户在支付流程当中的体验。

### 2.1.1 使用效果

网页上出现支付渠道选择菜单，点击其中渠道跳转到指定渠道的支付页面，WEB和WAP端效果分别如下图：  

PC端如下：

![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button2.gif)

移动H5端如下：  

![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button_wap.gif)

### 2.1.2 适用范围
<aside class="success">
可以实现:
</aside>
1. 秒支付button支持的渠道**支付宝网页/移动网页支付**，**公众号内微信支付**，**微信扫码支付**，**银联网页/移动网页支付**
2. 不需要自己写样式
3. 不需要自己做移动适配
4. 如果只有一个支付渠道，可以直接跳转付款页不弹出渠道选择框

<aside class="warning">
不能满足:
</aside>
1. 不能定制UI
2. 不能在新页面打开支付页，只能从当前页面跳转  
3. 一些进阶的参数不能在秒支付button使用，比如设置订单的超时时间等

## 2.2 使用前准备

1. BeeCloud[注册](http://payplatform.juhe.cn/register/)账号, 并完成企业认证

2. BeeCloud中创建应用，填写支付渠道所需参数, 可以参考[官网帮助文档](http://payplatform.juhe.cn/doc/payapply)

3. 申请渠道参数，并配置BeeCLoud各个支付渠道的参数，此处请参考官网的[渠道参数帮助页](https://payplatform.juhe.cn/doc/payapply/?index=0)
<aside class="notify">
BeeCloud中配置参数需要完成企业认证后才能填写!
</aside>

4. 激活秒支付button功能，进入APP->设置->秒支付button项，拖拽支付渠道开启该支付渠道，同时还可以调整你需要的渠道菜单的显示顺序，点击”保存“后会生成appid对应的**script标签**。**需要将此script标签放到任何需要使用秒支付Button的网页里**。

![支付设置前](http://beeclouddoc.qiniudn.com/sbutton.jpg)

## 2.3 接口说明
### 2.3.1 BC.click原型
  
`
BC.click(data, event);
` 

### 2.3.2 参数data选填字段说明
参数名 | 类型 | 含义 | 限制
----  | ---- | ---- | ---- 
return\_url | String | 支付成功后跳转地址，除微信内jsapi支付不支持 | 必须以http://或https://开头, **不允许带任何参数** 
debug | bool | 调试信息开关, 开启后将alert一些信息 | 默认为false 
optional | Object | 支付完成后，webhook将收到的自定义订单相关信息 | 目前只支持javascript基本类型的{key:value}, 不支持嵌套对象 
instant\_channel | String | 设置该字段后将直接调用渠道支付，不再显示渠道选择菜单 | "ali"(支付宝网页/移动网页), <br>"wxmp"(微信扫码),<br>"bcwxmp"(BC微信扫码),<br>"wx"(微信公众号支付),<br>"bcwx"(BC微信公众号),<br>"un"(银联网页),<br>"unwap"(银联手机网页),<br>"bckj"(银联快捷)
need\_ali\_guide | bool | 微信内是否使用支付宝支付引导页，若不使用设置false | 默认为true 
openid | String | 微信内调JSAPI支付必须参数 | 需要开发者微信网页授权获取  
use_app | bool | 移动端网页直接调用支付宝APP支付，默认true，传false不启用 | 仅限支付宝移动网页端起作用 

### 2.3.3 选填参数event说明
<aside class="notify">
注意只有在支付授权目录下支付时，微信才会调用jsapi中注册的函数；
</aside>
<aside class="notify">
测试授权目录下的支付不会出发wxJsapiFinish等事件
</aside>
<aside class="notify">
有关微信jsapi的返回结果res,请参考 https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7
</aside>

参数名 | 类型 | 描述 
----  | ---- | ---- 
dataError | function(msg) | 数据获取出错,将调用此接口; 只传递一个参数为Object,其中有错误描述 
wxJsapiFinish | function(res) | 微信jsapi的支付接口调用完成后将调用此接口; 只传递一个参数，为微信原生的结果Object 
wxJsapiSuccess | function(res) | 微信jsapi的接口支付成功后将调用此接口; 只传递一个参数，为微信原生的结果Object 
wxJsapiFail | function(res) | 微信jsapi的接口支付非成功都将调用此接口; 只传递一个参数，为微信原生的结果Object 

## 2.4 在支付页面集成代码

### 2.4.1 普通浏览器示例

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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
            "return_url" : "http://payservice.juhe.cn/spay/result.php", //支付成功后跳转的商户页面,可选，默认为http://payservice.juhe.cn/spay/result.php
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
                document.addEventListener('juhepay:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('juhepay:onready', bcPay);
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>

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
            "return_url" : "http://payservice.juhe.cn/spay/result.php", //支付成功后跳转的商户页面,可选，默认为http://payservice.juhe.cn/spay/result.php
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

    <script id='spay-script' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
                document.addEventListener('juhepay:onready', bcPay, false);
            }else if (document.attachEvent){
                document.attachEvent('juhepay:onready', bcPay);
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

### 2.4.2 微信JSAPI（微信公众号内支付）示例

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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>

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

    <script id='spay-script' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
                document.addEventListener('juhepay:onready', bcPay, false);
            }else if (document.attachEvent){
                document.attachEvent('juhepay:onready', bcPay);
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
<script id='spay-script' type='text/javascript' src='https://jspay.juhe.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
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
                document.addEventListener('juhepay:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('juhepay:onready', bcPay);
            }
        } else {
            bcPay();
        }
    }
</script>
</body>
</html>
```

## 2.5 处理支付结果
各支付渠道通常会提供多种方式获取支付结果：

1. (建议不要用)客户的支付页面在支付成功后跳转到商户指定的的url(秒支付 Button统一包装为return_url参数), 但是此方式受到客户操作影响,可能不成功。
2. 支付渠道在支付成功后，将相关通知（俗称’回调‘）商户指定的url（BeeCloud统一封装为webhook并且统一支持用户自定义回调参数’optional‘）

建议使用webhook作为处理支付结果方式，使用请参考[webhook指南]

# 3. Webhook开发
## 3.1 简介

Webhook是BeeCloud获得渠道的确认信息后，立刻向商户服务器发送的异步回调。支付，代付，退款成功时，BeeCloud将向商户指定的URL发送HTTP/HTTPS的POST数据请求。

如果商户需要接收此类消息来实现业务逻辑，需要:

1. 开通公网可以访问的IP地址(或域名）和端口（如果需要对传输加密，请使用支持HTTPS的URL地址，BeeCloud不要求HTTPS根证书认证）
2. 在 **控制台->选择应用->应用设置->Webhook参数设置** 中设置接收端URL，不同应用可设置不同URL，同一应用能且仅能设置一个测试URL，一个生产URL，另外在**控制台->应用设置->基本信息设置**中获取"Master Secret"
2. 处理POST请求报文，实现业务逻辑

<aside class="notify">
服务器间的交互，不像页面跳转同步通知（REST API中bill的参数return_url指定）可以在页面上显示出来，这种交互方式是通过后台通信来完成的，对用户是不可见的。
</aside>

## 3.2 推送机制

BeeCloud在收到渠道的确认结果后立刻发送Webhook，Webhook只会从如下IP地址发送：

- 123.57.146.46
- 182.92.114.175

商户服务器接收到某条Webhook消息时如果未返回字符串 **success**, BeeCloud将认为此条消息未能被成功处理, 将触发推送重试机制：

BeeCloud将在2秒，4秒，8秒，...，2^17秒（约36小时）时刻重发；如果在以上任一时刻，BeeCloud收到了 **success**，重试将终止。

## 3.3 处理Webhook消息

请参考各开发语言的Webhook demo学习如何处理Webhook消息。

### 3.3.1 验证数字签名

目的在于验证Webhook是由BeeCloud发起的，防止黑客向此Webhook接口发送伪造的订单信息。Beecloud使用MD5方式进行webhook加密，商户需要按照如下方法对参数进行数字签名验证。具体信息如下：  

验证方法 | MD5  
---- | ----
BeeCloud签名字段 | **signature**  
验签内容 | app_id + transaction\_id + transaction\_type + channel\_type + transaction\_fee + master\_secret

商户必须按照上表中“验签内容”中罗列的参数顺序，将参数连接成字符串，然后进行MD5数字签名(32字符十六进制)。比较所得结果与`signature`字段值是否相等。其中`master_secret`为注册后从BeeCloud获取的密钥。**注意，`signature`中的字母全部为小写**。

### 3.3.2 过滤重复的Webhook

同一条订单可能会发送多条支付成功的webhook消息，这有可能是由支付渠道本身触发的(比如渠道的重试)，也有可能是BeeCloud的Webhook重试。商户需要根据订单号进行判重，忽略已经处理过的订单号对应的Webhook。


### 3.3.3 验证订单金额

商户需要验证Webhook中的 **transaction_fee** （实际的交易金额）与客户内部系统中的相应订单的金额匹配。

这个验证的目的在于防止黑客反编译了iOS或者Android app的代码，将本来比如100元的订单金额改成了1分钱，应该识别这种情况，避免误以为用户已经足额支付。Webhook传入的消息里面应该以某种形式包含此次购买的商品信息，比如title或者optional里面的某个参数说明此次购买的产品是一部iPhone手机，或者直接根据订单号查询，客户需要在内部数据库里去查询iPhone的金额是否与该Webhook的订单金额一致，仅有一致的情况下，才继续走正常的业务逻辑。如果发现不一致的情况，排除程序bug外，需要去查明原因，防止不法分子对你的app进行二次打包，对你的客户的利益构成潜在威胁。而且即使有这样极端的情况发生，只要按照前述要求做了购买的产品与订单金额的匹配性验证，客户也不会有任何经济损失。

### 3.3.4 处理业务逻辑和返回

这里就可以完成业务逻辑的处理。最后返回结果。用户返回 **success** 字符串给BeeCloud表示 - **正确接收并处理了本次Webhook**，其他所有返回都代表需要继续重传本次的Webhook请求。

## 3.4 Webhook接口标准

```
HTTP 请求类型 : POST
HTTP 数据格式 : JSON
HTTP Content-type : application/json
```

### 3.4.1 字段说明


  Key             | Type          | Example
-------------     | ------------- | -------------
  signature       | String        | 32位小写
  timestamp       | Long          | 1426817510111
  channel_type     | String        | 'WX' or 'ALI' or 'UN' or 'KUAIQIAN' or 'JD' or 'BD' or 'YEE' or 'PAYPAL' or 'BC'
  sub\_channel\_type | String        | 'WX\_APP' or 'WX\_NATIVE' or 'WX\_JSAPI' or 'WX\_SCAN' or 'ALI\_APP' or 'ALI\_SCAN' or 'ALI\_WEB' or 'ALI\_QRCODE' or 'ALI\_OFFLINE\_QRCODE' or 'ALI\_WAP' or 'UN\_APP' or 'UN\_WEB' or 'PAYPAL\_SANDBOX' or 'PAYPAL\_LIVE' or 'JD\_WAP' or 'JD\_WEB' or 'YEE\_WAP' or 'YEE\_WEB' or 'YEE\_NOBANKCARD' or 'KUAIQIAN\_WAP' or 'KUAIQIAN\_WEB' or 'BD\_APP' or 'BD\_WEB' or 'BD\_WAP' or 'BC\_TRANSFER' or 'ALI_TRANSFER'  
  transaction_type | String        | 'PAY' or 'REFUND' or 'TRANSFER'（TRANSFER只代表BC企业打款和支付宝打款；微信打款和红包，没有webhook）
  transaction_id   | String        | '201506101035040000001'
  transaction_fee  | Integer       | 1 表示0.01元 
  trade_success  | Bool       | true
  message_detail   | Map(JSON)     | {orderId:xxxx}
  optional        | Map(JSON)     | {"agent_id":"Alice"}

### 3.4.2 参数含义

key  | value
---- | -----
signature | 服务器端通过计算 **app\_id + transaction\_id + transaction\_type + channel\_type + transaction\_fee + master\_secret** 的MD5生成的签名(32字符十六进制),请在接受数据时自行按照此方式验证signature的正确性，不正确不返回success即可. 其中master\_secret为用户创建Beecloud App时获取的参数。
timestamp | 服务端的时间（毫秒）
channel_type| WX/ALI/UN/KUAIQIAN/JD/BD/YEE/PAYPAL   分别代表微信/支付宝/银联/快钱/京东/百度/易宝/PAYPAL
sub_channel\_type|  代表以上各个渠道的子渠道，参看字段说明
transaction_type| PAY/REFUND  分别代表支付和退款的结果确认
transaction_id | 交易单号，对应支付请求的bill\_no或者退款请求的refund\_no,对于秒支付button为传入的out\_trade\_no
transaction_fee | 交易金额，是以分为单位的整数，对应支付请求的total\_fee或者退款请求的refund\_fee
trade_success | 交易是否成功，目前收到的消息都是交易成功的消息
message_detail| {orderId:xxx…..} 从支付渠道方获得的详细结果信息，例如支付的订单号，金额， 商品信息等，详见附录
optional| 附加参数，为一个JSON格式的Map，客户在发起购买或者退款操作时添加的附加信息
  
## 3.5 样例代码
目前BeeCloud提供获取webhook消息的各语言代码样例：  
[PHP DEMO](https://github.com/beecloud/beecloud-php/blob/master/demo/webhook.php)  
[.NET DEMO](https://github.com/beecloud/beecloud-dotnet/blob/master/BeeCloudSDKDemo/notify.aspx.cs)  
[Java DEMO](https://github.com/beecloud/beecloud-java/blob/master/demo/WebRoot/webhook_receiver_example/webhook_receiver.jsp)  
[Python DEMO](https://github.com/beecloud/beecloud-python/blob/master/demo/webhook.py)


<aside class="notify">
请注意发送的HTTP头部Content-type为application/json,而非大部分框架自动解析的application/x-www-form-urlencoded格式,可能需要自行读取后解析,注意参考各样例代码中的读取写法。
</aside>


## 3.6 附录 - message_detail 样例 

- **支付宝 (ALI)**

>支付宝 (ALI)

```
"message_detail":{
"bc_appid":"test”,
"discount":"0.00",
"payment_type":"1",
"subject":"测试",
"trade_no":"2015052300001000620053541865",
"buyer_email":"13909965298",
"gmt_create":"2015-05-23 22:26:20",
"notify_type":"trade_status_sync",
"quantity":"1",
"out_trade_no":"test_no",
"seller_id":"2088911356553910",
"notify_time":"2015-05-23 22:26:20",
"body":"测试",
"trade_status":"WAIT_BUYER_PAY",
"is_total_fee_adjust":"Y",
"total_fee":"0.10",
"seller_email":"test@test.com",
"price":"0.10",
"buyer_id":"2088802823343621",
"notify_id":"e79d45a7c43180db6041da31deb51bdf5g",
"use_coupon":"N",
"sign_type":"RSA",
"sign":"eOwNVySlvFDENsww4zWmo4iSv5XUG+O6T9jma1szEe15DlSFdMl8eJfwUzu37V77Tws+gfcKjvWSOX5mIS82vZU2Ga2u19COFFM20Zp0YEJwxw5zllCIAhd+A7KXX1EbcXid5Q/bVi/XUVffy9sd0HL39Ak53lyduzYQ/MmiwWY="
}
```

*部分字段含义*

  Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
    trade_status | String | WAIT_BUYER_PAY | 交易状态
     trade_no        | String        | 2014040311001004370000361525|支付宝交易号
  out\_trade_no | String  | test_no   |    商家内部交易号
     buyer_email   |  String | 13758698870 | 买家支付宝账号可以是email或者手机号
  total_fee | String  | 0.01  | 商品总价，单位为元
    price | String  | 0.01  | 商品单价，单位为元
  subject         | String        | 白开水                 |  订单标题
  discount        | String        | 0                     | 折扣     
  gmt_create    | String  | 2008-10-22 20:49:31 | 交易创建时间
  notify_type   | String | trade\_status_sync | 通知类型
  quantity  |   String  | 1 | 购买数量
  seller_id   | String  | 2088911356553910  | 卖家支付宝唯一用户号
  buyer_id  | String  | 2088802823343621 |  买家支付宝唯一用户号
  use_coupon  | String  |  N  |  买家是否使用了红包  （N/Y)


- **银联 (UN)**

>银联 (UN)

```
"message_detail":{
"bizType":"000201",
"orderId":"aa0c27e47b9e4ea1a595118ee0acf79f",
"txnSubType":"01",
"signature":"FPD/1uJ1HL9hRax8gR5S29rXYa9d9+U03qHgN4vNMJPWdK2NC9c0TIcfUVYYCKfphNiuzxXQUUWG3iLHe37QAdl2IDGbz76u3jQ5xZvXJBZ7d7CTfCBn5iBQuu8G4bFJOQMZYyQfPYz7joMSjdJl0/Nu7Lu1/m2xOxDIL90PhD5TrxheAWrCUfaN3Uw10dKXIwSiKVdu9wp32D9M1l6Wkhrso0jWbaKY4HNsa+jjzTAMpIvpROjRQZukdMM8NaI2uzXyBOewbSKY7/hexSW2tuXVnOUuiyPUDsk44RciZsaaDZkkql0HyB/hJCVsochYqzo6k9j0UYb8Xdj6e3UtXA==",
"traceNo":"510016",
"settleAmt":"1",
"settleCurrencyCode":"156",
"settleDate":"0528",
"txnType":"01",
"certId":"21267647932558653966460913033289351200",
"encoding":"UTF-8",
"version":"5.0.0",
"queryId":"201505281038235100168",
"accessType":"0",
"respMsg":"Success!",
"traceTime":"0528103823",
"txnTime":"20150528103823",
"merId":"898320548160202",
"currencyCode":"156",
"respCode":"00",
"signMethod":"01",
"txnAmt":"1"
}
```
 *部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  queryId        | String        | 2015081216170048 | 银联交易流水号
  traceNo       | String     | 510067 | 银联系统跟踪号
  orderId | String  | 2015081216171028   |    商家内部交易号
  txnTime    | String  | 20150528103823 | 交易创建时间
  txnAmt  | String  | 1 | 商品总价，单位为分
  signature        |   String  |      |   银联签名串 ，商户可忽略
  respCode   |  String |  00 |   交易返回码 00 代表成功
  respMsg    |  String |  Success! | 交易返回信息  Success!  代表成功
  

- **微信 (WX)**

>微信 (WX)

```
"message_detail":{
"transaction_id":"1006410636201505250163820565",
"nonce_str":"441956259efc417291d904f90f76fd69",
"bank_type":"CMB_CREDIT",
"openid":"oPSDwtwFnD54dB_ggPFGBO_KMdpo",
"fee_type":"CNY",
"mch_id":"xxx",
"cash_fee":"1",
"out_trade_no":"4095601432530130",
"appid":"xxx",
"total_fee":"1",
"trade_type":"JSAPI",
"result_code":"SUCCESS",
"time_end":"20150525130218",
"is_subscribe":"Y",
"return_code":"SUCCESS"
}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  transaction_id        | String     | 1006410636201505250163820565 | 微信交易号
  time_end    | String  | 20150528103823 | 交易结束时间
  out\_trade_no | String  | test_no   |    商家内部交易号
  total_fee | String  | 1 | 商品总价，单位为分
  cash_fee  | String  | 1 | 现金付款额
  openid        |   String  |      |   买家的openid
  return_code   |  String |  SUCCESS |   通信标示
  result_code    |  String |  SUCCESS |  业务结果
  
- **京东 (JD)**

>京东 (JD)

```
"message_detail":{
"CURRENCY":"CNY",
"CARDHOLDERNAME":"*俊",
"CARDHOLDERID":"**************8888",
"PAYAMOUNT":"2",
"TIME":"152337",
"DESC":"成功",
"DATE":"20150921",
"STATUS":"0",
"CODE":"0000",
"CARDHOLDERMOBILE":"138****8888",
"BANKCODE":"BOC",
"AMOUNT":"2",
"tradeSuccess":true,
"NOTE":"买矿泉水",
"ID":"159f69842fad43e3b3c2770130a2da75"
"CARDTYPE":"DEBIT_CARD",
"REMARK":"",
"TYPE":"S",
"CARDNO":"621790****8888",

}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  PAYAMOUNT        | String     | 2 | 支付金额，单位是分，退款时无此字段
  DATE  | String  | 20150921   |    交易时间，格式yyyyMMdd
  TIME  | String  | 152337  | 交易时间，格式HHmmss
  STATUS     |   String  |    0  |   交易返回码 0：状态成功，3：退款，4：部分退款，6：处理中，7：失败
  CODE   |  String |  0000 |   交易返回码，0000代表交易成功
  AMOUNT    |  String |  2|  交易金额，单位是分，一般和PAYAMOUNT相等，如果京东支付有满减活动，则为用户实际支付金额；退款时为退款金额
  ID    |  String |  159f69842fad43e3b3c2770130a2da75 |  交易号；退款时为退款单号
  OID    |  String |  159f69842fad43e3b3c2770130a2da75 |  原交易号，退款时才有，是指这笔退款在原来支付时候的订单号
  TYPE    |  String |  S |  交易类型编码，S：支付，R：退款 
  

- **百度 (BD)**

>百度 (BD)

```
"message_detail":{
"order_no":"e599fad3d7e149abaa318b74166517d7",
"bfb_order_no":"2015091010001399281110681948040",
"input_charset":"1",
"sign":"094fbaf6e3adea755cc0b06d65be2156",
"sp_no":"1000139928",
"unit_amount":"1",
"bank_no":"",
"transport_amount":"0",
"version":"2",
"bfb_order_create_time":"20150910143407",
"pay_result":"1",
"pay_time":"20150910143407",
"fee_amount":"0",
"buyer_sp_username":"",
"total_amount":"1",
"tradeSuccess":true,
"extra":"",
"sign_method":"1",
"currency":"1",
"pay_type":"2",
"unit_count":"1"}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  order_no        | String     | e599fad3d7e149abaa318b74166517d7 | 商户订单号
  bfb_order_no  | String  | 2015091010001399281110681948040   |    百度钱包交易号
  sp_no | String  | 1000139928  | 百度钱包商户号
  pay_result     |   String  |    1  |   支付结果代码 1：支付成功，2：等待支付，3：退款成功
  total_amount   |  Integer |  1 |   总金额，以分为单位
  pay_time   |  String |  20150910143407 |   支付时间
  pay_type   |  String |  2 |   支付方式,1:余额支付;2:网银支付;3:银行网关支付
   

- **PayPal (PAYPAL)**

>PayPal (PAYPAL)

```
"message_detail":{
"title":"PayPal payment test",
"total_fee":1,
"optional":{"PayPal key2":"PayPal value2","PayPal key1":"PayPal value1"},
"bill_no":"8D876079M42176727KXZKHFQ",
"channel":"PAYPAL_SANDBOX",
"access_token":"Bearer A015qoqdOWu7gGpk-1SQxYHrO97rfe18ONMJALm4-m4LGgI",
"currency":"USD",
"tradeSuccess":true}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  access_token        | String     | Bearer A015qoqdOWu7gGpk-1SQxYHrO97rfe18ONMJALm4-m4LGgI | PAYPAL访问授权码
  currency  | String  | USD   |    币种
  channel | String  | PAYPAL_SANDBOX  | PAYPAL沙箱支付或者真实支付


- **易宝网银 (YEE_WEB)**

>易宝网银 (YEE_WEB)

```
"message_detail":{
"r0_Cmd":"Buy",
"rb_BankId":"BOCO-NET",
"rp_PayDate":"20150912151013",
"p1_MerId":"10012506312",
"r3_Amt":"0.01",
"r9_BType":"2",
"r7_Uid":"",
"rq_SourceFee":"0.0",
"r5_Pid":"买矿泉水",
"rq_TargetFee":"0.0",
"r4_Cur":"RMB",
"r6_Order":"bfea66381a6149009fba5d35e2f0cfbf",
"r1_Code":"1",
"tradeSuccess":true,
"hmac":"4e37569abca34e4f1462568daaca9da6",
"r2_TrxId":"118262251787405I",
"ru_Trxtime":"20150912151043",
"r8_MP":"c37d661d-7e61-49ea-96a5-68c34e83db3b:dc5582b0-8c54-4bf5-b70b-4694283d2aa7",
"rq_CardNo":"",
"ro_BankOrderId":"2843719202150912"}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  r0_Cmd        | String     | Buy | 业务类型,固定值：Buy 
  p1_MerId  | String  | 10012506312   |    商户编号 
  r3_Amt  | String  | 0.01  | 支付金额 
  r2_TrxId  | String  | 118262251787405I  | 易宝交易流水号 
  r6_Order  | String  | bfea66381a6149009fba5d35e2f0cfbf  | 商户订单号 
  r1_Code | String  | 1 | 固定值：1 - 代表支付成功 
  r8_MP | String  | 测试  | 商户扩展信息
  r9_BType  | String  | 2 | 通知类型 1 - 浏览器重定向；2 - 服务器点对点通讯
  

- **易宝一键支付 (YEE_WAP)**

>易宝一键支付 (YEE_WAP)

```
"message_detail":{
"amount":1,
"bank":"招商银行",
"bankcode":"CMB",
"cardtype":2,
"lastno":"8530",
"merchantaccount":"10000418926",
"orderid":"1f74528ae33d478c8b64d8385357fbe0",
"sign":"KlGQYOJP8fXY7cfximI7xuO8VG9F2wbXy28fijmaFXqvqc3IhskKiStLTlt1rVTpejhqAAA9n9SxVOjgEeRqjjOTJcX4rNexlVsr1eCsN7SPjt2+CA+9elEHBG/oflhbg4RzkmbWUqZ1Hiib2cHqxB1mj+RZtEgW1MqO6QLqpaY=",
"status":1,
"tradeSuccess":true,
"yborderid":"411508290439050771"}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  amount        | Integer     | 1 | 订单金额,以「分」为单位
  merchantaccount | String  | 10000418926   |    商户编号 
  orderid | String  | 1f74528ae33d478c8b64d8385357fbe0  | 商户订单号 
  yborderid | Long  | 411508290439050771  | 易宝交易流水号 
  status  | Integer | 1 | 订单状态.1：成功
  bank  | String  | 招商银行  | 支付卡所属银行的名称 
  cardtype  | Integer | 2 | 支付卡的类型，1 为借记卡，2 为信用卡
  lastno  | String  | 8530  | 支付卡卡号后 4 位
  
- **易宝点卡支付 (YEE_NOBANKCARD)**

>易宝点卡支付 (YEE_NOBANKCARD)

```
"message_detail":{
"r0_Cmd":"ChargeCardDirect",
"p6_confirmAmount":"19.9",
"p1_MerId":"10001126856",
"p7_realAmount":"19.9",
"pc_BalanceAct":"0111001507010658538",
"r1_Code":"1",
"p8_cardStatus":"0",
"p5_CardNo":"0111001507010658538",
"p3_Amt":"0.1",
"tradeSuccess":true,
"p2_Order":"201509240000000000010",
"p4_FrpId":"TELECOM",
"hmac":"a571d6c3796dda8f83b5ef717d59000d",
"r2_TrxId":"516222232623803I",
"pb_BalanceAmt":"19.8",
"p9_MP":""}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  r1_Code        | String     | 1 | "1"代表支付成功，其他结果代表失败
  p1_MerId  | String  | 10001126856   |    商户编号 
  p2_Order  | String  | 201509240000000000010 | 易宝支付返回商户订单号 
  p3_Amt  | String  | 0.1 | 成功金额， 单位是元，保留两位小数,不足两位小数的将保留一位!(如0.10 将返回 0.1,0 会返回 0.0)
  p4_FrpId  | String  | TELECOM | 支付方式，“TELECOM”代表电信卡
  p5_CardNo | String  | 0111001507010658538 | 点卡卡号
  p6_confirmAmount  | String  | 19.9| 确认金额
  p7_realAmount | String  | 19.9  | 实际金额
  p8_cardStatus | String  | 0|  卡状态，0代表销卡成功，订单成功，其他为异常
  pb_BalanceAmt | String  | 19.8  | 支付余额
  pc_BalanceAct | String  | 0111001507010658538 | 余额卡号
  
- **BC企业打款**

>BC企业打款

```
"message_detail":{
"trade_pay_date":"Wed Jan 13 16:27:04 CST 2016",
"merchant_no":"110119759002",
"bank_code":"BOC",
"return_params":"juhepay",
"notify_datetime":"20162713T162401444",
"pay_tool":"TRAN",
"trade_currency":"CNY",
"category_code":"juhepay",
"buyer_info":{},
"is_success":"Y",
"card_type":"DE",
"trade_class":"DEFY",
"biz_trade_no":"bfe1dea75cae4bdcb52c406add16d398",
"out_trade_no":"bfe1dea75cae4bdcb52c406add16d398",
"trade_amount":"1",
"tradeSuccess":true,
"trade_finish_time":"Wed Jan 13 16:27:04 CST 2016",
"trade_status":"FINI",
"trade_pay_time":"Wed Jan 13 16:27:04 CST 2016",
"trade_no":"20160113100042000010570232",
"trade_subject":"测试代付",
"trade_finish_date":"Wed Jan 13 16:27:04 CST 2016"}
```  
  
  *部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  bank_code        | String     | BOC | 中国银行
  card_type | String  | DE   |    借记卡=DE;信用卡=CR
  out\_trade\_no  | String  | 201509240000000000010|商户订单号 
  trade_amount  | String  | 1 | 交易金额
  trade_status  | String  | FINI  | FINI=交易成功;REFU=交易退款;CLOS=交易关闭，失败
  trade_no  | String  | BC代付内部交易  | 20160113100042000010570232
  trade_subject | String  | 标题| 测试代付

- **BC_NATIVE,BC_WX_JSAPI,BC_WAP**

>BC_NATIVE,BC_WX_JSAPI,BC_WAP

```
"message_detail":{
"transaction_id":"1006410636201505250163820565",
"nonce_str":"",
"bank_type":"CFT",
"openid":"",
"fee_type":"CNY",
"mch_id":"",
"cash_fee":"1",
"out_trade_no":"4095601432530130",
"appid":"",
"total_fee":"1",
"trade_type":"NATIVE",
"result_code":"SUCCESS",
"time_end":"20150525130218",
"is_subscribe":"Y",
"return_code":"SUCCESS"
}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  transaction_id        | String     | 1006410636201505250163820565 | 渠道交易号
  time_end    | String  | 20150528103823 | 交易结束时间
  out\_trade_no | String  | test_no   |    商家内部交易号
  total_fee | String  | 1 | 商品总价，单位为分
  cash_fee  | String  | 1 | 现金付款额
  return_code   |  String |  SUCCESS |   通信标示
  result_code    |  String |  SUCCESS |  业务结果
  trade_type    |  String |  wap,native,jsapi |  业务结果
  
- **BC_ALI_QRCODE**

>BC_ALI_QRCODE 

```
"message_detail":{
"gmt_payment":"2016-09-05 11:14:24",
"trade_status":"TRADE_SUCCESS",
"buyer_email":"",
"notify_type":"trade_status_sync",
"gmt_create":"2016-09-05 11:14:24",
"sign":"",
"price":"0.01",
"notify_time":"2016-09-05 11:14:24",
"buyer_id":"",
"quantity":"1",
"sign_type":"RSA",
"seller_email":"",
"discount":"0.00",
"is_total_fee_adjust":"N",
"total_fee":"0.01",
"payment_type":"1",
"seller_id":"",
"bc_appid":"",
"transactionFee":1,
"out_trade_no":"bcdemo1473045206000",
"use_coupon":"N",
"subject":"PHP BC_ALI_QRCODE支付测试",
"trade_no":"100570018608201609055044123465",
"tradeSuccess":true
}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  out_trade_no        | String     | bcdemo1473045206000 | 自定义订单号
  trade_no    | String  | 100570018608201609055044123465 | 渠道交易号
  total_fee | String  | 0.01  | 商品总价，单位为元


- **BC_ALI_SCAN, BC_WX_SCAN**

>BC_ALI_SCAN, BC_WX_SCAN

```
"message_detail":{
"transaction_id":"100570018608201609085047252161",
"charset":"UTF-8",
"nonce_str":"1473299066490",
"bank_type":"CFT",
"openid":"oMJGHs6zGfvbb118O2iM6qq728p4",
"out_transaction_id":"4009092001201609083395588114",
"fee_type":"CNY",
"mch_id":"100570018608",
"uuid":"7437e977bb775ff863bf6649297a8fc6",
"version":"2.0",
"pay_result":"0",
"out_trade_no":"201609084000003",
"appid":"wx290ce4878c94369d",
"total_fee":"1",
"trade_type":"pay.weixin.micropay",
"result_code":"0",
"attach":"8fd7ab51-9a77-48a3-a537-a7f012e471f5",
"time_end":"20160908094426",
"is_subscribe":"N",
"sign_type":"MD5",
"status":"0"}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  out_trade_no        | String     | bcdemo1473045206000 | 自定义订单号
  transaction_id    | String  | 100570018608201609055044123465 | 渠道交易号
  total_fee | String  | 1 | 商品总价，单位为分
  
- **BC_GATEWAY,BC_EXPRESS**

>BC_GATEWAY,BC_EXPRESS

```
"message_detail":{
"order_no":"C201609100757505729611425",
"gmt_create":"2016-09-10 19:57:54",
"seller_email":"admin@juhe.cn",
"sign":"6dc3aa46c724c3f443bd5d8ac6cf67f5",
"discount":"0.00",
"body":"线上付款",
"title":"线上付款",
"is_success":"T",
"notify_id":"0071f71e6711413d87eaa72d38023773",
"notify_type":"WAIT_TRIGGER",
"tradeSuccess":true,
"price":"2000.00",
"total_fee":"2000.00",
"trade_status":"TRADE_FINISHED",
"sign_type":"MD5",
"seller_id":"100000000002164",
"is_total_fee_adjust":"0",
"notify_time":"2016-09-10 19:58:31",
"gmt_payment":"2016-09-10 19:58:31",
"quantity":"1",
"gmt_logistics_modify":"2016-09-10 19:58:35",
"payment_type":"2",
"transactionFee":"2000.00",
"trade_no":"101609103784154",
"seller_actions":"SEND_GOODS"}
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  order_no        | String     | bcdemo1473045206000 | 自定义订单号
  trade_no    | String  | 100570018608201609055044123465 | 渠道交易号
  total_fee | String  | 2000.00 | 商品总价，单位为元
  
- **BC_APP**

>BC_APP

```
"messageDetail"{
"amount":"25.00",doc
"transactionFee":"25.00",
"signature":"6d697026506564e0685323d1d933e7b3",
"tradeSuccess":true,
"transId":"20160910194901604",
"sign":"67f9254843673765c163dd868fa283e3",
"transDate":"20160910194916",
"sign_type":"MD5",
"transStatus":"00000",
"notify_id":"a3c119d2f9784e10a85539e7caabd476"},
```

*部分字段含义：*
 
 Key             | 类型           | Example               | 含义
-------------     | ------------- | -------------         | -------
  transId        | String     | bcdemo1473045206000 | 自定义订单号
  amount  | String  | 2000.00 | 商品总价，单位为元


# 4. Restful API列表

## 4.1 API请求地址

https://payapi.juhe.cn

## 4.2 支付

此接口为支付流程的第一步，主要功能在于生成订单，获取必要的参数信息，来进行下一步的支付流程。对于不同的渠道和支付方式，接口的返回值与后续的操作都不尽相同（例如微信App支付需要调用微信支付SDK的接口，支付宝网页支付需要跳转到获取的一段HTML网址等），请根据每一个channel的详细描述分别处理.

<aside class="warning">
secret是一个非常重要的数据，请您必须小心谨慎的确保此数据保存在足够安全的地方。您从BeeCloud官方获得此数据的同时，即表明您保证不会被用于非法用途和不会在没有得到您授权的情况下被盗用，一旦因此数据保管不善而导致的经济损失及法律责任，均由您独自承担。
</aside>

### 4.2.1 支付（线上）

URL:   */2/rest/bill*

Method: *POST*

请求参数格式: *JSON: Map*

请求参数详情:

- 公共请求参数：

参数名 | 类型 | 含义 | 描述 | 示例 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud平台的AppID | App在BeeCloud平台的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX\_APP、WX\_NATIVE、WX\_JSAPI、ALI\_APP、ALI\_WEB、ALI\_QRCODE、ALI\_WAP、UN\_APP、UN\_WEB、UN\_WAP、BC\_GATEWAY、BC\_EXPRESS、BC\_NATIVE、BC\_WX\_WAP、BC\_WX\_JSAPI、BC\_ALI\_QRCODE、BC_ALI_APP、BC_ALI_WEB、BC_ALI_WAP| 是
total_fee | Integer | 订单总金额 | 必须是正整数，单位为分 | 1 | 是
bill_no | String | 商户订单号 | 8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 | 是
title| String | 订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 | 是
buyer_id | String | 消费者ID | 商户为其用户分配的ID.可以是email、手机号、随机字符串等；最长64位；在商户自己系统内必须保证唯一。 | umwxkieI23Um | 否
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} | 否
analysis | Map | 分析数据 | 用于统计分析的数据，将会在控制台的统计分析报表中展示，**<mark>用户自愿上传</mark>** | 包括以下基本字段："product_map","ip" | 否
return_url | String | 同步返回页面| 支付渠道处理完请求后,当前页面自动跳转到商户网站里指定页面的http路径，**<mark>中间请勿有#,?等字符</mark>** | http://juhe.cn/returnUrl.jsp | 当channel参数为 ALI\_WEB 或 ALI\_QRCODE 或  UN\_WEB 或 JD\_WAP 或 JD\_WEB时为必填
limit_credit | Boolean | 禁用信用卡 | 禁用信用卡 | true/false | 否，**微信可用**
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
bill_timeout | Integer | 订单失效时间 | 必须为非零正整数，单位为秒，建议最短失效时间间隔必须<mark>大于</mark>360秒 | 360 | 否, **<mark>京东(JD)不支持该参数。</mark>** 


> 注：channel的参数值含义：  
WX\_APP: 微信手机原生APP支付  
WX\_NATIVE: 微信公众号二维码支付  
WX\_JSAPI: 微信公众号支付  
ALI\_APP: 支付宝手机原生APP支付  
ALI\_WEB: 支付宝PC网页支付  
ALI\_QRCODE: 支付宝内嵌二维码支付  
ALI\_WAP: 支付宝移动网页支付  
UN\_APP: 银联手机原生APP支付  
UN\_WEB: 银联PC网页支付  
UN\_WAP: 银联移动网页支付  
BC\_GATEWAY: 第三方网关支付  
BC\_EXPRESS: 第三方快捷支付  
BC\_NATIVE: BC版微信二维码支付  
BC\_WX\_WAP: BC版微信手机WAP支付  
BC\_WX\_JSAPI: BC版微信公众号支付  
BC\_ALI\_QRCODE: BC版支付宝线下扫码支付  
WX\_NATIVE: 微信二维码支付   


- 以下是`银联快捷（大额通道）(BC_EXPRESS)`的必填参数

参数名 | 类型 | 含义 | 例子
---- | ---- | ---- | ----
card_no| String | 银行卡号 | 

- 以下是`微信公众号支付(WX_JSAPI、BC_WX_JSAPI)`的必填参数

参数名 | 类型 | 含义 | 例子
---- | ---- | ---- | ----
openid| String | 用户相对于微信公众号的唯一id | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb

- 以下是`支付宝网页支付(ALI_WEB)`的选填参数

参数名 | 类型 | 含义 | 例子
---- | ---- | ---- | ----
show_url| String | 商品展示地址以http://开头 | http://juhe.cn

- 以下是`支付宝移动网页支付(ALI_WAP)`的选填参数

参数名 | 类型 | 含义 | 例子
---- | ---- | ---- | ----
use_app| Bool | 是否尝试掉起支付宝APP原生支付 | true

- 以下是`支付宝内嵌二维码支付(ALI_QRCODE)`的必填参数

参数名 | 类型 | 含义 | 例子
---- | ---- | ---- | ----
qr\_pay\_mode| String | 二维码类型 | 0,1,3

> 注： 二维码类型含义   
0： 订单码-简约前置模式, 对应 iframe 宽度不能小于 600px, 高度不能小于 300px   
1： 订单码-前置模式, 对应 iframe 宽度不能小于 300px, 高度不能小于 600px  
3： 订单码-迷你前置模式, 对应 iframe 宽度不能小于 75px, 高度不能小于 75px  

- 以下是`易宝移动网页支付(YEE_WAP)`的必填参数

参数名 | 类型 | 含义 
---- | ---- | ----
identity_id | String | 50位以内数字和/或字母组合，易宝移动网页（一键）支付用户唯一标识符，用于绑定用户一键支付的银行卡信息

- 以下是支付宝条码(ALI_SCAN)的选填参数：

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ---
terminal_id | string | 机具终端编号 | 商户机具终端编号 最长32位 | NJ\_T\_001 | 若机具商接入，terminal\_id(机具终端编号)必填，store\_id(商户门店编号)选填
store\_id | string | 商户门店编号 | 商户门店编号 最长32位 | NJ\_001 | 若系统商接入，store\_id（商户的门店编号）必填，terminal\_id(机具终端编号)选填


返回类型: *JSON: Map*
返回参数:

- 公共返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result_code | Integer | 返回码，0为正常
result_msg  | String | 返回信息， OK为正常
err_detail  | String | 具体错误信息
id  | String | 成功发起支付后返回支付表记录唯一标识

- **公共返回参数取值列表及其含义**

result_code | result_msg             | 含义
----        | ----                     | ----
0           | OK                     | 调用成功
1           | APP\_INVALID           | 根据app\_id找不到对应的APP或者app\_sign不正确
2           | PAY\_FACTOR_NOT\_SET   | 支付要素在后台没有设置
3           | CHANNEL\_INVALID       | channel参数不合法
4           | MISS\_PARAM            | 缺少必填参数
5           | PARAM\_INVALID         | 参数不合法
6           | CERT\_FILE\_ERROR      | 证书错误
7           | CHANNEL\_ERROR         | 渠道内部错误
14          | RUNTIME\_ERROR         | 运行时错误

> **当result_code不为0时，如需详细信息，请查看err\_detail字段**

**以下字段在result_code为0时有返回**

- WX_APP

参数名 | 类型 | 含义 
---- | ---- | ----
app_id | String | 微信应用APPID
partner_id | String | 微信支付商户号
package  | String | 微信支付打包参数
nonce_str  | String | 随机字符串
timestamp | String | 当前时间戳，单位是毫秒，13位
pay_sign  | String | 签名值
prepay_id  | String | 微信预支付id

- WX\_NATIVE、BC\_NATIVE、BC\_ALI\_QRCODE

参数名 | 类型 | 含义 
---- | ---- | ----
code_url | String | 二维码地址

- WX\_JSAPI、BC\_WX\_JSAPI

参数名 | 类型 | 含义 
---- | ---- | ----
app_id | String | 微信应用APPID
package  | String | 微信支付打包参数
nonce_str  | String | 随机字符串
timestamp | String | 当前时间戳，单位是毫秒，13位
pay_sign  | String | 签名
sign_type  | String | 签名类型，固定为MD5

- BC\_WX\_WAP

参数名 | 类型 | 含义 
---- | ---- | ----
url  | String | BC版微信WAP的跳转url

- ALI_APP

参数名 | 类型 | 含义 
---- | ---- | ----
order\_string | String | 支付宝签名串

- ALI\_WEB，ALI\_WAP

参数名 | 类型 | 含义 
---- | ---- | ----
html | String | 支付宝跳转form，是一段HTML代码，自动提交
url  | String | 支付宝跳转url

- ALI_QRCODE

参数名 | 类型 | 含义 
---- | ---- | ----
html | String | 支付宝跳转form，是一段HTML代码，自动提交
url  | String | 支付宝内嵌二维码地址，是一个URL

- UN_APP

参数名 | 类型 | 含义 
---- | ---- | ----
tn | String | 银联支付ticket number

- BC_EXPRESS

参数名 | 类型 | 含义 
---- | ---- | ----
html | String | 银联跳转form，是一段HTML代码，自动提交

- UN\_WEB、UN\_WAP

参数名 | 类型 | 含义 
---- | ---- | ----
html | String | 支付页自动提交form表单内容

- WX\_NATIVE 

参数名 | 类型 | 含义 
---- | ---- | ----
code_url | String | 二维码地址


### 4.2.2 支付（线下）

URL:   */2/rest/offline/bill*
Method: *POST*
请求参数格式: *JSON: Map*

请求参数详情:
- 以下为公共参数：

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud平台的AppID | App在BeeCloud平台的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX\_NATIVE、WX_SCAN、ALI\_OFFLINE\_QRCODE、ALI_SCAN、SCAN、BC\_ALI\_SCAN,BC\_WX\_SCAN(详见附注）| 是
total_fee | Integer | 订单总金额 | 必须是正整数，单位为分 | 1 | 是
bill_no | String | 商户订单号 | 8到32位数字和/或字母组合，请自行确保在商户系统中唯一，同一订单号不可重复提交，否则会造成订单重复 | 201506101035040000001 | 是
title| String | 订单标题 | UTF8编码格式，32个字节内，最长支持16个汉字 | 白开水 | 是
auth_code | String | 用户授权码| 当商户用扫码枪扫用户的条形码时得到的字符串 | 23891113455872 | 当channel参数为BC\_ALI\_SCAN,BC\_WX\_SCAN,BC\_SCAN,WX_SCAN或ALI_SCAN 时为必填
notify_url | String | 商户自定义回调地址 | 商户可通过此参数设定回调地址，此地址会覆盖用户在控制台设置的回调地址。**<mark>必须以`http://`或`https://`开头</mark>** | http://juhe.cn/notifyUrl.jsp
buyer_id | String | 消费者ID | 商户为其用户分配的ID.可以是email、手机号、随机字符串等；最长64位；在商户自己系统内必须保证唯一。 | umwxkieI23Um | 否
optional | Map | 附加数据 | 用户自定义的参数，将会在Webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} | 否
store_id | string | 门店号| 门店号 | 卡门店号 | 否

> 注1：channel的参数值含义：  
WX\_NATIVE: 微信二维码支付   
ALI\_OFFLINE\_QRCODE: 支付宝二维码支付  
WX\_SCAN: 微信条形码支付  
ALI\_SCAN: 支付宝条形码支付  
SCAN: 支付宝、微信统一条形码支付
BC\_ALI\_SCAN: BC版支付宝条形码支付  
BC\_WX\_SCAN: BC版支付宝条形码支付
BC\_SCAN: 支付宝、微信统一条形码支付


返回类型: *JSON: Map*
返回参数:

- **公共返回参数**

参数名 | 类型 | 含义 
---- | ---- | ----
result_code | Integer | 返回码，0为正常
result_msg  | String | 返回信息， OK为正常
err_detail  | String | 具体错误信息

- **公共返回参数取值列表及其含义**

result_code | result_msg             | 含义
----        | ----                     | ----
0           | OK                     | 调用成功
1           | APP\_INVALID           | 根据app\_id找不到对应的APP或者app\_sign不正确
2           | PAY\_FACTOR_NOT\_SET   | 支付要素在后台没有设置
3           | CHANNEL\_INVALID       | channel参数不合法
4           | MISS\_PARAM            | 缺少必填参数
5           | PARAM\_INVALID         | 参数不合法
6           | CERT\_FILE\_ERROR      | 证书错误
7           | CHANNEL\_ERROR         | 渠道内部错误
14          | RUNTIME\_ERROR         | 运行时错误

> **当result_code不为0时，如需详细信息，请查看err\_detail字段**

**<mark>以下字段在result_code为0时有返回</mark>**  

- ALI\_SCAN | WX\_SCAN | BC\_ALI\_SCAN|BC\_WX\_SCAN

参数名 | 类型 | 含义 
---- | ---- | ----
pay_result | boolean | 是否支付成功，false代表在等待用户输入密码，需查询

- WX\_NATIVE | ALI\_OFFLINE_QRCODE

参数名 | 类型 | 含义 
---- | ---- | ----
code_url | String | 二维码地址

- SCAN

参数名 | 类型 | 含义 
---- | ---- | ----
channel_type | String | 具体条形码支付类型，ALI\_SCAN或者WX\_SCAN


## 4.3 退款

### 4.3.1 退款（线上）

退款接口仅支持对已经支付成功的订单进行退款。 退款金额refund\_fee必须小于或者等于原始支付订单的total\_fee，如果是小于，则表示部分退款.

URL: */2/rest/refund*
Method: *POST*

请求参数格式: *JSON: Map*
请求参数详情:

- 请求参数

参数名 | 类型 | 含义   | 描述 | 例子 | 是否必填 |
---- | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062\-5e41\-44e3\-8f52\-f89d8cf2b6eb | 是 
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+master\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同渠道选不同的值 | WX ALI UN KUAIQIAN BD JD YEE | 否
refund_no | String | 商户退款单号 | 格式为:退款日期(8位) + 流水号(3~24 位)。请自行确保在商户系统中唯一，且退款日期必须是发起退款的当天日期,同一退款单号不可重复提交，否则会造成退款单重复。流水号可以接受数字或英文字符，建议使用数字，但不可接受“000” | 201506101035040000001 | 是
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 是 
refund_fee | Integer | 退款金额 | 必须为正整数，单位为分，必须小于或等于对应的已支付订单的total_fee | 1 | 是
notify_url | String | 商户自定义退款回调地址 | 商户可通过此参数设定退款回调地址，此地址会覆盖用户在控制台设置的退款回调地址。必须以`http://`或`https://`开头 | http://juhe.cn/notifyUrl.jsp | 否
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} | 否
refund_account | Integer | 退款资金来源 | 1:可用余额退款 0:未结算资金退款（默认使用未结算资金退款） | 1 | 否

返回类型: *JSON: Map*
返回参数:

- 公共返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer | 返回码，0为正常
result\_msg  | String | 返回信息，OK为正常
err\_detail  | String | 具体错误信息
id  | String | 成功发起预退款或者直接退款后返回退款表记录唯一标识

- 返回码和返回信息取值及含义参见支付公共返回参数部分, 以下是退款所特有的

result\_code | result\_msg                | 含义
----        | ----                         | ----
8           | NO\_SUCH_BILL             | 没有该订单
9           | BILL\_UNSUCCESS            | 该订单没有支付成功
10          | REFUND\_EXCEED\_TIME       | 已超过可退款时间
11          | ALREADY\_REFUNDING         | 该订单已有正在处理中的退款
12          | REFUND\_AMOUNT\_TOO\_LARGE | 提交的退款金额超出可退额度
13          | NO\_SUCH\_REFUND           | 没有该退款记录

**当channel为`ALI`，并且不是预退款时，以下字段在result_code为0时有返回**
 
参数名 | 类型 | 含义 
---- | ---- | ----
url | String | 支付宝退款地址，需用户在支付宝平台上手动输入支付密码处理


### 4.3.3 退款（线下）

URL: */2/rest/offline/refund*
Method: *POST*

请求参数格式: *JSON: Map*
请求参数详情:

- 参数列表

参数名 | 类型 | 含义   | 描述 | 例子 | 是否必填 |
---- | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062\-5e41\-44e3\-8f52\-f89d8cf2b6eb | 是 
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+master\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同渠道选不同的值 | 暂时仅支持ALI、WX| 否
refund_no | String | 商户退款单号 | 格式为:退款日期(8位) + 流水号(3~24 位)。请自行确保在商户系统中唯一，且退款日期必须是发起退款的当天日期,同一退款单号不可重复提交，否则会造成退款单重复。流水号可以接受数字或英文字符，建议使用数字，但不可接受“000” | 201506101035040000001 | 是
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 是 
refund_fee | Integer | 退款金额 | 必须为正整数，单位为分，必须小于或等于对应的已支付订单的total_fee | 1 | 是
refund_reason | String | 退款的原因说明 | 正常退款 | 否
optional | Map | 附加数据 | 用户自定义的参数，将会在webhook通知中原样返回，该字段主要用于商户携带订单的自定义数据 | {"key1":"value1","key2":"value2",...} | 否
operator_id | String | 商户的操作员编号 | OP001 | 否
store_id | String | 商户的门店编号 | NJ_S_001 | 否
refund_account | Integer | 退款资金来源 | 1:可用余额退款 0:未结算资金退款（默认使用未结算资金退款） | 1 | 否

返回类型: *JSON: Map*
返回参数:

- 公共返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer | 返回码，0为正常
result\_msg  | String | 返回信息，OK为正常
err\_detail  | String | 具体错误信息
id  | String | 成功发起退款后返回退款表记录唯一标识
refund_result | Bool | 退款是否成功

> 公共返回参数取值及含义参见支付公共返回参数部分, 以下是退款所特有的

result\_code | result\_msg                | 含义
----        | ----                         | ----
8           | NO\_SUCH_BILL             | 没有该订单
9           | BILL\_UNSUCCESS            | 该订单没有支付成功
10          | REFUND\_EXCEED\_TIME       | 已超过可退款时间
11          | ALREADY\_REFUNDING         | 该订单已有正在处理中的退款
12          | REFUND\_AMOUNT\_TOO\_LARGE | 提交的退款金额超出可退额度
13          | NO\_SUCH\_REFUND           | 没有该退款记录

## 4.4 查询

### 4.4.1 支付订单查询

URL:   */2/rest/bills*
Method: *GET*

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_APP、WX\_NATIVE、WX\_JSAPI、ALI、ALI\_APP、ALI\_WEB、ALI\_QRCODE、ALI_WAP、UN、UN\_APP、UN\_WEB、UN\_WAP、PAYPAL、PAYPAL\_SANDBOX、PAYPAL\_LIVE、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、JD、YEE、KUAIQIAN、BD、BD\_APP、BD\_WEB、BD\_WAP、BC、BC\_GATEWAY、BC\_EXPRESS、BC\_APP、BC\_NATIVE、BC\_WX\_WAP、BC\_WX\_JSAPI、BC\_ALI\_QRCODE(详见附注）| 否
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 否
spay_result | Bool | 订单是否成功 | 标识订单是否支付成功 | true | 否
refund_result | Bool | 订单是否已退款 | 标识订单是否已退款 | true | 否
need_detail | Bool | 是否需要返回渠道详细信息 | 决定是否需要返回渠道的回调信息，true为需要 | true | 否
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 | 否
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 | 否
skip | Integer| 查询起始位置 | 默认为0. 设置为10表示忽略满足条件的前10条数据| 0 | 否
limit| Integer | 查询的条数 | 默认为10，最大为50. 设置为10表示只返回满足条件的10条数据 | 10 | 否

> 注：  
1. bill\_no, start\_time, end\_time等查询条件互相为且关系  
2. start\_time, end\_time指的是订单生成的时间，而不是订单支付的时间   


返回类型: *JSON: Map*
返回参数:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
bills | List<Map> | 订单列表

- bills说明，每个Map的key\-value

参数名         | 类型          | 含义 
----          | ----         | ----
id      | String       | 订单记录的唯一标识，可用于查询单笔记录
bill\_no      | String       | 订单号
total\_fee    | Integer         | 订单金额，单位为分
trade\_no    | String         | 渠道交易号， 当支付成功时有值
channel       | String       | 渠道类型 WX、ALI、UN、JD、YEE、KUAIQIAN、PAYPAL、BD
sub_channel         | String       | 子渠道类型 WX_APP、WX_NATIVE、WX_JSAPI、WX_SCAN、ALI_APP、ALI_SCAN、ALI_WEB、ALI_QRCODE、ALI_OFFLINE_QRCODE、ALI_WAP、UN_APP、UN_WEB、UN_WAP、PAYPAL_SANDBOX、PAYPAL_LIVE、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、YEE_NOBANKCARD、KUAIQIAN_WAP、KUAIQIAN_WEB、BD_APP、BD_WEB、BD_WAP(详见 2. 支付 附注）
title         | String       | 订单标题
spay\_result  | Bool         | 订单是否成功
create_time | Long         | 订单创建时间, 毫秒时间戳, 13位
success_time | Long        | 订单支付成功时间，毫秒时间长，13位 
optional | String | 附加数据,用户自定义的参数，将会在webhook通知中原样返回，该字段是JSON格式的字符串 {"key1":"value1","key2":"value2",...}
message_detail | String         | 渠道详细信息， 当need_detail传入true时返回
revert_result  | Bool         | 订单是否已经撤销
refund_result  | Bool         | 订单是否已经退款


### 4.4.2 订单总数查询

URL:   */2/rest/bills/count*
Method: *GET*

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_APP、WX\_NATIVE、WX\_JSAPI、ALI、ALI\_APP、ALI\_WEB、ALI\_QRCODE、ALI_WAP、UN、UN\_APP、UN\_WEB、UN\_WAP、PAYPAL、PAYPAL\_SANDBOX、PAYPAL\_LIVE、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、JD、YEE、KUAIQIAN、BD、BD\_APP、BD\_WEB、BD\_WAP、BC、BC\_GATEWAY、BC\_EXPRESS、BC\_APP、BC\_NATIVE、BC\_WX\_WAP、BC\_WX\_JSAPI、BC\_ALI\_QRCODE(详见附注）| 否
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 否
spay_result | Bool | 订单是否成功 | 标识订单是否支付成功 | true | 否
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 | 否
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 | 否

> 注：  
1. bill\_no, start\_time, end\_time等查询条件互相为**<mark>且</mark>**关系  
2. start\_time, end\_time指的是订单生成的时间，而不是订单支付的时间   


返回类型: *JSON: Map*
返回参数:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
count | Integer | 查询订单结果数量


### 4.4.3 退款查询

URL:   */2/rest/refunds*
Method: GET

请求参数类型: JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_NATIVE、WX\_JSAPI、ALI、ALI\_APP、ALI\_WEB、ALI\_QRCODE、ALI_WAP、UN、UN\_APP、UN\_WEB、UN\_WAP、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、JD、YEE、KUAIQIAN、BD、BD\_APP、BD\_WEB、BD\_WAP(详见2.支付附注）| 否
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 否
refund_no | String | 商户退款单号 | 发起退款时填写的退款单号 | 201506101035040000001 | 否
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 | 否
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 | 否
need_approval | Bool | 需要审核 | 标识退款记录是否为预退款   | true | 否
need_detail | Bool | 是否需要返回渠道详细信息 | 决定是否需要返回渠道的回调信息，true为需要 | true | 否
skip | Integer | 查询起始位置 | 默认为0. 设置为10，表示忽略满足条件的前10条数据| 0 | 否
limit| Integer | 查询的条数 | 默认为10，最大为50. 设置为10，表示只查询满足条件的10条数据 | 10 | 否

> 注：  
1. bill\_no, refund\_no, start\_time, end\_time等查询条件互相为**<mark>且</mark>**关系.   
2. start\_time, end\_time指的是订单生成的时间，而不是订单支付的时间.   


返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
refunds | List<Map> | 退款列表


- refunds说明，每个Map的key\-value

参数名      | 类型         | 含义 
----       | ----        | ----
id      | String       | 退款记录的唯一标识，可用于查询单笔记录
bill\_no    | String      | 订单号
refund\_no  | String      | 退款号
total\_fee  | Integer      | 订单金额，单位为分
refund\_fee | Integer      | 退款金额，单位为分
title         | String       | 订单标题
channel    | String      | 渠道类型 WX、ALI、UN、JD、YEE、KUAIQIAN、BD
sub\_channel    | String      | 子渠道类型 WX\_NATIVE、WX\_JSAPI、WX\_APP、ALI\_APP、ALI\_WEB、ALI\_QRCODE、UN\_APP、UN\_WEB、UN\_WAP、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、BD\_APP、BD\_WEB、BD\_WAP(详见 1. 支付 附注）
finish     | bool        | 退款是否完成
result     | bool        | 退款是否成功
optional | String | 附加数据,用户自定义的参数，将会在webhook通知中原样返回，该字段是JSON格式的字符串 {"key1":"value1","key2":"value2",...}
message\_detail | String         | 渠道详细信息， 当need_detail传入true时返回
create\_time | Long       | 退款创建时间, 毫秒时间戳, 13位


### 4.4.4 退款总数查询

URL:   */2/rest/refunds/count*
Method: GET

请求参数类型: JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX、WX\_NATIVE、WX\_JSAPI、ALI、ALI\_APP、ALI\_WEB、ALI\_QRCODE、ALI_WAP、UN、UN\_APP、UN\_WEB、UN\_WAP、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、JD、YEE、KUAIQIAN、BD、BD\_APP、BD\_WEB、BD\_WAP(详见2.支付附注）| 否
bill_no | String | 商户订单号 | 发起支付时填写的订单号 | 201506101035040000001 | 否
refund_no | String | 商户退款单号 | 发起退款时填写的退款单号 | 201506101035040000001 | 否
start_time | Long | 起始时间 | 毫秒时间戳, 13位 | 1435890530000 | 否
end_time | Long | 结束时间 | 毫秒时间戳, 13位   | 1435890540000 | 否
need_approval | Bool | 需要审核 | 标识退款记录是否为预退款   | true | 否

> 注：  
1. bill\_no, refund\_no, start\_time, end\_time等查询条件互相为**<mark>且</mark>**关系.   
2. start\_time, end\_time指的是订单生成的时间，而不是订单支付的时间.   


返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
count | Integer | 查询退款结果数量


### 4.4.5 退款状态更新

退款状态更新接口提供查询退款状态以更新退款状态的功能，用于对退款后不发送回调的渠道（WX、YEE、KUAIQIAN、BD）退款后的状态更新。

URL:   */2/rest/refund/status*
Method: GET

请求参数类型:JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式，不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | 目前只支持WX、YEE、KUAIQIAN、BD | 是
refund_no | String | 商户退款单号 | 发起退款时填写的退款单号 | 201506101035040000001 | 是

返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer | 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
refund\_status | String | 退款状态

### 4.4.6 退款订单查询(指定ID)

URL:   */2/rest/refund/{id}*
Method: *GET*
id : 退款订单唯一标识

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是


返回类型: *JSON: Map*
返回参数:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息，有错误时，不会返回refund结果
refund | Map | 退款结果

- refund说明，每个Map的key\-value

参数名      | 类型         | 含义 
----       | ----        | ----
id      | String       | 退款记录的唯一标识，可用于查询单笔记录
bill\_no | String | 支付订单号
channel | String | WX、ALI、UN、JD、KUAIQIAN、BD、YEE
sub\_channel | String | WX_APP、WX_NATIVE、WX_JSAPI、ALI_APP、ALI_WEB、ALI_QRCODE、ALI_WAP、UN_APP、UN_WEB、UN_WAP、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、BD_APP、BD_WEB、BD_WAP (详见 2. 支付 附注）
finish | Bool | 退款是否完成
create_time | Long | 退款创建时间, 毫秒时间戳, 13位
optional | String | 附加数据,用户自定义的参数，将会在webhook通知中原样返回，该字段是JSON格式的字符串 {"key1":"value1","key2":"value2",...}
result | Bool| 退款是否成功
title | String | 商品标题
total_fee | Integer | 订单金额，单位为分
refund_fee | Integer | 退款金额，单位为分
refund_no | String | 退款单号
message\_detail | String         | 渠道详细信息

### 4.4.7 支付订单查询(指定ID)

URL:   */2/rest/bill/{id}*
Method: *GET*
id : 支付订单唯一标识

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app\_secret)，32位16进制格式,不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是


返回类型: *JSON: Map*

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息，有错误时，不会返回pay结果
pay | Map | 支付结果

- 返回码和返回信息取值及含义参见支付公共返回参数部分

- pay说明，每个Map的key\-value

参数名      | 类型         | 含义 
----       | ----        | ----
id      | String       | 订单记录的唯一标识，可用于查询单笔记录
bill\_no | String | 支付订单号
channel | String | WX、ALI、UN、JD、KUAIQIAN、YEE、BD、PAYPAL
sub\_channel | String | WX_APP、WX_NATIVE、WX_JSAPI、WX_SCAN、ALI_APP、ALI_SCAN、ALI_WEB、ALI_QRCODE、ALI_OFFLINE_QRCODE、ALI_WAP、UN_APP、UN_WEB、UN_WAP、PAYPAL_SANDBOX、PAYPAL_LIVE、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、YEE_NOBANKCARD、KUAIQIAN_WAP、KUAIQIAN_WEB、BD_APP、BD_WEB、BD_WAP (详见 2. 支付 附注）
trade\_no | String | 渠道返回的交易号，当支付成功时有值
create\_time | Long | 订单创建时间, 毫秒时间戳, 13位
optional | String | 附加数据,用户自定义的参数，将会在webhook通知中原样返回，该字段是JSON格式的字符串 {"key1":"value1","key2":"value2",...}
spay\_result | Bool| 订单是否成功
title | String | 商品标题
total\_fee | Integer | 订单金额，单位为分
message\_detail | String         | 渠道详细信息
revert\_result  | Bool         | 订单是否已经撤销
refund\_result  | Bool         | 订单是否已经退款

### 4.4.8 订单状态查询(线下刷卡专用)

URL:   */2/rest/offline/bill/status*
Method: POST

请求参数类型:JSON

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | BeeCloud应用APPID | BeeCloud的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
timestamp | Long | 签名生成时间 | 时间戳，毫秒数 | 1435890533866 | 是
app_sign | String | 加密签名 | 算法: md5(app\_id+timestamp+app_key)，32位16进制格式，不区分大小写 | b927899dda6f9a04afc57f21ddf69d69 | 是
bill_no | String | 订单号 | 要查询的订单号 | -|是
channel| String | 渠道类型 | 根据不同场景选择不同的支付方式 | WX\_NATIVE、WX\_SCAN、ALI\_OFFLINE\_QRCODE、ALI\_SCAN、BC\_ALI\_SCAN、BC\_WX\_SCAN(详见支付附注） | 否

返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ----
result\_code | Integer | 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
pay_result | Bool | 订单是否成功

## 4.5 用户系统（内测中）

商家的客户在商家平台注册的时候同时调用BeeCloud的用户系统注册接口，可以将客户的信息同步到BeeCloud，这些客户信息可以提供给【BeeCloud商业数据分析平台】使用。没有通过注册接口传入的buyer_id，直接通过订单中传入buyer_id，是没有办法进行统计分析的。

<aside class="notice">
对于老的订单数据可以通过【历史数据补全接口】和【批量用户导入接口】来加上历史数据加上buyer_id
</aside>

### 4.5.1 单个用户注册接口

URL: https://payapi.juhe.cn/2/rest/user  
Method: POST  

请求参数类型:JSON

- 请求参数

参数名 | 类型 | 含义 | 示例 | 是否必填
----  | ---- | ---- | ---- | ----
app_id | String | 该账户下任意app_id，用于验签 | | 是
timestamp | String | | |是
app_sign | String | | | 是
buyer_id | String | 商户为自己的用户分配的ID。可以是email、手机号、随机字符串等。最长32位。在商户自己系统内必须保证唯一。 | umwxkieI23Um |是


返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 
---- | ---- | ---- 
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息



### 4.5.2 批量用户导入接口

API: https://payapi.juhe.cn/2/rest/users  
Method: POST  

请求参数类型:JSON

- 请求参数

参数名 | 类型 | 含义 | 示例 | 是否必填
----  | ---- | ---- | ---- | ----
email | String | 用户账号 | test@juhe.cn | 是
app_id | String | 该账户下任意app_id，用于验签 | | 是
timestamp | String | | |是
app_sign | String | | | 是
buyer_ids | List<String> | 商户为自己的多个用户分配的IDs。每个ID可以是email、手机号、随机字符串等；最长32位；在商户自己系统内必须保证唯一。 | |是

返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 | 
---- | ---- | ---- |
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息



### 4.5.3 商户用户批量查询接口

API: *https://payapi.juhe.cn/2/rest/users*  
Method: GET  

请求参数类型:JSON

- 请求参数

请求参数类型: *JSON, 以para={}的方式请求*  
示例: *para={"key_a":1,"key_b":"value_b"}*, 需要对`para=`后面的部分做URL encode.

参数名 | 类型 | 含义 | 示例 | 是否必填
----  | ---- | ---- | ---- | ----
email | String | 商户的email。如果传入email，就查询该email下的用户。 | | 否
app_id | String | 该账户下任意app_id，用于验签 | | 是
timestamp | String | | |是
app_sign | String | | | 是
start_time | Long | 起始时间。该接口会返回此时间戳之后创建的用户。毫秒时间戳, 13位 | 1435890530000 | 否
end_time | Long | 结束时间。该接口会返回此时间戳之前创建的用户。毫秒时间戳, 13位 | 1435890540000 | 否

**注意：**  
**如果传入email, 就查询该email下的用户;如果不传email，就查询注册时使用该app_id注册的用户。**

返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 | 
---- | ---- | ---- |
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息
users | List | 获取到的用户信息列表

### 4.5.4 历史数据补全接口（批量）

该接口要求用户传入订单号与用户ID的对应关系，该接口会将历史数据中，属于该用户ID的订单数据进行标识。

API: https://payapi.juhe.cn/2/rest/history_bills  
Method: PUT  

请求参数类型:JSON

- 请求参数

参数名 | 类型 | 含义 | 示例 | 是否必填
----  | ---- | ---- | ---- | ----
app_id | String | 该账户下任意app_id，用于验签 | | 是
timestamp | String | | |是
app_sign | String | | | 是
bill_info | Map | Map的键是buyer_id, buyer\_id长度不能超过64位。值是一个List\<String>为订单号列表 | {"aaa@bb.com":["20170302005"], "xxx@bb.com":["20170302001","20170302002","20170302011"]} | 是 

返回类型: *JSON, Map*
返回详情:

- 返回参数

参数名 | 类型 | 含义 | 
---- | ---- | ---- |
result\_code | Integer| 返回码，0为正常
result\_msg  | String | 返回信息， OK为正常
err\_detail  | String | 具体错误信息

如果更新失败会返回以下信息：

参数名 | 类型 | 含义 | 示例
---- | ---- | ---- | ----
failed_bills | Map | 更新失败的订单信息,可能是部分信息。key是buyer\_id, value是隶属于该buyer\_id的订单列表| {"aaa@bb.com":["20170302005"], "xxx@bb.com":["20170302001","20170302011"]} 

**注意：重试时，请依据更新失败返回的失败订单信息进行重试，以避免重复更新历史订单信息**


### 4.5.5 商户交易详细数据

商户可以通过analysis字段上传自定义的销售信息，这些信息可以帮助商户对产品和客户做更好的分类与分析。

参数名 | 类型 | 含义 | 
---- | ---- | ---- |
anaylsis | Map | 附加数据 | 

- 订单包含的产品列表

key | type | value |
---- | ---- | ---- |
anaylsis | Map | {"product_map":{"product A": 1000,"product B":2000}} 1000是产品金额，单位分

- 订单下单用户的IP地址

<aside class="notice">
这里要上传的是客户端的IP（从客户浏览器/APP获得，不是商户服务器的IP）
</aside>

key | type | value |
---- | ---- | ---- |
anaylsis | Map | {"ip":"xxx.xx.xx.xxx"} 
