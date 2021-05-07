# 匿名字段

## 1. 接口 <a id="&#x63A5;&#x53E3;"></a>

**go支持只提供类型而不写字段名的方式，也就是匿名字段，也称为嵌入字段**

```text
package main

import "fmt"




type Person struct {
    name string
    sex  string
    age  int
}

type Student struct {
    Person
    id   int
    addr string
}

func main() {
    
    s1 := Student{Person{"5lmh", "man", 20}, 1, "bj"}
    fmt.Println(s1)

    s2 := Student{Person: Person{"5lmh", "man", 20}}
    fmt.Println(s2)

    s3 := Student{Person: Person{name: "5lmh"}}
    fmt.Println(s3)
}
```

输出结果：

```text
    {{5lmh man 20} 1 bj}
    {{5lmh man 20} 0 }
    {{5lmh  0} 0 }
```

**同名字段的情况**

```text
package main

import "fmt"


type Person struct {
    name string
    sex  string
    age  int
}

type Student struct {
    Person
    id   int
    addr string
    
    name string
}

func main() {
    var s Student
    
    s.name = "5lmh"
    fmt.Println(s)

    
    s.Person.name = "枯藤"
    fmt.Println(s)
}
```

输出结果：

```text
    {{  0} 0  5lmh}
    {{枯藤  0} 0  5lmh}
```

**所有的内置类型和自定义类型都是可以作为匿名字段去使用**

```text
package main

import "fmt"


type Person struct {
    name string
    sex  string
    age  int
}


type mystr string


type Student struct {
    Person
    int
    mystr
}

func main() {
    s1 := Student{Person{"5lmh", "man", 18}, 1, "bj"}
    fmt.Println(s1)
}
```

输出结果：

```text
    {{5lmh man 18} 1 bj}
```

**指针类型匿名字段**

```text
package main

import "fmt"


type Person struct {
    name string
    sex  string
    age  int
}


type Student struct {
    *Person
    id   int
    addr string
}

func main() {
    s1 := Student{&Person{"5lmh", "man", 18}, 1, "bj"}
    fmt.Println(s1)
    fmt.Println(s1.name)
    fmt.Println(s1.Person.name)
}
```

输出结果：

```text
    {0xc00005c360 1 bj}
    zs
    zs
```

