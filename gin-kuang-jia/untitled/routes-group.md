# routes group

## 1. routes group <a id="routes-group"></a>

* routes group是为了管理一些相同的URL

```text
package main

import (
   "github.com/gin-gonic/gin"
   "fmt"
)



func main() {
   
   
   r := gin.Default()
   
   v1 := r.Group("/v1")
   
   {
      v1.GET("/login", login)
      v1.GET("submit", submit)
   }
   v2 := r.Group("/v2")
   {
      v2.POST("/login", login)
      v2.POST("/submit", submit)
   }
   r.Run(":8000")
}

func login(c *gin.Context) {
   name := c.DefaultQuery("name", "jack")
   c.String(200, fmt.Sprintf("hello %s\n", name))
}

func submit(c *gin.Context) {
   name := c.DefaultQuery("name", "lily")
   c.String(200, fmt.Sprintf("hello %s\n", name))
}
```

效果演示:

