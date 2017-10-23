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



# 1. Restful API列表

## 1.1 API请求地址

https://payapi.juhe.cn

## 1.2 支付

此接口为支付流程的第一步，主要功能在于生成订单，获取必要的参数信息，来进行下一步的支付流程。对于不同的渠道和支付方式，接口的返回值与后续的操作都不尽相同（例如微信App支付需要调用微信支付SDK的接口，支付宝网页支付需要跳转到获取的一段HTML网址等），请根据每一个channel的详细描述分别处理.

<aside class="warning">
secret是一个非常重要的数据，请您必须小心谨慎的确保此数据保存在足够安全的地方。您从聚合支付官方获得此数据的同时，即表明您保证不会被用于非法用途和不会在没有得到您授权的情况下被盗用，一旦因此数据保管不善而导致的经济损失及法律责任，均由您独自承担。
</aside>

### 1.2.1 支付（线上）

URL:   */2/rest/bill*

Method: *POST*

请求参数格式: *JSON: Map*

请求参数详情:

- 公共请求参数：

参数名 | 类型 | 含义 | 描述 | 示例 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付平台的AppID | App在聚合支付平台的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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
coupon\_id | string | 卡券id | 传入卡券id，下单时会自动扣除优惠金额再发起支付 | 卡券id | 选填

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


### 1.2.2 支付（线下）

URL:   */2/rest/offline/bill*
Method: *POST*
请求参数格式: *JSON: Map*

请求参数详情:
- 以下为公共参数：

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付平台的AppID | App在聚合支付平台的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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
analysis | Map | 分析数据 | 用于统计分析的数据，将会在控制台的统计分析报表中展示，**<mark>用户自愿上传</mark>** | 包括以下基本字段："product","ip", 格式如下：{"product":[{"name":"product A", "count": 2, "price": 1000}, {"name": "product B", "count":1, "price": 2000}], "ip":"111.121.1.10"} product A，B是产品名字，2是件数，1000是单价（分）,ip是客户端客户的ip | 否
store_id | string | 门店号| 门店号 | 卡门店号 | 否
coupon\_id | string | 卡券id | 传入卡券id，下单时会自动扣除优惠金额再发起支付 | 卡券id | 选填

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


## 1.3 退款

### 1.3.1 退款（线上）

退款接口仅支持对已经支付成功的订单进行退款。 退款金额refund\_fee必须小于或者等于原始支付订单的total\_fee，如果是小于，则表示部分退款.

URL: */2/rest/refund*
Method: *POST*

请求参数格式: *JSON: Map*
请求参数详情:

- 请求参数

参数名 | 类型 | 含义   | 描述 | 例子 | 是否必填 |
---- | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062\-5e41\-44e3\-8f52\-f89d8cf2b6eb | 是 
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


### 1.3.3 退款（线下）

URL: */2/rest/offline/refund*
Method: *POST*

请求参数格式: *JSON: Map*
请求参数详情:

- 参数列表

参数名 | 类型 | 含义   | 描述 | 例子 | 是否必填 |
---- | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062\-5e41\-44e3\-8f52\-f89d8cf2b6eb | 是 
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

## 1.4 查询

### 1.4.1 支付订单查询

URL:   */2/rest/bills*
Method: *GET*

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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
bill\_fee | Integer         | 订单金额（原始的订单金额），单位为分
total\_fee    | Integer         | 实付金额（使用优惠券的时候是扣除优惠券优惠金额后客户实际支付的金额），单位为分
discount | Integer         | 优惠金额，单位为分
coupon\_id | String | 卡券ID，没有用到返回null
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


### 1.4.2 订单总数查询

URL:   */2/rest/bills/count*
Method: *GET*

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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


### 1.4.3 退款查询

URL:   */2/rest/refunds*
Method: GET

