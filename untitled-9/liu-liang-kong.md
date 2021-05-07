# 流量控

## 1. 流量控 <a id="&#x6D41;&#x91CF;&#x63A7;"></a>

利用`go` `channel`实现限流量控制，原理：设置一个缓冲通道，设置访问中间键，当用户请求连接时判断channel里面长度是不是大于设定的缓冲值，如果没有就存入一个值进入channel，如果大于缓冲值，channel自动阻塞。当用户请求结束的时候，取出channel里面的值。

如果想限制用户HTTP请求进行速率限制可以参考 [https://github.com/didip/tollbooth](https://github.com/didip/tollbooth) 这个中间键

目录：

-videos

--ce.html

-limiter.go

-main.go

main.go文件代码：

```text
package main

import (
    "log"
    "net/http"
    "text/template"
    "time"

    "github.com/julienschmidt/httprouter"
)

type middleWareHandler struct {
    r *httprouter.Router
    l *ConnLimiter
}


func NewMiddleWareHandler(r *httprouter.Router, cc int) http.Handler {
    m := middleWareHandler{}
    m.r = r
    m.l = NewConnLimiter(cc)
    return m
}

func (m middleWareHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    if !m.l.GetConn() {
        defer func() { recover() }()
        log.Panicln("Too many requests")
        return
    }
    m.r.ServeHTTP(w, r)
    defer m.l.ReleaseConn()
}


func RegisterHandlers() *httprouter.Router {
    router := httprouter.New()
    router.GET("/ce", ce)
    return router
}

func ce(w http.ResponseWriter, r *http.Request, p httprouter.Params) {
    
    time.Sleep(time.Second * 100)
    t, _ := template.ParseFiles("./videos/ce.html")
    t.Execute(w, nil)
}

func main() {
    r := RegisterHandlers()
    
    mh := NewMiddleWareHandler(r, 2)
    http.ListenAndServe(":9000", mh)
}
```

limiter.go文件代码

```text
package main

import (
    "log"
)


type ConnLimiter struct {
    concurrentConn int
    bucket         chan int
}


func NewConnLimiter(cc int) *ConnLimiter {
    return &ConnLimiter{
        concurrentConn: cc,
        bucket:         make(chan int, cc),
    }
}


func (cl *ConnLimiter) GetConn() bool {
    if len(cl.bucket) >= cl.concurrentConn {
        log.Printf("Reached the rate limitation.")
        return false
    }

    cl.bucket <- 1
    return true
}


func (cl *ConnLimiter) ReleaseConn() {
    c := <-cl.bucket
    log.Printf("New connction coming: %d", c)
}
```

videos/ce.html文件代码

```text
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    www.topgoer.com是个不错的go语言中文文档
</body>
</html>
```

