# go-admin

## 1. go-admin <a id="go-admin"></a>

**基于Gin + Vue + Element UI的前后端分离权限管理系统**

系统初始化极度简单，只需要配置文件中，修改数据库连接，系统启动后会自动初始化数据库信息以及必须的基础数据

[在线文档](https://wenjianzhang.github.io/go-admin-site)

[视频教程](https://space.bilibili.com/565616721/channel/detail?cid=125737)

### 1.1. ✨ 特性 <a id="&#x2728;-&#x7279;&#x6027;"></a>

* 遵循 RESTful API 设计规范
* 基于 GIN WEB API 框架，提供了丰富的中间件支持（用户认证、跨域、访问日志、追踪ID等）
* 基于Casbin的 RBAC 访问控制模型
* JWT 认证
* 支持 Swagger 文档\(基于swaggo\)
* 基于 GORM 的数据库存储，可扩展多种类型数据库
* 配置文件简单的模型映射，快速能够得到想要的配置
* 代码生成工具
* 表单构建工具
* 多命令模式
* TODO: 单元测试

### 1.2. 🎁 内置 <a id="&#x1F381;-&#x5185;&#x7F6E;"></a>

1. 用户管理：用户是系统操作者，该功能主要完成系统用户配置。
2. 部门管理：配置系统组织机构（公司、部门、小组），树结构展现支持数据权限。
3. 岗位管理：配置系统用户所属担任职务。
4. 菜单管理：配置系统菜单，操作权限，按钮权限标识等。
5. 角色管理：角色菜单权限分配、设置角色按机构进行数据范围权限划分。
6. 字典管理：对系统中经常使用的一些较为固定的数据进行维护。
7. 参数管理：对系统动态配置常用参数。
8. 操作日志：系统正常操作日志记录和查询；系统异常信息日志记录和查询。
9. 登录日志：系统登录日志记录查询包含登录异常。
10. 系统接口：根据业务代码自动生成相关的api接口文档。
11. 代码生成：根据数据表结构生成对应的增删改查相对应业务，全部可视化编程，基本业务可以0代码实现。
12. 表单构建：自定义页面样式，拖拉拽实现页面布局。
13. 服务监控：查看一些服务器的基本信息。

### 1.3. 准备工作 <a id="&#x51C6;&#x5907;&#x5DE5;&#x4F5C;"></a>

你需要在本地安装 \[go\] \[gin\] [node](http://nodejs.org/) 和 [git](https://git-scm.com/)

同时配套了系列教程包含视频和文档，如何从下载完成到熟练使用，强烈建议大家先看完这些教程再来实践本项目！！！

#### 1.3.1. 轻松实现go-admin写出第一个应用 - 文档教程 <a id="&#x8F7B;&#x677E;&#x5B9E;&#x73B0;go-admin&#x5199;&#x51FA;&#x7B2C;&#x4E00;&#x4E2A;&#x5E94;&#x7528;---&#x6587;&#x6863;&#x6559;&#x7A0B;"></a>

[步骤一 - 基础内容介绍](http://doc.zhangwj.com/go-admin-site/guide/intro/tutorial01.html)

[步骤二 - 实际应用 - 编写增删改查](http://doc.zhangwj.com/go-admin-site/guide/intro/tutorial02.html)

#### 1.3.2. 手把手教你从入门到放弃 - 视频教程 <a id="&#x624B;&#x628A;&#x624B;&#x6559;&#x4F60;&#x4ECE;&#x5165;&#x95E8;&#x5230;&#x653E;&#x5F03;---&#x89C6;&#x9891;&#x6559;&#x7A0B;"></a>

[如何启动go-admin](https://www.bilibili.com/video/BV1z5411x7JG)

[使用生成工具轻松实现业务](https://www.bilibili.com/video/BV1Dg4y1i79D)

[go-admin菜单的配置说明](https://www.bilibili.com/video/BV1Wp4y1D715)

[多命令启动方式讲解以及IDE配置](https://www.bilibili.com/video/BV1Fg4y1q7ph)

**如有问题请先看上述使用文档和文章，若不能满足，欢迎 issue 和 pr ，视频教程和文档持续更新中**

### 1.4. 📦 本地开发 <a id="&#x1F4E6;-&#x672C;&#x5730;&#x5F00;&#x53D1;"></a>

#### 1.4.1. 首次启动说明 <a id="&#x9996;&#x6B21;&#x542F;&#x52A8;&#x8BF4;&#x660E;"></a>

```text

git clone https://github.com/wenjianzhang/go-admin.git


cd ./go-admin


go build



vi ./config/setting.yml 




```

#### 1.4.2. 初始化数据库，以及服务启动 <a id="&#x521D;&#x59CB;&#x5316;&#x6570;&#x636E;&#x5E93;&#xFF0C;&#x4EE5;&#x53CA;&#x670D;&#x52A1;&#x542F;&#x52A8;"></a>

```text
# 首次配置需要初始化数据库资源信息
./go-admin init -c config/settings.yml -m dev


# 启动项目，也可以用IDE进行调试
./go-admin server -c config/settings.yml -p 8000 -m dev
```

#### 1.4.3. 文档生成 <a id="&#x6587;&#x6863;&#x751F;&#x6210;"></a>

```text
swag init  


go get -u github.com/swaggo/swag/cmd/swag
```

#### 1.4.4. 交叉编译 <a id="&#x4EA4;&#x53C9;&#x7F16;&#x8BD1;"></a>

```text
env GOOS=windows GOARCH=amd64 go build main.go



env GOOS=linux GOARCH=amd64 go build main.go
```

### 1.5. 🎬 在线体验 <a id="&#x1F3AC;-&#x5728;&#x7EBF;&#x4F53;&#x9A8C;"></a>

> admin / 123456

演示地址：[http://www.zhangwj.com](http://www.zhangwj.com/#/login)

