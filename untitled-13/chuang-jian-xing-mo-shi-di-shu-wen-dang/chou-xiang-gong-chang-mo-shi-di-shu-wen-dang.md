# 抽象工厂模式-地鼠文档

抽象工厂模式用于生成产品族的工厂，所生成的对象是有关联的。

如果抽象工厂退化成生成的对象无关联则成为工厂函数模式。

比如本例子中使用RDB和XML存储订单信息，抽象工厂分别能生成相关的主订单信息和订单详情信息。  
如果业务逻辑中需要替换使用的时候只需要改动工厂函数相关的类就能替换使用不同的存储方式了。

## abstractfactory.go <a id="ezrdg3"></a>

```text
package abstractfactory

import "fmt"


type OrderMainDAO interface {
    SaveOrderMain()
}


type OrderDetailDAO interface {
    SaveOrderDetail()
}


type DAOFactory interface {
    CreateOrderMainDAO() OrderMainDAO
    CreateOrderDetailDAO() OrderDetailDAO
}


type RDBMainDAO struct{}


func (*RDBMainDAO) SaveOrderMain() {
    fmt.Print("rdb main save\n")
}


type RDBDetailDAO struct{}


func (*RDBDetailDAO) SaveOrderDetail() {
    fmt.Print("rdb detail save\n")
}


type RDBDAOFactory struct{}

func (*RDBDAOFactory) CreateOrderMainDAO() OrderMainDAO {
    return &RDBMainDAO{}
}

func (*RDBDAOFactory) CreateOrderDetailDAO() OrderDetailDAO {
    return &RDBDetailDAO{}
}


type XMLMainDAO struct{}


func (*XMLMainDAO) SaveOrderMain() {
    fmt.Print("xml main save\n")
}


type XMLDetailDAO struct{}


func (*XMLDetailDAO) SaveOrderDetail() {
    fmt.Print("xml detail save")
}


type XMLDAOFactory struct{}

func (*XMLDAOFactory) CreateOrderMainDAO() OrderMainDAO {
    return &XMLMainDAO{}
}

func (*XMLDAOFactory) CreateOrderDetailDAO() OrderDetailDAO {
    return &XMLDetailDAO{}
}
```

## abstractfactory\_test.go <a id="g9u62f"></a>

```text
package abstractfactory

func getMainAndDetail(factory DAOFactory) {
    factory.CreateOrderMainDAO().SaveOrderMain()
    factory.CreateOrderDetailDAO().SaveOrderDetail()
}

func ExampleRdbFactory() {
    var factory DAOFactory
    factory = &RDBDAOFactory{}
    getMainAndDetail(factory)
    
    
    
}

func ExampleXmlFactory() {
    var factory DAOFactory
    factory = &XMLDAOFactory{}
    getMainAndDetail(factory)
    
    
    
}
```

文档更新时间: 2020-08-24 10:37   作者：kuteng

