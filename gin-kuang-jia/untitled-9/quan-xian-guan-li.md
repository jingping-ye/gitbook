# 权限管理

## 1. 权限管理 <a id="&#x6743;&#x9650;&#x7BA1;&#x7406;"></a>

Casbin是用于Golang项目的功能强大且高效的开源访问控制库。

### 1.1.1. 特征 <a id="&#x7279;&#x5F81;"></a>

Casbin的作用：

* 以经典{subject, object, action}形式或您定义的自定义形式实施策略，同时支持允许和拒绝授权。
* 处理访问控制模型及其策略的存储。
* 管理角色用户映射和角色角色映射（RBAC中的角色层次结构）。
* 支持内置的超级用户，例如root或administrator。超级用户可以在没有显式权限的情况下执行任何操作。
* 多个内置运算符支持规则匹配。例如，keyMatch可以将资源键映射/foo/bar到模式`/foo*`。

Casbin不执行的操作：

* 身份验证（又名验证username以及password用户登录时）
* 管理用户或角色列表。我相信项目本身管理这些实体会更方便。用户通常具有其密码，而Casbin并非设计为密码容器。但是，Casbin存储RBAC方案的用户角色映射。

### 1.1.2. 怎么运行的 <a id="&#x600E;&#x4E48;&#x8FD0;&#x884C;&#x7684;"></a>

在Casbin中，基于PERM元模型（策略，效果，请求，匹配器）将访问控制模型抽象为CONF文件。因此，切换或升级项目的授权机制就像修改配置一样简单。您可以通过组合可用的模型来定制自己的访问控制模型。例如，您可以在一个模型中同时获得RBAC角色和ABAC属性，并共享一组策略规则。

Casbin中最基本，最简单的模型是ACL。ACL的CONF模型为：

```text
＃请求定义
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[role_definition]
g = _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```

ACL模型的示例策略如下：

```text
p, alice, data1, read
p, bob, data2, write
```

### 1.1.3. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get github.com/casbin/casbin
```

### 1.1.4. 示例代码 <a id="&#x793A;&#x4F8B;&#x4EE3;&#x7801;"></a>

```text
package main

import (
    "fmt"
    "log"

    "github.com/casbin/casbin"
    xormadapter "github.com/casbin/xorm-adapter"
    "github.com/gin-gonic/gin"
    _ "github.com/go-sql-driver/mysql"
)

func main() {
    
    a, err := xormadapter.NewAdapter("mysql", "root:root@tcp(127.0.0.1:3306)/goblog?charset=utf8", true)
    if err != nil {
        log.Printf("连接数据库错误: %v", err)
        return
    }
    e, err := casbin.NewEnforcer("./rbac_models.conf", a)
    if err != nil {
        log.Printf("初始化casbin错误: %v", err)
        return
    }
    
    e.LoadPolicy()

    
    r := gin.New()

    r.POST("/api/v1/add", func(c *gin.Context) {
        fmt.Println("增加Policy")
        if ok, _ := e.AddPolicy("admin", "/api/v1/hello", "GET"); !ok {
            fmt.Println("Policy已经存在")
        } else {
            fmt.Println("增加成功")
        }
    })
    
    r.DELETE("/api/v1/delete", func(c *gin.Context) {
        fmt.Println("删除Policy")
        if ok, _ := e.RemovePolicy("admin", "/api/v1/hello", "GET"); !ok {
            fmt.Println("Policy不存在")
        } else {
            fmt.Println("删除成功")
        }
    })
    
    r.GET("/api/v1/get", func(c *gin.Context) {
        fmt.Println("查看policy")
        list := e.GetPolicy()
        for _, vlist := range list {
            for _, v := range vlist {
                fmt.Printf("value: %s, ", v)
            }
        }
    })
    
    r.Use(Authorize(e))
    
    r.GET("/api/v1/hello", func(c *gin.Context) {
        fmt.Println("Hello 接收到GET请求..")
    })

    r.Run(":9000") 
}


func Authorize(e *casbin.Enforcer) gin.HandlerFunc {

    return func(c *gin.Context) {

        
        obj := c.Request.URL.RequestURI()
        
        act := c.Request.Method
        
        sub := "admin"

        
        if ok, _ := e.Enforce(sub, obj, act); ok {
            fmt.Println("恭喜您,权限验证通过")
            c.Next()
        } else {
            fmt.Println("很遗憾,权限验证没有通过")
            c.Abort()
        }
    }
}
```

rbac\_models.conf里面的内容如下：

```text
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[role_definition]
g = _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```

配置链接数据库不需要手动创建数据库，系统自动创建`casbin_rule`表

使用postman请求[http://localhost:9000/api/v1/hello](http://localhost:9000/api/v1/hello)

运行解决结果显示为`很遗憾,权限验证没有通过`

下面我在数据表中添加数据在演示的时候可以直接手动按照图片的格式直接添加数据表，或者使用postman POST方式请求[http://localhost:9000/api/v1/add](http://localhost:9000/api/v1/add)

然后继续请求[http://localhost:9000/api/v1/hello](http://localhost:9000/api/v1/hello)

