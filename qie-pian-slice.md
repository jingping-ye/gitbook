# 切片Slice

## 1. 切片Slice <a id="&#x5207;&#x7247;slice"></a>

需要说明，slice 并不是数组或数组指针。它通过内部指针和相关属性引用数组片段，以实现变长方案。

```text
    1. 切片：切片是数组的一个引用，因此切片是引用类型。但自身是结构体，值拷贝传递。
    2. 切片的长度可以改变，因此，切片是一个可变的数组。
    3. 切片遍历方式和数组一样，可以用len()求长度。表示可用元素数量，读写操作不能超过该限制。 
    4. cap可以求出slice最大扩张容量，不能超出数组限制。0 <= len(slice) <= len(array)，其中array是slice引用的数组。
    5. 切片的定义：var 变量名 []类型，比如 var str []string  var arr []int。
    6. 如果 slice == nil，那么 len、cap 结果都等于 0。
```

### 1.1.1. 创建切片的各种方式 <a id="&#x521B;&#x5EFA;&#x5207;&#x7247;&#x7684;&#x5404;&#x79CD;&#x65B9;&#x5F0F;"></a>

```text
package main

import "fmt"

func main() {
   
   var s1 []int
   if s1 == nil {
      fmt.Println("是空")
   } else {
      fmt.Println("不是空")
   }
   
   s2 := []int{}
   
   var s3 []int = make([]int, 0)
   fmt.Println(s1, s2, s3)
   
   var s4 []int = make([]int, 0, 0)
   fmt.Println(s4)
   s5 := []int{1, 2, 3}
   fmt.Println(s5)
   
   arr := [5]int{1, 2, 3, 4, 5}
   var s6 []int
   
   s6 = arr[1:4]
   fmt.Println(s6)
}
```

### 1.1.2. 切片初始化 <a id="&#x5207;&#x7247;&#x521D;&#x59CB;&#x5316;"></a>

```text
全局：
var arr = [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
var slice0 []int = arr[start:end] 
var slice1 []int = arr[:end]        
var slice2 []int = arr[start:]        
var slice3 []int = arr[:] 
var slice4 = arr[:len(arr)-1]      //去掉切片的最后一个元素
局部：
arr2 := [...]int{9, 8, 7, 6, 5, 4, 3, 2, 1, 0}
slice5 := arr[start:end]
slice6 := arr[:end]        
slice7 := arr[start:]     
slice8 := arr[:]  
slice9 := arr[:len(arr)-1] //去掉切片的最后一个元素
```

代码：

```text
package main

import (
    "fmt"
)

var arr = [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
var slice0 []int = arr[2:8]
var slice1 []int = arr[0:6]        
var slice2 []int = arr[5:10]       
var slice3 []int = arr[0:len(arr)] 
var slice4 = arr[:len(arr)-1]      
func main() {
    fmt.Printf("全局变量：arr %v\n", arr)
    fmt.Printf("全局变量：slice0 %v\n", slice0)
    fmt.Printf("全局变量：slice1 %v\n", slice1)
    fmt.Printf("全局变量：slice2 %v\n", slice2)
    fmt.Printf("全局变量：slice3 %v\n", slice3)
    fmt.Printf("全局变量：slice4 %v\n", slice4)
    fmt.Printf("-----------------------------------\n")
    arr2 := [...]int{9, 8, 7, 6, 5, 4, 3, 2, 1, 0}
    slice5 := arr[2:8]
    slice6 := arr[0:6]         
    slice7 := arr[5:10]        
    slice8 := arr[0:len(arr)]  
    slice9 := arr[:len(arr)-1] 
    fmt.Printf("局部变量： arr2 %v\n", arr2)
    fmt.Printf("局部变量： slice5 %v\n", slice5)
    fmt.Printf("局部变量： slice6 %v\n", slice6)
    fmt.Printf("局部变量： slice7 %v\n", slice7)
    fmt.Printf("局部变量： slice8 %v\n", slice8)
    fmt.Printf("局部变量： slice9 %v\n", slice9)
}
```

输出结果：

```text
    全局变量：arr [0 1 2 3 4 5 6 7 8 9]
    全局变量：slice0 [2 3 4 5 6 7]
    全局变量：slice1 [0 1 2 3 4 5]
    全局变量：slice2 [5 6 7 8 9]
    全局变量：slice3 [0 1 2 3 4 5 6 7 8 9]
    全局变量：slice4 [0 1 2 3 4 5 6 7 8]
    -----------------------------------
    局部变量： arr2 [9 8 7 6 5 4 3 2 1 0]
    局部变量： slice5 [2 3 4 5 6 7]
    局部变量： slice6 [0 1 2 3 4 5]
    局部变量： slice7 [5 6 7 8 9]
    局部变量： slice8 [0 1 2 3 4 5 6 7 8 9]
    局部变量： slice9 [0 1 2 3 4 5 6 7 8]
```

