# 基本路由

* [**1.** 基本路由](ji-ben-lu-you.md#基本路由)

## 1. 基本路由 <a id="&#x57FA;&#x672C;&#x8DEF;&#x7531;"></a>

* gin 框架中采用的路由库是基于httprouter做的
* 地址为：[https://github.com/julienschmidt/httprouter](https://github.com/julienschmidt/httprouter)

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.String(http.StatusOK, "hello word")
    })
    r.POST("/xxxpost",getting)
    r.PUT("/xxxput")
    //监听端口默认为8080
    r.Run(":8000")
}
```

