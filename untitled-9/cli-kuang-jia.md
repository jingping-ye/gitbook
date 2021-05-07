# CLI框架

## 1. CLI框架 <a id="cli&#x6846;&#x67B6;"></a>

因为机缘巧合，因为希望能在VPS中使用百度网盘，了解到了一个开源的项目BaiduPCS-Go，可以用来直接存取访问百度网盘，做的相当不错

而且看ISSUES，作者可能还是个学生妹子，很强的样子。稍微看了下代码，发现了一个很不错的用来写命令行程序CLI的框架，也是在Github上开源的，因为Golang主要是用来写这个的，所以感觉比较有用的样子，学习一下，并且稍微做了个笔记。

这个框架就是

### 1.1.1. github.com/urfave/cli <a id="githubcomurfavecli"></a>

稍微整理了下具体使用的方式

1，最初的版本，如何引入等等

```text
package main

import (
  "fmt"
  "log"
  "os"
  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()
  app.Name = "boom"
  app.Usage = "make an explosive entrance"
  app.Action = func(c *cli.Context) error {
    fmt.Println("boom! I say!")
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

这里的 app.Action 中间的，就是CLI执行（回车）后的操作。乍看没啥东西，其实这个时候已经封装了 -help -version等等的方法了

执行这个GO程序，控制台输出 boom! I say! 如果执行 main -help 则输出命令行的帮助，类似下面的样子

```text
NAME:
   boom - make an explosive entrance

