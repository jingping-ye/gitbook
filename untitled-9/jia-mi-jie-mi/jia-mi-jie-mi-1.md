# 加密解密

## 1. 加密解密 <a id="&#x52A0;&#x5BC6;&#x89E3;&#x5BC6;"></a>

### 1.1.1. AES 简介 <a id="aes-&#x7B80;&#x4ECB;"></a>

高级加密标准（英语Advanced Encryption Standard，缩写AES）在密码学中又称 Rijndael 加密法，是美国联邦政府采用的一种区块加密标准。这个标准用来替代原先的 DES，已经被多方分析且广为全世界所使用。经过五年的甄选流程，高级加密标准由美国国家标准与技术研究院（NIST）于 2001 年 11 月 26 日发布于 FIPS PUB 197，并在 2002 年 5 月 26 日成为有效的标准。2006 年，高级加密标准已然成为对称密钥加密中最流行的算法之一。

该算法为比利时密码学家 Joan Daemen 和 Vincent Rijmen 所设计，结合两位作者的名字，以 Rijndael 为名投稿高级加密标准的甄选流程。（Rijndael 的发音近于 "Rhine doll"）

### 1.1.2. AES 特点 <a id="aes-&#x7279;&#x70B9;"></a>

| 项目 | 说明 | 备注 |
| :--- | :--- | :--- |
| 密钥长度 | 128位（16Byte）、192位（24Byte）、256位（32Byte） | 默认：128位 |
| 分组密码工作模式 | ECB,CBC,PCBC,CTR,CTS,CFB,CFB8 至 CFB128,OFB,OFB8 至 OFB128 |  |
| 填充方式 | NoPadding, ISO10126Padding, OAEPPadding, PKCS1Padding, PKCS5Padding, SSL3Padding |  |

采用不同的工作模式（分组密码工作模式），可能会涉及到 初始化向量（IV） 和 填充模式 的选择。

在密码学中，一个密钥只能加密长度等于密钥长度的数据。

为了加密更多的数据，需要对数据进行合理的分组。

分组密码工作模式则是按照不同的密码规则进行分组（用于加密和认证）。

最后一块数据长度不足密钥长度时，则需要使用合适的 填充模式 进行填充，然后加入处理。

分组过程中通常还会加入初始化向量进行随机化，以保证安全。

示例代码：

```text
package main

import (
    "bytes"
    "crypto/aes"
    "crypto/cipher"
    "encoding/base64"
    "errors"
    "fmt"
)





var PwdKey = []byte("DIS**#KKKDJJSKDI")


func PKCS7Padding(ciphertext []byte, blockSize int) []byte {
    padding := blockSize - len(ciphertext)%blockSize
    
    padtext := bytes.Repeat([]byte{byte(padding)}, padding)
    return append(ciphertext, padtext...)
}


func PKCS7UnPadding(origData []byte) ([]byte, error) {
    
    length := len(origData)
    if length == 0 {
        return nil, errors.New("加密字符串错误！")
    } else {
        
        unpadding := int(origData[length-1])
        
        return origData[:(length - unpadding)], nil
    }
}


func AesEcrypt(origData []byte, key []byte) ([]byte, error) {
    
    block, err := aes.NewCipher(key)
    if err != nil {
        return nil, err
    }
    
    blockSize := block.BlockSize()
    
    origData = PKCS7Padding(origData, blockSize)
    
    blocMode := cipher.NewCBCEncrypter(block, key[:blockSize])
    crypted := make([]byte, len(origData))
    
    blocMode.CryptBlocks(crypted, origData)
    return crypted, nil
}


func AesDeCrypt(cypted []byte, key []byte) ([]byte, error) {
    
    block, err := aes.NewCipher(key)
    if err != nil {
        return nil, err
    }
    
    blockSize := block.BlockSize()
    
    blockMode := cipher.NewCBCDecrypter(block, key[:blockSize])
    origData := make([]byte, len(cypted))
    
    blockMode.CryptBlocks(origData, cypted)
    
    origData, err = PKCS7UnPadding(origData)
    if err != nil {
        return nil, err
    }
    return origData, err
}


func EnPwdCode(pwd []byte) (string, error) {
    result, err := AesEcrypt(pwd, PwdKey)
    if err != nil {
        return "", err
    }
    return base64.StdEncoding.EncodeToString(result), err
}


func DePwdCode(pwd string) ([]byte, error) {
    
    pwdByte, err := base64.StdEncoding.DecodeString(pwd)
    if err != nil {
        return nil, err
    }
    
    return AesDeCrypt(pwdByte, PwdKey)

}
func main() {
    str := []byte("12fff我是ww.topgoer.com的站长枯藤")
    pwd, _ := EnPwdCode(str)
    bytes, _ := DePwdCode(pwd)
    fmt.Println(string(bytes))
}
```

