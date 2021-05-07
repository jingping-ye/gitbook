# 结构体

## 1. 结构体 <a id="&#x7ED3;&#x6784;&#x4F53;"></a>

Go语言中没有“类”的概念，也不支持“类”的继承等面向对象的概念。Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。

### 1.1. 类型别名和自定义类型 <a id="&#x7C7B;&#x578B;&#x522B;&#x540D;&#x548C;&#x81EA;&#x5B9A;&#x4E49;&#x7C7B;&#x578B;"></a>

#### 1.1.1. 自定义类型 <a id="&#x81EA;&#x5B9A;&#x4E49;&#x7C7B;&#x578B;"></a>

在Go语言中有一些基本的数据类型，如string、整型、浮点型、布尔等数据类型，Go语言中可以使用type关键字来定义自定义类型。

自定义类型是定义了一个全新的类型。我们可以基于内置的基本类型定义，也可以通过struct定义。例如：

```text
    //将MyInt定义为int类型
    type MyInt int
```

通过Type关键字的定义，MyInt就是一种新的类型，它具有int的特性。

#### 1.1.2. 类型别名 <a id="&#x7C7B;&#x578B;&#x522B;&#x540D;"></a>

类型别名是Go1.9版本添加的新功能。

类型别名规定：TypeAlias只是Type的别名，本质上TypeAlias与Type是同一个类型。就像一个孩子小时候有小名、乳名，上学后用学名，英语老师又会给他起英文名，但这些名字都指的是他本人。

```text
    type TypeAlias = Type
```

我们之前见过的rune和byte就是类型别名，他们的定义如下：

```text
    type byte = uint8
    type rune = int32
```

#### 1.1.3. 类型定义和类型别名的区别 <a id="&#x7C7B;&#x578B;&#x5B9A;&#x4E49;&#x548C;&#x7C7B;&#x578B;&#x522B;&#x540D;&#x7684;&#x533A;&#x522B;"></a>

类型别名与类型定义表面上看只有一个等号的差异，我们通过下面的这段代码来理解它们之间的区别。

```text

type NewInt int


type MyInt = int

func main() {
    var a NewInt
    var b MyInt

    fmt.Printf("type of a:%T\n", a) 
    fmt.Printf("type of b:%T\n", b) 
}
```

结果显示a的类型是main.NewInt，表示main包下定义的NewInt类型。b的类型是int。MyInt类型只会在代码中存在，编译完成时并不会有MyInt类型。

### 1.2. 结构体 <a id="&#x7ED3;&#x6784;&#x4F53;_1"></a>

Go语言中的基础数据类型可以表示一些事物的基本属性，但是当我们想表达一个事物的全部或部分属性时，这时候再用单一的基本数据类型明显就无法满足需求了，Go语言提供了一种自定义数据类型，可以封装多个基本数据类型，这种数据类型叫结构体，英文名称struct。 也就是我们可以通过struct来定义自己的类型了。

Go语言中通过struct来实现面向对象。

#### 1.2.1. 结构体的定义 <a id="&#x7ED3;&#x6784;&#x4F53;&#x7684;&#x5B9A;&#x4E49;"></a>

使用type和struct关键字来定义结构体，具体代码格式如下：

```text
    type 类型名 struct {
        字段名 字段类型
        字段名 字段类型
        …
    }
```

其中：

```text
    1.类型名：标识自定义结构体的名称，在同一个包内不能重复。
    2.字段名：表示结构体字段名。结构体中的字段名必须唯一。
    3.字段类型：表示结构体字段的具体类型。
```

举个例子，我们定义一个Person（人）结构体，代码如下：

```text
    type person struct {
        name string
        city string
        age  int8
    }
```

同样类型的字段也可以写在一行，

```text
    type person1 struct {
        name, city string
        age        int8
    }
```

这样我们就拥有了一个person的自定义类型，它有name、city、age三个字段，分别表示姓名、城市和年龄。这样我们使用这个person结构体就能够很方便的在程序中表示和存储人信息了。

语言内置的基础数据类型是用来描述一个值的，而结构体是用来描述一组值的。比如一个人有名字、年龄和居住城市等，本质上是一种聚合型的数据类型

#### 1.2.2. 结构体实例化 <a id="&#x7ED3;&#x6784;&#x4F53;&#x5B9E;&#x4F8B;&#x5316;"></a>

只有当结构体实例化时，才会真正地分配内存。也就是必须实例化后才能使用结构体的字段。

