---
title: 快速开始
weight: -20
resources:
  - name: ygo
    src: "ygo.png"
    title: 安装验证
  - name: demo
    src: "demo.png"
    title: 示例
---

这个文档为用户介绍了如何快速开始使用Ygo框架，包括框架的安装与基本的配置
<!--more-->

{{< toc >}}

## 安装准备

框架依赖：

* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [GO](https://go.dev/doc/install)
* [GCC](https://sourceforge.net/projects/mingw/) 

Git 是必需的：
* 使用 git 从源代码仓库下载Ygo框架代码
* 使用 Ygo 创建项目功能需依赖git

Go、GCC 是必需的：
* 从源代码构建 Ygo
* 安装 Ygo 命令行工具
  
## 框架安装

有两种方式完成[Ygo](https://github.com/y19941115mx/ygo)框架的安装

### 方式1：源码编译

```shell
# 下载对应版本的源码
git clone https://github.com/y19941115mx/ygo 

# 进入源码文件夹
cd ygo

# 安装 ygo 框架
go install 
```

### 方式2： 远程安装

```shell
go install github.com/y19941115mx/ygo@latest
```

远程安装本质上就是下载对应版本的源代码并在本地执行编译安装，[版本清单](https://github.com/y19941115mx/ygo/releases)中可以看到框架的所有版本，可以替换 `@latest` 为对应版本号

### 安装验证

执行Ygo的命令行工具，验证框架是否安装成功

```shell
ygo
```

可以看到命令行的执行结果
{{< img name="ygo" size="large" lazy=false >}}

## 框架使用

框架的使用需要以下几个步骤:

1. 创建你的新项目

   ```shell
   ygo new 
   ```
   根据命令行的交互提示，完成项目的创建


2. 切换到项目的根目录

   ```shell
   cd <project>
   ```

3. 安装项目依赖

    ```shell
    go mod tidy
    ```

4. 修改数据库的配置文件 `config\development\database.yml ` 

   ```yaml
    # 通用配置
    conn_max_idle: 10 # 连接池最大空闲连接数
    conn_max_open: 100 # 连接池最大连接数
    conn_max_lifetime: 1h # 连接数最大生命周期
    protocol: tcp # 传输协议
    loc: Local # 时区

    # 默认数据源配置 支持配置多个数据源
    default: 
        driver: mysql
        host: localhost
        port: 3306
        database: victor
        username: root
        password: "yy6689990"  # env(DB_PASSWORD) 支持使用环境变量
        charset: utf8mb4
        collation: utf8mb4_general_ci
        parse_time: true
    ```

5. 运行示例项目

   ```shell
   # 后面加上执行参数 app start 
   go run main.go app start
   ```
   访问浏览器[127.0.0.1:8888/swagger/index.html](127.0.0.1:8888/swagger/index.html) 即可访问demo的接口文档：
   {{< img name="demo" size="large" lazy=false >}}

{{< hint type=caution >}}

`ygo app start`命令启动的是框架中的web服务，与用户新创建的web项目没有关系。新创建的项目本质上是一个新的 Ygo 服务（执行项目编译的可执行文件可以使用ygo内置的所有命令和用户在项目中扩展的命令），这里用`./ygo` 表示项目编译出的可执行文件，启动项目的web服务需要执行命令`./ygo app start`，其他内置命令使用安装的 ygo 命令即可

{{< /hint >}}

```shell
# 切换项目根目录
cd <project>

# 项目根目录执行编译
go build -o ygo

# 运行 web 服务
./ygo app start 
```

至此，我们完成了使用ygo新建项目、快速启动项目的工作，下一步用户可以使用[ygo model](/command/model)命令，基于数据库快速完成api的创建