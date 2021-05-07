# proxy转发

## 1. proxy转发 <a id="proxy&#x8F6C;&#x53D1;"></a>

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

```text
package main

import (
    "fmt"
    "net/http"
    "net/http/httputil"
    "net/url"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
    u, _ := url.Parse("http://127.0.0.1:9091/")
    proxy := httputil.NewSingleHostReverseProxy(u)
    proxy.ServeHTTP(w, r)
}
func main() {
    http.HandleFunc("/topgoer", sayHello)
    err := http.ListenAndServe(":9090", nil)
    if err != nil {
        fmt.Println("HTTP server failed,err:", err)
        return
    }
}
```

```text
package main

import (
    "fmt"
    "net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
    fmt.Println("topgoer.com是个不错的网站我是9091里面的")
}
func main() {
    http.HandleFunc("/topgoer", sayHello)
    err := http.ListenAndServe(":9091", nil)
    if err != nil {
        fmt.Println("HTTP server failed,err:", err)
        return
    }
}
```

访问`http://localhost:9090/5lmh`实际执行的是`http://localhost:9091/5lmh`的方法

