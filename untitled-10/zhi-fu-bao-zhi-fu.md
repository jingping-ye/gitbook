# 支付宝支付

## 1. 支付宝支付 <a id="&#x652F;&#x4ED8;&#x5B9D;&#x652F;&#x4ED8;"></a>

本文采用沙箱环境

### 1.1.1. 开启沙箱 <a id="&#x5F00;&#x542F;&#x6C99;&#x7BB1;"></a>

文档：[https://docs.open.alipay.com/200/105311/](https://docs.open.alipay.com/200/105311/) 沙箱地址：[https://openhome.alipay.com/platform/appDaily.htm](https://openhome.alipay.com/platform/appDaily.htm)

### 1.1.2. 生成秘钥（已弃用，请直接使用步骤 3） <a id="&#x751F;&#x6210;&#x79D8;&#x94A5;&#xFF08;&#x5DF2;&#x5F03;&#x7528;&#xFF0C;&#x8BF7;&#x76F4;&#x63A5;&#x4F7F;&#x7528;&#x6B65;&#x9AA4;-3&#xFF09;"></a>

本文中的签名方法默认为 RSA2，采用支付宝提供的 RSA 签名 & 验签工具 生成秘钥时，秘钥的格式必须为 PKCS1，秘钥长度推荐 2048。所以在支付宝管理后台请注意配置 RSA2 \(SHA256\) 密钥。 生成秘钥对之后，将公钥提供给支付宝（通过支付宝后台上传）对我们请求的数据进行签名验证，我们的代码中将使用私钥对请求数据签名。

RSA 签名和验证工具下载：[https://docs.open.alipay.com/291/105971](https://docs.open.alipay.com/291/105971)

* 下载之后解压
* 双击 RSA签名验签工具.bat
* 秘钥格式选择 PKCS1
* 点击生成秘钥
* 复制公钥
* 回到沙箱中，点击查看应用公钥，然后点击修改
* 保存好私钥，我们一会需要在代码中用到
* 复制支付宝公钥，代码中验证需要用到
* 配置支付成功后的回调地址

### 1.1.3. 证书认证 <a id="&#x8BC1;&#x4E66;&#x8BA4;&#x8BC1;"></a>

目前新创建的支付宝应用只支持证书方式认证，已经弃用之前的公钥和私钥的方式

**公钥秘钥说明**

我们生成秘钥对之后，将公钥提供给支付宝（通过支付宝后台上传）对我们请求的数据进行签名验证，我们的代码中使用私钥对请求数据签名。

* 证书签名请求文件（用来提交给支付宝后台生成证书的）
* 应用私钥（调用支付宝接口的时候，我们需要使用该私钥对参数进行签名）
* 支付宝公钥证书（用来验证我们的签名的，现在已经被支付宝公钥证书取代）

#### 下载生成工具 <a id="&#x4E0B;&#x8F7D;&#x751F;&#x6210;&#x5DE5;&#x5177;"></a>

#### 生成 csr 证书签名请求文件 <a id="&#x751F;&#x6210;-csr-&#x8BC1;&#x4E66;&#x7B7E;&#x540D;&#x8BF7;&#x6C42;&#x6587;&#x4EF6;"></a>

工具安装好之后打开，点击获取

#### 输入信息 <a id="&#x8F93;&#x5165;&#x4FE1;&#x606F;"></a>

主要是组织/公司这块一定要写的和你支付宝中应用的名一样，不然不会通过的，填写完毕之后点击生成CSR文件 ，点击页面的打开文件位置，就可以看到三个文件了，分别是证书签名请求文件，应用公钥，应用私钥

#### 上传 CSR 证书签名请求文件 <a id="&#x4E0A;&#x4F20;-csr-&#x8BC1;&#x4E66;&#x7B7E;&#x540D;&#x8BF7;&#x6C42;&#x6587;&#x4EF6;"></a>

回到支付宝后台，点击 接口加签方式 设置，选择公钥证书，点击上次 CSR 生成证书，把我们刚才生成的那个证书 \(.csr\) 上传进去

#### 下载证书 <a id="&#x4E0B;&#x8F7D;&#x8BC1;&#x4E66;"></a>

上传好之后，会弹出让你下载证书的页面，把那三个证书都下载下来，分别是：应用公钥证书，支付宝公钥证书，支付宝根证书

### 1.1.4. 代码部分 <a id="&#x4EE3;&#x7801;&#x90E8;&#x5206;"></a>

下载第三方库

```text
    go get github.com/smartwalle/alipay/v3
```

#### 网页扫码支付 <a id="&#x7F51;&#x9875;&#x626B;&#x7801;&#x652F;&#x4ED8;"></a>

```text
package main

import (
    "fmt"
    "github.com/smartwalle/alipay"
    "net/http"
    "os/exec"
    "strings"
    "time"
)

var (
    
    appId = ""
    
    aliPublicKey = ""
    
    privateKey = ""
    client, _ = alipay.New(appId, aliPublicKey, privateKey, false)
)

func init() {
    client.LoadAppPublicCert("应用公钥证书")
    client.LoadAliPayPublicCert("支付宝公钥证书")
    client.LoadAliPayRootCert("支付宝根证书")
}


func WebPageAlipay() {
    pay := alipay.AliPayTradePagePay{}
    
    pay.ReturnURL = "http://localhost:8088/return"
    
    pay.Subject = "支付宝支付测试"
    
    pay.OutTradeNo = time.Now().String()
    
    pay.ProductCode = "FAST_INSTANT_TRADE_PAY"
    
    pay.TotalAmount = "0.01"

    url, err := client.TradePagePay(pay)
    if err != nil {
        fmt.Println(err)
    }
    payURL := url.String()
    
    fmt.Println(payURL)

    
    payURL = strings.Replace(payURL,"&","^&",-1)
    exec.Command("cmd", "/c", "start",payURL).Start()
}


func WapAlipay() {
    pay := alipay.AliPayTradeWapPay{}
    
    pay.ReturnURL = "http://localhost:8088/return"
    
    pay.Subject = "支付宝支付测试"
    
    pay.OutTradeNo = time.Now().String()
    
    pay.ProductCode = time.Now().String()
    
    pay.TotalAmount = "0.01"

    url, err := client.TradeWapPay(pay)
    if err != nil {
        fmt.Println(err)
    }
    payURL := url.String()
    
    fmt.Println(payURL)
    
    payURL = strings.Replace(payURL,"&","^&",-1)
    exec.Command("cmd", "/c", "start",payURL).Start()
}

func main() {
    
    WapAlipay()
    
    http.HandleFunc("/return", func(rep http.ResponseWriter, req *http.Request) {
        req.ParseForm()
        ok, err := client.VerifySign(req.Form)
        if err == nil && ok {
            rep.Write([]byte("支付成功"))
        }
    })
    fmt.Println("server start....")
    http.ListenAndServe(":8088", nil)
}
```

转自：[https://learnku.com/go/t/31515](https://learnku.com/go/t/31515)

