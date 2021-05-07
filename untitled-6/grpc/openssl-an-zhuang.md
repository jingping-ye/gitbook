# OpenSSL安装

* [**1.** OpenSSL安装](openssl-an-zhuang.md#openssl安装)
  * * [**1.1.1.** OpenSSL官网](openssl-an-zhuang.md#openssl官网)
    * [**1.1.2.** Windows安装方法](openssl-an-zhuang.md#windows安装方法)

## 1. OpenSSL安装 <a id="openssl&#x5B89;&#x88C5;"></a>

### 1.1.1. OpenSSL官网 <a id="openssl&#x5B98;&#x7F51;"></a>

官方下载地址： [https://www.openssl.org/source/](https://www.openssl.org/source/)

### 1.1.2. Windows安装方法 <a id="windows&#x5B89;&#x88C5;&#x65B9;&#x6CD5;"></a>

OpenSSL官网没有提供windows版本的安装包，可以选择其他开源平台提供的工具。例如

[http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html)

以该工具为例，安装步骤和使用方法如下：

进入下载页面选择下载的版本

选择安装的位置

剩下的都是下一步

设置环境变量，例如工具安装在C:\OpenSSL-Win64，则将C:\OpenSSL-Win64\bin；复制到Path中

打开命令行程序cmd（以管理员身份运行），运行以下命令:

```text
利用　openssl　生成公钥私钥 
生成公钥： openssl genrsa -out rsa_private_key.pem 1024 
生成私钥: openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

