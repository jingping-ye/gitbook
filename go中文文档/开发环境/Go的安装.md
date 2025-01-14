# Go 的安装

## 1. Go 的安装 <a id="go&#x7684;&#x5B89;&#x88C5;"></a>

### 1.1. 下载地址 <a id="&#x4E0B;&#x8F7D;&#x5730;&#x5740;"></a>

Go 官网下载地址：[https://golang.org/dl/](https://golang.org/dl/) \(打开有点慢\)

### 1.2. Windows 安装 <a id="windows&#x5B89;&#x88C5;"></a>

![go安装包](http://www.topgoer.cn/uploads/golang/images/m_605a82015567e238bdcc26fe40d87a28_r.png)

双击文件

一定要记住这个文件的位置后面还有用

### 1.3. Linux 下安装 <a id="linux&#x4E0B;&#x5B89;&#x88C5;"></a>

1.SSH 远程登录你的 linux 服务器

2.安装 mercurial 包

`[root@localhost ~]# yum install mercurial`

3.安装 git 包

`[root@localhost ~]# yum install git`

4.安装 gcc

`[root@localhost ~]# yum install gcc`

5.下载 Go 的压缩包:\(可选择最新的 Go 版本\)

```text
[root@localhost ~]# cd /usr/local/

[root@localhost local]# wget https://go.googlecode.com/files/go1.13.linux-amd64.tar.gz
```

注意：如果不能翻墙，去 go 语言资源站 下载相应的包。然后通过 ftp 上传到此目录。

6.下载完成 or ftp 上传完成，用 tar 命令来解压压缩包。

`[root@localhost local]# tar -zxvf go1.13.linux-amd64.tar.gz`

7.建立 Go 的工作空间（workspace，也就是 GOPATH 环境变量指向的目录）

GO 代码必须在工作空间内。工作空间是一个目录，其中包含三个子目录：

src ---- 里面每一个子目录，就是一个包。包内是 Go 的源码文件

pkg ---- 编译后生成的，包的目标文件

bin ---- 生成的可执行文件

这里，我们在/home 目录下, 建立一个名为 go\(可以不是 go, 任意名字都可以\)的文件夹， 然后再建立三个子文件夹\(子文件夹名必须为 src、pkg、bin\)。

```text
[root@localhost local]# cd /home/
[root@localhost home]# mkdir go
[root@localhost home]# cd go/
[root@localhost go]# mkdir bin
[root@localhost go]# mkdir src
[root@localhost go]# mkdir pkg
```

8.添加 PATH 环境变量 and 设置 GOPATH 环境变量

`[root@localhost go]# vi /etc/profile`

加入下面这三行:

```text
export GOROOT=/usr/local/go        ##Golang安装目录
export PATH=$GOROOT/bin:$PATH
export GOPATH=/home/go  ##Golang项目目录
```

保存后，执行以下命令，使环境变量立即生效:

`[root@localhost go]# source /etc/profile ##刷新环境变量`

至此，Go 语言的环境已经安装完毕。

9.验证一下是否安装成功，如果出现下面的信息说明安装成功了

```text
[root@localhost go]# go version        ##查看go版本
go version go1.13 linux/amd64
```

10.查看 Go 语言的环境信息

`[root@localhost go]# go env`

### 1.4. Mac 下安装 <a id="mac&#x4E0B;&#x5B89;&#x88C5;"></a>

没有 Mac 没有试过不知道怎么安装

### 1.5. 检查 <a id="&#x68C0;&#x67E5;"></a>

上一步安装过程执行完毕后，可以打开终端窗口，输入`go version`命令，查看安装的 Go 版本。
