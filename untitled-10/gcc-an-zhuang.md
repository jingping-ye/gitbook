# gcc安装

## 1. gcc安装 <a id="gcc&#x5B89;&#x88C5;"></a>

GCC原名为GNU C语言编译器（GNU C Compiler），只能处理C语言。但其很快扩展，变得可处理C++，后来又扩展为能够支持更多编程语言，如Fortran、Pascal、Objective -C、Java、Ada、Go以及各类处理器架构上的汇编语言等，所以改名GNU编译器套件（GNU Compiler Collection）

### 1.1.1. MinGW-W64 GCC安装与配置 <a id="mingw-w64-gcc&#x5B89;&#x88C5;&#x4E0E;&#x914D;&#x7F6E;"></a>

MinGW-w64下载地址：[https://sourceforge.net/projects/mingw-w64/files/](https://sourceforge.net/projects/mingw-w64/files/)

选择合适的版本

```text
    i686纯32位版供32位win系统使用

    x86_64是64位系统用的版本

    seh结尾是纯64位编译

    sjlj结尾是32 64两种编译，需加-m32或-m64参数

    posix通常用于跨平台，比win32兼容性好一些
```

我下载的是：[x86\_64-posix-sjlj](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/7.3.0/threads-posix/sjlj/x86_64-7.3.0-release-posix-sjlj-rt_v5-rev0.7z)\(这个已经编译好了解压完就能用）

### 1.1.2. 配置过程： <a id="&#x914D;&#x7F6E;&#x8FC7;&#x7A0B;&#xFF1A;"></a>

* 下载压缩包
* 将压缩包解压到硬盘
* 配置编译环境

假设我这里将下载的压缩包解压到：E:\mingw 目录下

### 1.1.3. 配置环境变量： <a id="&#x914D;&#x7F6E;&#x73AF;&#x5883;&#x53D8;&#x91CF;&#xFF1A;"></a>

右击“计算机” --&gt;属性 --&gt;高级系统设置 --&gt;环境环境 --&gt;系统变量 --&gt;“Path”变量 --&gt;编辑，追加 ;E:\mingw\bin

### 1.1.4. 验证环境是否安装 <a id="&#x9A8C;&#x8BC1;&#x73AF;&#x5883;&#x662F;&#x5426;&#x5B89;&#x88C5;"></a>

开始菜单 --&gt; 附件 --&gt; “命令行提示符” 或 “Win键+R”组合键，输入：cmd

在命令行下输入：gcc -v

如有输出GCC信息则配置成功，配置成功如图：

