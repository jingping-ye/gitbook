# 条件语句if

## 1. 条件语句if <a id="&#x6761;&#x4EF6;&#x8BED;&#x53E5;if"></a>

### 1.1.1. Go 语言条件语句： <a id="go-&#x8BED;&#x8A00;&#x6761;&#x4EF6;&#x8BED;&#x53E5;&#xFF1A;"></a>

条件语句需要开发者通过指定一个或多个条件，并通过测试条件是否为 true 来决定是否执行指定语句，并在条件为 false 的情况在执行另外的语句。

Go 语言提供了以下几种条件判断语句：

### 1.1.2. if 语句 if 语句 由一个布尔表达式后紧跟一个或多个语句组成。 <a id="if-&#x8BED;&#x53E5;-if-&#x8BED;&#x53E5;-&#x7531;&#x4E00;&#x4E2A;&#x5E03;&#x5C14;&#x8868;&#x8FBE;&#x5F0F;&#x540E;&#x7D27;&#x8DDF;&#x4E00;&#x4E2A;&#x6216;&#x591A;&#x4E2A;&#x8BED;&#x53E5;&#x7EC4;&#x6210;&#x3002;"></a>

Go 编程语言中 if 语句的语法如下：

```text
    • 可省略条件表达式括号。
    • 持初始化语句，可定义代码块局部变量。 
    • 代码块左 括号必须在条件表达式尾部。

    if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
    }
```

if 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则不执行。

```text
 x := 0





if n := "abc"; x > 0 {     
    println(n[2])
} else if x < 0 {    
    println(n[1])
} else {
    println(n[0])
}
```

```text
    *不支持三元操作符(三目运算符) "a > b ? a : b"。
```

#### 实例: <a id="&#x5B9E;&#x4F8B;"></a>

```text
package main

import "fmt"

func main() {
   
   var a int = 10
   
   if a < 20 {
       
       fmt.Printf("a 小于 20\n" )
   }
   fmt.Printf("a 的值为 : %d\n", a)
}
```

以上代码执行结果为：

```text
    a 小于 20
    a 的值为 : 10
```

### 1.1.3. if...else 语句 if 语句 后可以使用可选的 else 语句, else 语句中的表达式在布尔表达式为 false 时执行。 <a id="ifelse-&#x8BED;&#x53E5;-if-&#x8BED;&#x53E5;-&#x540E;&#x53EF;&#x4EE5;&#x4F7F;&#x7528;&#x53EF;&#x9009;&#x7684;-else-&#x8BED;&#x53E5;-else-&#x8BED;&#x53E5;&#x4E2D;&#x7684;&#x8868;&#x8FBE;&#x5F0F;&#x5728;&#x5E03;&#x5C14;&#x8868;&#x8FBE;&#x5F0F;&#x4E3A;-false-&#x65F6;&#x6267;&#x884C;&#x3002;"></a>

#### 语法 <a id="&#x8BED;&#x6CD5;"></a>

Go 编程语言中 if...else 语句的语法如下：

```text
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
} else {
  /* 在布尔表达式为 false 时执行 */
}
```

if 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则执行 else 语句块。

#### 实例: <a id="&#x5B9E;&#x4F8B;_1"></a>

```text
package main

import "fmt"

func main() {
   
   var a int = 100
   
   if a < 20 {
       
       fmt.Printf("a 小于 20\n" )
   } else {
       
       fmt.Printf("a 不小于 20\n" )
   }
   fmt.Printf("a 的值为 : %d\n", a)

}
```

以上代码执行结果为：

```text
    a 不小于 20
    a 的值为 : 100
```

### 1.1.4. if 嵌套语句 你可以在 if 或 else if 语句中嵌入一个或多个 if 或 else if 语句。 <a id="if-&#x5D4C;&#x5957;&#x8BED;&#x53E5;-&#x4F60;&#x53EF;&#x4EE5;&#x5728;-if-&#x6216;-else-if-&#x8BED;&#x53E5;&#x4E2D;&#x5D4C;&#x5165;&#x4E00;&#x4E2A;&#x6216;&#x591A;&#x4E2A;-if-&#x6216;-else-if-&#x8BED;&#x53E5;&#x3002;"></a>

#### 语法 <a id="&#x8BED;&#x6CD5;_1"></a>

Go 编程语言中 if...else 语句的语法如下：

```text
if 布尔表达式 1 {
   /* 在布尔表达式 1 为 true 时执行 */
   if 布尔表达式 2 {
      /* 在布尔表达式 2 为 true 时执行 */
   }
}
```

你可以以同样的方式在 if 语句中嵌套 else if...else 语句

#### 实例 <a id="&#x5B9E;&#x4F8B;_2"></a>

```text
package main

import "fmt"

func main() {
   
   var a int = 100
   var b int = 200
   
   if a == 100 {
       
       if b == 200 {
          
          fmt.Printf("a 的值为 100 ， b 的值为 200\n" )
       }
   }
   fmt.Printf("a 值为 : %d\n", a )
   fmt.Printf("b 值为 : %d\n", b )
}
```

以上代码执行结果为

```text
    a 的值为 100 ， b 的值为 200
    a 值为 : 100
    b 值为 : 200
```

