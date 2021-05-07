# 基于角色的访问控制框架

## 1. 基于角色的访问控制框架 <a id="&#x57FA;&#x4E8E;&#x89D2;&#x8272;&#x7684;&#x8BBF;&#x95EE;&#x63A7;&#x5236;&#x6846;&#x67B6;"></a>

Grbac是一个快速，优雅和简洁的[RBAC](https://en.wikipedia.org/wiki/Role-based_access_control)框架。它支持[增强的通配符](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#4-enhanced-wildcards)并使用[Radix](https://en.wikipedia.org/wiki/Radix)树匹配HTTP请求。令人惊奇的是，您可以在任何现有的数据库和数据结构中轻松使用它。

项目地址：[https://github.com/storyicon/grbac](https://github.com/storyicon/grbac)

grbac的作用是确保指定的资源只能由指定的角色访问。请注意，grbac不负责存储鉴权规则和分辨“当前请求发起者具有哪些角色”，更不负责角色的创建、分配等。这意味着您应该首先配置规则信息，并提供每个请求的发起者具有的角色。

grbac将`Host`、`Path`和`Method`的组合视为`Resource`，并将`Resource`绑定到一组角色规则（称为`Permission`）。只有符合这些规则的用户才能访问相应的`Resource`。

读取鉴权规则的组件称为`Loader`。grbac预置了一些`Loader`，你也可以通过实现`func()(grbac.Rules，error)`来根据你的设计来自定义`Loader`，并通过`grbac.WithLoader`加载它。

* [1. 最常见的用例](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#1-最常见的用例)
* [2. 概念](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#2-概念)
  * [2.1. Rule](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#21-rule)
  * [2.2. Resource](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#22-resource)
  * [2.3. Permission](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#23-permission)
  * [2.4. Loader](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#24-loader)
* [3. 其他例子](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#3-其他例子)
  * [3.1. gin && grbac.WithJSON](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#31-gin--grbacwithjson)
  * [3.2. echo && grbac.WithYaml](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#32-echo--grbacwithyaml)
  * [3.3. iris && grbac.WithRules](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#33-iris--grbacwithrules)
  * [3.4. ace && grbac.WithAdvancedRules](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#34-ace--grbacwithadvancedrules)
  * [3.5. gin && grbac.WithLoader](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#35-gin--grbacwithloader)
* [4. 增强的通配符](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#4-增强的通配符)
* [5. 运行效率](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#5-运行效率)
* [6. 生产环境](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#6-生产环境)

### 1.1. 1. 最常见的用例 <a id="1-&#x6700;&#x5E38;&#x89C1;&#x7684;&#x7528;&#x4F8B;"></a>

下面是最常见的用例，它使用`gin`，并将`grbac`包装成了一个中间件。通过这个例子，你可以很容易地知道如何在其他http框架中使用`grbac`（比如`echo`，`iris`，`ace`等）：

```text
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/storyicon/grbac"
    "net/http"
    "time"
)

func LoadAuthorizationRules() (rules grbac.Rules, err error) {
    
    
    
    
    
    return
}

func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

func Authorization() gin.HandlerFunc {
    
    
    
    
    
    
    
    rbac, err := grbac.New(grbac.WithLoader(LoadAuthorizationRules, time.Minute))
    if err != nil {
        panic(err)
    }
    return func(c *gin.Context) {
        roles, err := QueryRolesByHeaders(c.Request.Header)
        if err != nil {
            c.AbortWithError(http.StatusInternalServerError, err)
            return
        }
        state, _ := rbac.IsRequestGranted(c.Request, roles)
        if !state.IsGranted() {
            c.AbortWithStatus(http.StatusUnauthorized)
            return
        }
    }
}

func main(){
    c := gin.New()
    c.Use(Authorization())

    
    

    c.Run(":8080")
}
```

### 1.2. 2. 概念 <a id="2-&#x6982;&#x5FF5;"></a>

这里有一些关于`grbac`的概念。这很简单，你可能只需要三分钟就能理解。

#### 1.2.1. 2.1. Rule <a id="21-rule"></a>

```text

type Rule struct {
    
    
    
    
    ID int `json:"id"`
    *Resource
    *Permission
}
```

如你所见，`Rule`由三部分组成：`ID`，`Resource`和`Permission`。 “ID”确定规则的优先级。 当请求同时满足多个规则时（例如在通配符中）， `grbac`将选择具有最高ID的那个，然后使用其权限定义进行身份验证。 如果有多个规则同时具有最大的ID，则将随机使用其中一个规则（所以请避免这种情况）。

下面有一个非常简单的例子：

```text

- id: 0
  
  host: "*"
  path: "**"
  method: "*"
  
  authorized_roles:
  - "*"
  forbidden_roles: []
  allow_anyone: false


- id: 1
  
  host: domain.com
  path: "/article"
  method: "{DELETE,POST,PUT}"
  
  authorized_roles:
  - editor
  forbidden_roles: []
  allow_anyone: false
```

在以yaml格式编写的此配置文件中，ID=0 的规则表明任何具有任何角色的人都可以访问所有资源。 但是ID=1的规则表明只有`editor`可以对文章进行增删改操作。 这样，除了文章的操作只能由`editor`访问之外，任何具有任何角色的人都可以访问所有其他资源。

#### 1.2.2. 2.2. Resource <a id="22-resource"></a>

```text
type Resource struct {
    
    Host string `json:"host"`
    
    Path string `json:"path"`
    
    Method string `json:"method"`
}
```

Resource用于描述Rule适用的资源。 当执行`IsRequestGranted(c.Request，roles)`时，grbac首先将当前的`Request`与所有`Rule`中的`Resources`匹配。

Resource的每个字段都支持[增强的通配符](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#4-enhanced-wildcards)

#### 1.2.3. 2.3. Permission <a id="23-permission"></a>

```text

type Permission struct {
    
    
    
    
    AuthorizedRoles []string `json:"authorized_roles"`
    
    
    
    
    
    
    ForbiddenRoles []string `json:"forbidden_roles"`
    
    
    
    AllowAnyone bool `json:"allow_anyone"`
}
```

“Permission”用于定义绑定到的“Resource”的授权规则。 这是易于理解的，当请求者的角色符合“Permission”的定义时，他将被允许访问Resource，否则他将被拒绝访问。

为了加快验证的速度，`Permission`中的字段不支持“增强的通配符”。 在`AuthorizedRoles`和`ForbiddenRoles`中只允许`*`表示所有。

#### 1.2.4. 2.4. Loader <a id="24-loader"></a>

Loader用于加载Rule。 grbac预置了一些加载器，你也可以通过实现`func()(grbac.Rules, error)` 来自定义加载器并通过 `grbac.WithLoader` 加载它。

| method | description |
| :--- | :--- |
| WithJSON\(path, interval\) | 定期从`json`文件加载规则配置 |
| WithYaml\(path, interval\) | 定期从`yaml`文件加载规则配置 |
| WithRules\(Rules\) | 从`grbac.Rules`加载规则配置 |
| WithAdvancedRules\(loader.AdvancedRules\) | 以一种更紧凑的方式定义Rule，并使用`loader.AdvancedRules`加载 |
| WithLoader\(loader func\(\)\(Rules, error\), interval\) | 使用自定义函数定期加载规则 |

`interval`定义了Rules的重载周期。 当`interval <0`时，`grbac`会放弃周期加载Rules配置; 当`interval∈[0,1s）`时，`grbac`会自动将`interval`设置为`5s`;

### 1.3. 3. 其他例子 <a id="3-&#x5176;&#x4ED6;&#x4F8B;&#x5B50;"></a>

这里有一些简单的例子，可以让你更容易理解`grbac`的工作原理。 虽然`grbac`在大多数http框架中运行良好，但很抱歉我现在只使用gin，所以如果下面的例子中有一些缺陷，请告诉我。

#### 1.3.1. 3.1. gin && grbac.WithJSON <a id="31-gin--grbacwithjson"></a>

如果你想在`JSON`文件中编写配置文件，你可以通过`grbac.WithJSON(filepath，interval)`加载它，`filepath`是你的json文件路径，并且grbac将每隔interval重新加载一次文件。 。

```text
[
    {
        "id": 0,
        "host": "*",
        "path": "**",
        "method": "*",
        "authorized_roles": [
            "*"
        ],
        "forbidden_roles": [
            "black_user"
        ],
        "allow_anyone": false
    },
    {
        "id":1,
        "host": "domain.com",
        "path": "/article",
        "method": "{DELETE,POST,PUT}",
        "authorized_roles": ["editor"],
        "forbidden_roles": [],
        "allow_anyone": false
    }
]
```

以上是“JSON”格式的身份验证规则示例。它的结构基于[grbac.Rules](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#21-rule)。

```text

func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

func Authentication() gin.HandlerFunc {
    rbac, err := grbac.New(grbac.WithJSON("config.json", time.Minute * 10))
    if err != nil {
        panic(err)
    }
    return func(c *gin.Context) {
        roles, err := QueryRolesByHeaders(c.Request.Header)
        if err != nil {
            c.AbortWithError(http.StatusInternalServerError, err)
            return
        }

        state, err := rbac.IsRequestGranted(c.Request, roles)
        if err != nil {
            c.AbortWithStatus(http.StatusInternalServerError)
            return
        }

        if !state.IsGranted() {
            c.AbortWithStatus(http.StatusUnauthorized)
            return
        }
    }
}

func main(){
    c := gin.New()
    c.Use(Authentication())

    
    

    c.Run(":8080")
}
```

#### 1.3.2. 3.2. echo && grbac.WithYaml <a id="32-echo--grbacwithyaml"></a>

如果你想在`YAML`文件中编写配置文件，你可以通过`grbac.WithYAML(file，interval)`加载它，`file`是你的yaml文件路径，并且grbac将每隔一个interval重新加载一次文件。

```text

- id: 0
  
  host: "*"
  path: "**"
  method: "*"
  
  authorized_roles:
  - "*"
  forbidden_roles: []
  allow_anyone: false


- id: 1
  
  host: domain.com
  path: "/article"
  method: "{DELETE,POST,PUT}"
  
  authorized_roles:
  - editor
  forbidden_roles: []
  allow_anyone: false
```

以上是“YAML”格式的认证规则的示例。它的结构基于[grbac.Rules](ji-yu-jiao-se-de-fang-wen-kong-zhi-kuang-jia.md#21-rule)。

```text
func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

func Authentication() echo.MiddlewareFunc {
    rbac, err := grbac.New(grbac.WithYAML("config.yaml", time.Minute * 10))
    if err != nil {
            panic(err)
    }
    return func(echo.HandlerFunc) echo.HandlerFunc {
        return func(c echo.Context) error {
            roles, err := QueryRolesByHeaders(c.Request().Header)
            if err != nil {
                    c.NoContent(http.StatusInternalServerError)
                    return nil
            }
            state, err := rbac.IsRequestGranted(c.Request(), roles)
            if err != nil {
                    c.NoContent(http.StatusInternalServerError)
                    return nil
            }
            if state.IsGranted() {
                    return nil
            }
            c.NoContent(http.StatusUnauthorized)
            return nil
        }
    }
}

func main(){
    c := echo.New()
    c.Use(Authentication())

    
    

}
```

#### 1.3.3. 3.3. iris && grbac.WithRules <a id="33-iris--grbacwithrules"></a>

如果你想直接在代码中编写认证规则，`grbac.WithRules（rules）`提供了这种方式，你可以像这样使用它：

```text

func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

func Authentication() iris.Handler {
    var rules = grbac.Rules{
        {
            ID: 0,
            Resource: &grbac.Resource{
                Host: "*",
                Path: "**",
                Method: "*",
            },
            Permission: &grbac.Permission{
                AuthorizedRoles: []string{"*"},
                ForbiddenRoles: []string{"black_user"},
                AllowAnyone: false,
            },
        },
        {
            ID: 1,
            Resource: &grbac.Resource{
                Host: "domain.com",
                Path: "/article",
                Method: "{DELETE,POST,PUT}",
            },
            Permission: &grbac.Permission{
                AuthorizedRoles: []string{"editor"},
                ForbiddenRoles: []string{},
                AllowAnyone: false,
            },
        },
    }
    rbac, err := grbac.New(grbac.WithRules(rules))
    if err != nil {
        panic(err)
    }
    return func(c context.Context) {
        roles, err := QueryRolesByHeaders(c.Request().Header)
        if err != nil {
            c.StatusCode(http.StatusInternalServerError)
            c.StopExecution()
            return
        }
        state, err := rbac.IsRequestGranted(c.Request(), roles)
        if err != nil {
            c.StatusCode(http.StatusInternalServerError)
            c.StopExecution()
            return
        }
        if !state.IsGranted() {
            c.StatusCode(http.StatusUnauthorized)
            c.StopExecution()
            return
        }
    }
}

func main(){
    c := iris.New()
    c.Use(Authentication())

    
    

}
```

#### 1.3.4. 3.4. ace && grbac.WithAdvancedRules <a id="34-ace--grbacwithadvancedrules"></a>

如果你想直接在代码中编写认证规则，`grbac.WithAdvancedRules(rules)`提供了这种方式，你可以像这样使用它：

```text

func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

func Authentication() ace.HandlerFunc {
    var advancedRules = loader.AdvancedRules{
        {
            Host: []string{"*"},
            Path: []string{"**"},
            Method: []string{"*"},
            Permission: &grbac.Permission{
                AuthorizedRoles: []string{},
                ForbiddenRoles: []string{"black_user"},
                AllowAnyone: false,
            },
        },
        {
            Host: []string{"domain.com"},
            Path: []string{"/article"},
            Method: []string{"PUT","DELETE","POST"},
            Permission: &grbac.Permission{
                AuthorizedRoles: []string{"editor"},
                ForbiddenRoles: []string{},
                AllowAnyone: false,
            },
        },
    }
    auth, err := grbac.New(grbac.WithAdvancedRules(advancedRules))
    if err != nil {
        panic(err)
    }
    return func(c *ace.C) {
        roles, err := QueryRolesByHeaders(c.Request.Header)
        if err != nil {
        c.AbortWithStatus(http.StatusInternalServerError)
            return
        }
        state, err := auth.IsRequestGranted(c.Request, roles)
        if err != nil {
            c.AbortWithStatus(http.StatusInternalServerError)
            return
        }
        if !state.IsGranted() {
            c.AbortWithStatus(http.StatusUnauthorized)
            return
        }
    }
}

func main(){
    c := ace.New()
    c.Use(Authentication())

    
    

}
```

`loader.AdvancedRules`试图提供一种比`grbac.Rules`更紧凑的定义鉴权规则的方法。

#### 1.3.5. 3.5. gin && grbac.WithLoader <a id="35-gin--grbacwithloader"></a>

```text

func QueryRolesByHeaders(header http.Header) (roles []string,err error){
    
    
    
    return roles, err
}

type MySQLLoader struct {
    session *gorm.DB
}

func NewMySQLLoader(dsn string) (*MySQLLoader, error) {
    loader := &MySQLLoader{}
    db, err := gorm.Open("mysql", dsn)
    if err  != nil {
        return nil, err
    }
    loader.session = db
    return loader, nil
}

func (loader *MySQLLoader) LoadRules() (rules grbac.Rules, err error) {
    
    
    
    
    
    return
}

func Authentication() gin.HandlerFunc {
    loader, err := NewMySQLLoader("user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
    if err != nil {
        panic(err)
    }
    rbac, err := grbac.New(grbac.WithLoader(loader.LoadRules, time.Second * 5))
    if err != nil {
        panic(err)
    }
    return func(c *gin.Context) {
        roles, err := QueryRolesByHeaders(c.Request.Header)
        if err != nil {
            c.AbortWithStatus(http.StatusInternalServerError)
            return
        }

        state, err := rbac.IsRequestGranted(c.Request, roles)
        if err != nil {
            c.AbortWithStatus(http.StatusInternalServerError)
            return
        }
        if !state.IsGranted() {
            c.AbortWithStatus(http.StatusUnauthorized)
            return
        }
    }
}

func main(){
    c := gin.New()
    c.Use(Authorization())

    
    

    c.Run(":8080")
}
```

### 1.4. 4. 增强的通配符 <a id="4-&#x589E;&#x5F3A;&#x7684;&#x901A;&#x914D;&#x7B26;"></a>

`Wildcard`支持的语法：

```text
pattern:
  { term }
term:
  '*'         匹配任何非路径分隔符的字符串
  '**'        匹配任何字符串，包括路径分隔符.
  '?'         匹配任何单个非路径分隔符
  '[' [ '^' ] { character-range } ']'
        character class (must be non-empty)
  '{' { term } [ ',' { term } ... ] '}'
  c           匹配字符 c (c != '*', '?', '\\', '[')
  '\\' c      匹配字符 c

character-range:
  c           匹配字符 c (c != '\\', '-', ']')
  '\\' c      匹配字符 c
  lo '-' hi   匹配字符 c for lo <= c <= hi
```

### 1.5. 5. 运行效率 <a id="5-&#x8FD0;&#x884C;&#x6548;&#x7387;"></a>

```text
➜ gos test -bench=. 
goos: linux
goarch: amd64
pkg: github.com/storyicon/grbac/pkg/tree
BenchmarkTree_Query                   2000           541397 ns/op
BenchmarkTree_Foreach_Query           2000           1360719 ns/op
PASS
ok      github.com/storyicon/grbac/pkg/tree     13.182s
```

测试用例包含1000个随机规则，“BenchmarkTree\_Query”和“BenchmarkTree\_Foreach\_Query”函数分别测试四个请求：

```text
541397/(4*1e9)=0.0001s
```

当有1000条规则时，每个请求的平均验证时间为“0.0001s”，这很快（大多数时间在通配符的匹配上）。

### 1.6. 6. 生产环境 <a id="6-&#x751F;&#x4EA7;&#x73AF;&#x5883;"></a>

`grbac` 已经被以下企业用于生产环境:

![wallstreetcn](https://raw.githubusercontent.com/storyicon/grbac/master/docs/screenshot/wallstreetcn.png)

