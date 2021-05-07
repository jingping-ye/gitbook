# 多客服消息转发-地鼠文档

```text
transferCustomer := message.NewTransferCustomer("")
return &message.Reply{MsgType: message.MsgTypeTransfer, MsgData: transferCustomer}
```

**其中`NewTransferCustomer`如果参数可选，表示指定客服账号**

文档更新时间: 2020-08-19 10:29   作者：kuteng

