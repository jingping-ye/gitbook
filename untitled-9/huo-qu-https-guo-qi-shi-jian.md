# 获取https过期时间

## 1. 获取https过期时间 <a id="&#x83B7;&#x53D6;https&#x8FC7;&#x671F;&#x65F6;&#x95F4;"></a>

### 1.1.1. HTTPS介绍 <a id="https&#x4ECB;&#x7ECD;"></a>

HTTPS （全称：Hyper Text Transfer Protocol over SecureSocket Layer），是以安全为目标的 HTTP 通道，在HTTP的基础上通过传输加密和身份认证保证了传输过程的安全性 。HTTPS 在HTTP 的基础下加入SSL 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。 HTTPS 存在不同于 HTTP 的默认端口及一个加密/身份验证层（在 HTTP与 TCP 之间）。这个系统提供了身份验证与加密通讯方法。它被广泛用于万维网上安全敏感的通讯，例如交易支付等方面

### 1.1.2. 获取HTTPS证书过期时间 <a id="&#x83B7;&#x53D6;https&#x8BC1;&#x4E66;&#x8FC7;&#x671F;&#x65F6;&#x95F4;"></a>

```text
package main

import (
    "crypto/tls"
    "fmt"
    "net/http"
)

func main() {
    tr := &http.Transport{
        TLSClientConfig: &tls.Config{InsecureSkipVerify: true},
    }
    client := &http.Client{Transport: tr}

    seedUrl := "https://www.baidu.cn"
    resp, err := client.Get(seedUrl)
    defer resp.Body.Close()

    if err != nil {
        fmt.Errorf(seedUrl, " 请求失败")
        panic(err)
    }

    
    certInfo := resp.TLS.PeerCertificates[0]
    fmt.Println("过期时间:", certInfo.NotAfter)
    fmt.Println("组织信息:", certInfo.Subject)
}
```

