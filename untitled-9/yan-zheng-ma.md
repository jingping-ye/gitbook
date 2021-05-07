# 验证码

## 1. 验证码 <a id="&#x9A8C;&#x8BC1;&#x7801;"></a>

验证码套件可实现图像和音频验证码的生成和验证。

验证码解决方案是具有定义长度的数字序列0-9。验证码有两种表示形式：图像和音频。

图像表示是经过PNG编码的图像，上面印有解决方案，使得计算机难以使用OCR对其进行求解。

音频表示形式是WAVE编码（8 kHz无符号8位）声音，带有语音解决方案（当前为英语，俄语，中文和日语）。为了使计算机难以解决音频验证码，发音数字的声音具有随机的速度和音调，并且声音中随机混有背景噪声。

该程序包不需要外部文件或库来生成验证码表示形式。它是独立的。

为了一次性获得验证码，该程序包包括一个存储验证码ID，其解决方案和到期时间的内存存储。在调用Verify或VerifyString之后，立即将使用过的验证码从存储中删除，而未使用的验证码（用户使用验证码加载页面但未提交表单）将在预定义的到期时间后自动收集。开发人员还可以通过实现Store接口并向SetCustomStore注册对象来提供自定义存储（例如，在数据库中保存验证码ID和解决方案）。

验证码是通过调用New创建的，该函数返回验证码ID。但是，它们的表示是通过调用WriteImage或WriteAudio函数即时创建的。创建的表示形式不会存储在任何地方，但是随后对具有相同ID的这些函数的调用将编写相同的验证码解决方案。重新加载功能将为提供的验证码创建一个新的不同解决方案，如果用户在不重新加载整个页面的情况下无法解决显示的验证码，则允许用户“重新加载”验证码。验证和VerifyString用于验证给定的解决方案是给定的验证码ID的正确解决方案。

服务器提供了一个http.Handler，可以从URL自动提供验证码的图像和音频表示。它也可以用来重新加载验证码。有关详细信息，请参阅服务器功能文档，或查看“ capexample”子目录中的示例。

### 1.1.1. 图片实例 <a id="&#x56FE;&#x7247;&#x5B9E;&#x4F8B;"></a>

### 1.1.2. 更多 <a id="&#x66F4;&#x591A;"></a>

查看官网地址：[https://github.com/dchest/captcha](https://github.com/dchest/captcha)

### 1.1.3. 实例 <a id="&#x5B9E;&#x4F8B;"></a>

#### 使用： <a id="&#x4F7F;&#x7528;&#xFF1A;"></a>

```text
    go get github.com/dchest/captcha
```

### 1.1.4. 代码 <a id="&#x4EE3;&#x7801;"></a>

具体实例可以查看capexample目录，有生成的验证码图片。

```text
package main

import (
    "fmt"
    "github.com/dchest/captcha"
    "io"
    "log"
    "net/http"
    "text/template"
)

var formTemplate = template.Must(template.New("example").Parse(formTemplateSrc))

func showFormHandler(w http.ResponseWriter, r *http.Request) {
    if r.URL.Path != "/" {
        http.NotFound(w, r)
        return
    }
    d := struct {
        CaptchaId string
    }{
        captcha.New(),
    }
    if err := formTemplate.Execute(w, &d); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
}

func processFormHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "text/html; charset=utf-8")
    if !captcha.VerifyString(r.FormValue("captchaId"), r.FormValue("captchaSolution")) {
        io.WriteString(w, "Wrong captcha solution! No robots allowed!\n")
    } else {
        io.WriteString(w, "Great job, human! You solved the captcha.\n")
    }
    io.WriteString(w, "
Try another one")
}

func main() {
    http.HandleFunc("/", showFormHandler)
    http.HandleFunc("/process", processFormHandler)
    http.Handle("/captcha/", captcha.Server(captcha.StdWidth, captcha.StdHeight))
    fmt.Println("Server is at localhost:8666")
    if err := http.ListenAndServe("localhost:8666", nil); err != nil {
        log.Fatal(err)
    }
}

const formTemplateSrc = `
Captcha Example




`
```

