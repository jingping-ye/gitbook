# 返回值

## 1. 返回值 <a id="&#x8FD4;&#x56DE;&#x503C;"></a>

### 1.1.1. 函数返回值 <a id="&#x51FD;&#x6570;&#x8FD4;&#x56DE;&#x503C;"></a>

`"_"`标识符，用来忽略函数的某个返回值

Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。

返回值的名称应当具有一定的意义，可以作为文档使用。

没有参数的 return 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。

直接返回语句仅应当用在像下面这样的短函数中。在长的函数中它们会影响代码的可读性。

```text
package main

import (
    "fmt"
)

func add(a, b int) (c int) {
    c = a + b
    return
}

func calc(a, b int) (sum int, avg int) {
    sum = a + b
    avg = (a + b) / 2

    return
}

func main() {
    var a, b int = 1, 2
    c := add(a, b)
    sum, avg := calc(a, b)
    fmt.Println(a, b, c, sum, avg)
}
```

输出结果：

```text
    1 2 3 3 1
```

Golang返回值不能用容器对象接收多返回值。只能用多个变量，或 `"_"` 忽略。

```text
package main

func test() (int, int) {
    return 1, 2
}

func main() {
    
    

    x, _ := test()
    println(x)
}
```

输出结果：

```text
    1
```

多返回值可直接作为其他函数调用实参。

```text
package main

func test() (int, int) {
    return 1, 2
}

func add(x, y int) int {
    return x + y
}

func sum(n ...int) int {
    var x int
    for _, i := range n {
        x += i
    }

    return x
}

func main() {
    println(add(test()))
    println(sum(test()))
}
```

输出结果：

```text
    3
    3
```

命名返回参数可看做与形参类似的局部变量，最后由 return 隐式返回。

```text
package main

func add(x, y int) (z int) {
    z = x + y
    return
}

func main() {
    println(add(1, 2))
}
```

输出结果：

```text
    3
```

命名返回参数可被同名局部变量遮蔽，此时需要显式返回。

```text
func add(x, y int) (z int) {
    { 
        var z = x + y
        
        return z 
    }
}
```

命名返回参数允许 defer 延迟调用通过闭包读取和修改。

```text
package main

func add(x, y int) (z int) {
    defer func() {
        z += 100
    }()

    z = x + y
    return
}

func main() {
    println(add(1, 2)) 
}
```

输出结果：

```text
    103
```

显式 return 返回前，会先修改命名返回参数。

```text
package main

func add(x, y int) (z int) {
    defer func() {
        println(z) 
    }()

    z = x + y
    return z + 200 
}

func main() {
    println(add(1, 2)) 
}
```

输出结果：

```text
    203
    203
```

