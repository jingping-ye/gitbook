# 全局中间件

## 1. 全局中间件 <a id="&#x5168;&#x5C40;&#x4E2D;&#x95F4;&#x4EF6;"></a>

* 所有请求都经过此中间件

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
        status := c.Writer.Status()
        fmt.Println("中间件执行完毕", status)
        t2 := time.Since(t)
        fmt.Println("time:", t2)
    }
}

func main() {
    
    
    r := gin.Default()
    
    r.Use(MiddleWare())
    
    {
        r.GET("/ce", func(c *gin.Context) {
            
            req, _ := c.Get("request")
            fmt.Println("request:", req)
            
            c.JSON(200, gin.H{"request": req})
        })

    }
    r.Run()
}
```

输出结果：

注意: 请注意黑色的数据里面有一步算时间差没有执行\(需要学习Next就懂了\)

