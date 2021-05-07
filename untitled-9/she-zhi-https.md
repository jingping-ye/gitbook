# 设置https

## 1. 设置https <a id="&#x8BBE;&#x7F6E;https"></a>

### 1.1.1. Secure <a id="secure"></a>

Secure是用于Go的HTTP中间件，可促进快速获得安全性。这是一个标准的net / http Handler，可以与许多框架一起使用，也可以直接与Go的net / http包一起使用。

### 1.1.2. 用法 <a id="&#x7528;&#x6CD5;"></a>

```text

package main

import (
    "net/http"

    "github.com/unrolled/secure" 
)

var myHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello world"))
})

func main() {
    secureMiddleware := secure.New(secure.Options{
        AllowedHosts:          []string{"example\\.com", ".*\\.example\\.com"},
        AllowedHostsAreRegex:  true,
        HostsProxyHeaders:     []string{"X-Forwarded-Host"},
        SSLRedirect:           true,
        SSLHost:               "ssl.example.com",
        SSLProxyHeaders:       map[string]string{"X-Forwarded-Proto": "https"},
        STSSeconds:            31536000,
        STSIncludeSubdomains:  true,
        STSPreload:            true,
        FrameDeny:             true,
        ContentTypeNosniff:    true,
        BrowserXssFilter:      true,
        ContentSecurityPolicy: "script-src $NONCE",
        PublicKey:             `pin-sha256="base64+primary=="; pin-sha256="base64+backup=="; max-age=5184000; includeSubdomains; report-uri="https://www.example.com/hpkp-report"`,
    })

    app := secureMiddleware.Handler(myHandler)
    http.ListenAndServe("127.0.0.1:3000", app)
}
```

确保将安全中间件尽可能地靠近顶部（开始）（但在记录和恢复之后）。最好先进行允许的主机和SSL检查。

上面的示例仅允许使用主机名“ example.com”或“ ssl.example.com”的请求。同样，如果请求不是HTTPS，则将使用主机名“ ssl.example.com”将其重定向到HTTPS。满足这些要求后，它将添加以下标头：

```text
Strict-Transport-Security: 31536000; includeSubdomains; preload
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Content-Security-Policy: script-src 'nonce-a2ZobGFoZg=='
PublicKey: pin-sha256="base64+primary=="; pin-sha256="base64+backup=="; max-age=5184000; includeSubdomains; report-uri="https://www.example.com/hpkp-report"
```

**在开发时将IsDevelopment选项设置为true！**

如果IsDevelopment为true，则AllowedHosts，SSLRedirect，STS头和HPKP头将无效。这使您可以在开发/测试模式下工作，而不必进行任何恼人的重定向到HTTPS（即，开发可以在HTTP上进行），或者阻止localhost主机出现问题。

### 1.1.3. 可用选项 <a id="&#x53EF;&#x7528;&#x9009;&#x9879;"></a>

Secure带有多种配置选项（注意：这些不是默认选项值。请参见下面的默认值。）：

