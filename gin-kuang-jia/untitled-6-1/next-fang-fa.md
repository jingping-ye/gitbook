# Next\(\)方法

## 1. Next\(\)方法 <a id="next&#x65B9;&#x6CD5;"></a>

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

