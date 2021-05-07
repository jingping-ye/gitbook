# 中间件练习

## 1. 中间件练习 <a id="&#x4E2D;&#x95F4;&#x4EF6;&#x7EC3;&#x4E60;"></a>

* 定义程序计时中间件，然后定义2个路由，执行函数后应该打印统计的执行时间，如下：

```text
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin"
)


func myTime(c *gin.Context) {
    start := time.Now()
    c.Next()
    
    since := time.Since(start)
    fmt.Println("程序用时：", since)
}

func main() {
    
    
    r := gin.Default()
    
    r.Use(myTime)
    
    shoppingGroup := r.Group("/shopping")
    {
        shoppingGroup.GET("/index", shopIndexHandler)
        shoppingGroup.GET("/home", shopHomeHandler)
    }
    r.Run(":8000")
}

func shopIndexHandler(c *gin.Context) {
    time.Sleep(5 * time.Second)
}

func shopHomeHandler(c *gin.Context) {
    time.Sleep(3 * time.Second)
}
```

效果演示：

