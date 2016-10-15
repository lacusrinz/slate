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


<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a> -->

<!-- includes:
  - errors -->

<!-- search: true -->
---

# 1. 概览

BeeCloud支持线上线下各种场景的支付解决方案，本文档以场景的形式展示如何使用BeeCloud开发，完成支付技术的接入。

## 1.1 SDK安装

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
#
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

> 支付宝收银台收款代码示例：

``` java
BCOrder bcOrder = new BCOrder(PAY_CHANNEL.ALI_WEB, 1, billNo, title);//设定订单信息
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
BCBill bill = new BCBill(BCPay.PayChannel.ALI_WEB.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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

> 支付宝网页二维码收款代码示例：

``` csharp
BCBill bill = new BCBill(BCPay.PayChannel.ALI_QRCODE.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
bill.qrPayMode = "0";
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
#
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
<aside class="notice">
移动网页有特殊参数 use_app，默认掉起支付宝APP实现原生支付，可以关闭
</aside>

> 支付宝移动网页收款代码示例：

``` csharp
BCBill bill = new BCBill(BCPay.PayChannel.ALI_WAP.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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

## 2.3 网页上实现微信收款

### 2.3.1 微信公众号内网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
<aside class="notice">
公众号支付需要用到微信特殊参数openid，如何获取openid可以通过百度，有大量的教程
</aside>
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求微信
5. 返回必要的参数，然后将这些参数传入到微信的js方法中
<aside class="notice">
这些js方法只有在微信内的浏览器才会被识别，所以只能在微信内使用
</aside>
6. 跳转到微信APP付款
7. 支付完成返回微信公众号页面

> 微信公众号内网页收款代码示例：

```csharp
//服务端部分
BCBill bill = new BCBill(BCPay.PayChannel.WX_JSAPI.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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
#
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
<aside class="notice">
微信没有自己的网页，所以直接返回了二维码的内容，需要用户自己生存二维码
</aside>
6. 通过代码生成二维码展示到商家自己的页面上，用户扫码付款
7. 支付完成，没有return_url可以使用
<aside class="notice">
微信没有自己的网页，所以没有return url的概念，需要商家通过轮询自己后台的方式去查看订单是否已经支付，如果支付了则主动跳转告知用户
</aside>

> 微信在PC网页通过二维码收款代码示例：

```csharp
BCBill bill = new BCBill(BCPay.PayChannel.WX_NATIVE.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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

## 2.4 网页上实现银联收款

### 2.4.1 银联PC网页收款

1. 用户发起付款请求
2. 系统生成付款订单，包括订单号，订单标题，金额等信息
3. 将订单存入自己系统数据库中，标记订单为未支付
4. 调用BeeCloud SDK中的支付接口，请求银联
5. 银联返回一个html
6. 通过将html输出到页面进而打开银联收银台页面，用户输入银行卡号完成付款
7. 支付完成，用户跳转到设置的return url地址

> 银联PC网页收款代码示例：

```csharp
BCBill bill = new BCBill(BCPay.PayChannel.UN_WEB.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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

> 银联移动网页收款代码示例：

```csharp
BCBill bill = new BCBill(BCPay.PayChannel.UN_WAP.ToString(), 1, BCUtil.GetUUID(), "dotNet自来水");
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
#
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

> 网关收款代码示例：

```csharp
BCBill bill = new BCBill(BCPay.PayChannel.BC_GATEWAY.ToString(), 1, BCUtil.GetUUID(), "dotNet白开水");
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
#
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


# 3. 线下通过二维码收款

## 3.1 流程概述

支付宝微信支持面对面通过二维码的方式进行收付款
商户扫用户的二维码称为刷卡收款
用户扫商户的二维码称为扫码支付

## 3.2 支付宝扫码支付

## 3.3 微信扫码支付

## 3.4 支付宝刷卡收款

## 3.5 微信刷卡收款

# 4. 企业向个人打款

## 4.1 支付宝打款到支付宝

## 4.2 微信打款到微信

## 4.3 BeeCloud打款到银行卡

