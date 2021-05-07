# 状态模式-地鼠文档

状态模式用于分离状态和行为。

## state.go <a id="a443a"></a>

```text
package state

import "fmt"

type Week interface {
    Today()
    Next(*DayContext)
}

type DayContext struct {
    today Week
}

func NewDayContext() *DayContext {
    return &DayContext{
        today: &Sunday{},
    }
}

func (d *DayContext) Today() {
    d.today.Today()
}

func (d *DayContext) Next() {
    d.today.Next(d)
}

type Sunday struct{}

func (*Sunday) Today() {
    fmt.Printf("Sunday\n")
}

func (*Sunday) Next(ctx *DayContext) {
    ctx.today = &Monday{}
}

type Monday struct{}

func (*Monday) Today() {
    fmt.Printf("Monday\n")
}

func (*Monday) Next(ctx *DayContext) {
    ctx.today = &Tuesday{}
}

type Tuesday struct{}

func (*Tuesday) Today() {
    fmt.Printf("Tuesday\n")
}

func (*Tuesday) Next(ctx *DayContext) {
    ctx.today = &Wednesday{}
}

type Wednesday struct{}

func (*Wednesday) Today() {
    fmt.Printf("Wednesday\n")
}

func (*Wednesday) Next(ctx *DayContext) {
    ctx.today = &Thursday{}
}

type Thursday struct{}

func (*Thursday) Today() {
    fmt.Printf("Thursday\n")
}

func (*Thursday) Next(ctx *DayContext) {
    ctx.today = &Friday{}
}

type Friday struct{}

func (*Friday) Today() {
    fmt.Printf("Friday\n")
}

func (*Friday) Next(ctx *DayContext) {
    ctx.today = &Saturday{}
}

type Saturday struct{}

func (*Saturday) Today() {
    fmt.Printf("Saturday\n")
}

func (*Saturday) Next(ctx *DayContext) {
    ctx.today = &Sunday{}
}
```

## state\_test.go <a id="boy50l"></a>

```text
package state

func ExampleWeek() {
    ctx := NewDayContext()
    todayAndNext := func() {
        ctx.Today()
        ctx.Next()
    }

    for i := 0; i < 8; i++ {
        todayAndNext()
    }
    
    
    
    
    
    
    
    
    
}
```

文档更新时间: 2020-08-24 11:46   作者：kuteng

