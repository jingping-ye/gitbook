# 生成解析token

## 1. 生成解析token <a id="&#x751F;&#x6210;&#x89E3;&#x6790;token"></a>

如今有很多将身份验证内置到API中的方法 -JSON Web令牌只是其中之一。JSON Web令牌（JWT）作为令牌系统而不是在每次请求时都发送用户名和密码，因此比其他方法（如基本身份验证）具有固有的优势。要了解更多信息，请直接进入jwt.io上的介绍，然后再直接学习。

以下是JWT的实际应用示例。主要有两个部分：提供用户名和密码以获取令牌；并根据请求检查该令牌。

在此示例中，我们使用了两个库，即Go中的JWT实现以及将其用作中间件的方式。

最后，在使用此代码之前，您需要将APP\_KEY常量更改为机密（理想情况下，该常量将存储在代码库外部），并改进用户名/密码检查中的内容，TokenHandler以检查不仅仅是myusername/ mypassword组合。

下面的代码是gin框架对jwt的封装

```text
package main

import (
    "fmt"
    "net/http"
    "time"

    "github.com/dgrijalva/jwt-go"
    "github.com/gin-gonic/gin"
)


var jwtkey = []byte("www.topgoer.com")
var str string

type Claims struct {
    UserId uint
    jwt.StandardClaims
}

func main() {
    r := gin.Default()
    r.GET("/set", setting)
    r.GET("/get", getting)
    
    r.Run(":8080")
}


func setting(ctx *gin.Context) {
    expireTime := time.Now().Add(7 * 24 * time.Hour)
    claims := &Claims{
        UserId: 2,
        StandardClaims: jwt.StandardClaims{
            ExpiresAt: expireTime.Unix(), 
            IssuedAt:  time.Now().Unix(),
            Issuer:    "127.0.0.1",  
            Subject:   "user token", 
        },
    }
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
    
    tokenString, err := token.SignedString(jwtkey)
    if err != nil {
        fmt.Println(err)
    }
    str = tokenString
    ctx.JSON(200, gin.H{"token": tokenString})
}


func getting(ctx *gin.Context) {
    tokenString := ctx.GetHeader("Authorization")
    
    if tokenString == "" {
        ctx.JSON(http.StatusUnauthorized, gin.H{"code": 401, "msg": "权限不足"})
        ctx.Abort()
        return
    }

    token, claims, err := ParseToken(tokenString)
    if err != nil || !token.Valid {
        ctx.JSON(http.StatusUnauthorized, gin.H{"code": 401, "msg": "权限不足"})
        ctx.Abort()
        return
    }
    fmt.Println(111)
    fmt.Println(claims.UserId)
}

func ParseToken(tokenString string) (*jwt.Token, *Claims, error) {
    Claims := &Claims{}
    token, err := jwt.ParseWithClaims(tokenString, Claims, func(token *jwt.Token) (i interface{}, err error) {
        return jwtkey, nil
    })
    return token, Claims, err
}
```