USAGE:
   002 [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```

2，慢慢深入，接下来有参数，也就是执行的时候 XXX.EXE Arg这个Arg部分如何处理

```text
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Action = func(c *cli.Context) error {
    
    fmt.Printf("Hello %q", c.Args().Get(0))
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

这时候输入 topgoer.exe 1 参数1 ，则控制台返回 hello “1”;

3,关于FLAG

有很多命令行程序有flag 比如linux下的常用的 netstat -lnp 等等

```text
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Flags = []cli.Flag {
    cli.StringFlag{
      Name: "lang",
      Value: "english",
      Usage: "language for the greeting",
    },
  }

  app.Action = func(c *cli.Context) error {
    name := "Nefertiti"
    if c.NArg() > 0 {
      name = c.Args().Get(0)
    }
    if c.String("lang") == "spanish" {
      fmt.Println("Hola", name)
    } else {
      fmt.Println("Hello", name)
    }
    return nil
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

这个时候我们执行 topgoer.exe -lang english 控制台会输出 Hello Nefertiti；执行topgoer -lang spanish，则会输出 Hola Nefertiti。

在程序里，通过 判断 c.String\(“lang”\) 来决定程序分叉

当然程序稍微修改一下，通过一个属性也能对参数赋值

```text
app.Flags = []cli.Flag {
    cli.StringFlag{
      Name:        "lang",
      Value:       "english",
      Usage:       "language for the greeting",
      Destination: &language,         
    },
  }
```

使用的时候只要用 language 来判断就行了

4.关于commond和subcommand

```text
package main

import (
  "fmt"
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()

  app.Commands = []cli.Command{
    {
      Name:    "add",
      Aliases: []string{"a"},
      Usage:   "add a task to the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("added task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:    "complete",
      Aliases: []string{"c"},
      Usage:   "complete a task on the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("completed task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:        "template",
      Aliases:     []string{"t"},
      Usage:       "options for task templates",
      Subcommands: []cli.Command{
        {
          Name:  "add",
          Usage: "add a new template",
          Action: func(c *cli.Context) error {
            fmt.Println("new task template: ", c.Args().First())
            return nil
          },
        },
        {
          Name:  "remove",
          Usage: "remove an existing template",
          Action: func(c *cli.Context) error {
            fmt.Println("removed task template: ", c.Args().First())
            return nil
          },
        },
      },
    },
  }

  err := app.Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

上面的例子里面，罗列了好多个commond以及subcommand，通过定义这些，我们能实现 类似

可执行程序 命令 子命令的操作，比如 App.go add 123 控制台输出 added task: 123

只要遵循框架，这个时候这些命令（Command）以及FLAG的帮助说明都是自动生成的。比如上面这个程序的help，控制台会输出所有使用方式

类似

```text
NAME:
   002 - A new cli application

USAGE:
   002 [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     add, a       add a task to the list
     complete, c  complete a task on the list
     template, t  options for task templates
     help, h      Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```

最后，稍微扩张一下，类似MYSQL，FTP，TELNET等工具，很多控制台程序，都是进入类似一个自己的运行界面，这样其实CLI本身因为并没有中断，可以保存先前操作的信息。

所以比如FTP，TELNET，Mysql这种需要权限的工具，广泛使用。这时，我们往往只需要用户登陆一次，就可以继续执行上传下载查询通讯等等的后续操作。

废话不多说，直接上例子

```text
package main

import (
  "fmt"
  "log"
  "os"
  "strings"
  "bufio"
  "github.com/urfave/cli"
)

func main() {
  app := cli.NewApp()


  app.Action = func(c *cli.Context) {
        if c.NArg() != 0 {
            fmt.Printf("未找到命令: %s\n运行命令 %s help 获取帮助\n", c.Args().Get(0), app.Name)
            return
        }

        var prompt  string

        prompt = app.Name + " > "
        L:
      for {
        var input   string        
            fmt.Print(prompt)
      

        scanner := bufio.NewScanner(os.Stdin)
        scanner.Scan() 
        input = scanner.Text()
        
          switch input {
            case "close":
                fmt.Println("close.")
                break L
            default:
          }
        
              cmdArgs := strings.Split(input, " ")
            
              if len(cmdArgs) == 0 {
                  continue
              }

               s := []string{app.Name}
               s = append(s,cmdArgs...)

              c.App.Run(s)

      }

  return 
  }

  app.Commands = []cli.Command{
    {
      Name:    "add",
      Aliases: []string{"a"},
      Usage:   "add a task to the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("added task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:    "complete",
      Aliases: []string{"c"},
      Usage:   "complete a task on the list",
      Action:  func(c *cli.Context) error {
        fmt.Println("completed task: ", c.Args().First())
        return nil
      },
    },
    {
      Name:        "template",
      Aliases:     []string{"t"},
      Usage:       "options for task templates",
      Subcommands: []cli.Command{
        {
          Name:  "add",
          Usage: "add a new template",
          Action: func(c *cli.Context) error {
            fmt.Println("new task template: ", c.Args().First())
            return nil
          },
        },
        {
          Name:  "remove",
          Usage: "remove an existing template",
          Action: func(c *cli.Context) error {
            fmt.Println("removed task template: ", c.Args().First())
            return nil
          },
        },
      },
    },
  }     

  err := app.Run(os.Args)

  if err != nil {
    log.Fatal(err)
  }
}
```

这里在Action里面，其实加入了一个死循环，一直在听取程序的输入，直接执行（回车）的话，其实进入一个类似MySQL或者FTP类似的命令行界面。等待用户的进一步输入。 然后读取这个输入，通过调用原来的框架的app.Run\(os.Args\)来处理需求逻辑。

上述所有基本涵盖了命令行的所有可能的形式，以后就可以按照这个框架写出起码帮助感觉很正式的程序了。

转自：[http://www.sz-ming.com/2018/06/20/golang%E7%9A%84%E4%B8%80%E4%B8%AAcli%E6%A1%86%E6%9E%B6%E4%BB%8B%E7%BB%8D%EF%BC%8C%E4%B8%AA%E4%BA%BA%E5%AD%A6%E4%B9%A0%E5%A4%87%E5%BF%98/](http://www.sz-ming.com/2018/06/20/golang%E7%9A%84%E4%B8%80%E4%B8%AAcli%E6%A1%86%E6%9E%B6%E4%BB%8B%E7%BB%8D%EF%BC%8C%E4%B8%AA%E4%BA%BA%E5%AD%A6%E4%B9%A0%E5%A4%87%E5%BF%98/)

