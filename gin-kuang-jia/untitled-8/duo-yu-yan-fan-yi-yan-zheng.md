# 多语言翻译验证

## 1. 多语言翻译验证 <a id="&#x591A;&#x8BED;&#x8A00;&#x7FFB;&#x8BD1;&#x9A8C;&#x8BC1;"></a>

当业务系统对验证信息有特殊需求时，例如：返回信息需要自定义，手机端返回的信息需要是中文而pc端发挥返回的信息需要时英文，如何做到请求一个接口满足上述三种情况。

```text
package main

import (
    "fmt"

    "github.com/gin-gonic/gin"
    "github.com/go-playground/locales/en"
    "github.com/go-playground/locales/zh"
    "github.com/go-playground/locales/zh_Hant_TW"
    ut "github.com/go-playground/universal-translator"
    "gopkg.in/go-playground/validator.v9"
    en_translations "gopkg.in/go-playground/validator.v9/translations/en"
    zh_translations "gopkg.in/go-playground/validator.v9/translations/zh"
    zh_tw_translations "gopkg.in/go-playground/validator.v9/translations/zh_tw"
)

var (
    Uni      *ut.UniversalTranslator
    Validate *validator.Validate
)

type User struct {
    Username string `form:"user_name" validate:"required"`
    Tagline  string `form:"tag_line" validate:"required,lt=10"`
    Tagline2 string `form:"tag_line2" validate:"required,gt=1"`
}

func main() {
    en := en.New()
    zh := zh.New()
    zh_tw := zh_Hant_TW.New()
    Uni = ut.New(en, zh, zh_tw)
    Validate = validator.New()

    route := gin.Default()
    route.GET("/5lmh", startPage)
    route.POST("/5lmh", startPage)
    route.Run(":8080")
}

func startPage(c *gin.Context) {
    
    locale := c.DefaultQuery("locale", "zh")
    trans, _ := Uni.GetTranslator(locale)
    switch locale {
    case "zh":
        zh_translations.RegisterDefaultTranslations(Validate, trans)
        break
    case "en":
        en_translations.RegisterDefaultTranslations(Validate, trans)
        break
    case "zh_tw":
        zh_tw_translations.RegisterDefaultTranslations(Validate, trans)
        break
    default:
        zh_translations.RegisterDefaultTranslations(Validate, trans)
        break
    }

    
    Validate.RegisterTranslation("required", trans, func(ut ut.Translator) error {
        return ut.Add("required", "{0} must have a value!", true) 
    }, func(ut ut.Translator, fe validator.FieldError) string {
        t, _ := ut.T("required", fe.Field())
        return t
    })

    
    user := User{}
    c.ShouldBind(&user)
    fmt.Println(user)
    err := Validate.Struct(user)
    if err != nil {
        errs := err.(validator.ValidationErrors)
        sliceErrs := []string{}
        for _, e := range errs {
            sliceErrs = append(sliceErrs, e.Translate(trans))
        }
        c.String(200, fmt.Sprintf("%#v", sliceErrs))
    }
    c.String(200, fmt.Sprintf("%#v", "user"))
}
```

正确的链接：[http://localhost:8080/testing?user\_name=枯藤&tag\_line=9&tag\_line2=33&locale=zh](http://localhost:8080/testing?user_name=%E6%9E%AF%E8%97%A4&tag_line=9&tag_line2=33&locale=zh)

[http://localhost:8080/testing?user\_name=枯藤&tag\_line=9&tag\_line2=3&locale=en](http://localhost:8080/testing?user_name=%E6%9E%AF%E8%97%A4&tag_line=9&tag_line2=3&locale=en) 返回英文的验证信息

[http://localhost:8080/testing?user\_name=枯藤&tag\_line=9&tag\_line2=3&locale=zh](http://localhost:8080/testing?user_name=%E6%9E%AF%E8%97%A4&tag_line=9&tag_line2=3&locale=zh) 返回中文的验证信息

查看更多的功能可以查看官网 gopkg.in/go-playground/validator.v9

