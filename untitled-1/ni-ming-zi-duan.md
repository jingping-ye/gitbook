# 匿名字段

## 1. 匿名字段 <a id="&#x533F;&#x540D;&#x5B57;&#x6BB5;"></a>

Golang匿名字段 ：可以像字段成员那样访问匿名字段方法，编译器负责查找。

```text
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
}

func (self *User) ToString() string { 
    return fmt.Sprintf("User: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"}}
    fmt.Printf("Manager: %p\n", &m)
    fmt.Println(m.ToString())
}
```

输出结果:

```text
    Manager: 0xc42000a060
    User: 0xc42000a060, &{1 Tom}
```

通过匿名字段，可获得和继承类似的复用能力。依据编译器查找次序，只需在外层定义同名方法，就可以实现 "override"。

```text
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
    title string
}

func (self *User) ToString() string {
    return fmt.Sprintf("User: %p, %v", self, self)
}

func (self *Manager) ToString() string {
    return fmt.Sprintf("Manager: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"}, "Administrator"}

    fmt.Println(m.ToString())

    fmt.Println(m.User.ToString())
}
```

输出结果:

```text
    Manager: 0xc420074180, &{{1 Tom} Administrator}
    User: 0xc420074180, &{1 Tom}
```