结构体本身也是一种类型，我们可以像声明内置类型一样使用var关键字声明结构体类型。

```text
    var 结构体实例 结构体类型
```

#### 1.2.3. 基本实例化 <a id="&#x57FA;&#x672C;&#x5B9E;&#x4F8B;&#x5316;"></a>

```text
type person struct {
    name string
    city string
    age  int8
}

func main() {
    var p1 person
    p1.name = "pprof.cn"
    p1.city = "北京"
    p1.age = 18
    fmt.Printf("p1=%v\n", p1)  
    fmt.Printf("p1=%#v\n", p1) 
}
```

我们通过.来访问结构体的字段（成员变量）,例如p1.name和p1.age等。

### 1.3. 匿名结构体 <a id="&#x533F;&#x540D;&#x7ED3;&#x6784;&#x4F53;"></a>

在定义一些临时数据结构等场景下还可以使用匿名结构体。

```text
package main

import (
    "fmt"
)

func main() {
    var user struct{Name string; Age int}
    user.Name = "pprof.cn"
    user.Age = 18
    fmt.Printf("%#v\n", user)
}
```

#### 1.3.1. 创建指针类型结构体 <a id="&#x521B;&#x5EFA;&#x6307;&#x9488;&#x7C7B;&#x578B;&#x7ED3;&#x6784;&#x4F53;"></a>

我们还可以通过使用new关键字对结构体进行实例化，得到的是结构体的地址。 格式如下：

```text
    var p2 = new(person)
    fmt.Printf("%T\n", p2)     //*main.person
    fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"", city:"", age:0}
```

从打印的结果中我们可以看出p2是一个结构体指针。

需要注意的是在Go语言中支持对结构体指针直接使用.来访问结构体的成员。

```text
var p2 = new(person)
p2.name = "测试"
p2.age = 18
p2.city = "北京"
fmt.Printf("p2=%#v\n", p2) 
```

#### 1.3.2. 取结构体的地址实例化 <a id="&#x53D6;&#x7ED3;&#x6784;&#x4F53;&#x7684;&#x5730;&#x5740;&#x5B9E;&#x4F8B;&#x5316;"></a>

使用&对结构体进行取地址操作相当于对该结构体类型进行了一次new实例化操作。

```text
p3 := &person{}
fmt.Printf("%T\n", p3)     
fmt.Printf("p3=%#v\n", p3) 
p3.name = "博客"
p3.age = 30
p3.city = "成都"
fmt.Printf("p3=%#v\n", p3) 
```

p3.name = "博客"其实在底层是\(\*p3\).name = "博客"，这是Go语言帮我们实现的语法糖。

#### 1.3.3. 结构体初始化 <a id="&#x7ED3;&#x6784;&#x4F53;&#x521D;&#x59CB;&#x5316;"></a>

```text
type person struct {
    name string
    city string
    age  int8
}

func main() {
    var p4 person
    fmt.Printf("p4=%#v\n", p4) 
}
```

#### 1.3.4. 使用键值对初始化 <a id="&#x4F7F;&#x7528;&#x952E;&#x503C;&#x5BF9;&#x521D;&#x59CB;&#x5316;"></a>

使用键值对对结构体进行初始化时，键对应结构体的字段，值对应该字段的初始值。

```text
p5 := person{
    name: "pprof.cn",
    city: "北京",
    age:  18,
}
fmt.Printf("p5=%#v\n", p5) 
```

也可以对结构体指针进行键值对初始化，例如：

```text
p6 := &person{
    name: "pprof.cn",
    city: "北京",
    age:  18,
}
fmt.Printf("p6=%#v\n", p6) 
```

当某些字段没有初始值的时候，该字段可以不写。此时，没有指定初始值的字段的值就是该字段类型的零值。

```text
p7 := &person{
    city: "北京",
}
fmt.Printf("p7=%#v\n", p7) 
```

#### 1.3.5. 使用值的列表初始化 <a id="&#x4F7F;&#x7528;&#x503C;&#x7684;&#x5217;&#x8868;&#x521D;&#x59CB;&#x5316;"></a>

初始化结构体的时候可以简写，也就是初始化的时候不写键，直接写值：

```text
p8 := &person{
    "pprof.cn",
    "北京",
    18,
}
fmt.Printf("p8=%#v\n", p8) 
```

使用这种格式初始化时，需要注意：

```text
    1.必须初始化结构体的所有字段。
    2.初始值的填充顺序必须与字段在结构体中的声明顺序一致。
    3.该方式不能和键值初始化方式混用。
```

