# go-admin

## 1. go-admin <a id="go-admin"></a>

### 1.1.1. 前言 <a id="&#x524D;&#x8A00;"></a>

GoAdmin 可以帮助你的golang应用快速实现数据可视化，搭建一个数据管理平台。

demo: [https://demo.go-admin.cn](https://demo.go-admin.cn/) 账号：admin 密码：admin

代码地址： [https://github.com/GoAdminGroup/go-admin](https://github.com/GoAdminGroup/go-admin)

![](http://file.go-admin.cn/introduction/interface_2.png)

### 1.1.2. 特征 <a id="&#x7279;&#x5F81;"></a>

* 🚀 **高生产效率**: 10分钟内做一个好看的管理后台
* 🎨 **主题**: 默认为adminlte，更多好看的主题正在制作中，欢迎给我们留言
* 🔢 **插件化**: 提供插件使用，真正实现一个插件解决不了问题，那就两个
* ✅ **认证**: 开箱即用的rbac认证系统
* ⚙️ **框架支持**: 支持大部分框架接入，让你更容易去上手和扩展

### 1.1.3. 使用 <a id="&#x4F7F;&#x7528;"></a>

通过以下三步运行：

#### 第一步：导入 sql <a id="&#x7B2C;&#x4E00;&#x6B65;&#xFF1A;&#x5BFC;&#x5165;-sql"></a>

[mysql](https://raw.githubusercontent.com/GoAdminGroup/go-admin/master/data/admin.sql) [postgresql](https://raw.githubusercontent.com/GoAdminGroup/go-admin/master/data/admin.pgsql) [sqlite](https://raw.githubusercontent.com/GoAdminGroup/go-admin/master/data/admin.db)

#### 第二步：创建 main.go <a id="&#x7B2C;&#x4E8C;&#x6B65;&#xFF1A;&#x521B;&#x5EFA;-maingo"></a>

```text
package main

import (
    "github.com/gin-gonic/gin"
    _ "github.com/GoAdminGroup/go-admin/adapter/gin"
    _ "github.com/GoAdminGroup/go-admin/modules/db/drivers/mysql"
    "github.com/GoAdminGroup/go-admin/engine"
    "github.com/GoAdminGroup/go-admin/plugins/admin"
    "github.com/GoAdminGroup/themes/adminlte"
    "github.com/GoAdminGroup/go-admin/modules/config"
    "github.com/GoAdminGroup/go-admin/template"
        "github.com/GoAdminGroup/go-admin/template/chartjs"
        "github.com/GoAdminGroup/go-admin/template/types"
    "github.com/GoAdminGroup/go-admin/examples/datamodel"
    "github.com/GoAdminGroup/go-admin/modules/language"
)

func main() {
    r := gin.Default()

    eng := engine.Default()

    
    cfg := config.Config{
        Databases: config.DatabaseList{
            "default": {
            Host:         "127.0.0.1",
            Port:         "3306",
            User:         "root",
            Pwd:          "root",
            Name:         "godmin",
            MaxIdleCon: 50,
            MaxOpenCon: 150,
            Driver:       "mysql",
            },
            },
        UrlPrefix: "admin",
        
        Store: config.Store{
            Path:   "./uploads",
            Prefix: "uploads",
        },
        Language: language.CN, 
        
                Debug: true,
                
                InfoLogPath: "/var/logs/info.log",
                AccessLogPath: "/var/logs/access.log",
                ErrorLogPath: "/var/logs/error.log",
                ColorScheme: adminlte.ColorschemeSkinBlack,
    }

        
    adminPlugin := admin.NewAdmin(datamodel.Generators)

    
    template.AddComp(chartjs.NewChart())

    
    
    
    
    
    

    

            r.GET("/admin", func(ctx *gin.Context) {
                engine.Content(ctx, func(ctx interface{}) (types.Panel, error) {
                    return datamodel.GetContent()
                })
            })

    _ = eng.AddConfig(cfg).AddPlugins(adminPlugin).Use(r)

    _ = r.Run(":9033")
}
```

其他框架的例子: [https://github.com/GoAdminGroup/go-admin/tree/master/examples](https://github.com/GoAdminGroup/go-admin/tree/master/examples)

#### 第三步：运行 <a id="&#x7B2C;&#x4E09;&#x6B65;&#xFF1A;&#x8FD0;&#x884C;"></a>

```text
GO111MODULE=on go run main.go
```

访问：[http://localhost:9033/admin](http://localhost:9033/admin)

更多细节详见 [文档说明](http://www.go-admin.cn/docs/#/README)

[这里一个超级简单上手的例子](https://github.com/GoAdminGroup/example)

