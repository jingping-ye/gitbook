# net/http的大概流程

## 1. net/http的大概流程 <a id="nethttp&#x7684;&#x5927;&#x6982;&#x6D41;&#x7A0B;"></a>

### 1.1.1. gin框架预览 <a id="gin&#x6846;&#x67B6;&#x9884;&#x89C8;"></a>

上图大概是gin里面比较重要的模块. 从gin的官方第一个demo入手.

```text
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "go语言中文文档www.topgoer.com",
        })
    })
    r.Run() 
}
```

r.Run\(\)的源码:

```text
func (engine *Engine) Run(addr ...string) (err error) {
    defer func() { debugPrintError(err) }()

    address := resolveAddress(addr)
    debugPrint("Listening and serving HTTP on %s\n", address)
    err = http.ListenAndServe(address, engine)
    return
}
```

然后看到开始调用的是http.ListenAndServe\(address, engine\), 这个函数是net/http的函数. 然后请求数据就在net/http开始流转.

所以, gin源码阅读系列就是要弄明白以下几个问题:

* request数据是如何流转的
* gin框架到底扮演了什么角色
* 请求从gin流入net/http, 最后又是如何回到gin中
* gin的context为何能承担起来复杂的需求
* gin的路由算法
* gin的中间件是什么
* gin的Engine具体是个什么东西
* net/http的requeset, response都提供了哪些有用的东西

### 1.1.2. request数据是如何流转的 <a id="request&#x6570;&#x636E;&#x662F;&#x5982;&#x4F55;&#x6D41;&#x8F6C;&#x7684;"></a>

先不使用gin, 直接使用net/http来处理http请求

```text
func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("Hello World"))
    })

    if err := http.ListenAndServe(":8000", nil); err != nil {
        fmt.Println("start http server fail:", err)
    }
}
```

在浏览器中输入localhost:8000, 会看到Hello World. 下面利用这个简单demo看下request的流转流程.

### 1.1.3. HTTP是如何建立起来的 <a id="http&#x662F;&#x5982;&#x4F55;&#x5EFA;&#x7ACB;&#x8D77;&#x6765;&#x7684;"></a>

简单的说一下http请求是如何建立起来的\(需要有基本的网络基础, 可以找相关的书籍查看, 推荐看UNIX网络编程卷1：套接字联网API\)

在TCP/IP五层模型下, HTTP位于应用层, 需要有传输层来承载HTTP协议. 传输层比较常见的协议是TCP,UDP, SCTP等. 由于UDP不可靠, SCTP有自己特殊的运用场景, 所以一般情况下HTTP是由TCP协议承载的\(可以使用wireshark抓包然后查看各层协议\)

使用TCP协议的话, 就会涉及到TCP是如何建立起来的. 面试中能够常遇到的名词三次握手, 四次挥手就是在这里产生的. 具体的建立流程就不在陈述了, 大概流程就是图中左半边

所以说, 要想能够对客户端http请求进行回应的话, 就首先需要建立起来TCP连接, 也就是socket. 下面要看下net/http是如何建立起来socket?

### 1.1.4. net/http是如何建立socket的 <a id="nethttp&#x662F;&#x5982;&#x4F55;&#x5EFA;&#x7ACB;socket&#x7684;"></a>

从图上可以看出, 不管server代码如何封装, 都离不开bind,listen,accept这些函数. 就从上面这个简单的demo入手查看源码.

```text
func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("Hello World"))
    })

    if err := http.ListenAndServe(":8000", nil); err != nil {
        fmt.Println("start http server fail:", err)
    }
}
```

### 1.1.5. 注册路由 <a id="&#x6CE8;&#x518C;&#x8DEF;&#x7531;"></a>

```text
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("Hello World"))
    })
```

这段代码是在注册一个路由及这个路由的handler到DefaultServeMux中

```text

func (mux *ServeMux) Handle(pattern string, handler Handler) {
    mux.mu.Lock()
    defer mux.mu.Unlock()

    if pattern == "" {
        panic("http: invalid pattern")
    }
    if handler == nil {
        panic("http: nil handler")
    }
    if _, exist := mux.m[pattern]; exist {
        panic("http: multiple registrations for " + pattern)
    }

    if mux.m == nil {
        mux.m = make(map[string]muxEntry)
    }
    mux.m[pattern] = muxEntry{h: handler, pattern: pattern}

    if pattern[0] != '/' {
        mux.hosts = true
    }
}
```

可以看到这个路由注册太过简单了, 也就给gin, iris, echo等框架留下了扩展的空间, 后面详细说这个东西