```text
// ...
s := secure.New(secure.Options{
    AllowedHosts: []string{"ssl.example.com"}, // AllowedHosts is a list of fully qualified domain names that are allowed. Default is empty list, which allows any and all host names.
    AllowedHostsAreRegex: false,  // AllowedHostsAreRegex determines, if the provided AllowedHosts slice contains valid regular expressions. Default is false.
    HostsProxyHeaders: []string{"X-Forwarded-Hosts"}, // HostsProxyHeaders is a set of header keys that may hold a proxied hostname value for the request.
    SSLRedirect: true, // If SSLRedirect is set to true, then only allow HTTPS requests. Default is false.
    SSLTemporaryRedirect: false, // If SSLTemporaryRedirect is true, the a 302 will be used while redirecting. Default is false (301).
    SSLHost: "ssl.example.com", // SSLHost is the host name that is used to redirect HTTP requests to HTTPS. Default is "", which indicates to use the same host.
    SSLHostFunc: nil, // SSLHostFunc is a function pointer, the return value of the function is the host name that has same functionality as `SSHost`. Default is nil. If SSLHostFunc is nil, the `SSLHost` option will be used.
    SSLProxyHeaders: map[string]string{"X-Forwarded-Proto": "https"}, // SSLProxyHeaders is set of header keys with associated values that would indicate a valid HTTPS request. Useful when using Nginx: `map[string]string{"X-Forwarded-Proto": "https"}`. Default is blank map.
    STSSeconds: 31536000, // STSSeconds is the max-age of the Strict-Transport-Security header. Default is 0, which would NOT include the header.
    STSIncludeSubdomains: true, // If STSIncludeSubdomains is set to true, the `includeSubdomains` will be appended to the Strict-Transport-Security header. Default is false.
    STSPreload: true, // If STSPreload is set to true, the `preload` flag will be appended to the Strict-Transport-Security header. Default is false.
    ForceSTSHeader: false, // STS header is only included when the connection is HTTPS. If you want to force it to always be added, set to true. `IsDevelopment` still overrides this. Default is false.
    FrameDeny: true, // If FrameDeny is set to true, adds the X-Frame-Options header with the value of `DENY`. Default is false.
    CustomFrameOptionsValue: "SAMEORIGIN", // CustomFrameOptionsValue allows the X-Frame-Options header value to be set with a custom value. This overrides the FrameDeny option. Default is "".
    ContentTypeNosniff: true, // If ContentTypeNosniff is true, adds the X-Content-Type-Options header with the value `nosniff`. Default is false.
    BrowserXssFilter: true, // If BrowserXssFilter is true, adds the X-XSS-Protection header with the value `1; mode=block`. Default is false.
    CustomBrowserXssValue: "1; report=https://example.com/xss-report", // CustomBrowserXssValue allows the X-XSS-Protection header value to be set with a custom value. This overrides the BrowserXssFilter option. Default is "".
    ContentSecurityPolicy: "default-src 'self'", // ContentSecurityPolicy allows the Content-Security-Policy header value to be set with a custom value. Default is "". Passing a template string will replace `$NONCE` with a dynamic nonce value of 16 bytes for each request which can be later retrieved using the Nonce function.
    PublicKey: `pin-sha256="base64+primary=="; pin-sha256="base64+backup=="; max-age=5184000; includeSubdomains; report-uri="https://www.example.com/hpkp-report"`, // PublicKey implements HPKP to prevent MITM attacks with forged certificates. Default is "".
    ReferrerPolicy: "same-origin", // ReferrerPolicy allows the Referrer-Policy header with the value to be set with a custom value. Default is "".
    FeaturePolicy: "vibrate 'none';", // FeaturePolicy allows the Feature-Policy header with the value to be set with a custom value. Default is "".
    ExpectCTHeader: `enforce, max-age=30, report-uri="https://www.example.com/ct-report"`,

    IsDevelopment: true, // This will cause the AllowedHosts, SSLRedirect, and STSSeconds/STSIncludeSubdomains options to be ignored during development. When deploying to production, be sure to set this to false.
})
// ...
```

### 1.1.4. 默认选项 <a id="&#x9ED8;&#x8BA4;&#x9009;&#x9879;"></a>

```text
s := secure.New()

// Is the same as the default configuration options:

l := secure.New(secure.Options{
    AllowedHosts: []string,
    AllowedHostsAreRegex: false,
    HostsProxyHeaders: []string,
    SSLRedirect: false,
    SSLTemporaryRedirect: false,
    SSLHost: "",
    SSLProxyHeaders: map[string]string{},
    STSSeconds: 0,
    STSIncludeSubdomains: false,
    STSPreload: false,
    ForceSTSHeader: false,
    FrameDeny: false,
    CustomFrameOptionsValue: "",
    ContentTypeNosniff: false,
    BrowserXssFilter: false,
    ContentSecurityPolicy: "",
    PublicKey: "",
    ReferrerPolicy: "",
    FeaturePolicy: "",
    ExpectCTHeader: "",
    IsDevelopment: false,
})
```

另请注意，默认的错误主机处理程序将返回错误：

```text
http.Error(w, "Bad Host", http.StatusInternalServerError)
```

### 1.1.5. 将HTTP重定向到HTTPS <a id="&#x5C06;http&#x91CD;&#x5B9A;&#x5411;&#x5230;https"></a>

如果要将所有HTTP请求重定向到HTTPS，则可以使用以下示例。

```text

package main

import (
    "log"
    "net/http"

    "github.com/unrolled/secure" 
)

var myHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello world"))
})

func main() {
    secureMiddleware := secure.New(secure.Options{
        SSLRedirect: true,
        SSLHost:     "localhost:8443", 
    })

    app := secureMiddleware.Handler(myHandler)

    
    go func() {
        log.Fatal(http.ListenAndServe(":8080", app))
    }()

    
    
    
    log.Fatal(http.ListenAndServeTLS(":8443", "cert.pem", "key.pem", app))
}
```

