# 策略模式-地鼠文档

定义一系列算法，让这些算法在运行时可以互换，使得分离算法，符合开闭原则。

## strategy.go <a id="175ajf"></a>

```text
package strategy

import "fmt"

type Payment struct {
    context  *PaymentContext
    strategy PaymentStrategy
}

type PaymentContext struct {
    Name, CardID string
    Money        int
}

func NewPayment(name, cardid string, money int, strategy PaymentStrategy) *Payment {
    return &Payment{
        context: &PaymentContext{
            Name:   name,
            CardID: cardid,
            Money:  money,
        },
        strategy: strategy,
    }
}

func (p *Payment) Pay() {
    p.strategy.Pay(p.context)
}

type PaymentStrategy interface {
    Pay(*PaymentContext)
}

type Cash struct{}

func (*Cash) Pay(ctx *PaymentContext) {
    fmt.Printf("Pay $%d to %s by cash", ctx.Money, ctx.Name)
}

type Bank struct{}

func (*Bank) Pay(ctx *PaymentContext) {
    fmt.Printf("Pay $%d to %s by bank account %s", ctx.Money, ctx.Name, ctx.CardID)

}
```

## strategy\_test.go <a id="4he7tb"></a>

```text
package strategy

func ExamplePayByCash() {
    payment := NewPayment("Ada", "", 123, &Cash{})
    payment.Pay()
    // Output:
    // Pay $123 to Ada by cash
}

func ExamplePayByBank() {
    payment := NewPayment("Bob", "0002", 888, &Bank{})
    payment.Pay()
    // Output:
    // Pay $888 to Bob by bank account 0002
}
```

文档更新时间: 2020-08-24 11:45   作者：kuteng

