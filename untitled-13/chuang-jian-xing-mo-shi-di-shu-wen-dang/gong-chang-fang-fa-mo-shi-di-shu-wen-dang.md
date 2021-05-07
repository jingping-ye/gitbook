# 工厂方法模式-地鼠文档

工厂方法模式使用子类的方式延迟生成对象到子类中实现。

Go中不存在继承 所以使用匿名组合来实现

## factorymethod.go <a id="5b6y4e"></a>

```text
package factorymethod


type Operator interface {
    SetA(int)
    SetB(int)
    Result() int
}


type OperatorFactory interface {
    Create() Operator
}


type OperatorBase struct {
    a, b int
}


func (o *OperatorBase) SetA(a int) {
    o.a = a
}


func (o *OperatorBase) SetB(b int) {
    o.b = b
}


type PlusOperatorFactory struct{}

func (PlusOperatorFactory) Create() Operator {
    return &PlusOperator{
        OperatorBase: &OperatorBase{},
    }
}


type PlusOperator struct {
    *OperatorBase
}


func (o PlusOperator) Result() int {
    return o.a + o.b
}


type MinusOperatorFactory struct{}

func (MinusOperatorFactory) Create() Operator {
    return &MinusOperator{
        OperatorBase: &OperatorBase{},
    }
}


type MinusOperator struct {
    *OperatorBase
}


func (o MinusOperator) Result() int {
    return o.a - o.b
}
```

## factorymethod\_test.go <a id="cgn7ky"></a>

```text
package factorymethod

import "testing"

func compute(factory OperatorFactory, a, b int) int {
    op := factory.Create()
    op.SetA(a)
    op.SetB(b)
    return op.Result()
}

func TestOperator(t *testing.T) {
    var (
        factory OperatorFactory
    )

    factory = PlusOperatorFactory{}
    if compute(factory, 1, 2) != 3 {
        t.Fatal("error with factory method pattern")
    }

    factory = MinusOperatorFactory{}
    if compute(factory, 4, 2) != 2 {
        t.Fatal("error with factory method pattern")
    }
}
```

文档更新时间: 2020-08-24 10:37   作者：kuteng

