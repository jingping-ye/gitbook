# 简单工厂模式-地鼠文档

go 语言没有构造函数一说，所以一般会定义NewXXX函数来初始化相关类。  
NewXXX 函数返回接口时就是简单工厂模式，也就是说Golang的一般推荐做法就是简单工厂。

在这个simplefactory包中只有API 接口和NewAPI函数为包外可见，封装了实现细节。

## simple.go代码 <a id="g4u84h"></a>

```text
package simplefactory

import "fmt"


type API interface {
    Say(name string) string
}


func NewAPI(t int) API {
    if t == 1 {
        return &hiAPI{}
    } else if t == 2 {
        return &helloAPI{}
    }
    return nil
}


type hiAPI struct{}


func (*hiAPI) Say(name string) string {
    return fmt.Sprintf("Hi, %s", name)
}


type helloAPI struct{}


func (*helloAPI) Say(name string) string {
    return fmt.Sprintf("Hello, %s", name)
}
```

## simple\_test.go代码 <a id="1u7dbo"></a>

```text
package simplefactory

import "testing"


func TestType1(t *testing.T) {
    api := NewAPI(1)
    s := api.Say("Tom")
    if s != "Hi, Tom" {
        t.Fatal("Type1 test fail")
    }
}

func TestType2(t *testing.T) {
    api := NewAPI(2)
    s := api.Say("Tom")
    if s != "Hello, Tom" {
        t.Fatal("Type2 test fail")
    }
}
```

文档更新时间: 2020-08-24 10:37   作者：kuteng

