# 外观模式-地鼠文档

API 为facade 模块的外观接口，大部分代码使用此接口简化对facade类的访问。

facade模块同时暴露了a和b 两个Module 的NewXXX和interface，其它代码如果需要使用细节功能时可以直接调用。

## facade.go <a id="oxf94"></a>

```text
package facade

import "fmt"

func NewAPI() API {
    return &apiImpl{
        a: NewAModuleAPI(),
        b: NewBModuleAPI(),
    }
}


type API interface {
    Test() string
}


type apiImpl struct {
    a AModuleAPI
    b BModuleAPI
}

func (a *apiImpl) Test() string {
    aRet := a.a.TestA()
    bRet := a.b.TestB()
    return fmt.Sprintf("%s\n%s", aRet, bRet)
}


func NewAModuleAPI() AModuleAPI {
    return &aModuleImpl{}
}


type AModuleAPI interface {
    TestA() string
}

type aModuleImpl struct{}

func (*aModuleImpl) TestA() string {
    return "A module running"
}


func NewBModuleAPI() BModuleAPI {
    return &bModuleImpl{}
}


type BModuleAPI interface {
    TestB() string
}

type bModuleImpl struct{}

func (*bModuleImpl) TestB() string {
    return "B module running"
}
```

## facade\_test.go <a id="7hha3k"></a>

```text
package facade

import "testing"

var expect = "A module running\nB module running"


func TestFacadeAPI(t *testing.T) {
    api := NewAPI()
    ret := api.Test()
    if ret != expect {
        t.Fatalf("expect %s, return %s", expect, ret)
    }
}
```

文档更新时间: 2020-08-24 11:12   作者：kuteng

