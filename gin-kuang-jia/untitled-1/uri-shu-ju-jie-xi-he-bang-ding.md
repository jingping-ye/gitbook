# URI数据解析和绑定

## 1. URI数据解析和绑定 <a id="uri&#x6570;&#x636E;&#x89E3;&#x6790;&#x548C;&#x7ED1;&#x5B9A;"></a>

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)


type Login struct {
    
    User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
    Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`
}

func main() {
    
    
    r := gin.Default()
    
    r.GET("/:user/:password", func(c *gin.Context) {
        
        var login Login
        
        
        if err := c.ShouldBindUri(&login); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        
        if login.User != "root" || login.Pssword != "admin" {
            c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
            return
        }
        c.JSON(http.StatusOK, gin.H{"status": "200"})
    })
    r.Run(":8000")
}
```

效果演示：

