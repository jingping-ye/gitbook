# 自定义Logger

* [**1.** 自定义Logger](zi-ding-yi-logger.md#自定义logger)
  * [**1.1.** Logger](zi-ding-yi-logger.md#logger)
  * [**1.2.** 自定义 Logger](zi-ding-yi-logger.md#自定义-logger)

## 1. 自定义Logger <a id="&#x81EA;&#x5B9A;&#x4E49;logger"></a>

### 1.1. Logger <a id="logger"></a>

Gorm 建立了对 Logger 的支持，默认模式只会在错误发生的时候打印日志。

```text
// 开启 Logger, 以展示详细的日志
db.LogMode(true)

// 关闭 Logger, 不再展示任何日志，即使是错误日志
db.LogMode(false)

// 对某个操作展示详细的日志，用来排查该操作的问题
db.Debug().Where("name = ?", "jinzhu").First(&User{})
```

### 1.2. 自定义 Logger <a id="&#x81EA;&#x5B9A;&#x4E49;-logger"></a>

参考 GORM 的默认 logger 是怎么自定义的 [https://github.com/jinzhu/gorm/blob/master/logger.go](https://github.com/jinzhu/gorm/blob/master/logger.go)

例如，使用 [Revel](https://revel.github.io/) 的 Logger 作为 GORM 的输出

```text
db.SetLogger(gorm.Logger{revel.TRACE})
```

使用 `os.Stdout` 作为输出

```text
db.SetLogger(log.New(os.Stdout, "\r\n", 0))
```

