---
title: Go代码概览
date: 2018-12-16 00:11:06
cover: 
categories: Go
tags:
- Go
---

介绍
--

这篇文档介绍了如何进行简单的Go程序开发，如何获取和构建库，以及安装第三方库和命令行工具的标准方法。

Go要求你以特定的方式组织代码。请仔细阅读本文档，它解释了使用Go安装启动和运行程序的最简便方法。

你也可以查看[位于youtube上的视频教学](https://www.youtube.com/watch?v=XCsL89YtqCs)(请自备梯子)。

组织代码
----

### 预览

*   Go开发者通常将所有的Go代码保存在一个工作区 workspace 中。
*   工作空间包含多种版本控制存储库（例如，由Git）。
*   每个存储库都包含一个或多个包 package。
*   每个包 package 由一个或多个Go源文件组成。
*   包的目录路径决定了它的导入路径。

请注意，这与开发语言不同，在其他语言中，每个项目都有独立的工作空间，且工作空间与版本控制存储库紧密相关。

工作空间 Workspaces
---------------

工作空间(本质是一个文件夹)是一个目录层次结构，其根目录有三个目录。

*   src 目录包含了 .go 源文件
*   pkg 目录包含了编译后生成的文件
*   bin 目录包含了生成的可执行文件

src中的源码编译后会安装到pkg和bin目录下。

src目录通常包含多个版本控制存储库（如Git或Mercurial），用于跟踪一个或多个源包的开发

为了更直观的理解，这里有一个例子：

```sh
bin/
    hello                          # command executable
    outyet                         # command executable
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a           # package object
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go               # command source
	outyet/
	    main.go                # command source
	    main_test.go           # test source
	stringutil/
	    reverse.go             # package source
	    reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go              # package source
	    writer.go              # package source
    ... (省略了更多的仓库和包) ...
```

这个树状结构展示了这个工作空间workspace有两个仓库(example 和 image)。example仓库包含hello包、outyet包，和一个stringutil库(你可以在pkg文件夹里看到也有stringutil)。 img仓库包含了bmp包和其他省略的文件。

_原文是：The tree above shows a workspace containing two repositories (example and image). The example repository contains two commands (hello and outyet) and one library (stringutil). The image repository contains the bmp package and several others.  
对于command source和package source的区别，个人认为是outyet包内的main.go中main函数调用了hello.go，编译后生成二进制可执行文件，所以在bin中可以看到他们。 如有错误，还望指正。_

典型的工作空间包含许多包含许多包和命令的源代码库。大多数Go程序员将所有的Go源代码和依赖关系保存在一个工作空间中。

Commands 和 libraries 是由不同的源代码包构建的。我们稍后会讨论这个区别。

GOPATH 环境变量
-----------

GOPATH 环境变量指明了你的工作空间 workspace。它默认为一个名为go的主目录，所以各操作系统就会找下面的这些目录：Unix的`$HOME/go`，Plan 9的 `$home/go`和`%USERPROFILE%\go`，Windows的`C:\Users\YourName\go`。

如果你想换一个目录，必须[把GOPATH设置为对应的目录](https://pkg.go.dev/cmd/go#hdr-GOPATH_and_Modules)(另一个常见设置是GOPATH=$HOME)。注意GOPATH**绝对不能**与Go安装文件夹是同一个路径

在命令行输入 go env GOPATH 会显示出当前有效的GOPATH，如果环境变量未设置，它将打印默认位置。

为方便起见，将工作空间的bin子目录添加到PATH中：

```sh
$ export PATH=$PATH:$(go env GOPATH)/bin
```

为了简洁起见，本文剩余部分的脚本使用$ GOPATH而不是$（go env GOPATH）。如果你还没有设置GOPATH，为了使脚本正常运行，你可以用这些命令替换$ HOME / go，或者运行：

```sh
$ export GOPATH=$(go env GOPATH)

```

想了解更多关于GOPATH环境变量参数，可以查阅[go命令行文档](https://pkg.go.dev/cmd)

想自行设置工作空间workspace的所在位置，[请查阅安装文档](https://go.dev/doc/install)

### 包导入路径

包路径是一个字符串，是一个包的唯一标识。包的导入路径对应于其在工作空间 workspace 内或远程存储库中的位置。

标准库中的包被赋予了很短的导入路径名称，如 fmt 和 net/http。对于自己的软件包，你必须选择一个基本路径， 这个路径不能在将来添加到标准库或其他外部库时发生冲突(即必须是唯一的)。

如果将代码保存在某个源代码库中，则应该使用该代码源的根作为基本路径。例如，如果在github.com/user上有GitHub帐户，那应该是你的基本路径。

请注意，在构建之前，不要将代码发布到远程存储库，在实践中，你可以选择任意路径名称，只要它是标准库和更大的Go生态系统所独有的。

我们将使用github.com/user作为我们的基本路径。在工作区内创建一个目录，在其中保留源代码：

```sh
$ mkdir -p $GOPATH/src/github.com/user

```

### 你的第一个程序

要编译和运行一个简单的程序，首先选择一个包路径（我们将使用github.com/user/hello）并在工作空间workspace内创建一个对应的包目录：

```
$ mkdir $GOPATH/src/github.com/user/hello
```

接着在该文件夹下建立名为 hello.go 的文件，包含如下代码:

```go
package main

import "fmt"

func main() {
	fmt.Printf("Hello, world.\n")
}
```

现在你可以编译并安装它:

```sh
$ go install github.com/user/hello
```

你可以在系统的任何位置运行这行命令。go编译工具通过在GOPATH指定的工作空间内查找github.com/user/hello包来查找源代码。

如果从软件包目录运行go install，也可以省略软件包路径：

```sh
$ cd $GOPATH/src/github.com/user/hello
$ go install
```

这条命令编译 hello 文件并产生一个可执行的二进制文件。然后，它将这个二进制文件安装到工作空间的bin目录（在Windows下，是hello.exe文件）。 在当前例子中，是$ GOPATH/bin/hello，即$ HOME/go/bin/hello。

只有当错误发生时，编译工具才会打印输出，所以如果没有任何输出，说明已经成功执行了。

你可以通过在命令行键入完整路径来运行该程序：

```sh
$ $GOPATH/bin/hello
Hello, world.
```

如果你已经将$ GOPATH/bin添加到PATH中，只需输入二进制名称即可：

```sh
$ hello
Hello, world.
```

如果你正在使用Git，现在是初始化仓库，添加文件以及提交第一个更改的好时机。  
注意这是可选的：你不一定非得使用版本控制来编写Go代码。

```sh
$ cd $GOPATH/src/github.com/user/hello
$ git init
Initialized empty Git repository in /home/user/work/src/github.com/user/hello/.git/
$ git add hello.go
$ git commit -m "initial commit"
[master (root-commit) 0b4507d] initial commit
 1 file changed, 1 insertion(+)
  create mode 100644 hello.go
```

把代码推到远程仓库上就当作给你留下的小练习吧。

### 你的第一个依赖库

我们来编写一个库，并在hello程序中使用它。

第一步是选择一个包路径（我们将使用github.com/user/stringutil）并创建包目录：

```sh
$ mkdir $GOPATH/src/github.com/user/stringutil
```
接着在stringutil文件夹内创建名为 reverse.go 的文件，包含如下代码：

```go
// Package stringutil contains utility functions for working with strings.
package stringutil

// Reverse returns its argument string reversed rune-wise left to right.
func Reverse(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
```

现在使用go build命令来试着编译一下：

```sh
$ go build github.com/user/stringutil
```

如果正在当前目录下，你也可以这样：

```sh
$ go build
```

这将不会产生输出文件(注意，因为代码中并没有main)。为此，你必须使用go install，它将package对象放置在工作区的pkg目录中。

在确认了stringutil包的构建之后，修改原来的hello.go（在$ GOPATH/src/github.com/user/hello中）

```go
package main

import (
	"fmt"

	"github.com/user/stringutil"
)

func main() {
	fmt.Printf(stringutil.Reverse("!oG ,olleH"))
}
```

每当使用go命令安装一个包或二进制文件，它也会安装其所具有的任何依赖关系。所以当你安装hello程序，stringutil包也会被自动安装。

```sh
$ go install github.com/user/hello
```

运行新版本的程序，你应该看到一个新的，反转过来的消息：

```sh
$ hello
Hello, Go!
```

现在你的工作空间应该是这样的目录结构：

```sh
bin/
    hello                 # command executable
pkg/
    linux_amd64/          # this will reflect your OS and architecture
        github.com/user/
            stringutil.a  # package object
src/
    github.com/user/
        hello/
            hello.go      # command source
        stringutil/
            reverse.go    # package source
```

注意，go install将stringutil.a对象放置在映射其源目录的pkg/linux_amd64内的目录中。这样以后的go 命令可以找到包对象，避免了不必要的重新编译。linux_amd64部分是为了帮助交叉编译，并将反映你的系统的操作系统和体系结构。

go命令操作可执行文件是静态链接的;运行go程序时包对象不是必须存在

### 包名称

Go源文件中的第一条语句必须是：

```go
package name
```

其中name是导入包的默认名称。包中的所有文件必须使用相同的名称。

Go的惯例是包名称是导入路径的最后一个元素：导入为“crypto/rot13”的包应该被命名为rot13。

入口文件必须总是使用package main。

没有必要要求包名称在链接到一个二进制文件的所有软件包中是唯一的，只要导入路径（它们的完整文件名）是唯一的。

查看[Go进阶](/2018/12/16/go_2/)以了解更多。

测试
--

Go有一个由go test命令和测试包组成的轻量级测试框架。

可以通过创建名称以_test.go结尾的文件来编写一个测试，该文件包含名为TestXXX并带有签名func（t * testing.T）的函数。测试框架运行每个这样的函数;如果函数调用失败函数，如t.Error或t.Fail，则认为测试失败。

通过创建包含以下Go代码的$ GOPATH/src/github.com/user/stringutil/reverse_test.go文件，将测试添加到stringutil包中。

```go
package stringutil

import "testing"

func TestReverse(t *testing.T) {
	cases := []struct {
		in, want string
	}{
		{"Hello, world", "dlrow ,olleH"},
		{"Hello, 世界", "界世 ,olleH"},
		{"", ""},
	}
	for _, c := range cases {
		got := Reverse(c.in)
		if got != c.want {
			t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
		}
	}
}
```

使用命令执行测试：

```sh
$ go test github.com/user/stringutil
ok  	github.com/user/stringutil 0.165s
```

与之前一样，如果你正在当前目录下执行此命令，则可以省略软件包路径：

```sh
$ go test
ok  	github.com/user/stringutil 0.165s
```

查看[测试帮助文档](https://pkg.go.dev/cmd/go@go1.18.1#hdr-Test_packages)以了解更多。

远程包
---

包的路径可以描述如何使用Git或Mercurial等版本控制系统获取源代码。go会读取包的路径自动从远程存储库获取软件包。例如，上面的示例也保存在GitHub github.com/golang/example托管的Git存储库中。你可以使用go get命令获取，构建并安装它：

```sh
$ go get github.com/golang/example/hello
$ $GOPATH/bin/hello
Hello, Go examples!
```

如果指定的包不在工作空间内，go get将放置在由GOPATH指定的第一个工作区内。（如果包已经存在，则跳过远程抓取，并执行与go install相同的操作。）

执行go get命令后，工作空间目录树现在应该如下所示：

```sh
bin/
    hello                           # command executable
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a            # package object
        github.com/user/
            stringutil.a            # package object
src/
    github.com/golang/example/
	.git/                       # Git repository metadata
        hello/
            hello.go                # command source
        stringutil/
            reverse.go              # package source
            reverse_test.go         # test source
    github.com/user/
        hello/
            hello.go                # command source
        stringutil/
            reverse.go              # package source
            reverse_test.go         # test source
```

托管在GitHub上的hello文件依赖于同一个仓库中的stringutil包。 hello.go文件中的导入使用相同的导入路径约定，所以go get命令也能够定位和安装依赖包。

```go
import "github.com/golang/example/stringutil"
```

这个约定是让你的Go软件包供其他人使用的最简单的方法。你可以通过[Go Wiki](https://go.dev/wiki/Projects) 和[godoc.org](https://pkg.go.dev/) 查看可以引用的Go项目列表。

你可以查看[go命令行文档](https://pkg.go.dev/cmd)以了解更多

