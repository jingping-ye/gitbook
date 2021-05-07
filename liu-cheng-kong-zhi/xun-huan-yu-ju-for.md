# 循环语句for

## 1. 循环语句for <a id="&#x5FAA;&#x73AF;&#x8BED;&#x53E5;for"></a>

### 1.1.1. Golang for支持三种循环方式，包括类似 while 的语法。 <a id="golang-for&#x652F;&#x6301;&#x4E09;&#x79CD;&#x5FAA;&#x73AF;&#x65B9;&#x5F0F;&#xFF0C;&#x5305;&#x62EC;&#x7C7B;&#x4F3C;-while-&#x7684;&#x8BED;&#x6CD5;&#x3002;"></a>

for循环是一个循环控制结构，可以执行指定次数的循环。

#### 语法 <a id="&#x8BED;&#x6CD5;"></a>

Go语言的For循环有3中形式，只有其中的一种使用分号。

```text
    for init; condition; post { }
    for condition { }
    for { }
    init： 一般为赋值表达式，给控制变量赋初值；
    condition： 关系表达式或逻辑表达式，循环控制条件；
    post： 一般为赋值表达式，给控制变量增量或减量。
    for语句执行过程如下：
    ①先对表达式 init 赋初值；
    ②判别赋值表达式 init 是否满足给定 condition 条件，若其值为真，满足循环条件，则执行循环体内语句，然后执行 post，进入第二次循环，再判别 condition；否则判断 condition 的值为假，不满足条件，就终止for循环，执行循环体外语句。
```

```text
s := "abc"

for i, n := 0, len(s); i < n; i++ { 
    println(s[i])
}

n := len(s)
for n > 0 {                
    println(s[n])        
    n-- 
}

for {                    
    println(s)            
}
```

不要期望编译器能理解你的想法，在初始化语句中计算出全部结果是个好主意。

```text
package main

func length(s string) int {
    println("call length.")
    return len(s)
}

func main() {
    s := "abcd"

    for i, n := 0, length(s); i < n; i++ {     
        println(i, s[i])
    } 
}
```

输出:

```text
    call length.
    0 97
    1 98
    2 99
    3 100
```

#### 实例： <a id="&#x5B9E;&#x4F8B;&#xFF1A;"></a>

```text
package main

import "fmt"

func main() {

   var b int = 15
   var a int

   numbers := [6]int{1, 2, 3, 5}

   
   for a := 0; a < 10; a++ {
      fmt.Printf("a 的值为: %d\n", a)
   }

   for a < b {
      a++
      fmt.Printf("a 的值为: %d\n", a)
      }

   for i,x:= range numbers {
      fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
   }   
}
```

以上实例运行输出结果为:

```text
    a 的值为: 0
    a 的值为: 1
    a 的值为: 2
    a 的值为: 3
    a 的值为: 4
    a 的值为: 5
    a 的值为: 6
    a 的值为: 7
    a 的值为: 8
    a 的值为: 9
    a 的值为: 1
    a 的值为: 2
    a 的值为: 3
    a 的值为: 4
    a 的值为: 5
    a 的值为: 6
    a 的值为: 7
    a 的值为: 8
    a 的值为: 9
    a 的值为: 10
    a 的值为: 11
    a 的值为: 12
    a 的值为: 13
    a 的值为: 14
    a 的值为: 15
    第 0 位 x 的值 = 1
    第 1 位 x 的值 = 2
    第 2 位 x 的值 = 3
    第 3 位 x 的值 = 5
    第 4 位 x 的值 = 0
    第 5 位 x 的值 = 0
```

### 1.1.2. 循环嵌套 <a id="&#x5FAA;&#x73AF;&#x5D4C;&#x5957;"></a>

在 for 循环中嵌套一个或多个 for 循环

#### 语法 <a id="&#x8BED;&#x6CD5;_1"></a>

以下为 Go 语言嵌套循环的格式：

```text
for [condition |  ( init; condition; increment ) | Range]
{
   for [condition |  ( init; condition; increment ) | Range]
   {
      statement(s)
   }
   statement(s)
}
```

#### 实例： <a id="&#x5B9E;&#x4F8B;&#xFF1A;_1"></a>

以下实例使用循环嵌套来输出 2 到 100 间的素数：

```text
package main

import "fmt"

func main() {
   
   var i, j int

   for i=2; i < 100; i++ {
      for j=2; j <= (i/j); j++ {
         if(i%j==0) {
            break 
         }
      }
      if(j > (i/j)) {
         fmt.Printf("%d  是素数\n", i)
      }
   }  
}
```

以上实例运行输出结果为:

```text
    2  是素数
    3  是素数
    5  是素数
    7  是素数
    11  是素数
    13  是素数
    17  是素数
    19  是素数
    23  是素数
    29  是素数
    31  是素数
    37  是素数
    41  是素数
    43  是素数
    47  是素数
    53  是素数
    59  是素数
    61  是素数
    67  是素数
    71  是素数
    73  是素数
    79  是素数
    83  是素数
    89  是素数
    97  是素数
```

### 1.1.3. 无限循环 <a id="&#x65E0;&#x9650;&#x5FAA;&#x73AF;"></a>

如过循环中条件语句永远不为 false 则会进行无限循环，我们可以通过 for 循环语句中只设置一个条件表达式来执行无限循环：

```text
package main

import "fmt"

func main() {
    for true  {
        fmt.Printf("这是无限循环。\n");
    }
}
```

