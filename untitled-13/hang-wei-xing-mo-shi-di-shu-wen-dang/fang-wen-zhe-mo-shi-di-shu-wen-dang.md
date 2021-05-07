# 访问者模式-地鼠文档

访问者模式可以给一系列对象透明的添加功能，并且把相关代码封装到一个类中。

对象只要预留访问者接口`Accept`则后期为对象添加功能的时候就不需要改动对象。

## visitor.go <a id="g3cegs"></a>

```text
package visitor

import "fmt"

type Customer interface {
    Accept(Visitor)
}

type Visitor interface {
    Visit(Customer)
}

type EnterpriseCustomer struct {
    name string
}

type CustomerCol struct {
    customers []Customer
}

func (c *CustomerCol) Add(customer Customer) {
    c.customers = append(c.customers, customer)
}

func (c *CustomerCol) Accept(visitor Visitor) {
    for _, customer := range c.customers {
        customer.Accept(visitor)
    }
}

func NewEnterpriseCustomer(name string) *EnterpriseCustomer {
    return &EnterpriseCustomer{
        name: name,
    }
}

func (c *EnterpriseCustomer) Accept(visitor Visitor) {
    visitor.Visit(c)
}

type IndividualCustomer struct {
    name string
}

func NewIndividualCustomer(name string) *IndividualCustomer {
    return &IndividualCustomer{
        name: name,
    }
}

func (c *IndividualCustomer) Accept(visitor Visitor) {
    visitor.Visit(c)
}

type ServiceRequestVisitor struct{}

func (*ServiceRequestVisitor) Visit(customer Customer) {
    switch c := customer.(type) {
    case *EnterpriseCustomer:
        fmt.Printf("serving enterprise customer %s\n", c.name)
    case *IndividualCustomer:
        fmt.Printf("serving individual customer %s\n", c.name)
    }
}


type AnalysisVisitor struct{}

func (*AnalysisVisitor) Visit(customer Customer) {
    switch c := customer.(type) {
    case *EnterpriseCustomer:
        fmt.Printf("analysis enterprise customer %s\n", c.name)
    }
}
```

## visitor\_test.go <a id="4oo5jl"></a>

```text
package visitor

func ExampleRequestVisitor() {
    c := &CustomerCol{}
    c.Add(NewEnterpriseCustomer("A company"))
    c.Add(NewEnterpriseCustomer("B company"))
    c.Add(NewIndividualCustomer("bob"))
    c.Accept(&ServiceRequestVisitor{})
    
    
    
    
}

func ExampleAnalysis() {
    c := &CustomerCol{}
    c.Add(NewEnterpriseCustomer("A company"))
    c.Add(NewIndividualCustomer("bob"))
    c.Add(NewEnterpriseCustomer("B company"))
    c.Accept(&AnalysisVisitor{})
    
    
    
}
```

文档更新时间: 2020-08-24 11:53   作者：kuteng

