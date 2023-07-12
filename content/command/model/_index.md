---
title: 数据库模型命令
resources:
  - name: model1
    src: "model1.png"
    title: 模型命令
  - name: model2
    src: "model2.png"
    title: model test
  - name: model3
    src: "model3.png"
    title: model gen
  - name: model4
    src: "model4.png"
    title: model api
  - name: flag
    src: "flag.png"
    title: 参数
  - name: flag2
    src: "flag2.png"
    title: 参数
---

model 命令提供了自动根据数据库模型创建代码的命令行工具，如果已经定义好数据库表，能帮助使用者节省不少书写数据库CURD样板代码的时间

{{< hint type=warning >}}

需要在项目根目录执行命令，否则可能会有路径问题

{{< /hint >}}

{{< img name="model1" size="large" lazy=false >}}

包含三个命令：

* ygo model test 测试数据库是否可以连接
* ygo model gen 通过数据表自动生成gorm model代码
* ygo model api 通过数据表自动生成api代码

## model test

这个命令测试配置的数据库是否能够连接，以及数据库中都有哪些表

{{< img name="model2"  lazy=false >}}

## model gen

这个命令生成数据表对应的gorm的model

{{< img name="model3"  lazy=false >}}

接受两个参数:
- database: 用来表示模型连接的数据库配置地址，默认是database.default，表示config目录下的{env}目录下的database.yaml中的default配置
- output：用来表示模型文件的输出地址，建议填写相对路径
{{< img name="flag"  lazy=false >}}

## model api

这个命令会生成数据表对应的CRUD接口代码

{{< img name="model4"  lazy=false >}}


接受两个参数:
- database: 用来表示模型连接的数据库配置地址，默认是database.default，表示config目录下的{env}目录下的database.yaml中的default配置
- module：接口代码的模块名称 {http_module}/{module} 会作为代码的输出路径
- tables：可以通过参数直接指定数据表，跳过交互选择数据表的过程

{{< img name="flag2"  lazy=false >}}