### 1.1.3. 通过make来创建切片 <a id="&#x901A;&#x8FC7;make&#x6765;&#x521B;&#x5EFA;&#x5207;&#x7247;"></a>

```text
    var slice []type = make([]type, len)
    slice  := make([]type, len)
    slice  := make([]type, len, cap)
```

代码：

```text
package main

import (
    "fmt"
)

var slice0 []int = make([]int, 10)
var slice1 = make([]int, 10)
var slice2 = make([]int, 10, 10)

func main() {
    fmt.Printf("make全局slice0 ：%v\n", slice0)
    fmt.Printf("make全局slice1 ：%v\n", slice1)
    fmt.Printf("make全局slice2 ：%v\n", slice2)
    fmt.Println("--------------------------------------")
    slice3 := make([]int, 10)
    slice4 := make([]int, 10)
    slice5 := make([]int, 10, 10)
    fmt.Printf("make局部slice3 ：%v\n", slice3)
    fmt.Printf("make局部slice4 ：%v\n", slice4)
    fmt.Printf("make局部slice5 ：%v\n", slice5)
}
```

输出结果：

```text
    make全局slice0 ：[0 0 0 0 0 0 0 0 0 0]
    make全局slice1 ：[0 0 0 0 0 0 0 0 0 0]
    make全局slice2 ：[0 0 0 0 0 0 0 0 0 0]
    --------------------------------------
    make局部slice3 ：[0 0 0 0 0 0 0 0 0 0]
    make局部slice4 ：[0 0 0 0 0 0 0 0 0 0]
    make局部slice5 ：[0 0 0 0 0 0 0 0 0 0]
```

切片的内存布局

读写操作实际目标是底层数组，只需注意索引号的差别。

```text
package main

import (
    "fmt"
)

func main() {
    data := [...]int{0, 1, 2, 3, 4, 5}

    s := data[2:4]
    s[0] += 100
    s[1] += 200

    fmt.Println(s)
    fmt.Println(data)
}
```

输出:

```text
    [102 203]
    [0 1 102 203 4 5]
```

可直接创建 slice 对象，自动分配底层数组。

```text
package main

import "fmt"

func main() {
    s1 := []int{0, 1, 2, 3, 8: 100} 
    fmt.Println(s1, len(s1), cap(s1))

    s2 := make([]int, 6, 8) 
    fmt.Println(s2, len(s2), cap(s2))

    s3 := make([]int, 6) 
    fmt.Println(s3, len(s3), cap(s3))
}
```

输出结果:

```text
    [0 1 2 3 0 0 0 0 100] 9 9
    [0 0 0 0 0 0] 6 8
    [0 0 0 0 0 0] 6 6
```

使用 make 动态创建slice，避免了数组必须用常量做长度的麻烦。还可用指针直接访问底层数组，退化成普通数组操作。

```text
package main

import "fmt"

func main() {
    s := []int{0, 1, 2, 3}
    p := &s[2] 
    *p += 100

    fmt.Println(s)
}
```

输出结果:

```text
    [0 1 102 3]
```

至于 \[\]\[\]T，是指元素类型为 \[\]T 。

```text
package main

import (
    "fmt"
)

func main() {
    data := [][]int{
        []int{1, 2, 3},
        []int{100, 200},
        []int{11, 22, 33, 44},
    }
    fmt.Println(data)
}
```

输出结果：

```text
    [[1 2 3] [100 200] [11 22 33 44]]
```

可直接修改 struct array/slice 成员。

```text
package main

import (
    "fmt"
)

func main() {
    d := [5]struct {
        x int
    }{}

    s := d[:]

    d[1].x = 10
    s[2].x = 20

    fmt.Println(d)
    fmt.Printf("%p, %p\n", &d, &d[0])

}
```

输出结果:

```text
    [{0} {10} {20} {0} {0}]
    0xc4200160f0, 0xc4200160f0
```

### 1.1.4. 用append内置函数操作切片（切片追加） <a id="&#x7528;append&#x5185;&#x7F6E;&#x51FD;&#x6570;&#x64CD;&#x4F5C;&#x5207;&#x7247;&#xFF08;&#x5207;&#x7247;&#x8FFD;&#x52A0;&#xFF09;"></a>

```text
package main

import (
    "fmt"
)

func main() {

    var a = []int{1, 2, 3}
    fmt.Printf("slice a : %v\n", a)
    var b = []int{4, 5, 6}
    fmt.Printf("slice b : %v\n", b)
    c := append(a, b...)
    fmt.Printf("slice c : %v\n", c)
    d := append(c, 7)
    fmt.Printf("slice d : %v\n", d)
    e := append(d, 8, 9, 10)
    fmt.Printf("slice e : %v\n", e)

}
```

