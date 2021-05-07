# Cookie练习

## 1. Cookie练习 <a id="cookie&#x7EC3;&#x4E60;"></a>

* 模拟实现权限验证中间件
  * 有2个路由，login和home
  * login用于设置cookie
  * home是访问查看信息的请求
  * 在请求home之前，先跑中间件代码，检验是否存在cookie
* 访问home，会显示错误，因为权限校验未通过
* 然后访问登录的请求，登录并设置cookie
* 再次访问home，访问成功

```text
package main

import (
   "github.com/gin-gonic/gin"
   "net/http"
)

func AuthMiddleWare() gin.HandlerFunc {
   return func(c *gin.Context) {
      
      if cookie, err := c.Cookie("abc"); err == nil {
         if cookie == "123" {
            c.Next()
            return
         }
      }
      
      c.JSON(http.StatusUnauthorized, gin.H{"error": "err"})
      
      c.Abort()
      return
   }
}

func main() {
   
   r := gin.Default()
   r.GET("/login", func(c *gin.Context) {
      
      c.SetCookie("abc", "123", 60, "/",
         "localhost", false, true)
      
      c.String(200, "Login success!")
   })
   r.GET("/home", AuthMiddleWare(), func(c *gin.Context) {
      c.JSON(200, gin.H{"data": "home"})
   })
   r.Run(":8000")
}
```

访问 /home 和 /login进行测试

