# 反射

## 1. 反射 <a id="&#x53CD;&#x5C04;"></a>

反射是指在程序运行期对程序本身进行访问和修改的能力

### 1.1.1. 变量的内在机制 <a id="&#x53D8;&#x91CF;&#x7684;&#x5185;&#x5728;&#x673A;&#x5236;"></a>

* 变量包含类型信息和值信息 var arr \[10\]int arr\[0\] = 10
* 类型信息：是静态的元信息，是预先定义好的
* 值信息：是程序运行过程中动态改变的

### 1.1.2. 反射的使用 <a id="&#x53CD;&#x5C04;&#x7684;&#x4F7F;&#x7528;"></a>

* reflect包封装了反射相关的方法
* 获取类型信息：reflect.TypeOf，是静态的
* 获取值信息：reflect.ValueOf，是动态的

### 1.1.3. 空接口与反射 <a id="&#x7A7A;&#x63A5;&#x53E3;&#x4E0E;&#x53CD;&#x5C04;"></a>

* 反射可以在运行时动态获取程序的各种详细信息
* 反射获取interface类型信息

```text
package main

import (
   "fmt"
   "reflect"
)



func reflect_type(a interface{}) {
   t := reflect.TypeOf(a)
   fmt.Println("类型是：", t)
   
   k := t.Kind()
   fmt.Println(k)
   switch k {
   case reflect.Float64:
      fmt.Printf("a is float64\n")
   case reflect.String:
      fmt.Println("string")
   }
}

func main() {
   var x float64 = 3.4
   reflect_type(x)
}
```

* 反射获取interface值信息

```text
package main

import (
    "fmt"
    "reflect"
)



func reflect_value(a interface{}) {
    v := reflect.ValueOf(a)
    fmt.Println(v)
    k := v.Kind()
    fmt.Println(k)
    switch k {
    case reflect.Float64:
        fmt.Println("a是：", v.Float())
    }
}

func main() {
    var x float64 = 3.4
    reflect_value(x)
}
```

* 反射修改值信息

```text
package main

import (
    "fmt"
    "reflect"
)


func reflect_set_value(a interface{}) {
    v := reflect.ValueOf(a)
    k := v.Kind()
    switch k {
    case reflect.Float64:
        
        v.SetFloat(6.9)
        fmt.Println("a is ", v.Float())
    case reflect.Ptr:
        
        v.Elem().SetFloat(7.9)
        fmt.Println("case:", v.Elem().Float())
        
        fmt.Println(v.Pointer())
    }
}

func main() {
    var x float64 = 3.4
    
    reflect_set_value(&x)
    fmt.Println("main:", x)
}
```

### 1.1.4. 结构体与反射 <a id="&#x7ED3;&#x6784;&#x4F53;&#x4E0E;&#x53CD;&#x5C04;"></a>

查看类型、字段和方法

```text
package main

import (
    "fmt"
    "reflect"
)


type User struct {
    Id   int
    Name string
    Age  int
}


func (u User) Hello() {
    fmt.Println("Hello")
}


func Poni(o interface{}) {
    t := reflect.TypeOf(o)
    fmt.Println("类型：", t)
    fmt.Println("字符串类型：", t.Name())
    
    v := reflect.ValueOf(o)
    fmt.Println(v)
    
    
    for i := 0; i < t.NumField(); i++ {
        
        f := t.Field(i)
        fmt.Printf("%s : %v", f.Name, f.Type)
        
        
        val := v.Field(i).Interface()
        fmt.Println("val :", val)
    }
    fmt.Println("=================方法====================")
    for i := 0; i < t.NumMethod(); i++ {
        m := t.Method(i)
        fmt.Println(m.Name)
        fmt.Println(m.Type)
    }

}

func main() {
    u := User{1, "zs", 20}
    Poni(u)
}
```

查看匿名字段

```text
package main

import (
    "fmt"
    "reflect"
)


type User struct {
    Id   int
    Name string
    Age  int
}


type Boy struct {
    User
    Addr string
}

func main() {
    m := Boy{User{1, "zs", 20}, "bj"}
    t := reflect.TypeOf(m)
    fmt.Println(t)
    
    fmt.Printf("%#v\n", t.Field(0))
    
    fmt.Printf("%#v\n", reflect.ValueOf(m).Field(0))
}
```

修改结构体的值

```text
package main

import (
    "fmt"
    "reflect"
)


type User struct {
    Id   int
    Name string
    Age  int
}


func SetValue(o interface{}) {
    v := reflect.ValueOf(o)
    
    v = v.Elem()
    
    f := v.FieldByName("Name")
    if f.Kind() == reflect.String {
        f.SetString("kuteng")
    }
}

func main() {
    u := User{1, "5lmh.com", 20}
    SetValue(&u)
    fmt.Println(u)
}
```

调用方法

```text
package main

import (
    "fmt"
    "reflect"
)


type User struct {
    Id   int
    Name string
    Age  int
}

func (u User) Hello(name string) {
    fmt.Println("Hello：", name)
}

func main() {
    u := User{1, "5lmh.com", 20}
    v := reflect.ValueOf(u)
    
    m := v.MethodByName("Hello")
    
    args := []reflect.Value{reflect.ValueOf("6666")}
    
    
    m.Call(args)
}
```

获取字段的tag

```text
package main

import (
    "fmt"
    "reflect"
)

type Student struct {
    Name string `json:"name1" db:"name2"`
}

func main() {
    var s Student
    v := reflect.ValueOf(&s)
    
    t := v.Type()
    
    f := t.Elem().Field(0)
    fmt.Println(f.Tag.Get("json"))
    fmt.Println(f.Tag.Get("db"))
}
```

### 1.1.5. 反射练习 <a id="&#x53CD;&#x5C04;&#x7EC3;&#x4E60;"></a>

* 任务：解析如下配置文件
  * 序列化：将结构体序列化为配置文件数据并保存到硬盘
  * 反序列化：将配置文件内容反序列化到程序的结构体
* 配置文件有server和mysql相关配置

```text
#this is comment
;this a comment
;[]表示一个section
[server]
ip = 10.238.2.2
port = 8080

[mysql]
username = root
passwd = admin
database = test
host = 192.168.10.10
port = 8000
timeout = 1.2
```

代码地址：[https://github.com/lu569368/Practise\_reflex.git](https://github.com/lu569368/Practise_reflex.git)