输出结果：

```text
    slice a : [1 2 3]
    slice b : [4 5 6]
    slice c : [1 2 3 4 5 6]
    slice d : [1 2 3 4 5 6 7]
    slice e : [1 2 3 4 5 6 7 8 9 10]
```

append ：向 slice 尾部添加数据，返回新的 slice 对象。

```text
package main

import (
    "fmt"
)

func main() {

    s1 := make([]int, 0, 5)
    fmt.Printf("%p\n", &s1)

    s2 := append(s1, 1)
    fmt.Printf("%p\n", &s2)

    fmt.Println(s1, s2)

}
```

输出结果：

```text
    0xc42000a060
    0xc42000a080
    [] [1]
```

### 1.1.5. 超出原 slice.cap 限制，就会重新分配底层数组，即便原数组并未填满。 <a id="&#x8D85;&#x51FA;&#x539F;-slicecap-&#x9650;&#x5236;&#xFF0C;&#x5C31;&#x4F1A;&#x91CD;&#x65B0;&#x5206;&#x914D;&#x5E95;&#x5C42;&#x6570;&#x7EC4;&#xFF0C;&#x5373;&#x4FBF;&#x539F;&#x6570;&#x7EC4;&#x5E76;&#x672A;&#x586B;&#x6EE1;&#x3002;"></a>

```text
package main

import (
    "fmt"
)

func main() {

    data := [...]int{0, 1, 2, 3, 4, 10: 0}
    s := data[:2:3]

    s = append(s, 100, 200) 

    fmt.Println(s, data)         
    fmt.Println(&s[0], &data[0]) 

}
```

输出结果:

```text
    [0 1 100 200] [0 1 2 3 4 0 0 0 0 0 0]
    0xc4200160f0 0xc420070060
```

从输出结果可以看出，append 后的 s 重新分配了底层数组，并复制数据。如果只追加一个值，则不会超过 s.cap 限制，也就不会重新分配。 通常以 2 倍容量重新分配底层数组。在大批量添加数据时，建议一次性分配足够大的空间，以减少内存分配和数据复制开销。或初始化足够长的 len 属性，改用索引号进行操作。及时释放不再使用的 slice 对象，避免持有过期数组，造成 GC 无法回收。

### 1.1.6. slice中cap重新分配规律： <a id="slice&#x4E2D;cap&#x91CD;&#x65B0;&#x5206;&#x914D;&#x89C4;&#x5F8B;&#xFF1A;"></a>

```text
package main

import (
    "fmt"
)

func main() {

    s := make([]int, 0, 1)
    c := cap(s)

    for i := 0; i < 50; i++ {
        s = append(s, i)
        if n := cap(s); n > c {
            fmt.Printf("cap: %d -> %d\n", c, n)
            c = n
        }
    }

}
```

输出结果:

```text
    cap: 1 -> 2
    cap: 2 -> 4
    cap: 4 -> 8
    cap: 8 -> 16
    cap: 16 -> 32
    cap: 32 -> 64
```

### 1.1.7. 切片拷贝 <a id="&#x5207;&#x7247;&#x62F7;&#x8D1D;"></a>

```text
package main

import (
    "fmt"
)

func main() {

    s1 := []int{1, 2, 3, 4, 5}
    fmt.Printf("slice s1 : %v\n", s1)
    s2 := make([]int, 10)
    fmt.Printf("slice s2 : %v\n", s2)
    copy(s2, s1)
    fmt.Printf("copied slice s1 : %v\n", s1)
    fmt.Printf("copied slice s2 : %v\n", s2)
    s3 := []int{1, 2, 3}
    fmt.Printf("slice s3 : %v\n", s3)
    s3 = append(s3, s2...)
    fmt.Printf("appended slice s3 : %v\n", s3)
    s3 = append(s3, 4, 5, 6)
    fmt.Printf("last slice s3 : %v\n", s3)

}
```

输出结果：

```text
    slice s1 : [1 2 3 4 5]
    slice s2 : [0 0 0 0 0 0 0 0 0 0]
    copied slice s1 : [1 2 3 4 5]
    copied slice s2 : [1 2 3 4 5 0 0 0 0 0]
    slice s3 : [1 2 3]
    appended slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0]
    last slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0 4 5 6]
```

copy ：函数 copy 在两个 slice 间复制数据，复制长度以 len 小的为准。两个 slice 可指向同一底层数组，允许元素区间重叠。

