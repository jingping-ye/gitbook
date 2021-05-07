# 小程序码-地鼠文档

## 获取操作实例 <a id="e55asa"></a>

```text
mini := wc.GetMiniProgram(cfg)
qr:=mini.GetQrcode()
```

## 获取小程序二维码，适用于需要的码数量较少的业务场景 <a id="ccvyk0"></a>

```text
CreateWXAQRCode(coderParams QRCoder) (response []byte, err error)
```

## 获取小程序码，适用于需要的码数量较少的业务场景 <a id="daizhk"></a>

```text
GetWXACode(coderParams QRCoder) (response []byte, err error)
```

## 获取小程序码，适用于需要的码数量极多的业务场景 <a id="3jrseb"></a>

```text
GetWXACodeUnlimit(coderParams QRCoder) (response []byte, err error)
```

文档更新时间: 2020-08-19 10:33   作者：kuteng

