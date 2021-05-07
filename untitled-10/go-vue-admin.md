# go-vue-admin

## 1. gin-vue-admin <a id="gin-vue-admin"></a>

### 1.1. 基本介绍 <a id="&#x57FA;&#x672C;&#x4ECB;&#x7ECD;"></a>

#### 1.1.1. 项目介绍 <a id="&#x9879;&#x76EE;&#x4ECB;&#x7ECD;"></a>

[在线预览](http://qmplus.henrongyi.top/)

> Gin-vue-admin是一个基于vue和gin开发的全栈前后端分离的后台管理系统，集成jwt鉴权，动态路由，动态菜单，casbin鉴权，表单生成器，代码生成器等功能，提供多种示例文件，让您把更多时间专注在业务开发上。

#### 1.1.2. 贡献指南 <a id="&#x8D21;&#x732E;&#x6307;&#x5357;"></a>

Hi! 首先感谢你使用 gin-vue-admin。

Gin-vue-admin 是一套为后台管理平台准备的一整套前后端分离架构式的开源框架，旨在快速搭建后台管理系统。

Gin-vue-admin 的成长离不开大家的支持，如果你愿意为 gin-vue-admin 贡献代码或提供建议，请阅读以下内容。

**1.2.1 Issue 规范**

* issue 仅用于提交 Bug 或 Feature 以及设计相关的内容，其它内容可能会被直接关闭。如果你在使用时产生了疑问，请到 Slack 或 [Gitter](https://gitter.im/ElemeFE/element) 里咨询。
* 在提交 issue 之前，请搜索相关内容是否已被提出。

**1.2.2 Pull Request 规范**

* 请先 fork 一份到自己的项目下，不要直接在仓库下建分支。
* commit 信息要以`[文件名]: 描述信息` 的形式填写，例如 `README.md: fix xxx bug`。
* 确保 PR 是提交到 \`develop\` 分支，而不是 \`master\` 分支。
* 如果是修复 bug，请在 PR 中给出描述信息。
* 合并代码需要两名维护人员参与：一人进行 review 后 approve，另一人再次 review，通过后即可合并。

#### 1.1.3. 版本列表 <a id="&#x7248;&#x672C;&#x5217;&#x8868;"></a>

* master: 2.0, 用于生产环境
* develop: 2.0, 用于测试环境
* [gin-vue-admin\_v2.0\_dev](https://github.com/flipped-aurora/gin-vue-admin/tree/gin-vue-admin_v2_dev) （v2.0 不再兼容 v1.0）
* [gin-vue-admin\_v1.0\_stable](https://github.com/flipped-aurora/gin-vue-admin/tree/gin-vue-admin_v1_stable) （v1.0 稳定版，会持续更新和维护）
* [gin-vue-admin\_v1.0\_dev](https://github.com/flipped-aurora/gin-vue-admin/tree/gin-vue-admin_v1_dev) （v1.0 稳定版，会持续更新和维护）

### 1.2. 使用说明 <a id="&#x4F7F;&#x7528;&#x8BF4;&#x660E;"></a>

```text
- node版本 > v8.6.0
- golang版本 >= v1.11
- IDE推荐：Goland
- 各位在clone项目以后，把db文件导入自己创建的库后，最好前往七牛云申请自己的空间地址。
- 替换掉项目中的七牛云公钥，私钥，仓名和默认url地址，以免发生测试文件数据错乱
```

#### 1.2.1. web端 <a id="web&#x7AEF;"></a>

```text

git clone https://github.com/piexlmax/gin-vue-admin.git


cd web


npm install


npm run serve
```

#### 1.2.2. server端 <a id="server&#x7AEF;"></a>

```text



go list (go mod tidy)


go build
```

#### 1.2.3. swagger自动化API文档 <a id="swagger&#x81EA;&#x52A8;&#x5316;api&#x6587;&#x6863;"></a>

**2.3.1 安装 swagger**

**（1）可以翻墙**

```text
go get -u github.com/swaggo/swag/cmd/swag
```

**（2）无法翻墙**

由于国内没法安装 go.org/x 包下面的东西，需要先安装`gopm`

```text

go get -v -u github.com/gpmgo/gopm


gopm get -g -v github.com/swaggo/swag/cmd/swag


go install
```

**2.3.2 生成API文档**

```text
cd server
swag init
```

执行上面的命令后，server目录下会出现docs文件夹，登录[http://localhost:8888/swagger/index.html，即可查看swagger文档](http://localhost:8888/swagger/index.html%EF%BC%8C%E5%8D%B3%E5%8F%AF%E6%9F%A5%E7%9C%8Bswagger%E6%96%87%E6%A1%A3)

### 1.3. 技术选型 <a id="&#x6280;&#x672F;&#x9009;&#x578B;"></a>

* 前端：用基于`vue`的`Element-UI`构建基础页面。
* 后端：用`Gin`快速搭建基础restful风格API，`Gin`是一个go语言编写的Web框架。
* 数据库：采用`MySql`\(5.6.44\)版本，使用`gorm`实现对数据库的基本操作,已添加对sqlite数据库的支持。
* 缓存：使用`Redis`实现记录当前活跃用户的`jwt`令牌并实现多点登录限制。
* API文档：使用`Swagger`构建自动化文档。
* 配置文件：使用`fsnotify`和`viper`实现`yaml`格式的配置文件。
* 日志：使用`go-logging`实现日志记录。

### 1.4. 项目架构 <a id="&#x9879;&#x76EE;&#x67B6;&#x6784;"></a>

#### 1.4.1. 系统架构图 <a id="&#x7CFB;&#x7EDF;&#x67B6;&#x6784;&#x56FE;"></a>

#### 1.4.2. 前端详细设计图 （提供者:baobeisuper） <a id="&#x524D;&#x7AEF;&#x8BE6;&#x7EC6;&#x8BBE;&#x8BA1;&#x56FE;-&#xFF08;&#x63D0;&#x4F9B;&#x8005;baobeisuper&#xFF09;"></a>

![&#x524D;&#x7AEF;&#x8BE6;&#x7EC6;&#x8BBE;&#x8BA1;&#x56FE;](http://qmplusimg.henrongyi.top/naotu.png)

#### 1.4.3. 目录结构 <a id="&#x76EE;&#x5F55;&#x7ED3;&#x6784;"></a>

```text
    ├─server           （后端文件夹）
    │  ├─api            （API）
    │  ├─config         （配置包）
    │  ├─core              （內核）
    │  ├─db             （数据库脚本）
    │  ├─docs              （swagger文档目录）
    │  ├─global         （全局对象）
    │  ├─initialiaze    （初始化）
    │  ├─middleware     （中间件）
    │  ├─model          （结构体层）
    │  ├─resource       （资源）
    │  ├─router         （路由）
    │  ├─service         (服务)
    │  └─utils            （公共功能）
    └─web            （前端文件）
        ├─public        （发布模板）
        └─src           （源码包）
            ├─api       （向后台发送ajax的封装层）
            ├─assets    （静态文件）
            ├─components（组件）
            ├─router    （前端路由）
            ├─store     （vuex 状态管理仓）
            ├─style     （通用样式文件）
            ├─utils     （前端工具库）
            └─view      （前端页面）
```

### 1.5. 主要功能 <a id="&#x4E3B;&#x8981;&#x529F;&#x80FD;"></a>

* 权限管理：基于`jwt`和`casbin`实现的权限管理
* 文件上传下载：实现基于七牛云的文件上传操作（为了方便大家测试，我公开了自己的七牛测试号的各种重要token，恳请大家不要乱传东西）
* 分页封装：前端使用mixins封装分页，分页方法调用mixins即可
* 用户管理：系统管理员分配用户角色和角色权限。
* 角色管理：创建权限控制的主要对象，可以给角色分配不同api权限和菜单权限。
* 菜单管理：实现用户动态菜单配置，实现不同角色不同菜单。
* api管理：不同用户可调用的api接口的权限不同。
* 配置管理：配置文件可前台修改（测试环境不开放此功能）。
* 富文本编辑器：MarkDown编辑器功能嵌入。
* 条件搜索：增加条件搜索示例。
* restful示例：可以参考用户管理模块中的示例API。

  ```text
  前端文件参考: src\view\superAdmin\api\api.vue 
  后台文件参考: model\dnModel\api.go
  ```

* 多点登录限制：需要在`config.yaml`中把`system`中的`useMultipoint`修改为true\(需要自行配置Redis和Config中的Redis参数，测试阶段，有bug请及时反馈\)。
* 分片长传：提供文件分片上传和大文件分片上传功能示例。
* 表单生成器：表单生成器借助 [@form-generator](https://github.com/JakHuang/form-generator)。
* 代码生成器：后台基础逻辑以及简单curd的代码生成器。

### 1.6. 计划任务 <a id="&#x8BA1;&#x5212;&#x4EFB;&#x52A1;"></a>

* \[ \] 导入，导出Excel
* \[ \] Echart图表支持
* \[ \] 工作流，任务交接功能开发
* \[ \] 单独前端使用模式以及数据模拟

