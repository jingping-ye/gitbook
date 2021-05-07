# 消息解密-地鼠文档

## 获取操作实例 <a id="hegb4"></a>

```text
mini := wc.GetMiniProgram(cfg)
a:=mini.GetAuth()
```

## 解密数据 <a id="2l7az9"></a>

```text
Decrypt(sessionKey, encryptedData, iv string) (*PlainData, error)
```

其中结果为：

```text
type PlainData struct {
    OpenID    string `json:"openId"`
    UnionID   string `json:"unionId"`
    NickName  string `json:"nickName"`
    Gender    int    `json:"gender"`
    City      string `json:"city"`
    Province  string `json:"province"`
    Country   string `json:"country"`
    AvatarURL string `json:"avatarUrl"`
    Language  string `json:"language"`
    PhoneNumber     string `json:"phoneNumber"`
    PurePhoneNumber string `json:"purePhoneNumber"`
    CountryCode     string `json:"countryCode"`
    Watermark struct {
        Timestamp int64  `json:"timestamp"`
        AppID     string `json:"appid"`
    } `json:"watermark"`
}
```

根据需要取用户信息还是手机号信息

文档更新时间: 2020-08-19 10:32   作者：kuteng