#### 1.3.6. 结构体内存布局 <a id="&#x7ED3;&#x6784;&#x4F53;&#x5185;&#x5B58;&#x5E03;&#x5C40;"></a>

```text
type test struct {
    a int8
    b int8
    c int8
    d int8
}
n := test{
    1, 2, 3, 4,
}
fmt.Printf("n.a %p\n", &n.a)
fmt.Printf("n.b %p\n", &n.b)
fmt.Printf("n.c %p\n", &n.c)
fmt.Printf("n.d %p\n", &n.d)
```

输出：

```text
    n.a 0xc0000a0060
    n.b 0xc0000a0061
    n.c 0xc0000a0062
    n.d 0xc0000a0063
```

#### 1.3.7. 面试题 <a id="&#x9762;&#x8BD5;&#x9898;"></a>

```text
type student struct {
    name string
    age  int
}

func main() {
    m := make(map[string]*student)
    stus := []student{
        {name: "pprof.cn", age: 18},
        {name: "测试", age: 23},
        {name: "博客", age: 28},
    }

    for _, stu := range stus {
        m[stu.name] = &stu
    }
    for k, v := range m {
        fmt.Println(k, "=>", v.name)
    }
}
```

#### 1.3.8. 构造函数 <a id="&#x6784;&#x9020;&#x51FD;&#x6570;"></a>

Go语言的结构体没有构造函数，我们可以自己实现。 例如，下方的代码就实现了一个person的构造函数。 因为struct是值类型，如果结构体比较复杂的话，值拷贝性能开销会比较大，所以该构造函数返回的是结构体指针类型。

```text
func newPerson(name, city string, age int8) *person {
    return &person{
        name: name,
        city: city,
        age:  age,
    }
}
```

调用构造函数

```text
p9 := newPerson("pprof.cn", "测试", 90)
fmt.Printf("%#v\n", p9)
```

#### 1.3.9. 方法和接收者 <a id="&#x65B9;&#x6CD5;&#x548C;&#x63A5;&#x6536;&#x8005;"></a>

Go语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收者（Receiver）。接收者的概念就类似于其他语言中的this或者 self。

方法的定义格式如下：

```text
    func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
        函数体
    }
```

其中，

```text
    1.接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是self、this之类的命名。例如，Person类型的接收者变量应该命名为 p，Connector类型的接收者变量应该命名为c等。
    2.接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。
    3.方法名、参数列表、返回参数：具体格式与函数定义相同。
```

举个例子：

```text

type Person struct {
    name string
    age  int8
}


func NewPerson(name string, age int8) *Person {
    return &Person{
        name: name,
        age:  age,
    }
}


func (p Person) Dream() {
    fmt.Printf("%s的梦想是学好Go语言！\n", p.name)
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
}
```

方法与函数的区别是，函数不属于任何类型，方法属于特定的类型。

#### 1.3.10. 指针类型的接收者 <a id="&#x6307;&#x9488;&#x7C7B;&#x578B;&#x7684;&#x63A5;&#x6536;&#x8005;"></a>

指针类型的接收者由一个结构体的指针组成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。这种方式就十分接近于其他语言中面向对象中的this或者self。 例如我们为Person添加一个SetAge方法，来修改实例变量的年龄。

```text
    // SetAge 设置p的年龄
    // 使用指针接收者
    func (p *Person) SetAge(newAge int8) {
        p.age = newAge
    }
```

调用该方法：

```text
func main() {
    p1 := NewPerson("测试", 25)
    fmt.Println(p1.age) 
    p1.SetAge(30)
    fmt.Println(p1.age) 
}
```

#### 1.3.11. 值类型的接收者 <a id="&#x503C;&#x7C7B;&#x578B;&#x7684;&#x63A5;&#x6536;&#x8005;"></a>

当方法作用于值类型接收者时，Go语言会在代码运行时将接收者的值复制一份。在值类型接收者的方法中可以获取接收者的成员值，但修改操作只是针对副本，无法修改接收者变量本身。

```text


func (p Person) SetAge2(newAge int8) {
    p.age = newAge
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
    fmt.Println(p1.age) 
    p1.SetAge2(30) 
    fmt.Println(p1.age) 
}
```

#### 1.3.12. 什么时候应该使用指针类型接收者 <a id="&#x4EC0;&#x4E48;&#x65F6;&#x5019;&#x5E94;&#x8BE5;&#x4F7F;&#x7528;&#x6307;&#x9488;&#x7C7B;&#x578B;&#x63A5;&#x6536;&#x8005;"></a>

