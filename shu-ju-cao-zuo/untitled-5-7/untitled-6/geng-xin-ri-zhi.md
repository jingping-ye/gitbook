# 更新日志

* [**1.** 更新日志](geng-xin-ri-zhi.md#更新日志)
  * [**1.1.** v2.0](geng-xin-ri-zhi.md#v20)
  * [**1.2.** v1.0 - 2016.04.27](geng-xin-ri-zhi.md#v10---20160427)

## 1. 更新日志 <a id="&#x66F4;&#x65B0;&#x65E5;&#x5FD7;"></a>

### 1.1. v2.0 <a id="v20"></a>

WIP

### 1.2. v1.0 - 2016.04.27 <a id="v10---20160427"></a>

破坏性变更

* `gorm.Open` 返回类型为 `*gorm.DB` 而不是 `gorm.DB` ；
* 更新只会更新更改的字段
* 只会使用 `deleted_at IS NULL` 来检测软删除
* 新的 ToDBName 逻辑

在 GORM 将 struct，Field 的名称转换为 db 名称之前，只有那些来自 [golint](https://github.com/golang/lint/blob/master/lint.go#L702) 的常见初始化（如`HTTP`，`URI`）是特殊处理的，所以 `HTTP` 的数据库名称是 `http`，而不是 `h_t_t_p`。

但是像一些不在列表里的缩写，如 `SKU` db 名称为 `s_k_u`，这次升级修复了此问题。

* 错误 `RecordNotFound` 重命名为 `ErrRecordNotFound`。
* `mssql` dialect 被重命名为 "github.com/jinzhu/gorm/dialects/mssql"
* `Hstore` 字段类型被移到专属的包里 "github.com/jinzhu/gorm/dialects/postgres"

