# 文件监控

## 1. 文件监控 <a id="&#x6587;&#x4EF6;&#x76D1;&#x63A7;"></a>

### 1.1.1. Go的文件系统通知 <a id="go&#x7684;&#x6587;&#x4EF6;&#x7CFB;&#x7EDF;&#x901A;&#x77E5;"></a>

官网地址：[https://github.com/fsnotify/fsnotify](https://github.com/fsnotify/fsnotify)

fsnotify利用golang.org/x/sys而不是syscall从标准库。通过运行以下命令确保已安装最新版本：

```text
    go get -u golang.org/x/sys/...
```

### 1.1.2. 常问问题 <a id="&#x5E38;&#x95EE;&#x95EE;&#x9898;"></a>

当文件移到另一个目录时，仍在监视它吗？

不（不应该这样，除非您正在观看它的移动位置）。

当我查看目录时，是否也监视所有子目录？

不，您必须为要观看的任何目录添加监视（路线图＃18上有一个递归监视程序）。

我是否必须在单独的goroutine中观看错误和事件通道？

截至目前，是的。正在考虑使此单线程友好（请参阅howeyc＃7）

为什么我在OS X上收到同一文件的多个事件？

在OS X上对Spotlight进行索引可能会导致多个事件（请参见howeyc＃62）。临时的解决方法是将您的文件夹添加到Spotlight隐私设置中，直到我们具有本机FSEvents实现（请参阅＃11）。

一次可以查看多少个文件？

对于可创建的手表数量，存在特定于操作系统的限制：

* Linux：/ proc / sys / fs / inotify / max\_user\_watches包含该限制，达到此限制将导致“设备上没有剩余空间”错误。
* BSD / OSX：sysctl变量“ kern.maxfiles”和“ kern.maxfilesperproc”达到这些限制会导致“打开的文件太多”错误。

为什么通知不能与NFS文件系统或用户空间（FUSE）中的文件系统一起使用？

fsnotify需要底层操作系统的支持才能正常工作。当前的NFS协议不为文件通知提供网络级别的支持。

```text
package main

import (
    "fmt"
    "log"
    "os"
    "path/filepath"

    "github.com/fsnotify/fsnotify"
)

func main() {
    watch, _ := fsnotify.NewWatcher()
    w := Watch{
        watch: watch,
    }
    w.watchDir("./web")
    select {}
}

type Watch struct {
    watch *fsnotify.Watcher
}

func (w *Watch) watchDir(dir string) {
    filepath.Walk(dir, func(path string, info os.FileInfo, err error) error {
        if info.IsDir() {
            path, err := filepath.Abs(path)
            if err != nil {
                return err
            }
            err = w.watch.Add(path)
            if err != nil {
                return err
            }
        }
        return nil
    })
    log.Println("监控服务已经启动")
    go func() {
        for {
            select {
            case ev := <-w.watch.Events:
                {
                    if ev.Op&fsnotify.Create == fsnotify.Create {
                        fmt.Println("创建文件 : ", ev.Name)
                        fi, err := os.Stat(ev.Name)
                        if err == nil && fi.IsDir() {
                            w.watch.Add(ev.Name)
                            fmt.Println("添加监控 : ", ev.Name)
                        }
                    }
                    if ev.Op&fsnotify.Write == fsnotify.Write {
                        fmt.Println("写入文件 : ", ev.Name)
                    }
                    if ev.Op&fsnotify.Remove == fsnotify.Remove {
                        fmt.Println("删除文件 : ", ev.Name)
                        fi, err := os.Stat(ev.Name)
                        if err == nil && fi.IsDir() {
                            w.watch.Remove(ev.Name)
                            fmt.Println("删除监控 : ", ev.Name)
                        }
                    }
                    if ev.Op&fsnotify.Rename == fsnotify.Rename {
                        fmt.Println("重命名文件 : ", ev.Name)
                        w.watch.Remove(ev.Name)
                    }
                }
            case err := <-w.watch.Errors:
                {
                    fmt.Println("error : ", err)
                    return
                }
            }
        }
    }()
}
```

