# 微信登录-地鼠文档

## 获取操作实例 <a id="ag1jwr"></a>

```text
mini := wc.GetMiniProgram(cfg)
a:=mini.GetAuth()
```

## 根据 jscode 获取用户 session 信息 <a id="7elot5"></a>

```text
Code2Session(jsCode string) (result ResCode2Session, err error)
```

文档更新时间: 2020-08-19 10:32   作者：kuteng

