# 配置-地鼠文档

通常通过如下配置就可以获取到一个`officialAccount`的操作实例了。

```text
//使用memcache保存access_token，也可选择redis或自定义cache
wc := wechat.NewWechat()
redisOpts := &cache.RedisOpts{
    Host:        "127.0.0.1:6379",
    Database:    0,
    MaxActive:   10,
    MaxIdle:     10,
    IdleTimeout: 60, //second
}
redisCache := cache.NewRedis(redisOpts)
cfg := &offConfig.Config{
    AppID:     "xxx",
    AppSecret: "xxx",
    Token:     "xxx",
    //EncodingAESKey: "xxxx",
    Cache: redisCache,
}
officialAccount := wc.GetOfficialAccount(cfg)
```

**微信公众号支持的配置，如下：**

```text

type Config struct {
    AppID          string `json:"app_id"`           
    AppSecret      string `json:"app_secret"`       
    Token          string `json:"token"`            
    EncodingAESKey string `json:"encoding_aes_key"` 
    Cache          cache.Cache
}
```

**配置说明：**

| 参数 | 是否必须 | 说明 |
| :--- | :--- | :--- |
| AppID | 是 | 微信公众号APP ID |
| AppSecret | 是 | 微信公众号App Secret |
| EncodingAESKey | 否 | 如果指定则表示开启AES加密，消息和结果都会进行解密和加密 |
| Cache | 否 | 单独指定微信公众号用到的AccessToken保存的位置，会覆盖全局通过`wechat.SetCache`的设置 |

> 参数配置请前往[微信公众号后台](https://mp.weixin.qq.com/)获取

## 缓存 <a id="2bnavt"></a>

**作用：** 缓存`access_token`，保存在独立服务上可以保证`access_token`在有效期内，`access_token`是公众号的全局唯一接口调用凭据，公众号调用各接口时都需使用`access_token`。

> 推荐使用`redis`或者`memcache`

### Redis <a id="6c83uh"></a>

实例化如下：

```text
redisOpts := &cache.RedisOpts{
    Host:        "127.0.0.1:6379", 
    Password:    "",
    Database:    0, 
    MaxActive:   10, 
    MaxIdle:     10,  
    IdleTimeout: 60, 
}
redisCache := cache.NewRedis(redisOpts)
```

### Memcache <a id="e070hh"></a>

实例化如下：

```text
memCache:=cache.NewMemcache("127.0.0.1:11211")
```

### Memory <a id="4a84cs"></a>

进程内内存

> 不推荐使用该模式用于生产

实例化如下：

```text
memoryCache:=cache.NewMemory()
```

### 自定义Cache <a id="4pyce7"></a>

接口如下：

```text
//Cache interface
type Cache interface {
    Get(key string) interface{}
    Set(key string, val interface{}, timeout time.Duration) error
    IsExist(key string) bool
    Delete(key string) error
}
```

文件位置：`/cache/cache.go`

## 日志 <a id="9kq18t"></a>

sdk中使用`github.com/sirupsen/logrus`来进行日志的输出。  
默认的日志参数为：

```text
    
    log.SetFormatter(&log.TextFormatter{})

    
    log.SetOutput(os.Stdout)

    
    log.SetLevel(log.DebugLevel)
```

你可以在自己的项目中自定义logrus的输出配置来覆盖sdk中默认的行为  
参考: [logrus配置文档](http://github.com/sirupsen/logrus)

## 跳过接口验证 <a id="bl9iv0"></a>

微信公众号后台在填写接口配置信息时会进行接口的校验以确保接口是否可以正常相应。  
如果想要在sdk中关闭该设置，可以通过如下方法：

```text
officialAccount := wc.GetOfficialAccount(cfg)

server := officialAccount.GetServer(req, rw)


server.SkipValidate(true)
```

文档更新时间: 2020-08-19 10:31   作者：kuteng

