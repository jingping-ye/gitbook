# 配置 GOPATH

## 1. GOPATH 是什么?

> 简而言之，GOPATH 是放置所有 GO 项目的位置

- `GOPATH`是一个环境变量，用来表明你写的`go`项目的存放路径
- `GOPATH`路径最好只设置一个，所有的项目代码都放到`GOPATH`的`src`目录下。

## 2. 配置 GOPATH

1. 进入位置：我的电脑-属性-高级系统设置-环境变量-系统变量，添加变量`GOPATH=$yourpath`
2. 点击系统变量中的`Path`变量，添加`$yourGoInstallPath\bin`和上述 GOPATH 指向的项目目录地址

## 3.GO 项目目录

### 3.1 必须的 GO 项目目录结构

GO 要求在`GOPATH`下，必须存在以下目录：

```bash
$gopath
├─ bin # 用来存放编译后的 二进制文件(.exe)
├─ pkg # 用来存放编译后的库文件
└─ src #项目源码
```

使用源代码管理工具时，只需要管理`src`下的源代码即可

### 3.2 典型目录结构：通用

因为可以引用其他的包，也可以自己开发包，为了防止冲突，通常使用顶级域名来作为包名的前缀，一般使用 github 的个人地址来区分。

这样，当引入相同的包时，那么，其目录为

```go
import "github.com/zhangsan/studygo"
import "github.com/lisi/studygo"
```

```bash
$gopath
├─ bin
├─ pkg
└─ src
   ├─ github.com #张三
   │  ├─ project01  # 项目1
   │  │  ├─ module01 # 模块1
   │  │  └─ module02
   │  └─ project02
   │     ├─ module01
   │     └─ module03
   └─ github.com # 李四
      ├─ project01
      │  ├─ module01
      │  └─ module02
      └─ project02
         ├─ module01
         └─ module02

```

### 3.2 典型目录结构：个人开发者

```bash
$gopath
├─ bin
├─ pkg
└─ src # 项目源码
   ├─ project01 # 项目1
   │  ├─ module01 # 模块1
   │  └─ module02
   ├─ project02
   │  ├─ module01
   │  └─ module02
   └─ project03
      ├─ module01
      └─ module02
```

### 3.3 典型目录结构：企业开发者

企业架构，即在域名下按照公司的组织架构加一层，再建立各自的项目