```text
package main

import (
    "fmt"
)

func main() {

    data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    fmt.Println("array data : ", data)
    s1 := data[8:]
    s2 := data[:5]
    fmt.Printf("slice s1 : %v\n", s1)
    fmt.Printf("slice s2 : %v\n", s2)
    copy(s2, s1)
    fmt.Printf("copied slice s1 : %v\n", s1)
    fmt.Printf("copied slice s2 : %v\n", s2)
    fmt.Println("last array data : ", data)

}
```

输出结果:

```text
    array data :  [0 1 2 3 4 5 6 7 8 9]
    slice s1 : [8 9]
    slice s2 : [0 1 2 3 4]
    copied slice s1 : [8 9]
    copied slice s2 : [8 9 2 3 4]
    last array data :  [8 9 2 3 4 5 6 7 8 9]
```

应及时将所需数据 copy 到较小的 slice，以便释放超大号底层数组内存。

### 1.1.8. slice遍历： <a id="slice&#x904D;&#x5386;&#xFF1A;"></a>

```text
package main

import (
    "fmt"
)

func main() {

    data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    slice := data[:]
    for index, value := range slice {
        fmt.Printf("inde : %v , value : %v\n", index, value)
    }

}
```

输出结果：

```text
    inde : 0 , value : 0
    inde : 1 , value : 1
    inde : 2 , value : 2
    inde : 3 , value : 3
    inde : 4 , value : 4
    inde : 5 , value : 5
    inde : 6 , value : 6
    inde : 7 , value : 7
    inde : 8 , value : 8
    inde : 9 , value : 9
```

### 1.1.9. 切片resize（调整大小） <a id="&#x5207;&#x7247;resize&#xFF08;&#x8C03;&#x6574;&#x5927;&#x5C0F;&#xFF09;"></a>

```text
package main

import (
    "fmt"
)

func main() {
    var a = []int{1, 3, 4, 5}
    fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a))
    b := a[1:2]
    fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b))
    c := b[0:3]
    fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c))
}
```

输出结果：

```text
    slice a : [1 3 4 5] , len(a) : 4
    slice b : [3] , len(b) : 1
    slice c : [3 4 5] , len(c) : 3
```

### 1.1.10. 数组和切片的内存布局 <a id="&#x6570;&#x7EC4;&#x548C;&#x5207;&#x7247;&#x7684;&#x5185;&#x5B58;&#x5E03;&#x5C40;"></a>

### 1.1.11. 字符串和切片（string and slice） <a id="&#x5B57;&#x7B26;&#x4E32;&#x548C;&#x5207;&#x7247;&#xFF08;string-and-slice&#xFF09;"></a>

string底层就是一个byte的数组，因此，也可以进行切片操作。

```text
package main

import (
    "fmt"
)

func main() {
    str := "hello world"
    s1 := str[0:5]
    fmt.Println(s1)

    s2 := str[6:]
    fmt.Println(s2)
}
```

输出结果：

```text
    hello
    world
```

string本身是不可变的，因此要改变string中字符。需要如下操作： 英文字符串：

```text
package main

import (
    "fmt"
)

func main() {
    str := "Hello world"
    s := []byte(str) 
    s[6] = 'G'
    s = s[:8]
    s = append(s, '!')
    str = string(s)
    fmt.Println(str)
}
```

输出结果：

```text
    Hello Go!
```

### 1.1.12. 含有中文字符串： <a id="&#x542B;&#x6709;&#x4E2D;&#x6587;&#x5B57;&#x7B26;&#x4E32;&#xFF1A;"></a>

```text
package main

import (
    "fmt"
)

func main() {
    str := "你好，世界！hello world！"
    s := []rune(str) 
    s[3] = '够'
    s[4] = '浪'
    s[12] = 'g'
    s = s[:14]
    str = string(s)
    fmt.Println(str)
}
```

输出结果：

```text
你好，够浪！hello go
```

golang slice data\[:6:8\] 两个冒号的理解

常规slice , data\[6:8\]，从第6位到第8位（返回6， 7），长度len为2， 最大可扩充长度cap为4（6-9）

另一种写法： data\[:6:8\] 每个数字前都有个冒号， slice内容为data从0到第6位，长度len为6，最大扩充项cap设置为8

a\[x:y:z\] 切片内容 \[x:y\] 切片长度: y-x 切片容量:z-x

```text
package main

import (
    "fmt"
)

func main() {
    slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    d1 := slice[6:8]
    fmt.Println(d1, len(d1), cap(d1))
    d2 := slice[:6:8]
    fmt.Println(d2, len(d2), cap(d2))
}
```

数组or切片转字符串：

```text
    strings.Replace(strings.Trim(fmt.Sprint(array_or_slice), "[]"), " ", ",", -1)
```

