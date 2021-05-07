# 快速入门-地鼠文档

以下例子就演示了一个启动一个server，接收到用户发往公众号的消息然后做处理。

> * 测试公众号可以使用[微信公众平台接口测试平台](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)
> * 本地环境开发的话，可以使用 [ngrok](https://ngrok.com/)工具映射出来的公网地址，方便调试。

通过`go mod`初始化一个项目，并将`wechat sdk`下载下来：

```text
go mod init github.com/silenceper/wechat-example
go get -v github.com/silenceper/wechat/v2
```

**包含一个文件`main.go`**

> 代码中的配置参数请更改为自己的！

```text
package main

import (
    "fmt"
    "net/http"

    wechat "github.com/silenceper/wechat/v2"
    "github.com/silenceper/wechat/v2/cache"
    offConfig "github.com/silenceper/wechat/v2/officialaccount/config"
    "github.com/silenceper/wechat/v2/officialaccount/message"
)

func serveWechat(rw http.ResponseWriter, req *http.Request) {
    wc := wechat.NewWechat()
    
    memory := cache.NewMemory()
    cfg := &offConfig.Config{
        AppID:     "xxx",
        AppSecret: "xxx",
        Token:     "xxx",
        
        Cache: memory,
    }
    officialAccount := wc.GetOfficialAccount(cfg)

    
    server := officialAccount.GetServer(req, rw)
    
    server.SetMessageHandler(func(msg message.MixMessage) *message.Reply {
        
        
        text := message.NewText(msg.Content)
        return &message.Reply{MsgType: message.MsgTypeText, MsgData: text}
    })

    
    err := server.Serve()
    if err != nil {
        fmt.Println(err)
        return
    }
    
    server.Send()
}

func main() {
    http.HandleFunc("/", serveWechat)
    fmt.Println("wechat server listener at", ":8001")
    err := http.ListenAndServe(":8001", nil)
    if err != nil {
        fmt.Printf("start server error , err=%v", err)
    }
}
```

**运行**

```text
# go run main.go
wechat server listen at :8001
```

文档更新时间: 2020-08-19 10:28   作者：kuteng

