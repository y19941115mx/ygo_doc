---
title: 开发案例
weight: -1
---

下面将通过介绍框架提供的案例:`user`用户服务，展示框架推荐的业务开发流程：测试驱动开发

{{< toc >}}

## 目录结构

首先介绍下框架的根目录： 
-  `app` 项目的源代码文件夹
-  `config`存放配置文件
-  `test` 存放测试用例

**app源代码目录结构:**
```plain
app 
├─console 用户自定义命令相关
├─http 提供 http 服务
│  ├─httputil 提供了常用的http工具方法
│  ├─middleware 存放用户自定义的中间件
│  │  └─jwt jwt中间件
│  ├─module 划分不同Api模块
│  │  └─user user模块代码
│  └─swagger 接口文档相关
└─provider 存放服务的协议与具体实现
    └─user
```

`app/http` 目录存放http服务相关的代码，用户在这里定义http接口，推荐接口代码只关心接口输入、输出的处理，具体的业务逻辑则通过调用对应服务来实现，实现接口定义与业务逻辑解耦合

**app/http目录结构:**

```plain
http
│  kernel.go web服务的核心文件，初始化gin服务实例
│  route.go 路由定义，用户创建的接口需要在这里进行路由注册
│  swagger.go swagger相关文件，修改可能导致接口文档无法访问
├─httputil 工具类
│      error.go 提供统一的异常处理
│      response.go 提供统一的接口返回值1
├─middleware
│  └─jwt
│          middleware.go jwt的中间件，实现用户模块的token登录机制
├─module
│  └─user 案例user模块的接口代码
│          api.go 实现 user 模块的服务绑定与路由定义
│          api_login.go api的具体定义
│          api_register.go 
│          api_userinfo.go
│          api_verify.go
│          mapper.go 数据映射层，将服务的返回结果映射为dto中的模型定义
│          dto.go 数据展示层，定义 api 接口的输出结构体
└─swagger 接口文档相关，由框架自动生成
```

`app/provider`目录存放具体的服务协议与实现，执行命令`ygo provider new`，通过用户交互快速创建provider的模板代码

**app/provider目录结构:**

```plain
provider: 
└─user 用户服务
    contract.go 服务的接口定义
    service.go 服务的具体实现
    provider.go 服务的配置，一般保持默认即可
```