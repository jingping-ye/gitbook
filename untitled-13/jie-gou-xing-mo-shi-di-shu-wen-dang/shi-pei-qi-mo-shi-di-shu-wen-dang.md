# 适配器模式-地鼠文档

适配器模式用于转换一种接口适配另一种接口。

实际使用中Adaptee一般为接口，并且使用工厂函数生成实例。

在Adapter中匿名组合Adaptee接口，所以Adapter类也拥有SpecificRequest实例方法，又因为Go语言中非入侵式接口特征，其实Adapter也适配Adaptee接口。

## adapter.go <a id="6e470o"></a>

```text
package adapter


type Target interface {
    Request() string
}


type Adaptee interface {
    SpecificRequest() string
}


func NewAdaptee() Adaptee {
    return &adapteeImpl{}
}


type adapteeImpl struct{}


func (*adapteeImpl) SpecificRequest() string {
    return "adaptee method"
}


func NewAdapter(adaptee Adaptee) Target {
    return &adapter{
        Adaptee: adaptee,
    }
}


type adapter struct {
    Adaptee
}


func (a *adapter) Request() string {
    return a.SpecificRequest()
}
```

## adapter\_test.go <a id="b9pnf7"></a>

```text
package adapter

import "testing"

var expect = "adaptee method"

func TestAdapter(t *testing.T) {
    adaptee := NewAdaptee()
    target := NewAdapter(adaptee)
    res := target.Request()
    if res != expect {
        t.Fatalf("expect: %s, actual: %s", expect, res)
    }
}
```

文档更新时间: 2020-08-24 11:15   作者：kuteng

