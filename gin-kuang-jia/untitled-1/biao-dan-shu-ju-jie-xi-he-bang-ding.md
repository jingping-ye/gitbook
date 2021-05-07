# 表单数据解析和绑定

## 1. 表单数据解析和绑定 <a id="&#x8868;&#x5355;&#x6570;&#x636E;&#x89E3;&#x6790;&#x548C;&#x7ED1;&#x5B9A;"></a>

```text

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Documenttitle>
head>
<body>
    <form action="http://localhost:8000/loginForm" method="post" enctype="application/x-www-form-urlencoded">
        用户名<input type="text" name="username"><br>
        密码<input type="password" name="password">
        <input type="submit" value="提交">
    form>
body>
html>
```

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
    
    r.POST("/loginForm", func(c *gin.Context) {
        
        var form Login
        
        
        if err := c.Bind(&form); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        
        if form.User != "root" || form.Pssword != "admin" {
            c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
            return
        }
        c.JSON(http.StatusOK, gin.H{"status": "200"})
    })
    r.Run(":8000")
}
```

效果展示：

