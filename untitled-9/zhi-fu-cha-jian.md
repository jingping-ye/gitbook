# 支付插件

## 1. 支付插件 <a id="&#x652F;&#x4ED8;&#x63D2;&#x4EF6;"></a>

### 1.1.1. 一、安装 <a id="&#x4E00;&#x3001;&#x5B89;&#x88C5;"></a>

```text
$ go get github.com/iGoogle-ink/gopay
```

**查看 GoPay 版本**

 [版本更新记录](https://github.com/iGoogle-ink/gopay/blob/master/release_note.txt)

```text
import (
    "fmt"

    "github.com/iGoogle-ink/gopay"
)

func main() {
    fmt.Println("GoPay Version: ", gopay.Version)
}
```

### 1.1.2. 微信支付API <a id="&#x5FAE;&#x4FE1;&#x652F;&#x4ED8;api"></a>

* 统一下单：client.UnifiedOrder\(\)
  * JSAPI - JSAPI支付（或小程序支付）
  * NATIVE - Native支付
  * APP - app支付
  * MWEB - H5支付
* 提交付款码支付：client.Micropay\(\)
* 查询订单：client.QueryOrder\(\)
* 关闭订单：client.CloseOrder\(\)
* 撤销订单：client.Reverse\(\)
* 申请退款：client.Refund\(\)
* 查询退款：client.QueryRefund\(\)
* 下载对账单：client.DownloadBill\(\)
* 下载资金账单（正式）：client.DownloadFundFlow\(\)
* 交易保障：client.Report\(\)
* 拉取订单评价数据（正式）：client.BatchQueryComment\(\)
* 企业付款（正式）：client.Transfer\(\)
* 查询企业付款（正式）：client.GetTransferInfo\(\)
* 授权码查询OpenId（正式）：client.AuthCodeToOpenId\(\)
* 公众号纯签约（正式）：client.EntrustPublic\(\)
* APP纯签约-预签约接口-获取预签约ID（正式）：client.EntrustAppPre\(\)
* H5纯签约（正式）：client.EntrustH5\(\)
* 支付中签约（正式）：client.EntrustPaying\(\)
* 请求单次分账（正式）：client.ProfitSharing\(\)
* 请求多次分账（正式）：client.MultiProfitSharing\(\)
* 查询分账结果（正式）：client.ProfitSharingQuery\(\)
* 添加分账接收方（正式）：client.ProfitSharingAddReceiver\(\)
* 删除分账接收方（正式）：client.ProfitSharingRemoveReceiver\(\)
* 完结分账（正式）：client.ProfitSharingFinish\(\)
* 分账回退（正式）：client.ProfitSharingReturn\(\)
* 分账回退结果查询（正式）：client.ProfitSharingReturnQuery\(\)
* 企业付款到银行卡API（正式）：client.PayBank\(\)
* 查询企业付款到银行卡API（正式）：client.QueryBank\(\)
* 获取RSA加密公钥API（正式）：client.GetRSAPublicKey\(\)
* 自定义方法请求微信API接口：client.PostWeChatAPISelf\(\)

### 1.1.3. 微信公共API <a id="&#x5FAE;&#x4FE1;&#x516C;&#x5171;api"></a>

* wechat.GetParamSign\(\) =&gt; 获取微信支付所需参数里的Sign值（通过支付参数计算Sign值）
* wechat.GetSanBoxParamSign\(\) =&gt; 获取微信支付沙箱环境所需参数里的Sign值（通过支付参数计算Sign值）
* wechat.GetMiniPaySign\(\) =&gt; 获取微信小程序支付所需要的paySign
* wechat.GetH5PaySign\(\) =&gt; 获取微信内H5支付所需要的paySign
* wechat.GetAppPaySign\(\) =&gt; 获取APP支付所需要的paySign
* wechat.ParseNotifyToBodyMap\(\) =&gt; 解析微信支付异步通知的参数到BodyMap
* wechat.ParseNotify\(\) =&gt; 解析微信支付异步通知的参数
* wechat.ParseRefundNotify\(\) =&gt; 解析微信退款异步通知的参数
* wechat.VerifySign\(\) =&gt; 微信同步返回参数验签或异步通知参数验签
* wechat.Code2Session\(\) =&gt; 登录凭证校验：获取微信用户OpenId、UnionId、SessionKey
* wechat.GetAppletAccessToken\(\) =&gt; 获取微信小程序全局唯一后台接口调用凭据
* wechat.GetAppletPaidUnionId\(\) =&gt; 微信小程序用户支付完成后，获取该用户的 UnionId，无需用户授权
* wechat.GetUserInfo\(\) =&gt; 微信公众号：获取用户基本信息\(UnionID机制\)
* wechat.DecryptOpenDataToStruct\(\) =&gt; 加密数据，解密到指定结构体
* wechat.DecryptOpenDataToBodyMap\(\) =&gt; 加密数据，解密到 BodyMap
* wechat.GetOpenIdByAuthCode\(\) =&gt; 授权码查询openid
* wechat.GetAppLoginAccessToken\(\) =&gt; App应用微信第三方登录，code换取access\_token
* wechat.RefreshAppLoginAccessToken\(\) =&gt; 刷新App应用微信第三方登录后，获取的 access\_token
* wechat.DecryptRefundNotifyReqInfo\(\) =&gt; 解密微信退款异步通知的加密数据

### 1.1.4. QQ支付API <a id="qq&#x652F;&#x4ED8;api"></a>

* 提交付款码支付：client.MicroPay\(\)
* 撤销订单：client.Reverse\(\)
* 统一下单：client.UnifiedOrder\(\)
* 订单查询：client.OrderQuery\(\)
* 关闭订单：client.CloseOrder\(\)
* 申请退款：client.Refund\(\)
* 退款查询：client.RefundQuery\(\)
* 交易账单：client.StatementDown\(\)
* 资金账单：client.AccRoll\(\)
* 自定义方法请求微信API接口：client.PostQQAPISelf\(\)

### 1.1.5. QQ公共API <a id="qq&#x516C;&#x5171;api"></a>

* qq.ParseNotifyToBodyMap\(\) =&gt; 解析QQ支付异步通知的结果到BodyMap
* qq.ParseNotify\(\) =&gt; 解析QQ支付异步通知的参数
* qq.VerifySign\(\) =&gt; QQ同步返回参数验签或异步通知参数验签

### 1.1.6. 支付宝支付API <a id="&#x652F;&#x4ED8;&#x5B9D;&#x652F;&#x4ED8;api"></a>

> #### 因支付宝接口太多，如没实现的接口，还请开发者自行调用client.PostAliPayAPISelf\(\)方法实现！ <a id="&#x56E0;&#x652F;&#x4ED8;&#x5B9D;&#x63A5;&#x53E3;&#x592A;&#x591A;&#xFF0C;&#x5982;&#x6CA1;&#x5B9E;&#x73B0;&#x7684;&#x63A5;&#x53E3;&#xFF0C;&#x8FD8;&#x8BF7;&#x5F00;&#x53D1;&#x8005;&#x81EA;&#x884C;&#x8C03;&#x7528;clientpostalipayapiself&#x65B9;&#x6CD5;&#x5B9E;&#x73B0;&#xFF01;"></a>
>
> * 支付宝接口自行实现方法：client.PostAliPayAPISelf\(\)
> * 手机网站支付接口2.0（手机网站支付）：client.TradeWapPay\(\)
> * 统一收单下单并支付页面接口（电脑网站支付）：client.TradePagePay\(\)
> * APP支付接口2.0（APP支付）：client.TradeAppPay\(\)
> * 统一收单交易支付接口（商家扫用户付款码）：client.TradePay\(\)
> * 统一收单交易创建接口（小程序支付）：client.TradeCreate\(\)
> * 统一收单线下交易查询：client.TradeQuery\(\)
> * 统一收单交易关闭接口：client.TradeClose\(\)
> * 统一收单交易撤销接口：client.TradeCancel\(\)
> * 统一收单交易退款接口：client.TradeRefund\(\)
> * 统一收单退款页面接口：client.TradePageRefund\(\)
> * 统一收单交易退款查询：client.TradeFastPayRefundQuery\(\)
> * 统一收单交易结算接口：client.TradeOrderSettle\(\)
> * 统一收单线下交易预创建（用户扫商品收款码）：client.TradePrecreate\(\)
> * 单笔转账接口：client.FundTransUniTransfer\(\)
> * 转账业务单据查询接口：client.FundTransCommonQuery\(\)
> * 支付宝资金账户资产查询接口：client.FundAccountQuery\(\)
> * 换取授权访问令牌（获取access\_token，user\_id等信息）：client.SystemOauthToken\(\)
> * 支付宝会员授权信息查询接口（App支付宝登录）：client.UserInfoShare\(\)
> * 换取应用授权令牌（获取app\_auth\_token，auth\_app\_id，user\_id等信息）：client.OpenAuthTokenApp\(\)
> * 获取芝麻信用分：client.ZhimaCreditScoreGet\(\)
> * 身份认证初始化服务：client.UserCertifyOpenInit\(\)
> * 身份认证开始认证（获取认证链接）：client.UserCertifyOpenCertify\(\)
> * 身份认证记录查询：client.UserCertifyOpenQuery\(\)
> * 用户登陆授权：client.UserInfoAuth\(\)
> * 支付宝商家账户当前余额查询：client.DataBillBalanceQuery\(\)
> * 查询对账单下载地址：client.DataBillDownloadUrlQuery\(\)

### 1.1.7. 支付宝公共API <a id="&#x652F;&#x4ED8;&#x5B9D;&#x516C;&#x5171;api"></a>

* alipay.GetCertSN\(\) =&gt; 获取证书SN号（app\_cert\_sn、alipay\_cert\_sn）
* alipay.GetRootCertSN\(\) =&gt; 获取证书SN号（alipay\_root\_cert\_sn）
* alipay.GetRsaSign\(\) =&gt; 获取支付宝参数签名（参数sign值）
* alipay.SystemOauthToken\(\) =&gt; 换取授权访问令牌（得到access\_token，user\_id等信息）
* alipay.FormatPrivateKey\(\) =&gt; 格式化应用私钥
* alipay.FormatPublicKey\(\) =&gt; 格式化支付宝公钥
* alipay.FormatURLParam\(\) =&gt; 格式化支付宝请求URL参数
* alipay.ParseNotifyToBodyMap\(\) =&gt; 解析支付宝支付异步通知的参数到BodyMap
* alipay.ParseNotifyByURLValues\(\) =&gt; 通过 url.Values 解析支付宝支付异步通知的参数到BodyMap
* alipay.VerifySign\(\) =&gt; 支付宝异步通知参数验签
* alipay.VerifySignWithCert\(\) =&gt; 支付宝异步通知参数验签（证书方式）
* alipay.VerifySyncSign\(\) =&gt; 支付宝同步返回参数验签
* alipay.DecryptOpenDataToStruct\(\) =&gt; 解密支付宝开放数据到 结构体
* alipay.DecryptOpenDataToBodyMap\(\) =&gt; 解密支付宝开放数据到 BodyMap
* alipay.MonitorHeartbeatSyn\(\) =&gt; 验签接口

### 1.1.8. 二、文档说明 <a id="&#x4E8C;&#x3001;&#x6587;&#x6863;&#x8BF4;&#x660E;"></a>

### 1.1.9. 1、初始化GoPay客户端并做配置（HTTP请求均默认设置tls.Config{InsecureSkipVerify: true}） <a id="1&#x3001;&#x521D;&#x59CB;&#x5316;gopay&#x5BA2;&#x6237;&#x7AEF;&#x5E76;&#x505A;&#x914D;&#x7F6E;&#xFF08;http&#x8BF7;&#x6C42;&#x5747;&#x9ED8;&#x8BA4;&#x8BBE;&#x7F6E;tlsconfiginsecureskipverify-true&#xFF09;"></a>

* **微信**

微信官方文档：[官方文档](https://pay.weixin.qq.com/wiki/doc/api/index.html)

```text
import (
    "github.com/iGoogle-ink/gopay/wechat"
)






client := wechat.NewClient("wxdaa2ab9ef87b5497", mchId, apiKey, false)






client.SetCountry(gopay.China)






client.AddCertFilePath()
```

* **支付宝**

支付宝官方文档：[官方文档](https://openhome.alipay.com/docCenter/docCenter.htm)

支付宝RSA秘钥生成文档：[生成RSA密钥](https://opendocs.alipay.com/open/291/105971) （推荐使用 RSA2）

沙箱环境使用说明：[文档地址](https://opendocs.alipay.com/open/200/105311)

```text
import (
    "github.com/iGoogle-ink/gopay/alipay"
)





client := alipay.NewClient("2016091200494382", privateKey, false)



client.SetLocation().                       
    SetPrivateKeyType().                    
    SetAliPayRootCertSN().                  
    SetAppCertSN().                         
    SetAliPayPublicCertSN().                
    SetCharset("utf-8").                    
    SetSignType(alipay.RSA2).               
    SetReturnUrl("https://www.gopay.ink").  
    SetNotifyUrl("https://www.gopay.ink").  
    SetAppAuthToken().                      
    SetAuthToken()                          

err := client.SetCertSnByPath("appCertPublicKey.crt", "alipayRootCert.crt", "alipayCertPublicKey_RSA2.crt")
```

### 1.1.10. 2、初始化并赋值BodyMap（client的方法所需的入参） <a id="2&#x3001;&#x521D;&#x59CB;&#x5316;&#x5E76;&#x8D4B;&#x503C;bodymap&#xFF08;client&#x7684;&#x65B9;&#x6CD5;&#x6240;&#x9700;&#x7684;&#x5165;&#x53C2;&#xFF09;"></a>

* **微信请求参数**

具体参数请根据不同接口查看：[微信支付接口文档](https://pay.weixin.qq.com/wiki/doc/api/index.html)

```text
import (
    "github.com/iGoogle-ink/gopay/wechat"
)


bm := make(gopay.BodyMap)
bm.Set("nonce_str", gotil.GetRandomString(32))
bm.Set("body", "小程序测试支付")
bm.Set("out_trade_no", number)
bm.Set("total_fee", 1)
bm.Set("spbill_create_ip", "127.0.0.1")
bm.Set("notify_url", "http://www.gopay.ink")
bm.Set("trade_type", gopay.TradeType_Mini)
bm.Set("device_info", "WEB")
bm.Set("sign_type", gopay.SignType_MD5)
bm.Set("openid", "o0Df70H2Q0fY8JXh1aFPIRyOBgu8")


h5Info := make(map[string]string)
h5Info["type"] = "Wap"
h5Info["wap_url"] = "http://www.gopay.ink"
h5Info["wap_name"] = "H5测试支付"

sceneInfo := make(map[string]map[string]string)
sceneInfo["h5_info"] = h5Info

bm.Set("scene_info", sceneInfo)



sign := wechat.GetParamSign("wxdaa2ab9ef87b5497", mchId, apiKey, body)

bm.Set("sign", sign)
```

* **支付宝请求参数**

具体参数请根据不同接口查看：[支付宝支付API接口文档](https://opendocs.alipay.com/apis/api_1/alipay.trade.wap.pay)

```text

bm := make(gopay.BodyMap)
bm.Set("subject", "手机网站测试支付")
bm.Set("out_trade_no", "GZ201901301040355703")
bm.Set("quit_url", "https://www.gopay.ink")
bm.Set("total_amount", "100.00")
bm.Set("product_code", "QUICK_WAP_WAY")
```

### 1.1.11. 3、client 方法调用 <a id="3&#x3001;client-&#x65B9;&#x6CD5;&#x8C03;&#x7528;"></a>

* **微信 client**

  ```text
  wxRsp, err := client.UnifiedOrder(bm)
  wxRsp, err := client.Micropay(bm)
  wxRsp, err := client.QueryOrder(bm)
  wxRsp, err := client.CloseOrder(bm)
  wxRsp, err := client.Reverse(bm, "apiclient_cert.pem", "apiclient_key.pem", "apiclient_cert.p12")
  wxRsp, err := client.Refund(bm, "apiclient_cert.pem", "apiclient_key.pem", "apiclient_cert.p12")
  wxRsp, err := client.QueryRefund(bm)
  wxRsp, err := client.DownloadBill(bm)
  wxRsp, err := client.DownloadFundFlow(bm, "apiclient_cert.pem", "apiclient_key.pem", "apiclient_cert.p12")
  wxRsp, err := client.BatchQueryComment(bm, "apiclient_cert.pem", "apiclient_key.pem", "apiclient_cert.p12")
  wxRsp, err := client.Transfer(bm, "apiclient_cert.pem", "apiclient_key.pem", "apiclient_cert.p12")
  ```

* **支付宝 client**

```text

payUrl, err := client.TradeWapPay(bm)


payUrl, err := client.TradePagePay(bm)


payParam, err := client.TradeAppPay(bm)


aliRsp, err := client.TradePay(bm)




aliRsp, err := client.TradeCreate(bm)

aliRsp, err := client.TradeQuery(bm)
aliRsp, err := client.TradeClose(bm)
aliRsp, err := client.TradeCancel(bm)
aliRsp, err := client.TradeRefund(bm)
aliRsp, err := client.TradePageRefund(bm)
aliRsp, err := client.TradeFastPayRefundQuery(bm)
aliRsp, err := client.TradeOrderSettle(bm)
aliRsp, err := client.TradePrecreate(bm)
aliRsp, err := client.FundTransUniTransfer(bm)
aliRsp, err := client.FundTransCommonQuery(bm)
aliRsp, err := client.FundAccountQuery(bm)
aliRsp, err := client.SystemOauthToken(bm)
aliRsp, err := client.OpenAuthTokenApp(bm)
aliRsp, err := client.ZhimaCreditScoreGet(bm)
aliRsp, err := client.UserCertifyOpenInit(bm)
aliRsp, err := client.UserCertifyOpenCertify(bm)
aliRsp, err := client.UserCertifyOpenQuery(bm)
```

### 1.1.12. 4、微信统一下单后，获取微信小程序支付、APP支付、微信内H5支付所需要的 paySign <a id="4&#x3001;&#x5FAE;&#x4FE1;&#x7EDF;&#x4E00;&#x4E0B;&#x5355;&#x540E;&#xFF0C;&#x83B7;&#x53D6;&#x5FAE;&#x4FE1;&#x5C0F;&#x7A0B;&#x5E8F;&#x652F;&#x4ED8;&#x3001;app&#x652F;&#x4ED8;&#x3001;&#x5FAE;&#x4FE1;&#x5185;h5&#x652F;&#x4ED8;&#x6240;&#x9700;&#x8981;&#x7684;-paysign"></a>

* **微信（只有微信需要此操作）**

   微信小程序支付官方文档：[微信小程序支付API](https://developers.weixin.qq.com/miniprogram/dev/api/open-api/payment/wx.requestPayment.html)

APP支付官方文档：[APP端调起支付的参数列表文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_12)

微信内H5支付官方文档：[微信内H5支付文档](https://pay.weixin.qq.com/wiki/doc/api/external/jsapi.php?chapter=7_7&index=6)

```text
import (
    "github.com/iGoogle-ink/gopay/wechat"
)


timeStamp := strconv.FormatInt(time.Now().Unix(), 10)
prepayId := "prepay_id=" + wxRsp.PrepayId   







paySign := wechat.GetMiniPaySign(AppID, wxRsp.NonceStr, prepayId, wechat.SignType_MD5, timeStamp, apiKey)


timeStamp := strconv.FormatInt(time.Now().Unix(), 10)









paySign := wechat.GetAppPaySign(appid, partnerid, wxRsp.NonceStr, wxRsp.PrepayId, wechat.SignType_MD5, timeStamp, apiKey)


timeStamp := strconv.FormatInt(time.Now().Unix(), 10)
packages := "prepay_id=" + wxRsp.PrepayId   







paySign := wechat.GetH5PaySign(AppID, wxRsp.NonceStr, packages, wechat.SignType_MD5, timeStamp, apiKey)
```

### 1.1.13. 5、同步返回参数验签Sign、异步通知参数解析和验签Sign、异步通知返回 <a id="5&#x3001;&#x540C;&#x6B65;&#x8FD4;&#x56DE;&#x53C2;&#x6570;&#x9A8C;&#x7B7E;sign&#x3001;&#x5F02;&#x6B65;&#x901A;&#x77E5;&#x53C2;&#x6570;&#x89E3;&#x6790;&#x548C;&#x9A8C;&#x7B7E;sign&#x3001;&#x5F02;&#x6B65;&#x901A;&#x77E5;&#x8FD4;&#x56DE;"></a>

异步参数需要先解析，解析出来的结构体或BodyMap再验签

[Echo Web框架](https://github.com/labstack/echo)，有兴趣的可以尝试一下

异步通知处理完后，需回复平台固定数据

* **微信**

```text
import (
    "github.com/iGoogle-ink/gopay"
    "github.com/iGoogle-ink/gopay/wechat"
)


wxRsp, err := client.UnifiedOrder(bm)






ok, err := wechat.VerifySign(apiKey, wechat.SignType_MD5, wxRsp)






notifyReq, err := wechat.ParseNotify(c.Request())    

ok, err := wechat.VerifySign(apiKey, wechat.SignType_MD5, notifyReq)







notifyReq, err := wechat.ParseRefundNotify(c.Request())


refundNotify, err := wechat.DecryptRefundNotifyReqInfo(notifyReq.ReqInfo, apiKey)


rsp := new(wechat.NotifyResponse) 
rsp.ReturnCode = gopay.SUCCESS
rsp.ReturnMsg = gopay.OK
return c.String(http.StatusOK, rsp.ToXmlString())   
```

* **支付宝**

注意：APP支付、手机网站支付、电脑网站支付 暂不支持同步返回验签

支付宝支付后的同步/异步通知验签文档：[支付结果通知](https://opendocs.alipay.com/open/200/106120)

```text
import (
    "github.com/iGoogle-ink/gopay/alipay"
)


aliRsp, err := client.TradePay(bm)







ok, err := alipay.VerifySyncSign(aliPayPublicKey, aliRsp.SignData, aliRsp.Sign)






notifyReq, err = alipay.ParseNotify(c.Request())     

ok, err = alipay.VerifySign(aliPayPublicKey, notifyReq)




return c.String(http.StatusOK, "success")   
```

### 1.1.14. 6、微信、支付宝 公共API（仅部分说明） <a id="6&#x3001;&#x5FAE;&#x4FE1;&#x3001;&#x652F;&#x4ED8;&#x5B9D;-&#x516C;&#x5171;api&#xFF08;&#x4EC5;&#x90E8;&#x5206;&#x8BF4;&#x660E;&#xFF09;"></a>

* **微信 公共API**

官方文档：[code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/login/auth.code2Session.html)

button按钮获取手机号码：[button组件文档](https://developers.weixin.qq.com/miniprogram/dev/component/button.html)

微信解密算法文档：[解密算法文档](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/signature.html)

```text
import (
    "github.com/iGoogle-ink/gopay/wechat"
)





sessionRsp, err := wechat.Code2Session(appId, appSecret, wxCode)




data := "Kf3TdPbzEmhWMuPKtlKxIWDkijhn402w1bxoHL4kLdcKr6jT1jNcIhvDJfjXmJcgDWLjmBiIGJ5acUuSvxLws3WgAkERmtTuiCG10CKLsJiR+AXVk7B2TUQzsq88YVilDz/YAN3647REE7glGmeBPfvUmdbfDzhL9BzvEiuRhABuCYyTMz4iaM8hFjbLB1caaeoOlykYAFMWC5pZi9P8uw=="
iv := "Cds8j3VYoGvnTp1BrjXdJg=="
session := "lyY4HPQbaOYzZdG+JcYK9w=="
phone := new(wechat.UserPhone)





err := wechat.DecryptOpenDataToStruct(data, iv, session, phone)
fmt.Println(*phone)

sessionKey := "tiihtNczf5v6AKRyjwEUhQ=="
encryptedData := "CiyLU1Aw2KjvrjMdj8YKliAjtP4gsMZMQmRzooG2xrDcvSnxIMXFufNstNGTyaGS9uT5geRa0W4oTOb1WT7fJlAC+oNPdbB+3hVbJSRgv+4lGOETKUQz6OYStslQ142dNCuabNPGBzlooOmB231qMM85d2/fV6ChevvXvQP8Hkue1poOFtnEtpyxVLW1zAo6/1Xx1COxFvrc2d7UL/lmHInNlxuacJXwu0fjpXfz/YqYzBIBzD6WUfTIF9GRHpOn/Hz7saL8xz+W//FRAUid1OksQaQx4CMs8LOddcQhULW4ucetDf96JcR3g0gfRK4PC7E/r7Z6xNrXd2UIeorGj5Ef7b1pJAYB6Y5anaHqZ9J6nKEBvB4DnNLIVWSgARns/8wR2SiRS7MNACwTyrGvt9ts8p12PKFdlqYTopNHR1Vf7XjfhQlVsAJdNiKdYmYVoKlaRv85IfVunYzO0IKXsyl7JCUjCpoG20f0a04COwfneQAGGwd5oa+T8yO5hzuyDb/XcxxmK01EpqOyuxINew=="
iv2 := "r7BXXKkLb8qrSNn05n0qiA=="


userInfo := new(wechat.AppletUserInfo)
err = wechat.DecryptOpenDataToStruct(encryptedData, iv2, sessionKey, userInfo)
fmt.Println(*userInfo)

data := "Kf3TdPbzEmhWMuPKtlKxIWDkijhn402w1bxoHL4kLdcKr6jT1jNcIhvDJfjXmJcgDWLjmBiIGJ5acUuSvxLws3WgAkERmtTuiCG10CKLsJiR+AXVk7B2TUQzsq88YVilDz/YAN3647REE7glGmeBPfvUmdbfDzhL9BzvEiuRhABuCYyTMz4iaM8hFjbLB1caaeoOlykYAFMWC5pZi9P8uw=="
iv := "Cds8j3VYoGvnTp1BrjXdJg=="
session := "lyY4HPQbaOYzZdG+JcYK9w=="





bm, err := wechat.DecryptOpenDataToBodyMap(data, iv, session)
if err != nil {
     fmt.Println("err:", err)
     return
}
fmt.Println("WeChatUserPhone:", bm)
```

* **支付宝 公共API**

支付宝换取授权访问令牌文档：[换取授权访问令牌](https://opendocs.alipay.com/apis/api_9/alipay.system.oauth.token)

获取用户手机号文档：[获取用户手机号](https://opendocs.alipay.com/mini/api/getphonenumber)

支付宝加解密文档：[AES配置文档](https://opendocs.alipay.com/mini/introduce/aes) ，[AES加解密文档](https://opendocs.alipay.com/open/common/104567)

```text
import (
    "github.com/iGoogle-ink/gopay/alipay"
)






rsp, err := alipay.SystemOauthToken(appId, privateKey, grantType, codeOrToken)



phone := new(alipay.UserPhone)




err := alipay.DecryptOpenDataToStruct(encryptedData, secretKey, phone)
fmt.Println(*phone)
```

### 1.1.15. License <a id="license"></a>

```text
Copyright 2019 Jerry

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

