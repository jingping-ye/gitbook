# 开放平台-地鼠文档

状态：beta

[官方文档](https://developers.weixin.qq.com/doc/oplatform/Third-party_Platforms/Third_party_platform_appid.html)

## 快速入门 <a id="fflvv4"></a>

```text
wc := wechat.NewWechat()
memory := cache.NewMemory()
cfg := &openplatform.Config{
    AppID:     "xxx",
    AppSecret: "xxx",
    Token:     "xxx",
    EncodingAESKey: "xxx",
    Cache: memory,
}


appID := "xxx"

openPlatform := wc.GetOpenPlatform(cfg)
officialAccount := openPlatform.GetOfficialAccount(appID)


server := officialAccount.GetServer(req, rw)

server.SetMessageHandler(func(msg message.MixMessage) *message.Reply {
    if msg.InfoType == message.InfoTypeVerifyTicket {
        componentVerifyTicket, err := openPlatform.SetComponentAccessToken(msg.ComponentVerifyTicket)
        if err != nil {
            log.Println(err)
            return nil
        }
        
        fmt.Println(componentVerifyTicket)
        rw.Write([]byte("success"))
        return nil
    }
    
    


    return nil
})


err := server.Serve()
if err != nil {
    fmt.Println(err)
    return
}

server.Send()
```

## 代第三方公众号 - 发起网页授权 <a id="a3ooao"></a>

```text

appid := ""
officialAccount := openPlatform.GetOfficialAccount(appid)
oauth := officialAccount.PlatformOauth()

oauth.Redirect(rw, req, callback, "snsapi_userinfo", "", appid)
```

## 代第三方公众号 - 通过网页授权的code 换取access\_token <a id="2bzl5e"></a>

```text
officialAccount := openPlatform.GetOfficialAccount(appid)
componentAccessToken, err := openPlatform.GetComponentAccessToken()
if err != nil {
    fmt.Println(err)
}
accessToken, err := officialAccount.PlatformOauth().GetUserAccessToken(code, appid, componentAccessToken)
if err != nil {
    fmt.Println(err)
}
fmt.Println(accessToken)
```

## 维护AuthrToken <a id="2srvhk"></a>

AuthrToken是平台代第三方公众号调用微信接口的凭据，通过第三方公众号授权给平台时得到的refreshToken来获取

```text
func CheckAuthrToken(appid, refreshToken string) {
    
    token, err := openPlatform.GetAuthrAccessToken(appid)
    if err != nil {
        fmt.Println(err)
    }
    if token == "" {
        openPlatform.RefreshAuthrToken(appid, refreshToken)
    }
}
```

## 代第三方公众号 - 调用微信接口（以发送微信模板消息为例） <a id="svsmy"></a>

平台代第三方公众号调用微信接口，需要在调用前确保AuthrToken有效，其余操作与公众号一致。

```text
import "github.com/silenceper/wechat/v2/officialaccount/message"



CheckAuthrToken(appid, refreshToken)
msg := &message.TemplateMessage{
    ToUser:     openid,
    TemplateID: templateID,
    URL:        url,
    Data:       data,
}
officialAccount := openPlatform.GetOfficialAccount(appid)
template := message.NewTemplate(officialAccount.GetContext())
msgID, err := template.Send(msg)
if err != nil {
    fmt.Println(err)
}
fmt.Println(msgID)
```

> 微信的部分接口（如：获取jsconfig信息）区分了第三方平台调用和公众号直接调用的地址，在文档下方单独进行说明。

## 代第三方公众号 - 获取jsconfig信息 <a id="d6i9ck"></a>

```text
CheckAuthrToken(appid, refreshToken)
jsConfig, err := openPlatform.GetOfficialAccount(appid).PlatformJs().GetConfig(uri, appid)
if err != nil {
    fmt.Println(err)
}
fmt.Println(jsConfig)
```

## 全网发布校验 <a id="64gvze"></a>

微信第三方平台进行全网发布的时候，会有一个全网发布接入检测的过程。  
[官方文档](https://developers.weixin.qq.com/doc/oplatform/Third-party_Platforms/Post_Application_on_the_Entire_Network/releases_instructions.html)

```text
wc := wechat.NewWechat()
memory := cache.NewMemory()
cfg := &openplatform.Config{
    AppID:     "xxx",
    AppSecret: "xxx",
    Token:     "xxx",
    EncodingAESKey: "xxx",
    Cache: memory,
}


appID := "xxx"

openPlatform := wc.GetOpenPlatform(cfg)
officialAccount := openPlatform.GetOfficialAccount(appID)


server := officialAccount.GetServer(req, rw)

server.SetMessageHandler(func(msg message.MixMessage) *message.Reply {
    switch msg.InfoType {
    case message.InfoTypeVerifyTicket:
        
        
        rw.Write([]byte("success"))
    case message.InfoTypeAuthorized:
        
        
    }
    switch msg.MsgType {
    case message.MsgTypeText:
        if msg.Content == "TESTCOMPONENT_MSG_TYPE_TEXT" {
            
            return &message.Reply{
                MsgType: message.MsgTypeText,
                MsgData: message.NewText("TESTCOMPONENT_MSG_TYPE_TEXT_callback"),
            }
        }
        
        if strings.HasPrefix(msg.Content, "QUERY_AUTH_CODE") {
            
            rw.Write([]byte(""))
            var data = strings.Split(msg.Content, ":")
            if len(data) == 2 {
                
                customerMsg := message.NewCustomerTextMessage(string(msg.FromUserName), fmt.Sprintf("%s_from_api", data[1]))
                CheckAuthrToken(appid, refreshToken)
                officialAccount := openPlatform.GetOfficialAccount(appid)
                msgManager := message.NewMessageManager(officialAccount.GetContext())
                msgManager.Send(msg)
            }
        }
    }    
    return nil
})


err := server.Serve()
if err != nil {
    fmt.Println(err)
    return
}

server.Send()
```

## 公众号授权流程 <a id="eo2z73"></a>

小程序或者公众号授权给第三方平台的流程  
[官方文档](https://developers.weixin.qq.com/doc/oplatform/Third-party_Platforms/Authorization_Process_Technical_Description.html)

## 扫码授权 <a id="19mv8b"></a>

```text

loginPageURL, err := openPlatform.GetComponentLoginPage(redirectURI, authType, "")




authInfo, err := openPlatform.QueryAuthCode(authCode)
```

文档更新时间: 2020-08-19 10:35   作者：kuteng