STS标头仅在经过验证的HTTPS连接上发送（如果IsDevelopment为false，则发送）。SSLProxyHeaders如果您的应用程序位于代理之后，请确保设置该选项，以确保行为正确。如果所有HTTP和HTTPS请求都需要STS标头（不应该），则可以使用该ForceSTSHeader选项。请注意，如果IsDevelopment为true，即使ForceSTSHeader设置为true，它仍将禁用此标头。

要将该preload域包含在Chrome的预加载列表中，必须使用该标志。

### 1.1.6. 整合范例 <a id="&#x6574;&#x5408;&#x8303;&#x4F8B;"></a>

**chi**

```text

package main

import (
    "net/http"

    "github.com/pressly/chi"
    "github.com/unrolled/secure" 
)

func main() {
    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    r := chi.NewRouter()

    r.Get("/", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("X-Frame-Options header is now `DENY`."))
    })
    r.Use(secureMiddleware.Handler)

    http.ListenAndServe("127.0.0.1:3000", r)
}
```

**Echo**

```text

package main

import (
    "net/http"

    "github.com/labstack/echo"
    "github.com/unrolled/secure" 
)

func main() {
    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    e := echo.New()
    e.GET("/", func(c echo.Context) error {
        return c.String(http.StatusOK, "X-Frame-Options header is now `DENY`.")
    })

    e.Use(echo.WrapMiddleware(secureMiddleware.Handler))
    e.Logger.Fatal(e.Start("127.0.0.1:3000"))
}
```

**Gin**

```text

package main

import (
    "github.com/gin-gonic/gin"
    "github.com/unrolled/secure" 
)

func main() {
    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })
    secureFunc := func() gin.HandlerFunc {
        return func(c *gin.Context) {
            err := secureMiddleware.Process(c.Writer, c.Request)

            
            if err != nil {
                c.Abort()
                return
            }

            
            if status := c.Writer.Status(); status > 300 && status < 399 {
                c.Abort()
            }
        }
    }()

    router := gin.Default()
    router.Use(secureFunc)

    router.GET("/", func(c *gin.Context) {
        c.String(200, "X-Frame-Options header is now `DENY`.")
    })

    router.Run("127.0.0.1:3000")
}
```

**Goji**

```text

package main

import (
    "net/http"

    "github.com/unrolled/secure" 
    "github.com/zenazn/goji"
    "github.com/zenazn/goji/web"
)

func main() {
    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    goji.Get("/", func(c web.C, w http.ResponseWriter, req *http.Request) {
        w.Write([]byte("X-Frame-Options header is now `DENY`."))
    })
    goji.Use(secureMiddleware.Handler)
    goji.Serve() 
}
```

**Iris**

```text

package main

import (
    "github.com/kataras/iris/v12"
    "github.com/unrolled/secure" 
)

func main() {
    app := iris.New()

    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    app.Use(iris.FromStd(secureMiddleware.HandlerFuncWithNext))
    
    
    
    
    
    
    
    
    
    
    

    app.Get("/home", func(ctx iris.Context) {
        ctx.Writef("X-Frame-Options header is now `%s`.", "DENY")
    })

    app.Listen(":8080")
}
```

**Mux**

```text

package main

import (
    "log"
    "net/http"

    "github.com/gorilla/mux"
    "github.com/unrolled/secure" 
)

func main() {
    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    r := mux.NewRouter()
    r.Use(secureMiddleware.Handler)
    http.Handle("/", r)
    log.Fatal(http.ListenAndServe(fmt.Sprintf(":%d", 8080), nil))
}
```

**Negroni** 请注意，此实现具有一个名为的特殊帮助程序功能HandlerFuncWithNext。

```text

package main

import (
    "net/http"

    "github.com/urfave/negroni"
    "github.com/unrolled/secure" 
)

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
        w.Write([]byte("X-Frame-Options header is now `DENY`."))
    })

    secureMiddleware := secure.New(secure.Options{
        FrameDeny: true,
    })

    n := negroni.Classic()
    n.Use(negroni.HandlerFunc(secureMiddleware.HandlerFuncWithNext))
    n.UseHandler(mux)

    n.Run("127.0.0.1:3000")
}
```

