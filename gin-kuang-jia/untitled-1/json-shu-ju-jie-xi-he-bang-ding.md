# Json 数据解析和绑定

## 1. Json 数据解析和绑定 <a id="json-&#x6570;&#x636E;&#x89E3;&#x6790;&#x548C;&#x7ED1;&#x5B9A;"></a>

* 客户端传参，后端接收并解析到结构体

```text
package main

import (
   "github.com/gin-gonic/gin"
   "net/http"
)


type Login struct {
   
   User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
   Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`
}

func main() {
   
   
   r := gin.Default()
   
   r.POST("loginJSON", func(c *gin.Context) {
      
      var json Login
      
      if err := c.ShouldBindJSON(&json); err != nil {
         
         
         c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
         return
      }
      
      if json.User != "root" || json.Pssword != "admin" {
         c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
         return
      }
      c.JSON(http.StatusOK, gin.H{"status": "200"})
   })
   r.Run(":8000")
}
```

效果演示：

