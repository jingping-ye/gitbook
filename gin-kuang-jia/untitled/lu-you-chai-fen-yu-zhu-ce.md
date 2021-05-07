# 路由拆分与注册

## 1. 路由拆分与注册 <a id="&#x8DEF;&#x7531;&#x62C6;&#x5206;&#x4E0E;&#x6CE8;&#x518C;"></a>

### 1.1.1. 基本的路由注册 <a id="&#x57FA;&#x672C;&#x7684;&#x8DEF;&#x7531;&#x6CE8;&#x518C;"></a>

下面最基础的gin路由注册方式，适用于路由条目比较少的简单项目或者项目demo。

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func main() {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 1.1.2. 路由拆分成单独文件或包 <a id="&#x8DEF;&#x7531;&#x62C6;&#x5206;&#x6210;&#x5355;&#x72EC;&#x6587;&#x4EF6;&#x6216;&#x5305;"></a>

当项目的规模增大后就不太适合继续在项目的main.go文件中去实现路由注册相关逻辑了，我们会倾向于把路由部分的代码都拆分出来，形成一个单独的文件或包：

我们在routers.go文件中定义并注册路由信息：

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func setupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```

此时main.go中调用上面定义好的setupRouter函数：

```text
func main() {
    r := setupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

此时的目录结构：

```text
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers.go
```

把路由部分的代码单独拆分成包的话也是可以的，拆分后的目录结构如下：

```text
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```

routers/routers.go需要注意此时setupRouter需要改成首字母大写：

```text
package routers

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com",
    })
}


func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```

main.go文件内容如下：

```text
package main

import (
    "fmt"
    "gin_demo/routers"
)

func main() {
    r := routers.SetupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 1.1.3. 路由拆分成多个文件 <a id="&#x8DEF;&#x7531;&#x62C6;&#x5206;&#x6210;&#x591A;&#x4E2A;&#x6587;&#x4EF6;"></a>

当我们的业务规模继续膨胀，单独的一个routers文件或包已经满足不了我们的需求了，

```text
func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
  r.GET("/xx1", xxHandler1)
  ...
  r.GET("/xx30", xxHandler30)
    return r
}
```

因为我们把所有的路由注册都写在一个SetupRouter函数中的话就会太复杂了。

我们可以分开定义多个路由文件，例如：

```text
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    ├── blog.go
    └── shop.go
```

routers/shop.go中添加一个LoadShop的函数，将shop相关的路由注册到指定的路由器：

```text
func LoadShop(e *gin.Engine)  {
    e.GET("/hello", helloHandler)
  e.GET("/goods", goodsHandler)
  e.GET("/checkout", checkoutHandler)
  ...
}
```

routers/blog.go中添加一个LoadBlog的函数，将blog相关的路由注册到指定的路由器：

```text
func LoadBlog(e *gin.Engine) {
    e.GET("/post", postHandler)
  e.GET("/comment", commentHandler)
  ...
}
```

在main函数中实现最终的注册逻辑如下：

```text
func main() {
    r := gin.Default()
    routers.LoadBlog(r)
    routers.LoadShop(r)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 1.1.4. 路由拆分到不同的APP <a id="&#x8DEF;&#x7531;&#x62C6;&#x5206;&#x5230;&#x4E0D;&#x540C;&#x7684;app"></a>

有时候项目规模实在太大，那么我们就更倾向于把业务拆分的更详细一些，例如把不同的业务代码拆分成不同的APP。

因此我们在项目目录下单独定义一个app目录，用来存放我们不同业务线的代码文件，这样就很容易进行横向扩展。大致目录结构如下：

```text
gin_demo
├── app
│   ├── blog
│   │   ├── handler.go
│   │   └── router.go
│   └── shop
│       ├── handler.go
│       └── router.go
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```

其中app/blog/router.go用来定义post相关路由信息，具体内容如下：

```text
func Routers(e *gin.Engine) {
    e.GET("/post", postHandler)
    e.GET("/comment", commentHandler)
}
```

app/shop/router.go用来定义shop相关路由信息，具体内容如下：

```text
func Routers(e *gin.Engine) {
    e.GET("/goods", goodsHandler)
    e.GET("/checkout", checkoutHandler)
}
```

routers/routers.go中根据需要定义Include函数用来注册子app中定义的路由，Init函数用来进行路由的初始化操作：

```text
type Option func(*gin.Engine)

var options = []Option{}


func Include(opts ...Option) {
    options = append(options, opts...)
}


func Init() *gin.Engine {
    r := gin.New()
    for _, opt := range options {
        opt(r)
    }
    return r
}
```

main.go中按如下方式先注册子app中的路由，然后再进行路由的初始化：

```text
func main() {
    
    routers.Include(shop.Routers, blog.Routers)
    
    r := routers.Init()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

转自：[https://www.liwenzhou.com/posts/Go/gin\_routes\_registry/](https://www.liwenzhou.com/posts/Go/gin_routes_registry/)

