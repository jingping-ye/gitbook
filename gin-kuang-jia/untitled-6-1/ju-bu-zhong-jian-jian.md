# 局部中间件

## 1. 局部中间件 <a id="&#x5C40;&#x90E8;&#x4E2D;&#x95F4;&#x4EF6;"></a>

```text
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin"
)


func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        t := time.Now()
        fmt.Println("中间件开始执行了")
        
        c.Set("request", "中间件")
        
        c.Next()
        
        status := c.Writer.Status()
        fmt.Println("中间件执行完毕", status)
        t2 := time.Since(t)
        fmt.Println("time:", t2)
    }
}

func main() {
    
    
    r := gin.Default()
    
    r.GET("/ce", MiddleWare(), func(c *gin.Context) {
        
        req, _ := c.Get("request")
        fmt.Println("request:", req)
        
        c.JSON(200, gin.H{"request": req})
    })
    r.Run()
}
```

效果演示：

