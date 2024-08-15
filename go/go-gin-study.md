# Gin入门案例

## 安装

现在并安装

```shell
go get -u github.com/gin-gonic/gin
```

## 使用GET,POST,PUT,DELETE

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/testGet", func(context *gin.Context) {
		context.JSON(200, gin.H{
			"message": "test get response",
		})
	})
	r.POST("testPost", func(context *gin.Context) {
		context.JSON(200, gin.H{
			"message": "test post response",
		})
	})
	r.PUT("testPut", func(context *gin.Context) {
		context.JSON(200, gin.H{
			"message": "test put response",
		})
	})
	r.DELETE("testDelete", func(context *gin.Context) {
		context.JSON(200, gin.H{
			"message": "test delete response",
		})
	})
	err := r.Run()
	if err != nil {
		return
	}
}

```



## 获取路径中的参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	// 此规则只能匹配/user/1001,但是不能匹配/user/或者/user这种格式
	r.GET("/user/:id", func(context *gin.Context) {
		id := context.Param("id")
		context.JSON(200, gin.H{
			"message": "get id is " + id,
		})
	})
	r.GET("/user/:id/*action", func(context *gin.Context) {
		id := context.Param("id")
		action := context.Param("action")
		context.JSON(200, gin.H{
			"message": "the id is " + id + " and action is " + action,
		})
	})
	// 默认启动的是 8080端口，也可以自己定义启动端口
	err := r.Run()
	// router.Run(":3000") for a hard coded port
	if err != nil {
		return
	}
}

```

## 获取GET的参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/welcome", func(context *gin.Context) {
		firstName := context.DefaultQuery("firstName", "王进喜")

		lastName := context.Query("lastName") // context.Request.Query().Get("lastName")
		context.JSON(200, gin.H{
			"message": "the firstName is " + firstName + " and lastName is " + lastName,
		})
	})
	// 默认启动的是 8080端口，也可以自己定义启动端口
	err := r.Run()
	// router.Run(":3000") for a hard coded port
	if err != nil {
		return
	}
}

```

![image-20230101224020447](/Users/yangxudong/Library/Application Support/typora-user-images/image-20230101224020447.png)

## 获取POST中的参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.POST("/formPost", func(context *gin.Context) {
		name := context.PostForm("name")
		nick := context.DefaultPostForm("nick", "default nick")

		context.JSON(200, gin.H{
			"message": "the name is " + name + " and the nick is " + nick,
		})
	})
	// 默认启动的是 8080端口，也可以自己定义启动端口
	err := r.Run()
	// router.Run(":3000") for a hard coded port
	if err != nil {
		return
	}
}

```

![image-20230101224522009](/Users/yangxudong/Library/Application Support/typora-user-images/image-20230101224522009.png)

## GET跟POST混合

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.POST("/postget", func(context *gin.Context) {
		id := context.Query("id")
		page := context.DefaultQuery("page", "0")
		name := context.PostForm("name")
		message := context.PostForm("message")

		context.JSON(200, gin.H{
			"message": "id is " + id + " page is " + page + " name is " + name + " message is " + message,
		})
	})
	// 默认启动的是 8080端口，也可以自己定义启动端口
	err := r.Run()
	// router.Run(":3000") for a hard coded port
	if err != nil {
		return
	}
}

```



![image-20230101225028058](/Users/yangxudong/Library/Application Support/typora-user-images/image-20230101225028058.png)

## 上传文件

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
)

func main() {
   r := gin.Default()
   // 给表单限制上传大小， (默认32Mib)
   r.MaxMultipartMemory = 8 << 20 // 8 Mib
   r.POST("/upload", func(context *gin.Context) {
      // 单文件
      file, _ := context.FormFile("myFile")
      fmt.Println("file name:", file.Filename)
      // 指定上传文件的路径
      err := context.SaveUploadedFile(file, "./"+file.Filename)
      if err != nil {
         return
      }
      context.JSON(200, gin.H{
         "message": "success",
      })
   })
   r.POST("uploads", func(context *gin.Context) {
      form, _ := context.MultipartForm()
      files := form.File["myFiles"]
      for _, file := range files {
         fmt.Println("fileName:", file.Filename)
         err := context.SaveUploadedFile(file, "./"+file.Filename)
         if err != nil {
            return
         }
      }
      context.JSON(200, gin.H{
         "message": "success",
      })
   })
   // 默认启动的是 8080端口，也可以自己定义启动端口
   err := r.Run()
   // router.Run(":3000") for a hard coded port
   if err != nil {
      return
   }
}
```



![image-20230101231433400](/Users/yangxudong/Library/Application Support/typora-user-images/image-20230101231433400.png)

































