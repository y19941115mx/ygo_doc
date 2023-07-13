---
title: 应用服务
weight: -2
---

```go
func (c *Context) MustMakeApp() contract.App {
	return c.MustMake(contract.AppKey).(contract.App)
}
```