请求参数类型: JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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
total\_fee  | Integer      | 实付金额，单位为分
refund\_fee | Integer      | 退款金额，单位为分
title         | String       | 订单标题
channel    | String      | 渠道类型 WX、ALI、UN、JD、YEE、KUAIQIAN、BD
sub\_channel    | String      | 子渠道类型 WX\_NATIVE、WX\_JSAPI、WX\_APP、ALI\_APP、ALI\_WEB、ALI\_QRCODE、UN\_APP、UN\_WEB、UN\_WAP、JD_WAP、JD_WEB、YEE_WAP、YEE_WEB、KUAIQIAN_WAP、KUAIQIAN_WEB、BD\_APP、BD\_WEB、BD\_WAP(详见 1. 支付 附注）
finish     | bool        | 退款是否完成
result     | bool        | 退款是否成功
optional | String | 附加数据,用户自定义的参数，将会在webhook通知中原样返回，该字段是JSON格式的字符串 {"key1":"value1","key2":"value2",...}
message\_detail | String         | 渠道详细信息， 当need_detail传入true时返回
create\_time | Long       | 退款创建时间, 毫秒时间戳, 13位


### 1.4.4 退款总数查询

URL:   */2/rest/refunds/count*
Method: GET

请求参数类型: JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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


### 1.4.5 退款状态更新

退款状态更新接口提供查询退款状态以更新退款状态的功能，用于对退款后不发送回调的渠道（WX、YEE、KUAIQIAN、BD）退款后的状态更新。

URL:   */2/rest/refund/status*
Method: GET

请求参数类型:JSON，以para=**{}**的方式请求

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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

### 1.4.6 退款订单查询(指定ID)

URL:   */2/rest/refund/{id}*
Method: *GET*
id : 退款订单唯一标识

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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

### 1.4.7 支付订单查询(指定ID)

URL:   */2/rest/bill/{id}*
Method: *GET*
id : 支付订单唯一标识

请求参数类型: *JSON, 以para=**{}**的方式请求*

示例: para={"key\_a":1,"key\_b":"value\_b"}, 需要对para=后面的部分做URL encode.

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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

### 1.4.8 订单状态查询(线下刷卡专用)

URL:   */2/rest/offline/bill/status*
Method: POST

请求参数类型:JSON

- 请求参数

参数名 | 类型 | 含义 | 描述 | 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
app_id | String | 聚合支付应用APPID | 聚合支付的唯一标识 | 0950c062-5e41-44e3-8f52-f89d8cf2b6eb | 是
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


# 2. Webhook开发
## 2.1 简介

Webhook是聚合支付获得渠道的确认信息后，立刻向商户服务器发送的异步回调。支付，代付，退款成功时，聚合支付将向商户指定的URL发送HTTP/HTTPS的POST数据请求。

如果商户需要接收此类消息来实现业务逻辑，需要:

1. 开通公网可以访问的IP地址(或域名）和端口（如果需要对传输加密，请使用支持HTTPS的URL地址，聚合支付不要求HTTPS根证书认证）
2. 在 **控制台->选择应用->应用设置->Webhook参数设置** 中设置接收端URL，不同应用可设置不同URL，同一应用能且仅能设置一个测试URL，一个生产URL，另外在**控制台->应用设置->基本信息设置**中获取"Master Secret"
2. 处理POST请求报文，实现业务逻辑

<aside class="notify">
服务器间的交互，不像页面跳转同步通知（REST API中bill的参数return_url指定）可以在页面上显示出来，这种交互方式是通过后台通信来完成的，对用户是不可见的。
</aside>

## 2.2 推送机制

聚合支付在收到渠道的确认结果后立刻发送Webhook，Webhook只会从如下IP地址发送：

- 123.57.146.46
- 182.92.114.175

商户服务器接收到某条Webhook消息时如果未返回字符串 **success**, 聚合支付将认为此条消息未能被成功处理, 将触发推送重试机制：

聚合支付将在2秒，4秒，8秒，...，2^17秒（约36小时）时刻重发；如果在以上任一时刻，聚合支付收到了 **success**，重试将终止。

## 2.3 处理Webhook消息

请参考各开发语言的Webhook demo学习如何处理Webhook消息。

### 2.3.1 验证数字签名

目的在于验证Webhook是由聚合支付发起的，防止黑客向此Webhook接口发送伪造的订单信息。聚合支付使用MD5方式进行webhook加密，商户需要按照如下方法对参数进行数字签名验证。具体信息如下：  

验证方法 | MD5  
---- | ----
聚合支付签名字段 | **signature**  
验签内容 | app_id + transaction\_id + transaction\_type + channel\_type + transaction\_fee + master\_secret

商户必须按照上表中“验签内容”中罗列的参数顺序，将参数连接成字符串，然后进行MD5数字签名(32字符十六进制)。比较所得结果与`signature`字段值是否相等。其中`master_secret`为注册后从聚合支付获取的密钥。**注意，`signature`中的字母全部为小写**。

### 2.3.2 过滤重复的Webhook

同一条订单可能会发送多条支付成功的webhook消息，这有可能是由支付渠道本身触发的(比如渠道的重试)，也有可能是聚合支付的Webhook重试。商户需要根据订单号进行判重，忽略已经处理过的订单号对应的Webhook。


### 2.3.3 验证订单金额

商户需要验证Webhook中的 **transaction_fee** （实际的交易金额）与客户内部系统中的相应订单的金额匹配。

这个验证的目的在于防止黑客反编译了iOS或者Android app的代码，将本来比如100元的订单金额改成了1分钱，应该识别这种情况，避免误以为用户已经足额支付。Webhook传入的消息里面应该以某种形式包含此次购买的商品信息，比如title或者optional里面的某个参数说明此次购买的产品是一部iPhone手机，或者直接根据订单号查询，客户需要在内部数据库里去查询iPhone的金额是否与该Webhook的订单金额一致，仅有一致的情况下，才继续走正常的业务逻辑。如果发现不一致的情况，排除程序bug外，需要去查明原因，防止不法分子对你的app进行二次打包，对你的客户的利益构成潜在威胁。而且即使有这样极端的情况发生，只要按照前述要求做了购买的产品与订单金额的匹配性验证，客户也不会有任何经济损失。

### 2.3.4 处理业务逻辑和返回

这里就可以完成业务逻辑的处理。最后返回结果。用户返回 **success** 字符串给聚合支付表示 - **正确接收并处理了本次Webhook**，其他所有返回都代表需要继续重传本次的Webhook请求。

## 2.4 Webhook接口标准

```
HTTP 请求类型 : POST
HTTP 数据格式 : JSON
HTTP Content-type : application/json
```

### 2.4.1 字段说明


  Key             | Type          | Example
-------------     | ------------- | -------------
  signature       | String        | 32位小写
  timestamp       | Long          | 1426817510111
  channel_type     | String        | 'WX' or 'ALI' or 'UN' or 'KUAIQIAN' or 'JD' or 'BD' or 'YEE' or 'PAYPAL' or 'BC'
  sub\_channel\_type | String        | 'WX\_APP' or 'WX\_NATIVE' or 'WX\_JSAPI' or 'WX\_SCAN' or 'ALI\_APP' or 'ALI\_SCAN' or 'ALI\_WEB' or 'ALI\_QRCODE' or 'ALI\_OFFLINE\_QRCODE' or 'ALI\_WAP' or 'UN\_APP' or 'UN\_WEB' or 'PAYPAL\_SANDBOX' or 'PAYPAL\_LIVE' or 'JD\_WAP' or 'JD\_WEB' or 'YEE\_WAP' or 'YEE\_WEB' or 'YEE\_NOBANKCARD' or 'KUAIQIAN\_WAP' or 'KUAIQIAN\_WEB' or 'BD\_APP' or 'BD\_WEB' or 'BD\_WAP' or 'BC\_TRANSFER' or 'ALI_TRANSFER'  
  transaction_type | String        | 'PAY' or 'REFUND' or 'TRANSFER'（TRANSFER只代表BC企业打款和支付宝打款；微信打款和红包，没有webhook）
  transaction_id   | String        | '201506101035040000001'
  transaction_fee  | Integer       | 1 表示0.01元 
  bill\_fee        | Integer       | 2 标示0.02元
  discount         | Integer       | 1 标示0.01元
  coupon\_id | String | 卡券ID，没有用到返回null
  trade_success  | Bool       | true
  message_detail   | Map(JSON)     | {orderId:xxxx}
  optional        | Map(JSON)     | {"agent_id":"Alice"}

### 2.4.2 参数含义

key  | value
---- | -----
signature | 服务器端通过计算 **app\_id + transaction\_id + transaction\_type + channel\_type + transaction\_fee + master\_secret** 的MD5生成的签名(32字符十六进制),请在接受数据时自行按照此方式验证signature的正确性，不正确不返回success即可. 其中master\_secret为用户创建聚合支付 App时获取的参数。
timestamp | 服务端的时间（毫秒）
channel_type| WX/ALI/UN/KUAIQIAN/JD/BD/YEE/PAYPAL   分别代表微信/支付宝/银联/快钱/京东/百度/易宝/PAYPAL
sub_channel\_type|  代表以上各个渠道的子渠道，参看字段说明
transaction_type| PAY/REFUND  分别代表支付和退款的结果确认
transaction_id | 交易单号，对应支付请求的bill\_no或者退款请求的refund\_no,对于秒支付button为传入的out\_trade\_no
transaction_fee | 实付金额，是以分为单位的整数，当不使用优惠券时对应支付请求的total\_fee或者退款请求的refund\_fee，使用优惠券时是total_fee减掉优惠金额的实付金额
bill\_fee   | 订单金额（原始的订单金额），单位为分
discount  | 优惠金额，单位为分
coupon\_id | 卡券ID，没有用到返回null
trade_success | 交易是否成功，目前收到的消息都是交易成功的消息
message_detail| {orderId:xxx…..} 从支付渠道方获得的详细结果信息，例如支付的订单号，金额， 商品信息等，详见附录
optional| 附加参数，为一个JSON格式的Map，客户在发起购买或者退款操作时添加的附加信息
  

<aside class="notify">
请注意发送的HTTP头部Content-type为application/json,而非大部分框架自动解析的application/x-www-form-urlencoded格式,可能需要自行读取后解析,注意参考各样例代码中的读取写法。
</aside>


## 2.5 附录 - message_detail 样例 

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