# 原生中间件

## 1. 原生中间件 <a id="&#x539F;&#x751F;&#x4E2D;&#x95F4;&#x4EF6;"></a>

web开发的背景下，“中间件”通常意思是“包装原始应用并添加一些额外的功能的应用的一部分”。这个概念似乎总是不被人理解，但是我认为中间件非常棒。

首先，一个好的中间件有一个责任就是可插拔并且自足。这就意味着你可以在接口级别嵌入你的中间件他就能直接运行。它不会影响你编码方式，不是框架，仅仅是你请求处理里面的一层而已。完全没必要重写你的代码，如果你想使用中间件的一个功能，你就帮他插入到那里，如果不想使用了，就可以直接移除。

纵观Go语言，中间件是非常普遍的，即使在标准库中。虽然开始的时候不会那么明显，在标准库net/http中的函数StripText或者TimeoutHandler就是我们要定义和的中间件的样子，处理请求和相应的时候他们包装你的handler，并处理一些额外的步骤。

一开始，我们认为编写中间件似乎很容易，但是我们实际编写的时候也会遇到各种各样的坑。让我们来看看一些例子。

### 1.1.1. 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

```text

package middleware

import (
    "context"
    "math"
    "net/http"
    "strings"
)




const abortIndex int8 = math.MaxInt8 / 2 

type HandlerFunc func(*SliceRouterContext)


type SliceRouter struct {
    groups []*SliceGroup
}


type SliceGroup struct {
    *SliceRouter
    path     string
    handlers []HandlerFunc
}


type SliceRouterContext struct {
    Rw  http.ResponseWriter
    Req *http.Request
    Ctx context.Context
    *SliceGroup
    index int8
}

func newSliceRouterContext(rw http.ResponseWriter, req *http.Request, r *SliceRouter) *SliceRouterContext {
    newSliceGroup := &SliceGroup{}

    
    matchUrlLen := 0
    for _, group := range r.groups {
        
        
        if strings.HasPrefix(req.RequestURI, group.path) {
            pathLen := len(group.path)
            if pathLen > matchUrlLen {
                matchUrlLen = pathLen
                *newSliceGroup = *group 
            }
        }
    }

    c := &SliceRouterContext{Rw: rw, Req: req, SliceGroup: newSliceGroup, Ctx: req.Context()}
    c.Reset()
    return c
}

func (c *SliceRouterContext) Get(key interface{}) interface{} {
    return c.Ctx.Value(key)
}

func (c *SliceRouterContext) Set(key, val interface{}) {
    c.Ctx = context.WithValue(c.Ctx, key, val)
}

type SliceRouterHandler struct {
    coreFunc func(*SliceRouterContext) http.Handler
    router   *SliceRouter
}

func (w *SliceRouterHandler) ServeHTTP(rw http.ResponseWriter, req *http.Request) {
    c := newSliceRouterContext(rw, req, w.router)
    if w.coreFunc != nil {
        c.handlers = append(c.handlers, func(c *SliceRouterContext) {
            w.coreFunc(c).ServeHTTP(rw, req)
        })
    }
    c.Reset()
    c.Next()
}

func NewSliceRouterHandler(coreFunc func(*SliceRouterContext) http.Handler, router *SliceRouter) *SliceRouterHandler {
    return &SliceRouterHandler{
        coreFunc: coreFunc,
        router:   router,
    }
}


func NewSliceRouter() *SliceRouter {
    return &SliceRouter{}
}


func (g *SliceRouter) Group(path string) *SliceGroup {
    return &SliceGroup{
        SliceRouter: g,
        path:        path,
    }
}


func (g *SliceGroup) Use(middlewares ...HandlerFunc) *SliceGroup {
    g.handlers = append(g.handlers, middlewares...)
    existsFlag := false
    for _, oldGroup := range g.SliceRouter.groups {
        if oldGroup == g {
            existsFlag = true
        }
    }
    if !existsFlag {
        g.SliceRouter.groups = append(g.SliceRouter.groups, g)
    }
    return g
}


func (c *SliceRouterContext) Next() {
    c.index++
    for c.index < int8(len(c.handlers)) {
        
        
        c.handlers[c.index](c)
        c.index++
    }
}


func (c *SliceRouterContext) Abort() {
    c.index = abortIndex
}


func (c *SliceRouterContext) IsAborted() bool {
    return c.index >= abortIndex
}


func (c *SliceRouterContext) Reset() {
    c.index = -1
}
```

### 1.1.2. 代码测试 <a id="&#x4EE3;&#x7801;&#x6D4B;&#x8BD5;"></a>

#### 实现两个中间件 <a id="&#x5B9E;&#x73B0;&#x4E24;&#x4E2A;&#x4E2D;&#x95F4;&#x4EF6;"></a>

```text

package middleware

import (
    "log"
)

func TraceLogSliceMW() func(c *SliceRouterContext) {
    return func(c *SliceRouterContext) {
        log.Println("trace_in")
        c.Next()
        log.Println("trace_out")
    }
}
```

```text

package middleware

func Url() func(c *SliceRouterContext) {
    return func(c *SliceRouterContext) {
        if c.Req.RequestURI == "/favicon.ico" {
            c.Abort()
        }
    }
}
```

#### 代码测试 <a id="&#x4EE3;&#x7801;&#x6D4B;&#x8BD5;_1"></a>

```text

package main

import (
    "fmt"
    "net/http"

    "github.com/student/middleware/middleware"
)

func main() {
    
    sliceRouter := middleware.NewSliceRouter()
    
    sliceRouter.Group("/").Use(middleware.Url(), middleware.TraceLogSliceMW(), func(c *middleware.SliceRouterContext) {
        fmt.Println("中间件")
    })
    sliceRouter.Group("/topgoer").Use(middleware.Url(), middleware.TraceLogSliceMW(), topgoer)
    routerHandler := middleware.NewSliceRouterHandler(nil, sliceRouter)
    err := http.ListenAndServe(":9090", routerHandler)
    if err != nil {
        fmt.Println("HTTP server failed,err:", err)
        return
    }
}
func topgoer(c *middleware.SliceRouterContext) {
    fmt.Println("www.topgoer.com是个不错的go语言中文文档")
}
```

