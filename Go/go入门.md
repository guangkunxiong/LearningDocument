# Go

## 创建第一个Go程序

Go 源文件总是用全小写字母形式的短小单词命名，并且以.go 扩展名结尾。如果要在源文件的名字中使用多个单词，我们通常直接是将多个单词连接起来作为源文件名，而不是使用其他分隔符，比如下划线。也就是说，我们通常使用 `helloworld.go` 作为文件名而不是 `hello_world.go`。这是因为下划线这种分隔符，在 Go 源文件命名中有特殊作用。

示例代码

```go
package main

import "fmt"

func main() {
    fmt.Println("hello, world")
}
```

```go
package main
```

这一行代码定义了 Go 中的一个包 package。包是 Go 语言的基本组成单元，通常使用单个的小写单词命名，一个 Go 程序本质上就是一组包的集合。所有 Go 代码都有自己隶属的包，在这里我们的“hello，world”示例的所有代码都在一个名为 main 的包中。**main 包在 Go 中是一个特殊的包，整个 Go 程序中仅允许存在一个名为 main 的包。**

``` go
func main() {
    fmt.Println("hello, world")
}
```

**这里的 main 函数会比较特殊：当你运行一个可执行的 Go 程序的时候，所有的代码都会从这个入口函数开始运行。**这段代码的第一行声明了一个名为 main 的、没有任何参数和返回值的函数。如果某天你需要给函数声明参数的话，那么就必须把它们放置在圆括号 () 中。

**注意点 1：标准 Go 代码风格使用 Tab 而不是空格来实现缩进的**，当然这个代码风格的格式化工作也可以交由 `gofmt` 完成。

**注意点 2：**我们调用了一个名为 `Println `的函数，这个函数位于 `Go` 标准库的 `fmt` 包中。为了在我们的示例程序中使用 `fmt` 包定义的` Println` 函数，我们其实做了两步操作

**注意点 3：**我们还是回到 main 函数体实现上，把关注点放在传入到 `Println` 函数的字符串“hello, world”上面。你会发现，我们传入的字符串也就是我们执行程序后在终端的标准输出上看到的字符串。

### Go 语言中程序是怎么编译的？

``` go
$go build main.go //编译go
//查看生成的可执行文件
$ls
main* main.go
```

上面显示的文件里面有我们刚刚创建的、以.go 为后缀的源代码文件，还有刚生成的可执行文件（Windows 系统下为 main.exe，其余系统下为 main）。

**Go 是一种编译型语言，这意味着只有你编译完 Go 程序之后，才可以将生成的可执行文件交付于其他人，并运行在没有安装 Go 的环境中。**

``` go
//编译并运行
$go run main.go
hello, world
```

#### Go module 构建模式

``` go
//hellomodule下创建稍微复杂的代码
package main

import (
  "github.com/valyala/fasthttp"
  "go.uber.org/zap"
)

var logger *zap.Logger

func init() {
  logger, _ = zap.NewProduction()
}

func fastHTTPHandler(ctx *fasthttp.RequestCtx) {
  logger.Info("hello, go module", zap.ByteString("uri", ctx.RequestURI()))
}

func main() {
  fasthttp.ListenAndServe(":8081", fastHTTPHandler)
}
```



为的是彻底解决 Go 项目复杂版本依赖的问题。

在 Go 1.16 版本中，Go module 已经成为了 Go 默认的包依赖管理机制和 Go 源码构建机制。Go Module 的核心是一个名为 go.mod 的文件，在这个文件中存储了这个 module 对第三方依赖的全部信息。

``` 

$go mod init github.com/bigwhite/hellomodule
go: creating new go.mod: module github.com/bigwhite/hellomodule
go: to add module requirements and sums:
  go mod tidy
//go mod init 命令的执行结果是在当前目录下生成了一个 go.mod 文件：
$cat go.mod
module github.com/bigwhite/hellomodule

go 1.16
```

其实，一个 module 就是一个包的集合，这些包和 module 一起打版本、发布和分发。go.mod 所在的目录被我们称为它声明的 module 的根目录。

不过呢，这个时候的 go.mod 文件内容还比较简单，第一行内容是用于声明 module 路径（module path）的。而且，module 隐含了一个命名空间的概念，module 下每个包的导入路径都是由 module path 和包所在子目录的名字结合在一起构成。

go.mod 中没有包的版本信息，我们需要按提示手工添加信息到 go.mod 中，除了按提示手动添加外，我们也可以使用 go mod tidy 命令，让 Go 工具自动添加。

```

$go mod tidy       
go: downloading go.uber.org/zap v1.18.1
go: downloading github.com/valyala/fasthttp v1.28.0
go: downloading github.com/andybalholm/brotli v1.0.2
... ...
```

```
//go mod tidy 执行后，我们 go.mod 的最新内容变成了这个样子：
module github.com/bigwhite/hellomodule

go 1.16

require (
  github.com/valyala/fasthttp v1.28.0
  go.uber.org/zap v1.18.1
)
```

这个时候，go.mod 已经记录了 `hellomodule` 直接依赖的包的信息。不仅如此，`hellomodule` 目录下还多了一个名为 `go.sum` 的文件，这个文件记录了 `hellomodule` 的直接依赖和间接依赖包的相关版本的 `hash` 值，用来校验本地包的真实性。在构建的时候，如果本地依赖包的` hash` 值与 `go.sum` 文件中记录的不一致，就会被拒绝构建。

有了 go.mod 以及 `hellomodule` 依赖的包版本信息后，我们再来执行构建：

```
$go build main.go
$ls
go.mod    go.sum    main*    main.go
```

小结

- Go 包是 Go 语言的基本组成单元。一个 Go 程序就是一组包的集合，所有 Go 代码都位于包中；
- Go 源码可以导入其他 Go 包，并使用其中的导出语法元素，包括类型、变量、函数、方法等，而且，main 函数是整个 Go 应用的入口函数；
- Go 源码需要先编译，再分发和运行。如果是单 Go 源文件的情况，我们可以直接使用 go build 命令 +Go 源文件名的方式编译。不过，对于复杂的 Go 项目，我们需要在 Go Module 的帮助下完成项目的构建。