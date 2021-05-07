# JSON Web令牌

## 1. JSON Web令牌 <a id="json-web&#x4EE4;&#x724C;"></a>

如今有很多将身份验证内置到API中的方法 -JSON Web令牌只是其中之一。JSON Web令牌（JWT）作为令牌系统而不是在每次请求时都发送用户名和密码，因此比其他方法（如基本身份验证）具有固有的优势。要了解更多信息，请直接进入jwt.io上的介绍，然后再直接学习。

以下是JWT的实际应用示例。主要有两个部分：提供用户名和密码以获取令牌；并根据请求检查该令牌。

在此示例中，我们使用了两个库，即Go中的JWT实现以及将其用作中间件的方式。

最后，在使用此代码之前，您需要将APP\_KEY常量更改为机密（理想情况下，该常量将存储在代码库外部），并改进用户名/密码检查中的内容，TokenHandler以检查不仅仅是myusername/ mypassword组合。

```text
package main

import (
    "io"
    "log"
    "net/http"
    "time"

    jwtmiddleware "github.com/auth0/go-jwt-middleware"
    "github.com/dgrijalva/jwt-go"
)

const (
    APP_KEY = "www.topgoer.com"
)

func main() {

    
    
    
    http.HandleFunc("/token", TokenHandler)
    http.Handle("/", AuthMiddleware(http.HandlerFunc(ExampleHandler2)))

    
    if err := http.ListenAndServe(":8080", nil); err != nil {
        log.Fatal(err)
    }
}


func TokenHandler(w http.ResponseWriter, r *http.Request) {

    w.Header().Add("Content-Type", "application/json")
    r.ParseForm()

    
    username := r.Form.Get("username")
    password := r.Form.Get("password")
    if username != "myusername" || password != "mypassword" {
        w.WriteHeader(http.StatusUnauthorized)
        io.WriteString(w, `{"error":"invalid_credentials"}`)
        return
    }

    
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
        "user": username,
        "exp":  time.Now().Add(time.Hour * time.Duration(1)).Unix(),
        "iat":  time.Now().Unix(),
    })
    tokenString, err := token.SignedString([]byte(APP_KEY))
    if err != nil {
        w.WriteHeader(http.StatusInternalServerError)
        io.WriteString(w, `{"error":"token_generation_failed"}`)
        return
    }
    io.WriteString(w, `{"token":"`+tokenString+`"}`)
    return
}


func AuthMiddleware(next http.Handler) http.Handler {
    if len(APP_KEY) == 0 {
        log.Fatal("HTTP server unable to start, expected an APP_KEY for JWT auth")
    }
    jwtMiddleware := jwtmiddleware.New(jwtmiddleware.Options{
        ValidationKeyGetter: func(token *jwt.Token) (interface{}, error) {
            return []byte(APP_KEY), nil
        },
        SigningMethod: jwt.SigningMethodHS256,
    })
    return jwtMiddleware.Handler(next)
}

func ExampleHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Add("Content-Type", "application/json")
    io.WriteString(w, `{"status":"ok"}`)
}
func ExampleHandler2(w http.ResponseWriter, r *http.Request) {
    w.Header().Add("Content-Type", "application/json")
    io.WriteString(w, `{"status":"ok22222"}`)
}
```

我们在上面的示例流程中显示，首先获取一个令牌，然后在调用端点时使用该令牌。这些是我们使用的命令：

```text
    curl -H "Content-Type: application/x-www-form-urlencoded" \
         -d "username=myusername&password=mypassword" \
         http://localhost:8080/token
```

```text
    curl -H "Authorization: Bearer {{ TOKEN }}" \
         -H "Content-Type: application/json" \
         http://localhost:8080
```

gin框架封装jwt链接地址：[http://www.topgoer.com/gin框架/其他/生成解析token.html](http://www.topgoer.com/gin%E6%A1%86%E6%9E%B6/%E5%85%B6%E4%BB%96/%E7%94%9F%E6%88%90%E8%A7%A3%E6%9E%90token.html)