### 1.1.6. 服务监听及响应 <a id="&#x670D;&#x52A1;&#x76D1;&#x542C;&#x53CA;&#x54CD;&#x5E94;"></a>

上面路由已经注册到net/http了, 下面就该如何建立socket了, 以及最后又如何取到已经注册到的路由, 将正确的响应信息从handler中取出来返回给客户端

```text
if err := http.ListenAndServe(":8000", nil); err != nil {
    fmt.Println("start http server fail:", err)
}
```

```text

func ListenAndServe(addr string, handler Handler) error {
    server := &Server{Addr: addr, Handler: handler}
    return server.ListenAndServe()
}
```

```text

func (srv *Server) ListenAndServe() error {
    
    ln, err := net.Listen("tcp", addr) 
    if err != nil {
        return err
    }
    return srv.Serve(tcpKeepAliveListener{ln.(*net.TCPListener)})
}
```

```text

func (srv *Server) Serve(l net.Listener) error {
    
    for {
        rw, e := l.Accept() 
        if e != nil {
            select {
            case <-srv.getDoneChan():
                return ErrServerClosed
            default:
            }
            if ne, ok := e.(net.Error); ok && ne.Temporary() {
                if tempDelay == 0 {
                    tempDelay = 5 * time.Millisecond
                } else {
                    tempDelay *= 2
                }
                if max := 1 * time.Second; tempDelay > max {
                    tempDelay = max
                }
                srv.logf("http: Accept error: %v; retrying in %v", e, tempDelay)
                time.Sleep(tempDelay)
                continue
            }
            return e
        }
        tempDelay = 0
        c := srv.newConn(rw)
        c.setState(c.rwc, StateNew) 
        go c.serve(ctx) 
    }
}
```

```text

func (c *conn) serve(ctx context.Context) {
    
    serverHandler{c.server}.ServeHTTP(w, w.req)
    w.cancelCtx()
    if c.hijacked() {
        return
    }
    w.finishRequest()
    
}
```

```text

func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
    handler := sh.srv.Handler
    if handler == nil {
        handler = DefaultServeMux
    }
    if req.RequestURI == "*" && req.Method == "OPTIONS" {
        handler = globalOptionsHandler{}
    }
    handler.ServeHTTP(rw, req)
}
```

```text

func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) {
    if r.RequestURI == "*" {
        if r.ProtoAtLeast(1, 1) {
            w.Header().Set("Connection", "close")
        }
        w.WriteHeader(StatusBadRequest)
        return
    }
    h, _ := mux.Handler(r) 
    h.ServeHTTP(w, r)
}


func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
```

这基本是整个过程的代码了. 基本上是:

* ln, err := net.Listen\("tcp", addr\)做了初试化了socket, bind, listen的操作.
* rw, e := l.Accept\(\)进行accept, 等待客户端进行连接
* go c.serve\(ctx\) 启动新的goroutine来处理本次请求. 同时主goroutine继续等待客户端连接, 进行高并发操作
* h, \_ := mux.Handler\(r\) 获取注册的路由, 然后拿到这个路由的handler, 然后将处理结果返回给客户端

从这里也能够看出来, net/http基本上提供了全套的服务.

### 1.1.7. 为什么会出现很多go框架 <a id="&#x4E3A;&#x4EC0;&#x4E48;&#x4F1A;&#x51FA;&#x73B0;&#x5F88;&#x591A;go&#x6846;&#x67B6;"></a>

```text

func (mux *ServeMux) match(path string) (h Handler, pattern string) {
    
    v, ok := mux.m[path]
    if ok {
        return v.h, v.pattern
    }

    
    var n = 0
    for k, v := range mux.m {
        if !pathMatch(k, path) {
            continue
        }
        if h == nil || len(k) > n {
            n = len(k)
            h = v.h
            pattern = v.pattern
        }
    }
    return
}
```

从这段函数可以看出来, 匹配规则过于简单, 当能匹配到路由的时候就返回其对应的handler, 当不能匹配到时就返回/. 所以net/http的路由匹配无法满足复杂的需求开发. 所以基本所有的go框架干的最主要的一件事情就是重写net/http的route

所以我们直接说gin就是一个httprouter也不过分, 当然gin也提供了其他比较主要的功能, 后面会一一介绍

还有一个go框架要实现的东西是http.ResponseWriter

综述, net/http基本已经提供http服务的70%的功能, 那些号称贼快的go框架, 基本上都是提供一些功能, 让我们能够更好的处理客户端发来的请求.

转自：[https://www.jianshu.com/p/ef8217daf6f7](https://www.jianshu.com/p/ef8217daf6f7)

