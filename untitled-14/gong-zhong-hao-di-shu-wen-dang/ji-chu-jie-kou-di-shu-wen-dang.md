# 基础接口-地鼠文档

微信公众号操作基础接口

## 获取`access_token` <a id="5qhbmp"></a>

> 在每个操作实例对象中也有`GetAccessToken`方法
>
> ```text
> ak,err:=officialAccount.GetAccessToken()
> ```

### 替换获取`access_token`的方法 <a id="33lxdl"></a>

默认在sdk内部是ak获取之后是存放在cache中，如果你有其他系统以及存放了ak的话，只需要实现如下interface就可以了：

```text

type AccessTokenHandle interface {
    GetAccessToken() (accessToken string, err error)
}
```

然后通过wechat对象，进行设置:

```text
officialAccount.SetAccessTokenHandle=customAccessTokenHandle
```

## 获取微信服务器 IP \(或IP段\) <a id="s8euv"></a>

如果公众号基于安全等考虑，需要获知微信服务器的IP地址列表，以便进行相关限制，可以通过该接口获得微信服务器IP地址列表或者IP网段信息。

### 获取微信callback IP地址 <a id="dp8tgo"></a>

```text
ipList, err:=officialAccount.GetBasic().GetCallbackIP()
```

### 获取微信API接口 IP地址 <a id="gay9hd"></a>

```text
ipList, err:=officialAccount.GetBasic().GetAPIDomainIP()
```

## 清理接口调用频次 <a id="dvi0pi"></a>

> 此接口官方有每月调用限制，不可随意调用

```text
err:=officialAccount.GetBasic().ClearQuota()
```

文档更新时间: 2020-08-19 10:26   作者：kuteng

