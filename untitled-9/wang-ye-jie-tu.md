# 网页截图

## 1. 网页截图 <a id="&#x7F51;&#x9875;&#x622A;&#x56FE;"></a>

在这篇文章中，我们将研究如何利用[Chrome的调试协议](https://chromedevtools.github.io/devtools-protocol/)来加载网页并截图。通过一个名为的程序包[chromedp](https://github.com/chromedp/chromedp)，一切都可以实现，该程序包使我们可以通过Go代码控制Chrome实例。您还需要安装Chrome或使用类似于[chrome/headless-shellDocker](https://hub.docker.com/r/chromedp/headless-shell/)映像的工具。

我们将代码中的过程分为：

* 启动Chrome
* 运行任务：例如加载网页并截图
* 将屏幕截图保存到文件

```text
package main

import (
    "context"
    "io/ioutil"
    "log"

    "github.com/chromedp/cdproto/page"
    "github.com/chromedp/chromedp"
)

func main() {

    
    
    ctx, cancel := chromedp.NewContext(context.Background(), chromedp.WithDebugf(log.Printf))
    defer cancel()

    url := "https://www.topgoer.com/"
    filename := "topgoer.png"

    
    
    var imageBuf []byte
    if err := chromedp.Run(ctx, ScreenshotTasks(url, &imageBuf)); err != nil {
        log.Fatal(err)
    }

    
    if err := ioutil.WriteFile(filename, imageBuf, 0644); err != nil {
        log.Fatal(err)
    }
}

func ScreenshotTasks(url string, imageBuf *[]byte) chromedp.Tasks {
    return chromedp.Tasks{
        chromedp.Navigate(url),
        chromedp.ActionFunc(func(ctx context.Context) (err error) {
            *imageBuf, err = page.CaptureScreenshot().WithQuality(90).Do(ctx)
            return err
        }),
    }
}
```

另外，如果您想将页面另存为pdf而不是图像，则可以将CaptureScreenshot行替换为以下内容：

```text
    *imageBuf, _, err = page.PrintToPDF().WithPrintBackground(false).Do(ctx)
```

