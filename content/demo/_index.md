---
title: 开发案例
weight: -1
resources:
  - name: demo1
    src: "demo1.png"
    title: provider/user/contract
---

下面将通过介绍框架提供的案例:`user`用户服务，展示框架推荐的业务开发流程：测试驱动开发

{{< toc >}}

## 目录结构

首先介绍下框架的根目录： 
-  `app` 项目的源代码文件夹
-  `config`存放配置文件
-  `test` 存放测试用例

源代码文件夹

```plain
app 
├─console 用户自定义命令相关
├─http 定义 http 对外服务
└─provider 存放服务的协议与具体实现
```

**app/http:**

存放http服务相关的代码，用户在这里定义http api，具体的业务逻辑通过绑定服务实例，调用服务接口来实现，实现接口定义与业务逻辑解耦合

{{< hint type=note >}}
框架的内置功能[服务](/provider/guide)，例如日志服务，也是以同样的方式进行绑定与使用
{{< /hint >}}

```plain
http
│  kernel.go web服务的核心文件，初始化gin服务实例
│  route.go 路由定义，用户创建的接口需要在这里进行路由注册
│  swagger.go swagger相关文件，修改可能导致接口文档无法访问
├─httputil 工具类
│      error.go 提供统一的异常处理
│      response.go 提供统一的接口返回值1
├─middleware gin中间件
│  └─jwt
│          middleware.go jwt的中间件，实现用户模块的token登录机制
├─module
│  └─user 案例user模块的接口代码
│          api.go 进行 user 模块的服务绑定与路由定义
│          api_login.go api的具体定义
│          api_register.go 
│          api_userinfo.go
│          api_verify.go
│          dto.go 数据展示层，定义 api 接口的输出结构
│          mapper.go 数据映射层，将服务的返回结果映射为dto中的模型定义
└─swagger 接口文档相关，由框架自动生成
```
框架的http服务是基于[gin](https://gin-gonic.com/zh-cn/)框架进行扩展实现的，因此http api的使用支持所有的 gin 操作，框架提供的命令行[middleware migrate](/command/middleware/) 可以快速将gin的中间件迁移到当前项目中

**app/provider:**

存放具体的服务协议与实现，执行命令[provider new](/command/provider/)，快速创建provider的模板代码

```plain
provider: 
└─user 用户服务
    contract.go 服务的接口定义、服务领域对象domain定义
    service.go 服务的具体实现
    provider.go 服务的配置，一般保持默认即可
```

## web服务开发规范

API 是整个系统对外暴露服务的部分，API 层的工作是对客户端提供的数据进行校验，将数据转化为服务所需的领域对象后调用服务层接口service，再通过mapper层将服务的返回结果整合为客户端需要的DTO结构，最后将DTO的结果返回给调用方。API 中不应该有任何的业务规则与逻辑，只完成数据对象的转换与校验工作，真正的业务逻辑应通过服务接口的形式进行定义与实现

{{< img name="demo1"  lazy=false size="tiny" >}}

## 以终为始：从测试用例开始

框架推荐的开发流程，接口所实现的具体业务逻辑是在`app/provider`中以协议的形式定义出来：
```go

```


