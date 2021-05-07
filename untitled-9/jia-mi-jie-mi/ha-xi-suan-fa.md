# 哈希算法

## 1. 哈希算法 <a id="&#x54C8;&#x5E0C;&#x7B97;&#x6CD5;"></a>

### 1.1.1. 使用场景 <a id="&#x4F7F;&#x7528;&#x573A;&#x666F;"></a>

* 对用户输入的密码进行加密
* 用户登录时对用户的密码进行比对

### 1.1.2. 例子 <a id="&#x4F8B;&#x5B50;"></a>

```text
package main

import (
    "errors"
    "fmt"

    "golang.org/x/crypto/bcrypt"
)

func main() {
    userPassword := "123456"
    passwordbyte, err := GeneratePassword(userPassword)
    if err != nil {
        fmt.Println("加密出错了")
    }
    fmt.Println(passwordbyte)
    
    
    
    mysql_password := "$2a$10$I8WaWXgiBw8j/IBejb3t/.s5NoOYLvoQzL6mIM2g3TEu4z0HenzqK"
    isOk, _ := ValidatePassword(userPassword, mysql_password)
    if !isOk {
        fmt.Println("密码错误")
        return
    }
    fmt.Println(isOk)
}


func GeneratePassword(userPassword string) ([]byte, error) {
    return bcrypt.GenerateFromPassword([]byte(userPassword), bcrypt.DefaultCost)
}


func ValidatePassword(userPassword string, hashed string) (isOK bool, err error) {
    if err = bcrypt.CompareHashAndPassword([]byte(hashed), []byte(userPassword)); err != nil {
        return false, errors.New("密码比对错误！")
    }
    return true, nil

}
```

**注意**

`golang.org/x/crypto/bcrypt`这个包下载有些难度，需要的小伙伴可以自行百度

官网地址：[http://golang.org/x/crypto/bcrypt](http://golang.org/x/crypto/bcrypt)

