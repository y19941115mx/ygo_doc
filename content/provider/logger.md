---
title: 日志服务
---

使用的并不是官方的 error 库，而是 github.com/pkg/errors 库，第三方 error 库，错误信息会带上堆栈信息，更容易定位问题


```go
logger.Error(c, err.Error(), map[string]interface{}{
			"stack": fmt.Sprintf("%+v", err),
		})
```