---
title: 介绍
weight: -1000
---

Ygo 框架使用ServiceProvider机制来满足协议，通过service Provder提供某个协议服务的具体实现。这样如果开发者对具体的实现协议的服务类的具体实现不满意，则可以很方便的通过切换具体协议的Service Provider来进行具体服务的切换。

一个ServiceProvider是一个单独的文件夹，它包含服务提供和服务实现。具体可以参考framework/provider/demo

一个SerivceProvider就是一个独立的包，这个包可以作为插件独立地发布和分享。

你也可以定义一个无contract的ServiceProvider，其中的Name()需要保证唯一。


## 服务的定义

推荐使用面向领域驱动的代码设计思想(DDD)来实现业务模块：首先对业务逻辑抽象出领域对象DO，再基于这一个领域对象设计服务接口，保证不同模块对业务逻辑的理解保持一致

### contract.go

在 contract.go 定义了这个服务提供方提供的协议接口。hade 框架任务，作为一个业务的服务提供者，定义一个好的协议是最重要的事情。

所以 contract.go 中定义了一个 Service 接口，在其中定义各种方法，包含输入参数和返回参数。

```go
// 定义服务拥有的接口能力
type Service interface {
	Register(ctx context.Context, user *User) (*User, error)

	SendRegisterMail(ctx context.Context, user *User) error

	VerifyRegister(ctx context.Context, captcha string) error

	Login(ctx context.Context, user *User) (*User, error)

	GetUser(ctx context.Context, userID uint) (*User, error)

	GetLoginUser(ctx context.Context) (*User, error)
}

// 定义用户服务的领域模型 Do
type User struct {
        // 数据库模型：PO 部分
	gorm.Model
	UserName  string    `gorm:"column:username"`
	Password  string    `gorm:"column:password"`
	Email     string    `gorm:"column:email"`
	CreatedAt time.Time `gorm:"column:created_at"`

	Captcha string `gorm:"-"` // 注册验证码
	Token   string `gorm:"-"` // 用户token
}

```

按照DDD的设计思想，在实现一个业务模块的时候通常会有三种模型的需求
- 展示层DTO，最终在接口中返回，在网络上传输的数据对象
- 领域模型DO，对业务的抽象，不一定依赖数据表字段
- 数据库模型层PO，对应数据库中的某个数据表，通过这个模型可以直接操作数据表

用户模块的业务只涉及一个数据表，因此这里将数据库模型层和领域模型层进行了合并，使用者自己写业务模块时如果涉及多张表，可以将数据库模型单独拆分出来。

{{< img name="demo2"  lazy=false size="tiny" >}}

### service.go

service.go提供具体的实现，它至少需要提供一个实例化的方法 NewService(params ...interface{}) (interface{}, error)

### provider.go

provider.go 提供服务适配的实现，实现一个Provider必须实现对应的五个方法

- Name() // 指定这个服务提供者提供的服务对应的接口的关键字
- Register() // 这个服务提供者注册的时候调用的方法，一般是指定初始化服务的函数名
- IsDefer() // 这个服务是否是使用时候再进行初始化，false为注册的时候直接进行初始化服务
- Params() // 初始化服务的时候对服务注入什么参数，一般把 container 注入到服务中
- Boot() // 初始化之前调用的函数，一般设置一些全局的Provider

