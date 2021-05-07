# 高效读取配置信息

## 1. 高效读取配置信息 <a id="&#x9AD8;&#x6548;&#x8BFB;&#x53D6;&#x914D;&#x7F6E;&#x4FE1;&#x606F;"></a>

### 1.1.1. 开始使用 <a id="&#x5F00;&#x59CB;&#x4F7F;&#x7528;"></a>

我们将通过一个非常简单的例子来了解如何使用。

首先，我们需要在任意目录创建两个文件（my.ini 和 main.go），在这里我们选择 /tmp/ini 目录。

```text
    $ mkdir -p /tmp/ini
    $ cd /tmp/ini
    $ touch my.ini main.go
    $ tree .
    .
    ├── main.go
    └── my.ini

    0 directories, 2 files
```

现在，我们编辑 my.ini 文件并输入以下内容（_部分内容来自 Grafana_）。

```text
    # possible values : production, development
    app_mode = development

    [paths]
    # Path to where grafana can store temp files, sessions, and the sqlite3 db (if that is used)
    data = /home/git/grafana

    [server]
    # Protocol (http or https)
    protocol = http

    # The http port  to use
    http_port = 9999

    # Redirect to correct domain if host header does not match domain
    # Prevents DNS rebinding attacks
    enforce_domain = true
```

很好，接下来我们需要编写 main.go 文件来操作刚才创建的配置文件。

```text
package main

import (
    "fmt"
    "os"

    "gopkg.in/ini.v1"
)

func main() {
    cfg, err := ini.Load("my.ini")
    if err != nil {
        fmt.Printf("Fail to read file: %v", err)
        os.Exit(1)
    }

    
    fmt.Println("App Mode:", cfg.Section("").Key("app_mode").String())
    fmt.Println("Data Path:", cfg.Section("paths").Key("data").String())

    
    fmt.Println("Server Protocol:",
        cfg.Section("server").Key("protocol").In("http", []string{"http", "https"}))
    
    fmt.Println("Email Protocol:",
        cfg.Section("server").Key("protocol").In("smtp", []string{"imap", "smtp"}))

    
    fmt.Printf("Port Number: (%[1]T) %[1]d\n", cfg.Section("server").Key("http_port").MustInt(9999))
    fmt.Printf("Enforce Domain: (%[1]T) %[1]v\n", cfg.Section("server").Key("enforce_domain").MustBool(false))

    
    cfg.Section("").Key("app_mode").SetValue("production")
    cfg.SaveTo("my.ini.local")
}
```

运行程序，我们可以看下以下输出：

```text
    $ go run main.go
    App Mode: development
    Data Path: /home/git/grafana
    Server Protocol: http
    Email Protocol: smtp
    Port Number: (int) 9999
    Enforce Domain: (bool) true

    $ cat my.ini.local
    # possible values : production, development
    app_mode = production

    [paths]
    # Path to where grafana can store temp files, sessions, and the sqlite3 db (if that is used)
    data = /home/git/grafana
    ...
```

### 1.1.2. 高级用法 <a id="&#x9AD8;&#x7EA7;&#x7528;&#x6CD5;"></a>

我写了一个demo如果需要可以下载 [高级用法](https://github.com/lu569368/configini)

这个只是这个包功能的很小一部分！如果想获取更多的功能可以查看[官网文档](https://ini.unknwon.io/docs)

