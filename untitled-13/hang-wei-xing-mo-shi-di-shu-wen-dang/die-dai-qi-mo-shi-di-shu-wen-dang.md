# 迭代器模式-地鼠文档

送代器模式用于使用相同方式送代不同类型集合或者隐藏集合类型的具体实现。

可以使用送代器模式使遍历同时应用送代策略，如请求新对象、过滤、处理对象等。

## iterator.go <a id="2u41dw"></a>

```text
package iterator

import "fmt"

type Aggregate interface {
    Iterator() Iterator
}

type Iterator interface {
    First()
    IsDone() bool
    Next() interface{}
}

type Numbers struct {
    start, end int
}

func NewNumbers(start, end int) *Numbers {
    return &Numbers{
        start: start,
        end:   end,
    }
}

func (n *Numbers) Iterator() Iterator {
    return &NumbersIterator{
        numbers: n,
        next:    n.start,
    }
}

type NumbersIterator struct {
    numbers *Numbers
    next    int
}

func (i *NumbersIterator) First() {
    i.next = i.numbers.start
}

func (i *NumbersIterator) IsDone() bool {
    return i.next > i.numbers.end
}

func (i *NumbersIterator) Next() interface{} {
    if !i.IsDone() {
        next := i.next
        i.next++
        return next
    }
    return nil
}

func IteratorPrint(i Iterator) {
    for i.First(); !i.IsDone(); {
        c := i.Next()
        fmt.Printf("%#v\n", c)
    }
}
```

## iterator\_test.go <a id="9hyd6u"></a>

```text
package iterator

func ExampleIterator() {
    var aggregate Aggregate
    aggregate = NewNumbers(1, 10)

    IteratorPrint(aggregate.Iterator())
    
    
    
    
    
    
    
    
    
    
    
}
```

文档更新时间: 2020-08-24 11:41   作者：kuteng

