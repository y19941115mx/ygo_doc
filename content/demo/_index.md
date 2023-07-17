---
title: 开发案例
weight: -1
resources:
  - name: demo1
    src: "demo1.png"
    title: 流程图
  - name: demo2
    src: "demo2.png"
    title: 数据模型层
  - name: demo3
    src: "demo3.png"
    title: 可视化测试页面
  - name: demo4
    src: "demo4.png"
    title: vscode debug插件
    
---

下面将通过介绍框架提供的案例:`user`用户服务，展示框架推荐的业务开发流程

{{< toc >}}

## 目录结构

框架的根目录： 
-  `app` 项目的源代码文件夹
-  `config`存放配置文件
-  `test` 存放测试用例

app 源代码文件夹

```plain
app 
├─console 用户自定义命令相关
├─http 定义 http 对外服务
└─provider 存放服务的协议与具体实现
```

**app/http:**

存放http服务相关的代码，用户在这里定义http api。具体的业务逻辑通过绑定服务实例，调用对应服务接口来实现

```plain
http
│  kernel.go web服务核心文件负责初始化gin服务实例，可在此对gin实例做全局配置
│  route.go 路由定义文件，用户创建的接口需要在这里注册路由
│  swagger.go swagger相关文件，修改可能导致接口文档无法访问
├─httputil 工具类
│      error.go 统一异常处理
│      response.go 统一接口返回
├─middleware gin中间件，存放中间件
│  └─jwt
│          middleware.go jwt的中间件，用来实现demo用户模块的token登录机制
├─module api 模块
│  └─user 案例user模块的接口代码
│          api.go 接口实现核心文件，负责服务绑定与路由定义
│          api_login.go api的具体实现
│          api_register.go 
│          api_userinfo.go
│          api_verify.go
│          dto.go 数据展示层，定义 api 接口的输出结构
│          mapper.go 数据映射层，将服务的返回结果映射为dto中的模型定义
└─swagger 接口文档相关，由框架自动生成
```

框架[http服务](/provider/kernel.md)基于[gin](https://gin-gonic.com/zh-cn/)，支持 gin 的所有功能操作。此外，为了帮助用户在自己的项目中管理和使用官方gin中间件，框架提供了命令行工具[middleware](/command/middleware/) 

**app/provider:**

存放具体的服务协议与实现

```plain
provider: 
└─user 用户服务
    contract.go 服务的接口定义、领域对象DO定义
    service.go 服务的具体实现
    provider.go 服务的配置，一般保持默认即可
```

## 开发规范

API 层是整个系统对外暴露服务的部分，这一层的工作是对客户端提供的数据进行校验，将数据转化为服务所需的数据后调用具体的服务，mapper层将服务的返回结果整合为客户端需要的DTO结构，最后将DTO的结果返回给调用方

框架中的 [service provider层](/provider/guide)是业务功能实现的核心，负责将业务逻辑封装成具体的服务，供上层API调用

{{< img name="demo1"  lazy=false size="small" >}}

### 1. 协同合作，文档先行

在实际工作中，前后端是通过接口来进行交互的，因此需要有一个接口文档将接口和参数都展示出来，以实现前端和后端的并行开发。  
框架提供了[swagger 命令](/command/swagger/)，用户只需要在api的处理函数的头部，以[swaggo](https://github.com/swaggo/swag#how-to-use-it-with-gin) 的规范只填写接口的请求、参数和返回值等接口信息，不用具体实现，之后执行以下命令，就可以自动生成接口文档

```shell
ygo swagger gen
```

启动[web应用](/command/app)，接口文档访问地址[127.0.0.1:8888/swagger/index.html](127.0.0.1:8888/swagger/index.html) 

### 2. 抽象业务，定义服务

执行[provider 命令](/command/provider/)，快速创建service provider的模板代码
```shell
ygo provider new
```
在[contract.go文件](/provider/guide#contract.go)中定义用户服务对外提供的能力。正如用户服务的调用方不仅仅只有用户模块的接口，服务的设计需要从`服务需要提供哪些对外能力`这一角度思考反复迭代

### 3. 接口开发，检验服务

设计用户服务协议定义服务对外提供的能力后，下一步不是急于实现服务，而是需要验证当前的服务协议是否能满足当前的业务需求。也就是能否使用当前的服务实现 API 接口的业务逻辑

以`Register`用户注册接口为例，分析业务逻辑：用户需要首先进行注册操作(Register)获得验证码，之后发送邮件SendRegisterMail将携带有验证码的邮件发送给用户，通知用户点击激活

```go
func (api *UserApi) Register(c *gin.Context) {
    ...
	userWithCaptcha, err := userService.Register(c, model)
	if err != nil {
		logger.Error(c, err.Error(), map[string]interface{}{
			"stack": fmt.Sprintf("%+v", err),
		})
		httputil.FailWithError(c, err)
		return
	}
	// 发送邮件
	if err := userService.SendRegisterMail(c, userWithCaptcha); err != nil {
		httputil.FailWithError(c, err)
		return
	}
    ...
}
```
验证当前的服务能力能够满足注册接口的需要，再验证其他接口
### 4. 实现协议，测试收尾

确定了用户服务的设计可行，那么恭喜你，业务模块的开发已经进入到最后一步：在[service.go文件](/provider/guide#service.go)中实现服务

{{< hint type=note >}}
在用户服务的实现案例中，你可以看到很多[内置服务](/provider/guide)的使用，比如orm服务、cache服务等。能够直观感受到将具体的业务逻辑抽象为服务协议的设计优势。之后，我们开发的用户服务也可以被其他业务模块调用！！！
{{< /hint >}}

最后，为了保证代码质量，确保服务协议全部实现正确，框架提供了基于 goconvey 的单元测试最佳实践，基于文件内存的方式创建 SQLite 数据库，本地无需下载和配置mysql数据库系统也可运行测试:

- 编辑文件`test/env.go`，设置项目根路径地址`BasePath`
- 确保`config\testing\database.yaml`中数据库的配置没有修改，使用 SQLite 数据库来模拟 MySQL 数据库操作：
```yaml
driver: sqlite
dsn: "file::memory:?cache=shared" # 在内存中创建一个sqlite数据库
```
- 仿照`test/userservice_test.go`文件新建`xxxservice_test.go`服务测试代码
- 执行测试用例
```shell
cd test

go test -v
```
- 也可以启动goconvey的可视化测试页面，执行测试用例(可选)
```bash
#安装 goconvey 
go install github.com/smartystreets/goconvey

#执行命令,启动可视化测试web服务
cd test
goconvey
# 访问web服务: http://127.0.0.1:8080/
```

{{< img name="demo3"  lazy=false  >}}


## 框架 debug

写代码少不了debug，框架提供了基于vscode的debug配置文件 `.vscode\launch.json`，可以直接使用vscdode的debug插件进行代码的开发调试，当前有两个debug配置：
- app start 处理web服务的调试
- app dev 处理model命令这种需要用户输入的调试

{{< img name="demo4"  lazy=false  >}}