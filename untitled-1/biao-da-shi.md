# 表达式

## 1. 表达式 <a id="&#x8868;&#x8FBE;&#x5F0F;"></a>

Golang 表达式 ：根据调用者不同，方法分为两种表现形式:

```text
    instance.method(args...) ---> <type>.func(instance, args...)
```

前者称为 method value，后者 method expression。

两者都可像普通函数那样赋值和传参，区别在于 method value 绑定实例，而 method expression 则须显式传参。

```text
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) Test() {
    fmt.Printf("%p, %v\n", self, self)
}

func main() {
    u := User{1, "Tom"}
    u.Test()

    mValue := u.Test
    mValue() 

    mExpression := (*User).Test
    mExpression(&u) 
}
```

输出结果:

```text
    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}
```

需要注意，method value 会复制 receiver。

```text
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self User) Test() {
    fmt.Println(self)
}

func main() {
    u := User{1, "Tom"}
    mValue := u.Test 

    u.id, u.name = 2, "Jack"
    u.Test()

    mValue()
}
```

输出结果

```text
    {2 Jack}
    {1 Tom}
```

在汇编层面，method value 和闭包的实现方式相同，实际返回 FuncVal 类型对象。

```text
    FuncVal { method_address, receiver_copy }
```

可依据方法集转换 method expression，注意 receiver 类型的差异。

```text
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) TestPointer() {
    fmt.Printf("TestPointer: %p, %v\n", self, self)
}

func (self User) TestValue() {
    fmt.Printf("TestValue: %p, %v\n", &self, self)
}

func main() {
    u := User{1, "Tom"}
    fmt.Printf("User: %p, %v\n", &u, u)

    mv := User.TestValue
    mv(u)

    mp := (*User).TestPointer
    mp(&u)

    mp2 := (*User).TestValue 
    mp2(&u)
}
```

输出:

```text
    User: 0xc42000a060, {1 Tom}
    TestValue: 0xc42000a0a0, {1 Tom}
    TestPointer: 0xc42000a060, &{1 Tom}
    TestValue: 0xc42000a100, {1 Tom}
```

将方法 "还原" 成函数，就容易理解下面的代码了。

```text
package main

type Data struct{}

func (Data) TestValue() {}

func (*Data) TestPointer() {}

func main() {
    var p *Data = nil
    p.TestPointer()

    (*Data)(nil).TestPointer() 
    (*Data).TestPointer(nil)   

    

    
    
}
```

