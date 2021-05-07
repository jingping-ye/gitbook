# JSSDK-地鼠文档

## 获取js-sdk操作实例 <a id="emeolk"></a>

```text
oa := wc.GetOfficialAccount(cfg)
j:=oa.GetJs()
```

## 获取js配置 <a id="8vqj41"></a>

```text
GetConfig(uri string) (config *Config, err error)
```

其中 `Config` 结果为：

```text

type Config struct {
    AppID     string `json:"app_id"`
    Timestamp int64  `json:"timestamp"`
    NonceStr  string `json:"nonce_str"`
    Signature string `json:"signature"`
}
```

## 替换`js-ticket`取值方式 <a id="9jsvq5"></a>

默认js-ticket是存放在sdk设置的cache，如果需要自定义取值，可以实现`credential.JsTicketHandle`接口：

```text

type JsTicketHandle interface {
    
    GetTicket(accessToken string) (ticket string, err error)
}
```

然后通过`js.SetJsTicketHandle(ticketHandle credential.JsTicketHandle)`进行设置

文档更新时间: 2020-08-19 10:30   作者：kuteng

