# http编程

## 1. http编程 <a id="http&#x7F16;&#x7A0B;"></a>

### 1.1.1. web工作流程 <a id="web&#x5DE5;&#x4F5C;&#x6D41;&#x7A0B;"></a>

* Web服务器的工作原理可以简单地归纳为
  * 客户机通过TCP/IP协议建立到服务器的TCP连接
  * 客户端向服务器发送HTTP协议请求包，请求服务器里的资源文档
  * 服务器向客户机发送HTTP协议应答包，如果请求的资源包含有动态语言的内容，那么服务器会调用动态语言的解释引擎负责处理“动态内容”，并将处理得到的数据返回给客户端
  * 客户机与服务器断开。由客户端解释HTML文档，在客户端屏幕上渲染图形结果

### 1.1.2. HTTP协议 <a id="http&#x534F;&#x8BAE;"></a>

* 超文本传输协议\(HTTP，HyperText Transfer Protocol\)是互联网上应用最为广泛的一种网络协议，它详细规定了浏览器和万维网服务器之间互相通信的规则，通过因特网传送万维网文档的数据传送协议
* HTTP协议通常承载于TCP协议之上

### 1.1.3. HTTP服务端 <a id="http&#x670D;&#x52A1;&#x7AEF;"></a>

```text
package main

import (
    "fmt"
    "net/http"
)

func main() {
    
    
    http.HandleFunc("/go", myHandler)
    
    
    
    http.ListenAndServe("127.0.0.1:8000", nil)
}


func myHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Println(r.RemoteAddr, "连接成功")
    
    fmt.Println("method:", r.Method)
    
    fmt.Println("url:", r.URL.Path)
    fmt.Println("header:", r.Header)
    fmt.Println("body:", r.Body)
    
    w.Write([]byte("www.5lmh.com"))
}
```

### 1.1.4. HTTP服务端 <a id="http&#x670D;&#x52A1;&#x7AEF;_1"></a>

```text
package main

import (
    "fmt"
    "io"
    "net/http"
)

func main() {
    
    
    resp, _ := http.Get("http://127.0.0.1:8000/go")
    defer resp.Body.Close()
    
    fmt.Println(resp.Status)
    fmt.Println(resp.Header)

    buf := make([]byte, 1024)
    for {
        
        n, err := resp.Body.Read(buf)
        if err != nil && err != io.EOF {
            fmt.Println(err)
            return
        } else {
            fmt.Println("读取完毕")
            res := string(buf[:n])
            fmt.Println(res)
            break
        }
    }
}
```

