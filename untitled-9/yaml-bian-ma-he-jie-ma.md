# Yaml编码和解码

## 1. Yaml编码和解码 <a id="yaml&#x7F16;&#x7801;&#x548C;&#x89E3;&#x7801;"></a>

### 1.1.1. 介绍 <a id="&#x4ECB;&#x7ECD;"></a>

YAML Ain’t Markup Language，一种非常简洁的非标记语言，可以快速的对Yaml进行编码和解码。

官网地址：[https://gopkg.in/yaml.v2](https://gopkg.in/yaml.v2)

GoDoc：[https://godoc.org/gopkg.in/yaml.v2](https://godoc.org/gopkg.in/yaml.v2)

### 1.1.2. 基本规则 <a id="&#x57FA;&#x672C;&#x89C4;&#x5219;"></a>

* 大小写敏感、易于使用，容易阅读
* 使用缩进表示层级关系
* 只能使用空格键
* 适合表示程序语言的数据结构
* 缩进长度没有限制，只要元素对齐就表示这些元素属于一个层级
* 使用\#表示注释
* 字符串可以不用引号标注
* 可用于不同程序间交换数据
* 支持泛型工具
* 丰富的表达能力和可扩展性

### 1.1.3. Yaml文件 <a id="yaml&#x6587;&#x4EF6;"></a>

```text
    mysql:
      user: root
      password: 123456
      host: 192.198.1.1
      port: 3306
      dbname: mdb
    redis:
      host: 192.168.1.1
      port: 1234
      auth: 123456
    nginx_proxy:
      counter: 3
      nginx_list: [
        192.168.1.1,
        192.168.1.2,
        192.168.1.3
      ]
```

### 1.1.4. 代码实现 <a id="&#x4EE3;&#x7801;&#x5B9E;&#x73B0;"></a>

```text
package main

import (
  "fmt"
  "gopkg.in/yaml.v2"
  "io/ioutil"
  "log"
)

type MySQLConfig struct {
  User     string `yaml:"user"`
  Password string `yaml:"password"`
  Host     string `yaml:"host"`
  Port     int    `yaml:"port"`
  DbName   string `yaml:"dbname"`
}
type RedisConfig struct {
  Host string `yaml:"host"`
  Port int    `yaml:"port"`
  Auth string `yaml:"auth"`
}
type NginxProxyConfig struct {
  Counter   int      `yaml:"counter"`
  NginxList []string `yaml:"nginx_list"`
}

type ServiceYaml struct {
  MySQL      MySQLConfig      `yaml:"mysql"`
  Redis      RedisConfig      `yaml:"redis"`
  NginxProxy NginxProxyConfig `yaml:"nginx_proxy"`
}

func main() {
  filename := "/web/config.yaml"
  y := new(ServiceYaml)
  yamlFile, err := ioutil.ReadFile(filename)
  if err != nil {
    fmt.Printf("read file err %v\n", err)
    return
  }

  err = yaml.Unmarshal(yamlFile, y)
  if err != nil {
    log.Fatalf("yaml unmarshal: %v\n", err)
    return
  }

  fmt.Printf("MySQL host : %s, port : %d, user : %s, password : %s, db_name : %s \n",
    y.MySQL.Host, y.MySQL.Port, y.MySQL.User, y.MySQL.Password, y.MySQL.DbName)

  fmt.Printf("Redis host : %s, port %d, auth : %s\n",
    y.Redis.Host, y.Redis.Port, y.Redis.Auth)

  fmt.Printf("Vip Counter: %d, Vip List : %v\n", y.NginxProxy.Counter, y.NginxProxy.NginxList)

  n, err := yaml.Marshal(y)
  if err != nil {
    log.Fatalf("marshal err : %v\n", err)
    return
  }
  fmt.Printf("yaml marshal : %v\n", string(n))
}
```

### 1.1.5. 运行结果 <a id="&#x8FD0;&#x884C;&#x7ED3;&#x679C;"></a>

* 转自公众号：sir小龙虾

