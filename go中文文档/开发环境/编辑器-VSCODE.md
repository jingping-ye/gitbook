# 编辑器

## 1. 编辑器

### 1.1. Windows 安装 vs code\(mac 版咱也没有电脑咱也不敢试\) <a id="windows-&#x5B89;&#x88C5;vs-codemac&#x7248;&#x54B1;&#x4E5F;&#x6CA1;&#x6709;&#x7535;&#x8111;&#x54B1;&#x4E5F;&#x4E0D;&#x6562;&#x8BD5;"></a>

`Visual Studio Code`，简称`VS Code`，它是目前使用人数最多的编辑器。尽管它由微软发布于 2015 年，与其他热门编辑器相比显得有些年轻，但它在过去几年中一直在不停的更新，它在最新的`Stack Overflow`调查中被选为`Web`开发人员中最受欢迎的文本编辑器。

`VS Code`不仅仅是一个基本的代码编辑器。有人说它更像是`IDE`而不是代码编辑器，因为它提供了许多通常只在`IDE`中才有的功能。主要功能包括内置调试工具，智能代码提示，集成终端以及对简易的`Git`操作（微软刚收购了`GitHub`）。作为初学者，您可以利用这些功能大大提高编程效率。

在 `VS Code`中找到的每个功能都完成一项出色的工作，构建了一些简单的功能集，包括语法高亮、智能补全、集成 `git` 和编辑器内置调试工具等，将使你开发更高效。

下载地址：[https://code.visualstudio.com/](https://code.visualstudio.com/)

选择 windows 版本下载，vscode 有新版本时候会自动更新，重启即可更新。

傻瓜式安装一直下一步就好了！

### 1.2. 配置 <a id="&#x914D;&#x7F6E;"></a>

#### 1.2.1. 安装中文简体插件 <a id="&#x5B89;&#x88C5;&#x4E2D;&#x6587;&#x7B80;&#x4F53;&#x63D2;&#x4EF6;"></a>

点击左侧菜单栏最后一项`管理扩展`，在搜索框中输入`chinese`，选中结果列表第一项，点击`install`安装。

安装完毕后右下角会提示重启`VS Code`，重启之后你的`VS Code`就显示中文啦！

#### 1.2.2. 安装 go 插件 <a id="&#x5B89;&#x88C5;go&#x63D2;&#x4EF6;"></a>

启动`vscode`选择插件-&gt;搜`go`选择`Go for Visual Studio Code`插件点击安装即可。如图：

### 1.3. 安装 Go 语言开发工具包 <a id="&#x5B89;&#x88C5;go&#x8BED;&#x8A00;&#x5F00;&#x53D1;&#x5DE5;&#x5177;&#x5305;"></a>

在 Go 语言开发的时候为我们提供诸如代码提示、代码自动补全等功能。

Windows 平台按下`Ctrl+Shift+P`，Mac 平台按`Command+Shift+P`，这个时候`VS Code`界面会弹出一个输入框，如下图：

我们在这个输入框中输入&gt;`go:install`，下面会自动搜索相关命令，我们选择`Go:Install/Update Tools`这个命令

选中并会回车执行该命令（或者使用鼠标点击该命令）

VS Code 此时会下载并安装上图列出来的 16 个工具，但是由于国内的网络环境基本上都会出现安装失败

#### 1.3.1. 有两种方法解决这个问题：

**方法一：使用 git 下载源代码再安装**

我们可以手动从`github`上下载工具，\(执行此步骤前提需要你的电脑上已经安装了`git`\)

第一步：现在自己的`GOPATH`的`src`目录下创建`golang.org/x`目录

第二步：在终端`/cmd中cd`到`GOPATH/src/golang.org/x`目录下

第三步：执行`git clone https://github.com/golang/tools.git tools`命令

第四步：执行`git clone https://github.com/golang/lint.git`命令

第五步：按下`Ctrl/Command+Shift+P`再次执行`Go:Install/Update Tools`命令，在弹出的窗口全选并点击确定，这一次的安装都会 SUCCESSED 了。

经过上面的步骤就可以安装成功了。 这个时候创建一个 Go 文件，就能正常使用代码提示、代码格式化等工具了。

方法二：下载已经编译好的可执行文件 如果上面的步骤执行失败了或者懒得一步一步执行，可以直接下载我已经编译好的可执行文件，拷贝到自己电脑上的 `go/bin`\(GO 的安装包目录不是代码包啊\) 目录下。 `https://pan.baidu.com/s/180J5j0n5Kt6wANjQyGdWDA`，密码:dxti。

注意：特别是 Mac 下需要给拷贝的这些文件赋予可执行的权限。

#### 1.3.2. 修改 vscode 终端 cmd 启动

在运行代码的时候需要终端运行，有的小伙伴终端默认的是 powershell，有的直接默认是 cmd，如果你的是 powershell 需要修改为 cmd，如果默认的就是 cmd 直接放弃这块就好了

1.在文件 -&gt; 首选项 -&gt; 设置中打开 settings 页面, 搜索 shell 或则找到 Terminal&gt;Integrated&gt;Shell:Windows,

添加`"terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\cmd.exe",` 后面的地址是你的 cmd 地址
