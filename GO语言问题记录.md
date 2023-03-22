# GO语言问题记录

### 1\.**在IDEA中下载第3方依赖包后，发现IDEA认不到**

如果您的代理设置不正确，请确保已正确设置 GOPROXY 环境变量。在 IntelliJ IDEA 中，可以通过以下步骤设置 GOPROXY 环境变量：

- 打开“Settings”  > "Languages & Framkeworks" > “Go” > “Go Modules”。

- 在“Environment variables”下添加 GOPROXY，并将其设置为您的代理地址。

- GOPROXY=<https://goproxy.io>

<img src="C:/jizong.liang/note/配图/go_001.PNG"/>

### **如何下载项目中的第3方依赖**

go mod tidy

### **一次错误的记录**

我的./main.go程序设计如下：

package main

import (

```
"github.com/gin-gonic/gin"
```

)

func main() {

```
g:=gin.New()

//Router(g)

v1 := g.Group("api/v1")

{

v1.POST("/template", Template)

}

g.Run(":8080")
```

}

在./main.go的同一个目录下有另外一个文件./Template.go，代码如下：

package main

import "github.com/gin-gonic/gin"

func Template(c \*gin.Context) {

```
c.JSON(200, gin.H{

"message": "template",

})
```

}

这个时候执行main，系统会报错.\\main.go:13:24: undefined: Template

但是如果我将Template调整到./service/下，即./service/Template.go

./main.go调整为：

package main

import (

```
"example.com/server/service"

"github.com/gin-gonic/gin"
```

)

func main() {

```
g:=gin.New()

//Router(g)

v1 := g.Group("api/v1")

{

v1.POST("/template", service.Template)

}

g.Run(":8080")
```

}

这个时候就可以编译通过