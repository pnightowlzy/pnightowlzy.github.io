---
layout: wiki
title: Swagger 
cate1: Go
cate2: Web development
description: web 开发之API接口文档
keywords: API, GO
---
制作API文件，是前后端开发人员之间沟通的桥梁。

参考：https://github.com/swaggo/swag/blob/master/README_zh-CN.md

# 步骤说明
1. go install github.com/swaggo/swag/cmd/swag@latest
2. 在项目根目录下，执行 swag init 命令，生成swagger文件。
3. 修改 main.go 文件，添加注释。
```go
// @title Server Name
// @version 1.0
// @description Server Description
// @BasePath /basepath
func main() {
    r := gin.Default()

    c := controller.NewController()

    v1 := r.Group("/api/v1")
    {
        accounts := v1.Group("/accounts")
        {
            accounts.GET(":id", c.ShowAccount)
            accounts.GET("", c.ListAccounts)
            accounts.POST("", c.AddAccount)
            accounts.DELETE(":id", c.DeleteAccount)
            accounts.PATCH(":id", c.UpdateAccount)
            accounts.POST(":id/images", c.UploadAccountImage)
        }
    //...
    }
    r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))
    r.Run(":8080")
}
//...
```

4. 设置 swag 注释格式。
```go
// ShowAccount godoc
// @Summary      Show an account
// @Description  get string by ID
// @Tags         accounts
// @Accept       json
// @Produce      json
// @Param        id   path      int  true  "Account ID"
// @Success      200  {object}  model.Account
// @Failure      400  {object}  httputil.HTTPError
// @Failure      404  {object}  httputil.HTTPError
// @Failure      500  {object}  httputil.HTTPError
// @Router       /accounts/{id} [get]
```

5. swag init 命令生成 swagger.json 文件。
6. 需要在main中加入文件引用。
```go
_ "server_name/docs"
```
7. 运行程序，然后在浏览器中访问 http://localhost:8080/swagger/index.html 。


## 可能存在的问题
1. [unkown LeftDelim and RightDelim in swag.Spec #1568](https://github.com/swaggo/swag/issues/1568)
```bash
go get -u github.com/swaggo/swag  
```

2. 没有找到docs。
需要在main中加入文件引用。
```go
_ "server_name/docs"
```