```text
    1.需要修改接收者中的值
    2.接收者是拷贝代价比较大的大对象
    3.保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。
```

#### 1.3.13. 任意类型添加方法 <a id="&#x4EFB;&#x610F;&#x7C7B;&#x578B;&#x6DFB;&#x52A0;&#x65B9;&#x6CD5;"></a>

在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 举个例子，我们基于内置的int类型使用type关键字可以定义新的自定义类型，然后为我们的自定义类型添加方法。

```text

type MyInt int


func (m MyInt) SayHello() {
    fmt.Println("Hello, 我是一个int。")
}
func main() {
    var m1 MyInt
    m1.SayHello() 
    m1 = 100
    fmt.Printf("%#v  %T\n", m1, m1) 
}
```

注意事项： 非本地类型不能定义方法，也就是说我们不能给别的包的类型定义方法。

#### 1.3.14. 结构体的匿名字段 <a id="&#x7ED3;&#x6784;&#x4F53;&#x7684;&#x533F;&#x540D;&#x5B57;&#x6BB5;"></a>

结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。

```text

type Person struct {
    string
    int
}

func main() {
    p1 := Person{
        "pprof.cn",
        18,
    }
    fmt.Printf("%#v\n", p1)        
    fmt.Println(p1.string, p1.int) 
}
```

匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

#### 1.3.15. 嵌套结构体 <a id="&#x5D4C;&#x5957;&#x7ED3;&#x6784;&#x4F53;"></a>

一个结构体中可以嵌套包含另一个结构体或结构体指针。

```text

type Address struct {
    Province string
    City     string
}


type User struct {
    Name    string
    Gender  string
    Address Address
}

func main() {
    user1 := User{
        Name:   "pprof",
        Gender: "女",
        Address: Address{
            Province: "黑龙江",
            City:     "哈尔滨",
        },
    }
    fmt.Printf("user1=%#v\n", user1)
}
```

#### 1.3.16. 嵌套匿名结构体 <a id="&#x5D4C;&#x5957;&#x533F;&#x540D;&#x7ED3;&#x6784;&#x4F53;"></a>

```text

type Address struct {
    Province string
    City     string
}


type User struct {
    Name    string
    Gender  string
    Address 
}

func main() {
    var user2 User
    user2.Name = "pprof"
    user2.Gender = "女"
    user2.Address.Province = "黑龙江"    
    user2.City = "哈尔滨"                
    fmt.Printf("user2=%#v\n", user2) 
}
```

当访问结构体成员时会先在结构体中查找该字段，找不到再去匿名结构体中查找。

#### 1.3.17. 嵌套结构体的字段名冲突 <a id="&#x5D4C;&#x5957;&#x7ED3;&#x6784;&#x4F53;&#x7684;&#x5B57;&#x6BB5;&#x540D;&#x51B2;&#x7A81;"></a>

嵌套结构体内部可能存在相同的字段名。这个时候为了避免歧义需要指定具体的内嵌结构体的字段。

```text

type Address struct {
    Province   string
    City       string
    CreateTime string
}


type Email struct {
    Account    string
    CreateTime string
}


type User struct {
    Name   string
    Gender string
    Address
    Email
}

func main() {
    var user3 User
    user3.Name = "pprof"
    user3.Gender = "女"
    
    user3.Address.CreateTime = "2000" 
    user3.Email.CreateTime = "2000"   
}
```

#### 1.3.18. 结构体的“继承” <a id="&#x7ED3;&#x6784;&#x4F53;&#x7684;&#x7EE7;&#x627F;"></a>

Go语言中使用结构体也可以实现其他编程语言中面向对象的继承。

```text

type Animal struct {
    name string
}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)
}


type Dog struct {
    Feet    int8
    *Animal 
}

func (d *Dog) wang() {
    fmt.Printf("%s会汪汪汪~\n", d.name)
}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ 
            name: "乐乐",
        },
    }
    d1.wang() 
    d1.move() 
}
```

#### 1.3.19. 结构体字段的可见性 <a id="&#x7ED3;&#x6784;&#x4F53;&#x5B57;&#x6BB5;&#x7684;&#x53EF;&#x89C1;&#x6027;"></a>

结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。

#### 1.3.20. 结构体与JSON序列化 <a id="&#x7ED3;&#x6784;&#x4F53;&#x4E0E;json&#x5E8F;&#x5217;&#x5316;"></a>

JSON\(JavaScript Object Notation\) 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。JSON键值对是用来保存JS对象的一种方式，键/值对组合中的键名写在前面并用双引号""包裹，使用冒号:分隔，然后紧接着值；多个键值之间使用英文,分隔。

```text

type Student struct {
    ID     int
    Gender string
    Name   string
}


type Class struct {
    Title    string
    Students []*Student
}

func main() {
    c := &Class{
        Title:    "101",
        Students: make([]*Student, 0, 200),
    }
    for i := 0; i < 10; i++ {
        stu := &Student{
            Name:   fmt.Sprintf("stu%02d", i),
            Gender: "男",
            ID:     i,
        }
        c.Students = append(c.Students, stu)
    }
    
    data, err := json.Marshal(c)
    if err != nil {
        fmt.Println("json marshal failed")
        return
    }
    fmt.Printf("json:%s\n", data)
    
    str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
    c1 := &Class{}
    err = json.Unmarshal([]byte(str), c1)
    if err != nil {
        fmt.Println("json unmarshal failed!")
        return
    }
    fmt.Printf("%#v\n", c1)
}
```

#### 1.3.21. 结构体标签（Tag） <a id="&#x7ED3;&#x6784;&#x4F53;&#x6807;&#x7B7E;&#xFF08;tag&#xFF09;"></a>

Tag是结构体的元信息，可以在运行的时候通过反射的机制读取出来。

Tag在结构体字段的后方定义，由一对反引号包裹起来，具体的格式如下：

```text
    `key1:"value1" key2:"value2"`
```

结构体标签由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。键值对之间使用一个空格分隔。 注意事项： 为结构体编写Tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如不要在key和value之间添加空格。

例如我们为Student结构体的每个字段定义json序列化时使用的Tag：

```text

type Student struct {
    ID     int    `json:"id"` 
    Gender string 
    name   string 
}

func main() {
    s1 := Student{
        ID:     1,
        Gender: "女",
        name:   "pprof",
    }
    data, err := json.Marshal(s1)
    if err != nil {
        fmt.Println("json marshal failed!")
        return
    }
    fmt.Printf("json str:%s\n", data) 
}
```

#### 1.3.22. 小练习： <a id="&#x5C0F;&#x7EC3;&#x4E60;&#xFF1A;"></a>

猜一下下列代码运行的结果是什么

```text
package main

import "fmt"

type student struct {
    id   int
    name string
    age  int
}

func demo(ce []student) {
    
    ce[1].age = 999
    
    
}
func main() {
    var ce []student  
    ce = []student{
        student{1, "xiaoming", 22},
        student{2, "xiaozhang", 33},
    }
    fmt.Println(ce)
    demo(ce)
    fmt.Println(ce)
}
```

#### 1.3.23. 删除map类型的结构体 <a id="&#x5220;&#x9664;map&#x7C7B;&#x578B;&#x7684;&#x7ED3;&#x6784;&#x4F53;"></a>

```text
package main

import "fmt"

type student struct {
    id   int
    name string
    age  int
}

func main() {
    ce := make(map[int]student)
    ce[1] = student{1, "xiaolizi", 22}
    ce[2] = student{2, "wang", 23}
    fmt.Println(ce)
    delete(ce, 2)
    fmt.Println(ce)
}
```

#### 1.3.24. 实现map有序输出\(面试经常问到\) <a id="&#x5B9E;&#x73B0;map&#x6709;&#x5E8F;&#x8F93;&#x51FA;&#x9762;&#x8BD5;&#x7ECF;&#x5E38;&#x95EE;&#x5230;"></a>

```text
package main

import (
    "fmt"
    "sort"
)

func main() {
    map1 := make(map[int]string, 5)
    map1[1] = "www.topgoer.com"
    map1[2] = "rpc.topgoer.com"
    map1[5] = "ceshi"
    map1[3] = "xiaohong"
    map1[4] = "xiaohuang"
    sli := []int{}
    for k, _ := range map1 {
        sli = append(sli, k)
    }
    sort.Ints(sli)
    for i := 0; i < len(map1); i++ {
        fmt.Println(map1[sli[i]])
    }
}
```

#### 1.3.25. 小案例 <a id="&#x5C0F;&#x6848;&#x4F8B;"></a>

采用切片类型的结构体接受查询数据库信息返回的参数

地址：[https://github.com/lu569368/struct](https://github.com/lu569368/struct)

