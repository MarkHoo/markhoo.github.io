---
title: Go进阶
date: 2018-12-16 00:10:05
cover: 
categories: Go
tags:
- Go
---

文档介绍
----

Go是一门年轻的语言。虽然它借鉴了现有语言的思想，但自身独有的特性使得与其他语系相近的语言在代码表现上有所不同。 将C++或Java程序直接转换为Go可能会产生令人不太满意的结果——这是思考方式上的不同。 从Go的角度思考问题可能产生一个运行正常但与C++、Java相比完全不同的程序。换句话说，为了写好Go程序， 重点在于理解Go的特性及惯用法，例如命名、格式化、项目结构搭建等，这样你的程序可读性就更强。

这篇文档着重介绍了编写Go代码的要点。在此之前，强烈建议你了解并熟悉[Go学习指南](/2018/12/16/go_1/)，[如何编写Go代码](/2018/12/16/go_3/)和[语言规范](https://go.dev/ref/spec)

例子
--

[Go 标准库源码](https://golang.google.cn/src/) 不仅作为核心库，还作为如何使用Go语言的例子。 而且，许多包都含有可直接运行的可执行示例。如果你对如何解决某个问题可能存在疑问，标准库的文档，源码和示例也许可以给你提供灵感。

格式化代码
-----

格式问题是最争议性但最不重要的。人们可以适应不同的代码格式，但是如果他们不需要这么做，如果每个人都遵守相同的风格， 那么浪费在这个话题的时间就会减少。

为此，在Go中我们采取了与其他语言不同的方法——让机器来处理格式问题。gofmt 可以像fmt一样在包级而不是源文件级上运行、读取Go程序， 并以标准的缩进和垂直对齐样式改变源文件，并在必要时重新格式化注释。如果你想知道如何处理一些新的代码格式，运行gofmt； 如果答案看起来不对，重新安排你的程序（或者提交一个关于gofmt的错误），格式的问题别去管它——交给机器就好。

作为一个例子，没有必要花时间在结构的字段上排列格式。 Gofmt会为你做到这一点。
```go
type T struct {
    name string // name of the object
    value int // its value
}
```
gofmt 会自动帮你排列成这样
```go
type T struct {
    name    string // name of the object
    value   int    // its value
}
```
标准包中的所有Go代码已经使用gofmt格式化。

有些格式化的细节你或许感兴趣：

缩进

*   gofmt默认使用制表符缩进。你也可以自己选择使用空格。

行长度

*   Go没有行长度的限制。

括号

*   Go比C和Java所需要的括号更少：if,for,switch这样的控制结构在语法上不需要括号。另外，与其他语言不同，运算符优先级层次更短，更清晰(间距表明了优先级)。

x<<8 + y<<16

注释
--

Go提供了C风格的 `/**/` 块级注释和C++风格的 `//` 行级注释。块注释大多作为包注释出现，同时也在表达式中或者禁用大量代码时用到。

godoc 命令——可以调起一个服务器——能够处理Go源文件中注释以形成文档。写在代码段首行且没有插入换行符的注释，会被 godoc 视作该代码段的解释文本。 注释的内容和风格决定了 godoc 生成的文档质量。

每个包都应该有个包注释，以块级注释的形式加在 package 代码之上。对于多文件组成的包，包注释只需要存在于一个文件中，任何一个都可以。  
包注释应该提供包的整体介绍及相关信息，它将首先出现在 godoc 生成页面的顶端。下面是个例子：
```go
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term \[ '*' | '+' | '?' \]
    term:
        '^'
        '$'
        '.'
        character
        '\[' \[ '^' \] character-ranges '\]'
        '(' regexp ')'
*/
package regexp
```

如果包内容比较简单，包注释也可以写成这样的格式：

```go
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```

注释**不需要**额外格式化，例如写成星状条幅。_注：有的程序员也许会这么写，但 godoc **不希望**这样_：

```go
//**********************************************************
//                       \_oo0oo\_                           *
//                      o8888888o                          *
//                      88" . "88                          *
//                      (| -_- |)                          *
//                      0\  =  /0                          *
//                    ___/`---'\\___                        *
//                  .' \\\|     |// '.                      *
//                 / \\\|||  :  |||// \                     *
//                / _||||| -:- |||||- \                    *
//               |   | \\\\  -  /// |   |                   *
//               | \\_|  ''\\---/''  |_/ |                   *
//               \  .-\\__  '-'  ___/-. /                   *
//             ___'. .'  /--.--\  `. .'___                 *
//          ."" '<  `.___\\_<|>_/___.' >' "".               *
//         | | :  `\- \\`.;`\ _ /`;.`/ - ` : | |             *
//         \  \ \`_.   \\_ __\ /__ _/   .-\` /  /             *
//     =====`-.____`.___ \\_____/___.-`___.-'=====          *
//                       `=---='                           *
//                                                         *
//                                                         *
//     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         *
//                                                         *
//               佛祖保佑        永无BUG                   *
//                                                         *
//                                                         *
//**********************************************************
```

如果这么写，生成的输出结果可能无法呈现出固定的文字宽度，所以你在使用 godoc 生成文档或 gofmt 格式化代码的时候， **坚决不要**用空格做格式对齐。

注释是纯文本，所以像 HTML 或 _this_ 这样的注释会逐字复制，请不要这样用。

godoc 已经调整为以固定宽度的字体显示，非常适合注释中包含代码段。你可以查看标准库中 fmt 包的注释显示效果，看起来挺不错的。

根据上下文，godoc 可能不会重新格式化注释，所以请确认注释内容直观上没有问题：包括正确的拼写、标点、段落、折行等。

重复：写在代码段首行且没有插入换行符的注释，会被 godoc 视作该代码段的解释文本，每一个包都应该有自己的包文档。 程序的每个导出(大写)名称(包括结构体、方法等)都应该有一个文档注释。

文档应该是完整的句子，这样有助于自动化生成和展现。较长的文档段落第一句话应该是一句总结以作为开头(原因在下面)。
```go
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```
如果每个文档注释都以它所描述的项目名称开头，那么 godoc 的输出可以通过 grep 查找。想象一下，你不记得名称 Compile ， 但正在寻找正则表达式的解析函数，所以你可以输入这样的命令行，
```sh
$ godoc regexp | grep -i parse
```
如果包中的所有文档注释都以 “This function...” 开始，grep 就没办法帮到你。 但是，由于标准库包会在文档中以项目名称开头，所以您会看到类似这样的内容，这会帮你找到想要查找的单词。

```sh
$ godoc regexp | grep parse
    Compile parses a regular expression and returns, if successful, a Regexp
    parsed. It simplifies safe initialization of global variables holding
    cannot be parsed. It simplifies safe initialization of global variables
$
```
Go的声明语法允许声明分组。一个单独的文档可以引入某一组相关的常量或变量。如果整个声明内容写的很敷衍，那么文档可能就没有什么太大帮助：

```go
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)

分组声明还可以标明代码内容之间的关系，例如一组变量受锁保护。

var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```

命名规范
----

Go的命名规范和其他所有编程语言一样重要。在Go中甚至上升到语义级别：包外的可见性由其第一个字符是否为大写来确定。 因此值得花一些时间讨论Go程序中的命名规范。

### 包名

在导入包时，包名将成为内容的访问者。例如导入 `bytes` 包：
```go
import "bytes"
```
导入该包后可直接调用 `bytes.Buffer`。

如果每个引用包的人都可以使用相同的名称来引用包内容，这意味着包名称应该足够的简洁、明了。

在Go中，包名应该是小写、单名词命名， 不应该有下划线或驼峰——简洁的包名有助于他人在引用时书写更方便。

不要担心包名重复，包名称只是导入的默认名称; 它不需要在所有代码中都是唯一的，在极少数情况下，导入包可以选择不同的名称在本地使用。 相同的包名很罕见，因为导入的文件名决定了正在使用哪个包（_这一点见仁见智，毕竟小公司非常多，并不是所有公司流程化做的都很好_）。

在Go中包名的另一个约定是：包名是代码所在目录的名称；位于 `src/encoding/base64` 目录下的包，被导入时写作 `encoding/base64`，但其包名是 `base64` ，既不是 `encoding_base64` 也不是 `encodingBase64` 。

包的使用者使用包名来调用包的内容，所以好的名称可以在很大程度上避免产生误解（_原文是：The importer of a package will use the name to refer to its contents, so exported names in the package can use that fact to avoid stutter_） ，不要用 `import .` 这样的写法，在测试时可能会这么干，但其他情况下请尽量避免。

例如， `buffered reader` 类型在 `bufio` 包下被命名为 `Reader`，而不是 `BufReader`， 原因在于使用者将 `BufReader` 视作 `bufio.Reader`，这样做更加清晰明了。 此外，由于导入的实体总是使用它们的包名称来寻址，所以`bufio.Reader`不会与`io.Reader`冲突。

类似的，创建 `ring.Ring` 新实例的函数通常被称为 `NewRing` ，但由于 `Ring` 是包导出的唯一类型，并且由于该包称为 `ring` ，所以它是 叫做 `New`，包的使用者调用时感觉像 `ring.New` 。使用包的结构来帮助你选择好的命名来方便他人使用。

另一个例子是 `once.Do; once.Do(setup)` 可读性强而且不容易被视作 `once.DoOrWaitUntilDone(setup)`而产生歧义。 起一个很长名称不会自动使可读性更强(_这绝对是在嘲讽OC_)。一个有用的文档评论往往比一个额外的长名称更有价值。

### Getters

_在Java中有人将Get方法称为属性获取器，Set方法称为属性设置器。这里的Getters含义与之相对应，Go开发组真是对其他语言怨念颇深。_

Go并不为getter和setter提供自动支持。开发者自己提供getter和setter没有任何问题，这样做通常是合适的，但是把Get放在getter的名字里既不是惯用的也不是必须的。

如果你有一个属性起名为 `owner`(小写不可导出)，那么getter方法应该起名为`Owner`(大写可导出)，而不是`GetOwner`。 大写名称的可导出机制提供了用于区分字段和方法的钩子。如果需要setter函数的话，可以命名为`SetOwner`。这样在实践中就具备了很好的可读性：
```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```
### 接口名

按照惯例，单方法接口是由方法名加上一个后缀或类似的修饰来构造一个代理名词：`Reader, Writer, Formatter, CloseNotifier`等等

在标准库中已经有一系列接口包含了如`Read, Write, Close, Flush, String`这样的方法名，起到了很好的示范作用。为了避免混乱，不要再给你的函数起这样的名字。 相反，如果你代码中实现了一个与标准库的接口，给它一个与接口中的方法相同的名字和签名;例如把你自己的格式化文本方法起名叫`String`而不是`ToString`。

### 驼峰命名

最后，Go惯例使用`MixedCaps`或者`mixedCaps`来替代下划线来分割多个单词

分号
--

像C一样，Go使用分号来终止语句(_原文是:Go's formal grammar uses semicolons to terminate statements_)，但与C不同，这些分号不会出现在源代码中。 词法分析器使用一个简单的规则来在扫描时自动插入分号，所以代码中几乎不需要输入分号(_for循环是需要的_)。

规则是这样的：如果换行符之前的最后一个标记是一个标识符（包括像int和float64这样的单词），一个基本文字（如数字或字符串常量）或下面这些关键字及符号中的某一个

break continue fallthrough return ++ -- ) }

那么词法分析器就在这之后插入一个分号。这可以被概括为：“如果换行符出现在可以表明语句结束的标记之后，就插入分号”。

分号也可以在大括号闭合之前即时省略，如果像这样就不需要分号
```go
go func() { for { dst <- <-src } }()
```
Go程序仅在诸如for循环分割子句的地方使用分号，以区分初始化程序，循环条件和循环的元素。如果你用这种方式编写代码，一行中分隔多个语句也需要分号。

引入分号插入规则的一个后果是，不能在下一行放置`if, for, switch, select`这些控制结构的开始大括号。如果你这样做了，语法分析器会把分号插入大括号之前， 这可能会导致不必要的错误。应该这样做：
```go
if i < f() {
    g()
}

而不是这样做：

if i < f()  // wrong!
{           // wrong!
    g()
}
```
控制结构
----

Go的控制结构与C语言类似，但在某些方面有极其重要的不同。 没有do或while循环，只是略微泛化的`for`; `switch`更灵活; `if和switch`接受一个可选的初始化语句，这一点类似于`for`; `break和continue`可以采用一个可选的标签来确定什么时候跳出或继续; 添加了新的控制结构`select`。 语法也略有不同：没有括号，而且主体必须始终用大括号分隔。

### if

在Go中简单的`if`结构看起来是这样的：
```go
if x > 0 {
    return y
}

if 条件后强制大括号鼓励在多行上写简单的if语句。无论如何，这样做是很好的语法风格，特别是当主体包含控制语句， 例如`return`或`break`时。

由于`if`和`switch`接受一个初始化语句，所以通常可以看到被用来设置局部变量。

if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```
在Go标准库中，你会发现有些if语句不按照上面那样写，特别是以`break，continue，goto或return`结尾时， 省略了不必要的else语句。_这样做可以减少逻辑嵌套，在出现多个err错误层级判断时，这是很重要的技巧，可以极大的提高可读性。_
```go
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```
这是一个处理一系列错误条件的常见例子。由于错误情况往往以return语句结束，所以产生的代码不需要else语句。

### 重复声明和赋值

在刚才的例子里我们看到了使用`:=`的短声明细节。这行代码调用`os.Open`读取文件。

```go
f, err := os.Open(name)
```

上面声明了两个变量`f`和`err`，在接下来的几行之后又调用了`f.Stat`获取文件信息。

```go
d, err := f.Stat()
```

这行代码看起来像是声明了`d`和`err`。注意：`err`的声明出现在2行代码中，这种重复是合法 的——`err`是由第一个语句声明的，但是只在第二个语句中重新赋值。这意味着`f.Stat`这行代码使用了已声 明的`err`变量，并给它一个新的值。

在`：=`声明中，即使已经声明了一个变量v也允许再次出现，只要满足以下条件：

*   这个声明与v的现有声明在同一个作用域范围内（如果v已经在外部作用域中声明，声明将创建一个新的变量§），
*   在初始化中相应的值可以赋给v，
*   声明中至少有一个其他变量正在被重新声明。(_这一条最重要_)

这种不寻常的特性是纯粹的实用主义，使得易于复用单一的值，在较长的`if-else`代码链中，你会经常遇到这种情况。

§这里值得注意的是，在Go中，函数参数和返回值的作用域与函数体相同，即使它们出现在包含代码的大括号的外面。

### for

Go的`for`关键字与C既有相同也有不同。它将C中的`for和while`结合了起来，并去除了`do-while`。有三种形式，其中只有一种是分号的。
```go
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```
短声明可以很容易地在循环中声明索引变量。
```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```
如果你循环访问数组，切片`slice`，字符串或映射`map`，或者从通道`channel`读取，`range`可以很好的帮你管理循环。
```
for key, value := range oldMap {
    newMap[key] = value
}
```
如果您只需要range中的第一项（键或索引），请扔掉第二项(_不写就行，但这里有坑，m的类型你得首先确认好_)：
```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```
如果您只需要范围中的第二项（值），请使用空白标识符（下划线）放弃第一个：
```go
sum := 0
for _, value := range array {
    sum += value
}
```
空白标识符有很多用途，我们将在下面讨论它。

对于字符串，`range`关键字替你做了很多工作，通过解析UTF-8分解出各个Unicode代码点。 错误的编码消耗一个字节并产生替换符文U + FFFD。(内置类型`rune`是单个Unicode代码点的Go术语， 详细信息请参见 [Go语言规范](/spec)。)
```go
for pos, char := range "中文\\x80辞海" { // \\x80 不是合法的 UTF-8 编码
		fmt.Printf("character %#U starts at byte position %d\\n", char, pos)
	}
}
```
会打印如下结果：
```
character U+4E2D '中' starts at byte position 0
character U+6587 '文' starts at byte position 3
character U+FFFD '�' starts at byte position 6
character U+8F9E '辞' starts at byte position 7
character U+6D77 '海' starts at byte position 10
```
最后，Go没有逗号运算符，也没有`++和--`(_你可以用`+=1或者-=1`来替代_)。 因此，如果你想在一个for中运行多个变量，你应该使用并行赋值（尽管这排除了++和-- ）。

```go
// Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

### Switch

Go的`switch`比C可用性更强。表达式不一定是常数，甚至不一定是整数，从上到下求值，直到找到匹配条件的代码段，如果switch没有表达式， 则相当于`if true`。因此`if-else-if-else`链与`switch`可以看作是等价的。

```go
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

case条件可以使用逗号分隔

```go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```
尽管与其他类似C的语言几乎没有什么共同之处，但break语句可以用来尽早终止切换。 有时候，有必要打破整个循环，在Go中可以通过在循环中加一个标签并“break”标签来完成。这个例子显示了两种用法。

```go
Loop:
	for n := 0; n < len(src); n += size {
		switch {
		case src[n] < sizeOne:
			if validateOnly {
				break
			}
			size = 1
			update(src[n])

		case src[n] < sizeTwo:
			if n+1 >= len(src) {
				err = errShortInput
				break Loop
			}
			if validateOnly {
				break
			}
			size = 2
			update(src[n] + src\[n+1])
		}
	}
```

当然，continue语句也接受一个可选标签，但是它只适用于循环。

作为本节的结尾，下面是使用两个switch语句的例子：

```go
// Compare returns an integer comparing the two byte slices,
// lexicographically.
// The result will be 0 if a == b, -1 if a < b, and +1 if a > b
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

### Type switch

switch也可以用来检查接口变量的动态类型，这种用法涉及到类型断言的语法。如果switch在表达式中声明了一个变量， 变量将在每个子句中都有对应的类型。在这种情况下重用名字是常见的惯用法，实际上，是在每个判断条件中声明了一个具有相同名称不同类型的变量。(_原文：A switch can also be used to discover the dynamic type of an interface variable. Such a type switch uses the syntax of a type assertion with the keyword type inside the parentheses. If the switch declares a variable in the expression, the variable will have the corresponding type in each clause. It's also idiomatic to reuse the name in such cases, in effect declaring a new variable with the same name but a different type in each case._)

```go
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", \*t) // t has type \*bool
case *int:
    fmt.Printf("pointer to integer %d\n", \*t) // t has type \*int
}
```

函数
--

### 多返回值

Go的一个特性是函数和方法可以返回多个值。这个做法可以用来改进C程序中的一些笨拙的操作方式：同时带内错误返回例如EOF的-1和修改地址传递的参数。

在C中，一个写入错误通过一个负数标明，并将错误代码分散在一个不同的位置。在Go中，Write方法可以同时返回一个计数值和一个错误值： “是的，你写了一些字节，但不是全部，因为你的设备出现了问题”。标准库os包中的Write方法如下：

```
func (file *File) Write(b \[\]byte) (n int, err error)
```

如文档所示，当`n!=len(b)`时这个函数返回写入的字节数，和一个不为nil的`error`值。这是一种常见的错误处理方式;更多示例请参阅错误处理部分。

多返回值可以玩出很多的花样。这有一个简单的例子，从一个字节片段中的一个位置抓取一个数字，返回数字和下一个位置。

```go
func nextInt(b []byte, i int) (int, int) {
    for ; i < len(b) && !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i < len(b) && isDigit(b[i]); i++ {
        x = x*10 + int(b[i]) - '0'
    }
    return x, i
}
```

你可以使用这个函数来遍历切片

```go
for i := 0; i < len(b); {
        x, i = nextInt(b, i)
        fmt.Println(x)
    }
```

### 为返回值命名

Go函数的返回值或结果“参数”可以被赋予名称，并用作常规变量，就像传入的参数一样。 当命名时， 它们在函数开始时被初始化为各自类型的零值; 如果函数执行一个不带参数的返回语句，那么结果参数的当前值被用作返回值。

在声明函数返回值类型时命名并不是强制性的，但是这样做可以使代码更简洁明了：代码即文档。如果我们命名nextInt的结果，那么显而易见明白返回int是哪个

```go
func nextInt(b []byte, pos int) (value, nextPos int) {
```

如果在声明函数返回值类型时命名，代码的可读性就很强。以下是io包中的一个例子：

```go
func ReadFull(r Reader, buf []byte) (n int, err error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return
}
```

### Defer 关键字

defer 会在函数正常返回，也就是return之后添加一个函数调用。 因此，defer通常用来释放函数内部资源。这是一种在其他语言不常见但有效的方式。(_与Java的finally和python的with不太一样_) 典型的例子是解锁互斥或关闭文件。(_原文:Go's defer statement schedules a function call (the deferred function) to be run immediately before the function executing the defer returns. It's an unusual but effective way to deal with situations such as resources that must be released regardless of which path a function takes to return. The canonical examples are unlocking a mutex or closing a file._)

```go
// Contents returns the file's contents as a string.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf\[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
```

像例子中这样使用defer来调用函数关闭文件有两个好处：首先，它保证你永远不会忘记关闭文件，这是一个容易犯的错误， 特别是如果你稍后编辑函数来添加一个新的返回路径。 其次，它靠近开始操作的位置，这比放置在函数结尾要清楚得多。

当defer执行时会对执行函数的参数进行计算，而不是执行调用时。单个延迟调用可以推迟多个函数的执行。(_原文:The arguments to the deferred function (which include the receiver if the function is a method) are evaluated when the defer executes, not when the call executes. Besides avoiding worries about variables changing values as the function executes, this means that a single deferred call site can defer multiple function executions. Here's a silly example._)

```go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

被defer修饰的函数会按照LIFO(先进先出)顺序执行，所以这段代码在结束后会打印`4 3 2 1 0`。 更合理的方式是通过程序追踪函数执行的顺序，

```go
func trace(s string)   { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }

// Use them like this:
func a() {
    trace("a")
    defer untrace("a")
    // do something....
}
```

让我们搞复杂点(_这个贱兮兮的例子被人发现出现在面试题上，原来出自官方文档_)

```go
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
```

输出结果

```
entering: b
in b
entering: a
in a
leaving: a
leaving: b
```

对于习惯于使用其他语言进行块级资源管理的程序员来说，延迟可能看起来很奇特，但其最有趣和最强大的恰恰是它并不基于块， 而是基于功能的。 在关于panic和recover的部分，我们将看到展现其特性的另一个例子。

Data
----

### Allocation with new

_原文标题就长这样，为防止有其他含义就原样放上来。_

Go有两个内存分配原语，即内置函数`new`和`make`。他们的实现方式不同，以适用于不同的情况，这可能造成混淆，但规则很简单。

让我们先来谈谈`new`，这是一个系统的内置函数，但与其他语言中的new不同，它不初始化内存，只是将其清零。 也就是说，`new(T)`为一个类型为`*T`的值分配一块已经归零的地址。在Go术语中，它返回一个指向类型T的新值的指针。(_原文是： Let's talk about new first. It's a built-in function that allocates memory, but unlike its namesakes in some other languages it does not initialize the memory, it only zeros it. That is, new(T) allocates zeroed storage for a new item of type T and returns its address, a value of type *T. In Go terminology, it returns a pointer to a newly allocated zero value of type T._)

`new`返回的内存已经清零，所以在设计数据结构时，其内部的各种属性零值无需进一步初始化。这意味着用户只需要new一下就能正常工作。例如bytes.Buffer的文档指出“Buffer的零值是一个准备使用的空缓冲区”。 同样，sync.Mutex没有显式的构造函数或Init方法。 相反，sync.Mutex的零值被定义为一个解锁的互斥体。(_原文：Since the memory returned by new is zeroed, it's helpful to arrange when designing your data structures that the zero value of each type can be used without further initialization. This means a user of the data structure can create one with new and get right to work. For example, the documentation for bytes.Buffer states that "the zero value for Buffer is an empty buffer ready to use." Similarly, sync.Mutex does not have an explicit constructor or Init method. Instead, the zero value for a sync.Mutex is defined to be an unlocked mutex._)

这个特性很有用，考虑这样的数据结构：

```go
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

类型SyncedBuffer的值可以在分配或刚刚声明时立即使用。在下面的代码片段中，p和v都可以正常工作而无需进一步初始化。

```go
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

### Constructors and composite literals

有时零值不够好用，需要一个初始化构造函数，就像在这个例子(_注意，这段代码是摘自系统源码，为包内自调用，故为小写，不是错误。_)

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}
```

File内含有很多个属性，我们可以简单的用composite literal(一种结构体的初始化方式)，每次使用都会创建一个新实例。

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}
```

请注意，与C不同的是，返回局部变量的地址是完全可以的， 与该变量相关联的存储在函数返回后仍然存在。 实际上，每一次复合文本的地址分配一个新的实例，所以我们可以把这两行合并成一行。

```go
 return &File{fd, name, nil, 0}
```

composite literal的属性字段按顺序排列，并且都必须存在。 然而，通过将元素明确地标记为键值对的形式，初始值设定项可以以任何顺序出现， 缺少的元素将保留为其各自的零值。 因此我们可以这样写

```go
return &File{fd: fd, name: name}
```

有一种极端情况，如果一个composite literal根本不包含任何字段，它会为该类型创建一个零值。`new(File)`和`&File{}`是等价的。

也可以为数组，切片和map集合创建composite literal，标签是索引或key。

(_文档这部分代码有问题，原文也讲不通，实际工作中给map切片赋值也没有这样干的，原文：Composite literals can also be created for arrays, slices, and maps, with the field labels being indices or map keys as appropriate. In these examples, the initializations work regardless of the values of Enone, Eio, and Einval, as long as they are distinct._)

```go
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

### Allocation with make

我们把讨论的话题扯回来。

内置函数`make(T, args)`与`new(T)`的使用目标不同，它仅被用于创建slice切片、map集合和channel通道， 调用后会返回初始化过的(内存不清零)、类型为T(不是`*T`)的值。调用make创建这三种类型的区别在于，所涉及到的内部数据结构必须在使用前先进行初始化。(_原文：It creates slices, maps, and channels only, and it returns an initialized (not zeroed) value of type T (not *T). The reason for the distinction is that these three types represent, under the covers, references to data structures that must be initialized before use._)

以切片为例，切片是包含有以下三项内容的描述符：指向内部数据元素(数组内)的指针，长度和容量(capacity)。在这些项被初始化前，切片是nil。(_原文：A slice, for example, is a three-item descriptor containing a pointer to the data (inside an array), the length, and the capacity, and until those items are initialized, the slice is nil._ )

对于slice切片、map集合和channel通道而言，`make`初始化了内部的数据结构以备调用。(_原文：For slices, maps, and channels, make initializes the internal data structure and prepares the value for use._)

```go
make([]int, 10, 100)
```

这行代码分配了一个含有100个整数的数组，然后创建一个长度为10，容量为100的slice，指向数组的前10个元素。 （当创建一个切片时，容量可以省略;更多信息请参见切片部分）。而`new([]int)`返回一个指向新分配的、空切片(_zeroed slice structure_)， 即指向nil的切片指针。

这个例子说明了`new`与`make`的不同：

```go
var p *[]int = new([]int)       // 分配了 slice 结构; *p == nil; 不常用
var v  []int = make([]int, 100) // slice v 指向含有100整数的数组

// 完全不必要的复杂写法
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// 惯用法
v := make([]int, 100)
```

重申一遍，`make`**仅支持maps, slices和channels**，并且**不返回指针**。为了显示的获得指针，使用`new`或使用`&`手动取地址。

### 数组

在详细规划内存时，数组非常有用，有时可以帮助避免分配_avoid allocation_。在使用上主要是作为切片的基础，这是下一节我们要讨论的主题。作为前置基础，我们先来简单的了解下。

Go和C的数组概念有非常大的区别。在Go中：

*   数组是值。将一个数组分配给另一个数组会复制所有元素。
*   尤其是，如果将一个数组传递给一个函数，它将会收到一个数组的拷贝，而不是指向数组的指针。
*   数组的大小是其类型的一部分。类型`[10]int`和`[20]int`是不同的。

值传递的特性非常有用但代价高昂，如果你想像C一样高效的操作数组，就需要传递数组的指针。

```go
func Sum(a *[3]float64) (sum float64) {
    for _, v := range *a {
        sum += v
    }
    return
}

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&array)  // 注意这里明确的调用指针
```

以上并不是Go的惯用法，俺们经常用slice来替代。

### Slices

对于序列化数据而言，切片slice更常用、更强大、更方便。除了具有显式维度的操作（如变换矩阵）外， Go中的大部分序列化操作都是用切片而非数组完成。

切片持有对底层数组的引用，如果将一个切片分配给另一个切片，则两者都持有相同的数组引用。

如果一个函数接受一个slice参数，它对slice的元素的改变对调用者来说是可见的，类似于传递了底层数组的指针。

os包中的Read函数接受slice参数，而非数组指针和数组长度值；切片自带的长度属性会设置该函数读取多少数据的上限。

```go
func (f *File) Read(buf []byte) (n int, err error)
```

该方法返回读取的字节数和错误值（如果有的话）。要读入较大缓冲区buf的前32个字节，将传入切片参数执行切片操作就行。

```go
n, err := f.Read(buf[0:32])
```

对切片的再切片操作常见且高效，如果暂时把效率放在一边，也可以这样做：

```go
var n int
var err error
for i := 0; i < 32; i++ {
    nbytes, e := f.Read(buf[i:i+1])  // Read one byte.
    if nbytes == 0 || e != nil {
       err = e
       break
    }
    n += nbytes
}
```

一个切片的长度可以改变，只要它仍然在底层数组的范围内；只是把底层数组再分配给自己而已。可以通过内置函数`cap`查看切片的容量。(_原文：The length of a slice may be changed as long as it still fits within the limits of the underlying array; just assign it to a slice of itself. The capacity of a slice, accessible by the built-in function cap, reports the maximum length the slice may assume._)

下面这个函数将数据添加到切片。如果数据超过容量(capacity)，切片将重新分配(简而言之，超出预设长度时切片会自动扩充容量)。该函数使用`len和cap`来度量一个值为nil的切片是合法的，返回的结果会是0

```go
func Append(slice, data []byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}
```

尽管Append可以修改slice的元素，但是slice本身（运行时数据结构包含指针，长度和容量）是按值传递的，所以必须返回slice。

向切片追加数据是非常有用的，内置函数`append`可以执行这个操作。为了理解这个函数的设计，我们需要了解更多的知识，稍后再进一步讲解。

### 二维切片

Go的数组和切片是一维的。为了实现二维数组和切片，可通过创建数组套数组或切片套切片的结构，像这样：

```go
type Transform [3][3]float64  // 3x3 数组
type LinesOfText [][]byte     // 每个切片的元素也是切片.
```

因为切片长度是可变的，所以每个内切片可以具有不同的长度。如示例所示：每行都有一个独立的长度。

```go
text := LinesOfText{
	[]byte("Now is the time"),
	[]byte("for all good gophers"),
	[]byte("to bring some fun to the party."),
}
```

有时需要分配一个二维切片，例如处理像素的扫描线。有两种方法来实现这一点。 一种是独立分配每个切片; 另一种是分配一个单一的数组，并指向单个切片。 使用哪个取决于你的实际情况。 如果切片长度可能增长或缩小， 它们应该被独立分配以避免覆盖下一行; 如果不是，用单一分配来构造对象会更有效率。 作为参考，这里是两种方法的样例。

```go
// Allocate the top-level slice.
picture := make([][]uint8, YSize) // One row per unit of y.
// Loop over the rows, allocating the slice for each row.
for i := range picture {
	picture[i] = make([]uint8, XSize)
}
```

这是另一种方法

```go
// Allocate the top-level slice, the same as before.
picture := make([][]uint8, YSize) // One row per unit of y.
// Allocate one large slice to hold all the pixels.
pixels := make([]uint8, XSize*YSize) // Has type []uint8 even though picture is [][]uint8.
// Loop over the rows, slicing each row from the front of the remaining pixels slice.
for i := range picture {
	picture[i], pixels = pixels[:XSize], pixels[XSize:]
}
```

### Maps

map是方便且强大的内置数据结构，它将一种类型（键）的值与另一种类型（元素或值）的值相关联。键可以是定义相等运算符的任何类型，例如整数 ，浮点数和复数，字符串，指针，接口（只要动态类型支持比较判断），结构体和数组。切片不能用作映射关键字，因为它们之间没有定义比较关系。 像切片一样，map保存对基础数据结构的引用。如果将map传递给更改map内容的函数，则更改将在函数外部可见。

map可以使用通常的复合字面值语法和冒号分隔的键值对来构建，所以在初始化时很容易构建。

```go
var timeZone = map[string]int{
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

分配和提取映射值在语法上看起来就像操作数组和切片一样，只不过索引不一定是整数。

```go
offset := timeZone["EST"]
```

尝试使用map不存在的键获取map值时，将返回map中对应值类型的零值。例如，如果map包含整数，查找一个不存在的键将返回0。 一个集合_set_可以被实现为值类型为bool的映射。将map实体设置为true以将值放入集合_set_中，然后通过简单索引进行测试。

```go
attended := map[string]bool{
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // 如果在map中不存在会返回 false
    fmt.Println(person, "was at the meeting")
}
```

有时你需要把是否存在与零值区分开，是否存在名为"UTC"的键或改建所对应的值是否为0，你可以这样做：

```go
var seconds int
var ok bool
seconds, ok = timeZone[tz]
```

这样的判断方式被称为"comma ok"(_For obvious reasons this is called the “comma ok” idiom._)。在下面的例子中，如果"tz"在map中存在， "seconds"会被赋予键"tz"所对应的值，同时"ok"会被赋值为true，相反，当"tz"在map中不存在，"seconds"会被赋予对应类型的零值(在这个例子中是0)，同时"ok"会被赋值为false。

```go
func offset(tz string) int {
    if seconds, ok := timeZone[tz]; ok {
        return seconds
    }
    log.Println("unknown time zone:", tz)
    return 0
}
```

如果只是想测试map中的key是否存在，可以使用空白标识符`_`来占位。

```go
_, present := timeZone[tz]
```

想要删除map中的键值对，可以使用内置函数`delete`，所需的参数是目标map和要删除的对应的键。即使该键不存在，调用此函数依然是安全的。

```go
delete(timeZone, "PDT")  // Now on Standard Time
```

### Printing

Go中的格式化打印风格类似于C的printf家族，但是使用方式更丰富功能更强大。这些函数存在于fmt包中，并且有大写的名字：`fmt.Printf，fmt.Fprintf，fmt.Sprintf`等等。 字符串函数`Sprintf`返回一个字符串，而不是填充提供的缓冲区。

你不需要提供格式字符串。对于`Printf，Fprintf和Sprintf`中的每一个，都有另一组函数与之对应，例如`Print和Println`。 这些对应的函数为每个参数生成默认格式。 `Println`在参数之间插入一个空格，并在输出中附加一个换行符。 `Print`在两侧的操作数都不是字符串时，会添加空格。 在这个例子中，每行的输出相同。

```go
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

格式化打印函数`fmt.Fprint`和fmt包下的其他打印函数将任何实现`io.Writer`接口的对象作为第一个参数; 变量`os.Stdout和os.Stderr`就是这样的实例。

从这里开始与C有所不同。首先，诸如`%d`这样的数字格式不会将符号或大小作为标志；而是使用参数的类型来决定这些属性。

```go
var x uint64 = 1<<64 - 1
fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x))
```

会输出

```
18446744073709551615 ffffffffffffffff; -1 -1
```

如果你只是想要默认的打印格式，可以使用`%v`，它产生的格式化样式与调用`Print 和Println`相同，此外， 这种方法可以打印任何类型的值，包括数组、切片、结构体、map集合等。上一节打印时区就可以这样做。

```go
fmt.Printf("%v\n", timeZone)  // or just fmt.Println(timeZone)
```

会输出为

```go
map[CST:-21600 PST:-28800 EST:-18000 UTC:0 MST:-25200]
```

对于map来说，键可以以任何顺序输出。打印一个结构体时，使用格式`%+v`会用结构体的名称来标注结构体的字段， 对于任何值，格式`%#v`都以完整的Go语法打印出结果。

```go
type T struct {
    a int
    b float64
    c string
}
t := &T{ 7, -2.35, "abc\tdef" }
fmt.Printf("%v\n", t)
fmt.Printf("%+v\n", t)
fmt.Printf("%#v\n", t)
fmt.Printf("%#v\n", timeZone)
```

输出结果(请注意&符号)

```go
&{7 -2.35 abc   def}
&{a:7 b:-2.35 c:abc     def}
&main.T{a:7, b:-2.35, c:"abc\tdef"}
map[string] int{"CST":-21600, "PST":-28800, "EST":-18000, "UTC":0, "MST":-25200}
```

当应用于`string`或`[]byte`类型的值时，引用的字符串格式也可以通过`%q`获得。 如果可能，`%#q`将使用反引号。 `%q`也适用于整数和runes，会产生一个单引号符号常量(_原文是single-quoted rune，可以理解为把一堆runes拆成单个的rune_)。 另外，`%x`能够格式化字符串，字节数组和字节切片以及整数，产生一个十六进制字符串，并在字节之间放置空格。

另外还有个非常便利的`%T`，能输出值的类型。

```go
fmt.Printf("%T\n", timeZone)
```

会输出为

```go
map[string] int
```

如果你想修改某个自定类型输出格式，只需要为该类型定义方法`String() string`(类似Java的toString)。例如：

```go
func (t *T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
```

会输出为

```
7/-2.35/"abc\tdef"
```

如果你需要打印T类型的值以及指向T的指针，那么String函数的接收者必须是值类型的； 这个例子使用了一个指针，因为这对结构类型来说更有效率。更多信息请查看值与指针类型接收者(_pointers vs. value receivers_)。

在`String`方法中不要用`Sprintf`来构造返回的字符串，因为打印流程是可以重入的，所以会出现死循环。

```go
type MyString string

func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", m) // Error: will recur forever.
}
```

修复的方案也很简单，将无参调用改为传参就行了。

```go
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", string(m)) // OK: note conversion.
}
```

在下面的初始化章节我们可以看到其他的使用方式来避免出现这样的情况。

另一个格式化输出方案是传入格式化字符串，`Printf`传递了`...interface{}`来接收不定长参数，interface接口保证了能够接收任何类型。

```go
func Printf(format string, v ...interface{}) (n int, err error) {
```

在函数Printf中，v的作用类似`[]interface{}`类型的变量，但是如果它被传递给另一个可变参数函数，它就像一个常规的参数列表。 函数`log.Println`将其参数直接传递给`fmt.Sprintln`。

```go
// Println prints to the standard logger in the manner of fmt.Println.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // Output takes parameters (int, string)
}
```

我们在嵌套调用`Sprintln`之后写入`...`，以告诉编译器将v视为参数列表;否则它只会将v作为单个切片进行参数传递。

这个对打印输出只是介绍了大概，详情请查看标准库`fmt`包。

另外`...`可以指定特定类型，如下：

```go
func Min(a ...int) int {
    min := int(^uint(0) >> 1)  // largest int
    for _, i := range a {
        if i < min {
            min = i
        }
    }
    return min
}
```

### Append

现在我们要补全对内置函数`append`的介绍。`append`的函数签名与 上面提到的`Append`不同：

```go
func append(slice []T, elements ...T) []T
```

其中T是任何给定类型的占位符。你无法在Go中直接写出这样的函数代码(Go不支持泛型)，这个类型 由函数调用者决定。这就是`append`内置的原因：它需要编译器支持。

`append`将元素追加到slice切片的尾部并返回最新的切片。返回切片的原因在于， 就跟我们上面提到的`Append`一样，底层数组可能由于长度不够而完全改变了。

```go
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

结果是\[1 2 3 4 5 6\]。看上去`append`有点像`Printf`，可以 传递任意数量的参数。

但是假如我们想把slice切片拼接在一起呢？很简单，使用`...`，这段代码生成与 上面相同的结果：

```go
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x) //[1 2 3 4 5 6]
```

不使用`...`将无法通过编译，会报类型错误，因为y并非int类型。

Initialization
--------------

_常量、变量及全局初始化_

尽管与C或C++相比看起来区别并不大，但是在Go中的初始化功能更强大。在初始化过程中可以构建复杂的结构，并且可以正确处理已初始化对象之间的顺序问题，即使在不同的包中也是如此。

### 常量

Go中的常量就是我们平常说的(其他语言的)常量。它们在编译期创建，即使在函数中被定义为locals,也只能 是数字，字符(runes)，字符串或布尔值。由于编译的限制，定义它们的表达式必须是常量表达式——编译期会进行校验。 例如`1<<3`是一个常量表达式，而`math.Sin(math.Pi/4)`不是，因为函数调用 `math.Sin`发生在运行时。

在Go中，使用iota枚举器创建枚举常量。由于iota可以是表达式的一部分，并且表达式可以 隐式地重复，所以很容易构建复杂的值集合。

```go
type ByteSize float64

const (
    _           = iota // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

将函数（如`String`）附加到任何自定义类型的功能使得任意值可以自动格式化以进行打印。 虽然更常见于结构，但是这种技术对像ByteSize这样的自定义浮点类型也是有用的。

```go
func (b ByteSize) String() string {
    switch {
    case b >= YB:
        return fmt.Sprintf("%.2fYB", b/YB)
    case b >= ZB:
        return fmt.Sprintf("%.2fZB", b/ZB)
    case b >= EB:
        return fmt.Sprintf("%.2fEB", b/EB)
    case b >= PB:
        return fmt.Sprintf("%.2fPB", b/PB)
    case b >= TB:
        return fmt.Sprintf("%.2fTB", b/TB)
    case b >= GB:
        return fmt.Sprintf("%.2fGB", b/GB)
    case b >= MB:
        return fmt.Sprintf("%.2fMB", b/MB)
    case b >= KB:
        return fmt.Sprintf("%.2fKB", b/KB)
    }
    return fmt.Sprintf("%.2fB", b)
}
```

表达式YB打印为1.00YB，而ByteSize（1e13）打印为9.09TB。

此处`Sprintf`用于实现ByteSize的`String`方法是安全的（避免循环）， 因为它使用`%f`来调用`Sprintf`，而不是字符串格式： `Sprintf`只会在传递字符串时调用`String`方法，`%f`传递的是浮点值。

### 变量

变量可以采取像常量一样的初始化方式，但初始化器可以在运行时计算(一般)表达式(_原文并未提到什么情况下的表达式算是"不一般"的表达式_)。

```go
var (
    home   = os.Getenv("HOME")
    user   = os.Getenv("USER")
    gopath = os.Getenv("GOPATH")
)
```

### init函数

每个源文件都可以定义自己的`init`初始化函数来设置所需的任何初始化状态，实际上每个文件可以有 多个初始化函数。注意：在所有变量声明都计算了它们的初始化值后才会调用`init`， 并且只有在所有导入的包已经被初始化后才执行`init`.(_原文：Finally, each source file can define its own niladic init function to set up whatever state is required. (Actually each file can have multiple init functions.) And finally means finally: init is called after all the variable declarations in the package have evaluated their initializers, and those are evaluated only after all the imported packages have been initialized._)

除了不能在初始化中声明之外，初始化函数的一个常见用途是在实际执行开始之前验证或修复 程序状态的正确性。(_存疑，原文：Besides initializations that cannot be expressed as declarations, a common use of init functions is to verify or repair correctness of the program state before real execution begins._)

```go
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
    if gopath == "" {
        gopath = home + "/go"
    }
    // gopath may be overridden by --gopath flag on command line.
    flag.StringVar(&gopath, "gopath", gopath, "override default GOPATH")
}
```

Methods
-------

_不同语言对method翻译不同，而c++甚至对函数和方法两个词有不同的解释。为防止造成先入为主的印象，本小结全部使用method而不翻译。_

### Pointers vs. Values method接收者————指针与值

就像我们在上面 ByteSize 例子中看到的，可以为任何指定类型定义method(除了指针和接口)；接收者(_receiver_)不一定是个结构体。

在之前关于切片的讨论中，我们提到了`Append`，可以把这个method改为由切片接收。 首先，我们声明一个类型，然后把该类型的值作为`Append`的接收者。

```go
type ByteSlice []byte

func (slice ByteSlice) Append(data []byte) []byte {
    // 内容与上面定义的Append完全相同 此处省略
}
```

这里同样需要返回新的silce切片。 但我们可以通过重新定义将ByteSlice指针作为接收者的方式来消除这种笨拙的操作， 以达到更新切片内容的目的。

```go
func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // 内容跟上面一样，但是取消了返回值
    *p = slice
}
```

实际上我们可以有更好的法子。如果我们按照的`Write`接口来写的话，

```go
func (p *ByteSlice) Write(data []byte) (n int, err error) {
    slice := *p
    // Again as above.
    *p = slice
    return len(data), nil
}
```

那么`*BytesSlice`类型就满足标准库中的`io.Writer`接口， 可以实现很多便利的操作。例如调用`fmt`进行打印：

```go
var b ByteSlice
fmt.Fprintf(&b, "This hour has %d days\n", 7)
```

我们显示的采用取指针的方式传递`&b`是因为`Write`method的接收者是 `*ByteSlice`(因此`*ByteSlice`实现了接口`io.Write`)。

对于method的接收者，指针和值的区别在于：前者只能由指针调用，后者可以由指针和值调用。

之所以会有这条规则，是因为指针作为method接收者时，调用method可以修改接收者(的属性)； 如果在这种情况下，使用接收者的值调用method，会导致产生该接收者值的拷贝， 即menthod对接收者属性的修改会无效化，语言不允许出现这样的错误。

但也有例外，当接收者的值可寻址的情况下，语言通过自动插入地址运算符来处理值调用接收者为指针 的情况。在我们的例子中，变量b是**可寻址**的，所以我们用`b.Write`调用Write——编译器 会把替我们把它改为`&b`。

顺便说一下，在一个字节切片上使用Write的是标准库`bytes.Buffer`的核心。

_**并不是所有变量都是可寻址的**，map的值就不是。在标准库`reflect.CanAddr`中是这样解释的： `A value is addressable if it is an element of a slice, an element of an addressable array, a field of an addressable struct, or the result of dereferencing a pointer. (此处存疑，有人说这只是针对反射部分的"可寻址"含义进行说明。仅做参考)`_

_尽管这一节原文很拗口，依然强烈建议比对原文以加深理解：  
We pass the address of a ByteSlice because only *ByteSlice satisfies io.Writer. The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.  
This rule arises because pointer methods can modify the receiver; invoking them on a value would cause the method to receive a copy of the value, so any modifications would be discarded. The language therefore disallows this mistake. There is a handy exception, though. When the value is addressable, the language takes care of the common case of invoking a pointer method on a value by inserting the address operator automatically. In our example, the variable b is addressable, so we can call its Write method with just b.Write. The compiler will rewrite that to (&b).Write for us._

_关于Go语言的方法接受者类型什么时候用值类型什么时候用指针类型，讨论的帖子非常多， 建议大家搜索比较下，实际操作中还是挺复杂的。_

接口及其他类型
-------

### Interfaces 接口

在Go中提供了一种方式来描述对象的行为，这种方式被称为接口(interface)： 如果能满足某个对象的描述，就可以把它当作这个东西来用。之前我们已经见到了一些实现接口简单的例子； 如上面提到的通过`String`实现自定义输出。只包含有一或两个方法的接口在Go中很常见，且通常会给出一个 派生自该方法的名称，例如实现Writer接口的`io.Writer`。

一个自定义类型可以实现多个接口。例如，一个集合(collection)实现了包含`Len(),Less(i,j int) bool,Swap(i,j int)` 的排序接口(sort包下的`sort.Interface`)，同样可以再实现自定义格式化输出：

```go
type Sequence []int

// Methods required by sort.Interface.
func (s Sequence) Len() int {
    return len(s)
}
func (s Sequence) Less(i, j int) bool {
    return s[i] < s[j]
}
func (s Sequence) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

// Method for printing - sorts the elements before printing.
func (s Sequence) String() string {
    sort.Sort(s)
    str := "["
    for i, elem := range s {
        if i > 0 {
            str += " "
        }
        str += fmt.Sprint(elem)
    }
    return str + "]"
}
```

### Conversions 类型转换

上面的`String`方法中使用`Sprint`重新设置了输出样式，我们可以 在调用`Sprint`前将`Sequence`转换为`[]int`。

```go
func (s Sequence) String() string {
    sort.Sort(s)
    return fmt.Sprint([]int(s))
}
```

这是另一种在String方法中安全地调用Sprintf的示例，由于`Sequence与[]int`类型相同，所以在它们之间进行转换是合法的。 转换不会创建一个新的值，这只是暂时的行为，就好像现有的值有一个新的类型。(还有其他合法的转换，例如从整数到浮点，但这样会创建一个新的值。）)

这是Go中的一个惯用法，用于转换表达式的类型以访问不同的方法集。我们可以使用以下方式来简化：

```go
type Sequence []int

// Method for printing - sorts the elements before printing
func (s Sequence) String() string {
    sort.IntSlice(s).Sort()
    return fmt.Sprint([]int(s))
}
```

现在Sequence不需要再实现`sort.Interface`，我们通过使用类型转换的方式 也达到了同样的效果。这在实践中并不很常见，但很有效。

### 接口类型转换和类型断言

我们之前看到的type switch曾经提到了这样的转换用法：对每一种条件(case)，某种意义上 将其转换为符合该条件的类型。这里有一个关于`fmt.Printf`代码如何使用switch将一个值转换为 字符串输出的简化版本。如果传入的是一个字符串，我们希望直接返回字符串；而如果传入的值实现了`String`方法，我们就 调用其自行实现的输出方法：

```go
type Stringer interface {
    String() string
}

var value interface{} // Value provided by caller.
switch str := value.(type) {
case string:
    return str
case Stringer:
    return str.String()
}
```

第一个条件找到了具体的值，第二个条件将传入的接口转换为另一个接口。

那么如果我们只关心单独一种类型呢？如果我们知道传入的接口持有字符串，想提取出内容怎么办？ 单类型断言(可以把上面的例子当作多类型断言)可以解决这个问题。单类型断言从接口中提取指定类型的值。 这个语法借用自type switch但不使用`type`关键字：

```go
value.(typeName)
```

返回结果是类型为typeName的全新值。该类型必须是接口所拥有的具体类型， 或者该值可以转换为的其他种接口类型。为了提取我们知道的字符串中的值，可以这样写：

```go
str, ok := value.(string)
if ok {
    fmt.Printf("string value is: %q\n", str)
} else {
    fmt.Printf("value is not a string\n")
}
```

如果类型断言失败，`str`变量依然会是个字符串类型，但是会是一个字符串的零值——即一个空字符串。

下面这种写法，与上面的type switch相同：

```go
if str, ok := value.(string); ok {
    return str
} else if str, ok := value.(Stringer); ok {
    return str.String()
}
```

### 总结

_一句话总结：构造时返回接口是最好的。另加密这块术语实在让人发疯，翻的不好请见谅。_

如果一个类型只是为了实现一个接口而存在，并且永远不会有超出这个接口的方法，那么就不需要导出类型本身，除了产生多余文档并没有其他作用。 _If a type exists only to implement an interface and will never have exported methods beyond that interface, there is no need to export the type itself. Exporting just the interface makes it clear the value has no interesting behavior beyond what is described in the interface. It also avoids the need to repeat the documentation on every instance of a common method._

在这种情况下，构造函数应该返回一个接口值而不是实现类型。例如，在标准库中hash包`crc32.NewIEEE和adler32.New` 返回了`hash.Hash32`接口。在Go程序中将CRC-32算法替换为Adler-32只需要改变构造函数调用，其余的代码不受算法改变的影响。_In such cases, the constructor should return an interface value rather than the implementing type. As an example, in the hash libraries both crc32.NewIEEE and adler32.New return the interface type hash.Hash32. Substituting the CRC-32 algorithm for Adler-32 in a Go program requires only changing the constructor call; the rest of the code is unaffected by the change of algorithm._

_A similar approach allows the streaming cipher algorithms in the various crypto packages to be separated from the block ciphers they chain together. The Block interface in the crypto/cipher package specifies the behavior of a block cipher, which provides encryption of a single block of data. Then, by analogy with the bufio package, cipher packages that implement this interface can be used to construct streaming ciphers, represented by the Stream interface, without knowing the details of the block encryption._

`crypto/cipher`接口如下：

```go
type Block interface {
    BlockSize() int
    Encrypt(src, dst []byte)
    Decrypt(src, dst []byte)
}

type Stream interface {
    XORKeyStream(dst, src []byte)
}
```

这是计数器(CTR)的定义，它将块cipher转换为流cipher；注意块cipher的细节被抽象了：

```go
// NewCTR returns a Stream that encrypts/decrypts using the given Block in
// counter mode. The length of iv must be the same as the Block's block size.
func NewCTR(block Block, iv []byte) Stream
```

NewCTR不仅适用于一个特定的加密算法和数据源，还适用于Block接口和任何Stream的任何实现。 由于它们返回接口值，因此将CTR加密替换为其他加密模式是本地更改。 构造函数的调用必须是编辑过的，但是由于周围的代码只能将结果视为Stream，所以不会注意到其差异。_NewCTR applies not just to one specific encryption algorithm and data source but to any implementation of the Block interface and any Stream. Because they return interface values, replacing CTR encryption with other encryption modes is a localized change. The constructor calls must be edited, but because the surrounding code must treat the result only as a Stream, it won't notice the difference._

### 接口和方法

由于几乎所有东西都可以附加方法，所以几乎任何东西都可以满足任何你想要实现的接口。 一个极具代表性的例子是http包中的Handler接口。只要实现了这个接口就可以接收HTTP请求。

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

`ResponseWriter`本身是一个接口，提供了访问返回给客户端的响应结果的(一些)方法。这些方法 包括标准的`Witre`方法，所以在使用`io.Writer`的地方就可以使用`http.ResponseWriter`。 http请求是一个结构体，包含了来自客户端的解析后的请求内容。

为简洁起见，让我们忽略POST并假设HTTP请求总是GET; 简化不会影响处理程序的设置方式。 这是一个简单但完整的处理程序实现来计算页面被访问的次数。

```go
// Simple counter server.
type Counter struct {
    n int
}

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ctr.n++
    fmt.Fprintf(w, "counter = %d\n", ctr.n)
}
```

请注意Fprintf如何将内容输出到http.ResponseWriter。作为参考，以下展示如何给服务器添加路由。

```go
import "net/http"
...
ctr := new(Counter)
http.Handle("/counter", ctr)
```

但是为什么建立`Counter`结构体？我们明明只需要一个整数。(接收器需要是一个指针，这样增量对于调用者可见)

```go
// Simpler counter server.
type Counter int

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    *ctr++
    fmt.Fprintf(w, "counter = %d\n", *ctr)
}
```

如果程序有一些内部状态，需要根据一个页面是否被访问来修改状态该怎么办？绑定一个channel。

```go
// A channel that sends a notification on each visit.
// (Probably want the channel to be buffered.)
type Chan chan *http.Request

func (ch Chan) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ch <- req
    fmt.Fprint(w, "notification sent")
}
```

最后，假设我们想要显示调用服务器二进制文件时使用的参数。编写一个函数来打印参数很容易。

```go
func ArgServer() {
    fmt.Println(os.Args)
}
```

既然我们可以为除了指针和接口外的任何类型定义方法，那么我们同样可以为函数编写一个方法，在标准库`http` 中是这样写的：

```go
// The HandlerFunc type is an adapter to allow the use of
// ordinary functions as HTTP handlers.  If f is a function
// with the appropriate signature, HandlerFunc(f) is a
// Handler object that calls f.
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, req).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}
```

`HandlerFunc`实现了符合`ServeHTTP`接口的函数，所以`f`可以 接收HTTP请求。看看这个方法的实现：方法接收方是函数f，方法调用f。这可能看起来很奇怪，但与接收器是一个通道和在通道里发送方法没有什么不同。

采用统一的思路，我们首先修改`ArgServer`：

```go
// Argument server.
func ArgServer(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintln(w, os.Args)
}
```

`ArgServer`现在与`HandlerFunc`具有相同的签名了， 所以可以转换类型以达到方法方法的目的，就像我们之前将Sequence转换为IntSlice来访问IntSlice.Sort一样。

```go
http.Handle("/args", http.HandlerFunc(ArgServer))
```

当有人访问页面/args时，该页面的处理程序会接收到ArgServer和HandlerFunc。 HTTP服务器将调用该类型的ServeHTTP方法，将ArgServer作为接收方，然后调用ArgServer（通过HandlerFunc.ServeHTTP中的调用f（w，req））。这样就实现了我们的目的——将系统参数显示出来。

在本节中，我们从一个结构体，一个整数，一个通道和一个函数中创建了一个HTTP服务器， 所有这些能够实现，都是因为接口只是一组方法的组合，这些方法可以被定义为（几乎）任何类型。

空标识符 _
------

我们在循环和map中已经几次提到了空标识符。空标识符可以在分配或声明任何类型的任何值时将其丢弃掉且没有副作用。 (_但从业务角度来讲，丢弃err是自寻死路。_)这有点像写入Unix的 /dev/null 文件：它代表一个只有写权限的值，用作 该变量的占位符，与实际值不相关。它拥有广泛的用途。

### 多返回值中的空标识符

在range循环中使用空标识符是很常见的。

如果需要在左侧分配多个值，但是其中一个值不会被使用，位于左侧的空标识符避免了创建虚拟变量的需要，并且明确指出该值将被丢弃。 例如，当调用一个返回值和错误的函数时，只有错误是重要的，使用空标识符丢弃不需要使用的值：

```go
if _, err := os.Stat(path); os.IsNotExist(err) {
	fmt.Printf("%s does not exist\n", path)
}
```

有时候你会看到代码丢弃了错误值以忽略错误，这种行为非常可怕。总是检查错误返回值——这是Go中错误存在的意义。

```go
// Bad! This code will crash if path does not exist.
fi, _ := os.Stat(path)
if fi.IsDir() {
    fmt.Printf("%s is a directory\n", path)
}
```

### 未使用的包引用和变量

如果导包或声明了变量却没有使用就会报错。未使用的导包行为或使程序膨胀编译变慢， 一个被初始化但没有被使用的变量会浪费算力，甚至引发更多的错误。然而，一个程序在开发过程中， 经常会出现未使用的导入和变量，以后可能还会用到，删除它们会很麻烦，为了让编译继续下去， 空标识符提供了很好的解决办法。

这个写了一半的程序有两个未使用的导入和一个未使用的变量，编译会报告错误：

```go
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
}
```

要使编译器暂时不报unused err，使用空标识符来引用导入的包。类似地， 将未使用的变量fd分配给空标识符，这样就可以正常编译了。

```go
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

var _ = fmt.Printf // For debugging; delete when done.
var _ io.Reader    // For debugging; delete when done.

func main() {
    fd, err := os.Open("test.go")
    if err != nil {
        log.Fatal(err)
    }
    // TODO: use fd.
    _ = fd
}
```

这样的临时措施应该尽快清理，如果短时间内无法进行，应该添加详细的注释，以便日后查找处理。

### Import for side effect

_为了使用包的副作用而进行导包_

前面例子中的`fmt或io`等未使用的导入应最终被使用或删除。 但是有时会为了副作用导入一个包。 例如，在其init函数期间，`net/http/pprof` 包会提供调试信息给HTTP处理程序。 它有一个导出的API，但大多数客户只需要处理程序注册并通过网页访问数据。 要仅为副作用导入包，请将包重命名为空标识符：

```go
import _ "net/http/pprof"
```

在这种使用场景下，被导入的包不需要拥有名称，如果有而我们没有使用，编译器会报错。

### 接口检查

就像我们在上面看到的，一个类型不需要显示的标明它实现了一个接口(像Java、Python会使用关键字或符号很明确的告诉使用者"看我实现了一个接口")。 大多数接口转换是静态的，因此在编辑期会进行检查。例如，将`*os.File`传递给期望 `os.File`类型的函数不会通过编译，除非`*os.File`实现了`io.Reader`接口。

有的接口会在运行时进行类型检查。例如在标准库的`encoding/json`包中，定义了`Marshaler`接口， 当JSON编码器接收到实现了该接口的值，编码器会调用marshaling方法把它转换为JSON而不是执行标准转换。 编码器会在运行时做类型断言：

```go
m, ok := val.(json.Marshaler)
```

如果确实有必要检查是否一个类型实现了某个接口，而不实际使用接口本身（也许作为错误检查的一部分），则可以使用空标识符来忽略类型声明的值：

```go
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler\n", val, val)
}
```

出现这种情况的一个地方是在实际满足接口的类型的包内需要保证。 如果一个类型（例如json.RawMessage）需要一个自定义的JSON表示， 它应该实现json.Marshaler，但是没有静态转换会导致编译器自动验证。 如果类型无意中不能满足接口，JSON编码器仍然可以工作，但不会使用自定义实现。 为了保证实现是正确的，可以这样做：

```go
var _ json.Marshaler = (*RawMessage)(nil)
```

这是一个会在运行时检查的类型转换。一旦`json.Marshaler`接口发生变化， 这个包将无法正常编译，进而我们将会注意到它需要更新。

在这个构造中空白标识符的出现表明该声明只存在于类型检查中，而不是创建一个变量。 不过，不要为满足接口的每种类型做这个。 按照惯例，只有在代码中不存在静态转换的情况下，才会使用这种声明，这种情况很少见。_原文是：The appearance of the blank identifier in this construct indicates that the declaration exists only for the type checking, not to create a variable. Don't do this for every type that satisfies an interface, though. By convention, such declarations are only used when there are no static conversions already present in the code, which is a rare event._

Embedding
---------

_结构体的组合嵌入_

Go并不提供典型的，类型驱动的子类化概念，但是它有能力通过在结构或接口中嵌入类型来实现。

接口的组合十分简单。我们之前提到过 `io.Reader 和 io.Writer`：

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

`io`包同样导出了其他几个接口，这些接口制定了可以实现多个多个这种方法的对象。 例如，`io.ReadWriter`接口包含了 Read 和 Write。我们可以通过明确地列出两个方法来实现io.ReadWriter，但是嵌入两个接口以形成新的接口更容易和更具启发性，如下所示：

```go
// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}
```

一个ReadWriter既可以做Reader的任务也可以做Writer的任务，它是两个接口的组合(必须是不想交的方法集合)。**只有接口可以嵌入接口**。

同样的思想也可以适用于结构体，但影响更深远。`bufio`包有两个结构体类型 `bufio.Reader 和 bufio.Writer`， 每个都实现了来自io包的类似接口。同时`bufio`实现了带有缓冲的读写器(buffered reader/writer)，它通过将 读写器组合到一个结构体中实现：它在结构体中列出了类型但没有给出它们的字段名：

```go
// ReadWriter stores pointers to a Reader and a Writer.
// It implements io.ReadWriter.
type ReadWriter struct {
    *Reader  // *bufio.Reader
    *Writer  // *bufio.Writer
}
```

嵌入的元素是指向结构体的指针，所以必须初始化为指向有效的结构体才能使用。

```go
type ReadWriter struct {
    reader *Reader
    writer *Writer
}
```

为了满足io接口，我们还需要提供如下方法：

```go
func (rw *ReadWriter) Read(p []byte) (n int, err error) {
    return rw.reader.Read(p)
}
```

这样做开销很小，而且不仅具有 `bufio.Reader 和 bufio.Writer` 方法，还 满足三个接口`io.Reader, io.Writer, 和 io.ReadWriter`。

当我们嵌入一个类型时，这个类型的方法变成了外部类型的方法，但是当它们被调用时，方法的接收者是内部类型，而不是外部类型。 在我们的例子中，当调用bufio.ReadWriter的Read方法时，其效果与上面写出的转发方法完全相同; 接收器是ReadWriter的reader段，而不是ReadWriter本身。

这个例子显示了一个常规的命名字段和一个匿名嵌入字段。

Job类型现在拥有`Log, Logf 和 *log.Logger中的函数`。我们可以给Logger一个字段名，但 现在没必要这么做。现在初始化后我们可以使用job来执行：

job.Log("starting now...")

`Logger`是Job中的常规字段，所以我们可以在Job的构造函数中初始化它：

```go
func NewJob(command string, logger *log.Logger) *Job {
    return &Job{command, logger}
}
```

或者

```go
job := &Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
```

如果我们需要直接引用一个嵌入字段，忽略包限定符的字段的类型名称将作为字段名称，就像我们的ReaderWriter结构的Read方法中那样。

在这里，如果我们需要访问一个Job变量job的\*log.Logger，我们会写job.Logger，如果我们想要改进Logger的方法，这会很有用。

```go
func (job *Job) Logf(format string, args ...interface{}) {
    job.Logger.Logf("%q: %s", job.Command, fmt.Sprintf(format, args...))
}
```

组合嵌入不同类型引入了名称冲突的问题，但解决它们很简单。首先，字段或方法X在类型的更深层嵌套部分隐藏任何其他项目X.如果log.Logger包含一个名为Command的字段或方法，那么Job的命令字段将会占主导地位。

其次，如果相同的名字出现在相同的嵌套层次上，通常意味着错误的产生;如果Job结构包含另一个称为Logger的字段或方法，那么嵌入log.Logger会是错误的。 但是，如果在类型定义之外的程序中从不引用到重复的名称，那么就没问题。这提供了一些保护，防止外部嵌入类型的变化; 如果添加的字段与另一个子类型中的一个字段发生冲突（如果两个字段均未曾使用过），则没有问题。

并发
--

### Share by communicating

并发编程是个很大的话题，而Go对支持并发有独到的亮点。

在许多环境中，通过共享变量的方式，在并发编程中对其进行细致化的访问操作是很困难的。 Go鼓励使用一种不同的方式，即通过channel传递需要共享的值，而非在单独的执行线程中共享。 在任何时候，只有一个goroutine(go协程)可以访问这个值。使用这种方式，数据竞争就不会发生。 为了鼓励这种思维方式，我们把它简化为一个口号：

Do not communicate by sharing memory; instead, share memory by communicating.  
_不要通过共享内存来通讯；应当通过通讯来共享内存_

这种思路可以被应用的地方很多。例如，引用计数可以通过在整数变量周围包裹一个互斥体来完成， 但是作为一个更高层次的方法， 使用channel来控制访问使编写清晰、正确的程序变得更加容易。

这种模式可以按在一个CPU上运行的典型的单线程程序来考虑——它不需要使用同步原语(synchronization primitives)， 现在在这样的条件下再运行一个同样不需要同步的实例——现在让他们两个通讯，如果通讯过程是同步的， 他们依然不需要采用其他的同步方式或工具。Unix的pipelines就是这样的典型实现。尽管Go的并发 思想源自Hoare的通讯顺序进程(CSP)，但也可以被看作是Unix管道的类型安全的泛化。

### Goroutines

被称为 goroutines(go协程) 是因为其他已经被大家熟知的名词——线程threads 协程coroutines 进程processes 等等——这些叫法都无法传达goroutines准确的内涵。goroutines有一个简单的模型：它是一个在同一地址空间中 与其他goroutines同时执行的函数。他是轻量级的，仅比堆栈空间的分配开销多一点点。而堆栈在分配初期 占用很小，使用代价低廉，只是随着分配、释放堆存储空间才逐渐增大。

goroutines 可以被多路复用到多个OS线程，所以如果某个发生阻塞，比如在等待I/O的时候， 其他的会继续运行。这样的设计隐藏了许多线程创建和管理的复杂性。

用`go`关键字在一个函数或方法调用前添加前缀，以在新的goroutine中运行调用。 当调用完毕，goroutine会自动退出，这个效果和在后台运行命令的Unix shell的&符号相似。

```go
go list.Sort()  // 并发运行 list.Sort; 别等它.
```

可以在这样的场景下很方便的使用：

```go
func Announce(message string, delay time.Duration) {
    go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  //注意这一行的括号，调用匿名函数时必须写
}
```

在Go中这样可以实现闭包：这样就确保了函数引用的变量正常使用。

这些例子不太实际，因为函数没有办法传递完成信号。为此，我们需要使用channel。

### Channels

类似于map,channel使用`make`来创建，返回的值是底层数据结构的引用。 如果提供了一个可选的整数参数，它将设置通道的缓冲区大小。对于无缓冲或同步通道，默认值为零。

```go
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files
```

无缓冲的渠道将通信（交换数据）与同步相结合， 确保两个goroutines在计算时处于已知状态。

对于channel的使用有很多惯用法。在之前的小节我们实现过一个排序。使用channel 可以允许启动的goroutine等待排序结束。

```go
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
    <-c   // Wait for sort to finish; discard sent value.
```

在有数据可以接收之前，接收器总是阻塞的。如果channel没有缓冲，则发送器也会阻塞， 直到接收器接收到传递的值。如果channel有一个缓冲区，发送器只会在所有值全部被复制到缓冲区 后，才发生阻塞；如果缓冲区已满，则会等待某个接收器检索其中的数据。_原文：Receivers always block until there is data to receive. If the channel is unbuffered, the sender blocks until the receiver has received the value. If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; if the buffer is full, this means waiting until some receiver has retrieved a value._

一个拥有缓冲区的channel可以被当作信号来使用，例如限流。在这个例子里，传入的请求被传递给句柄， 它将一个值发送到通道，处理请求，然后从通道接收一个值，为下一个使用者准备好“信号量”。 channel的缓冲区容量限制了同时处理请求的数量。

```go
var sem = make(chan int, MaxOutstanding)

func handle(r *Request) {
    sem <- 1    // Wait for active queue to drain.
    process(r)  // May take a long time.
    <-sem       // Done; enable next request to run.
}

func Serve(queue chan *Request) {
    for {
        req := <-queue
        go handle(req)  // Don't wait for handle to finish.
    }
}
```

一旦MaxOutstanding处理程序正在执行process， 将阻塞任何尝试发送到已填充的通道缓冲区的行为， 直到其中一个现有的处理程序完成并从缓冲区被接收。

这个思路存在一个问题：Serve为每个传入的请求创建新的goroutine。于是，如果请求速度太快， 程序可能会消耗无限的资源。我们可以通过改变Serve的使用门槛来解决这个缺陷。注意这里有一个 我们随后会修复的错误：

```go
func Serve(queue chan *Request) {
    for req := range queue {
        sem <- 1
        go func() {
            process(req) // Buggy; see explanation below.
            <-sem
        }()
    }
}
```

这个问题出在for循环——循环变量会被每个迭代重复使用——所以req变量是被所有goroutine 共享的。这里有一个解决方案，将req的值作为参数传递给goroutine中的闭包：：

```go
func Serve(queue chan *Request) {
    for req := range queue {
        sem <- 1
        go func(req *Request) {
            process(req)
            <-sem
        }(req)
    }
}
```

比较这个版本和以前的版本，看看在关闭声明和运行方式上的区别。 另一个解决方案就是创建一个新名字相同的变量，如下所示：

```go
func Serve(queue chan *Request) {
    for req := range queue {
        req := req // Create new instance of req for the goroutine.
        sem <- 1
        go func() {
            process(req)
            <-sem
        }()
    }
}
```
这可能看起来很奇怪

```go
req := req
```

但这是合法的，而且是很常见的惯用法。你得到一个新的变量，故意在本地隐藏循环变量， 这个新的变量对每个goroutine来说都是唯一的。

回到我们之前的Serve问题，另一种管理资源的方法是，启动固定数量的goroutine， 来处理所有从channel中获取到的请求。goroutines的数量限制了同时处理的请求数量。 这个Serve函数还可以添加一个channel参数，它会被告知什么时候退出；在启动goroutine后，它会阻止从该channel继续接收请求。

```go
func handle(queue chan *Request) {
    for r := range queue {
        process(r)
    }
}

func Serve(clientRequests chan *Request, quit chan bool) {
    // Start handlers
    for i := 0; i < MaxOutstanding; i++ {
        go handle(clientRequests)
    }
    <-quit  // Wait to be told to exit.
}
```

### Channels of channels

_在channel中生成channel_

Go最重要的特性之一就是，channel可以被其他channel生成和传递。 这个特性的一个常见用途是实现安全的并行解复用。_原文是：One of the most important properties of Go is that a channel is a first-class value that can be allocated and passed around like any other. A common use of this property is to implement safe, parallel demultiplexing._

在上面的例子中，handle是一个理想化的处理器，但我们并没有定义它处理的类型。 如果这种类型含有包含了返回内容的channel，每个发送请求的客户端都能够提供自己的接收路径。

```go
type Request struct {
    args        []int
    f           func([]int) int
    resultChan  chan int
}
```

结构体内包含一个带有参数的函数，以及请求后接收反馈的channel。

```go
func sum(a []int) (s int) {
    for _, v := range a {
        s += v
    }
    return
}

request := &Request{[]int{3, 4, 5}, sum, make(chan int)}
// Send request
clientRequests <- request
// Wait for response.
fmt.Printf("answer: %d\n", <-request.resultChan)

handle函数只需要做很小的改动：

func handle(queue chan *Request) {
    for req := range queue {
        req.resultChan <- req.f(req.args)
    }
}
```

在实际场景中，上面的代码还有很多的地方需要完善，现在咱们已经完成了一个简单的、能够限速的、 并行的非阻塞RPC系统，并且代码中没有使用互斥体(这样效率就比较高)。

### Parallelization 并行

这种思想的另一种实现形式是在多个CPU内核之间进行并行计算。如果计算可以分解成相互独立的单独部分， 则可以并行化，每个部分使用channel发出完成信号。

假设我们有个计算元素矢量的耗时操作，每一步操作的值是独立的，就像下面这样：

```go
type Vector []float64

// Apply the operation to v[i], v[i+1] ... up to v[n-1].
func (v Vector) DoSome(i, n int, u Vector, c chan int) {
    for ; i < n; i++ {
        v[i] += u.Op(v[i])
    }
    c <- 1    // signal that this piece is done
}
```

我们独立地启动一个循环，每个CPU一个。他们可以以任何顺序完成，但没关系; 我们只需要在启动所有goroutine之后通过清空channel来计算完成信号。

```go
const numCPU = 4 // number of CPU cores

func (v Vector) DoAll(u Vector) {
    c := make(chan int, numCPU)  // Buffering optional but sensible.
    for i := 0; i < numCPU; i++ {
        go v.DoSome(i*len(v)/numCPU, (i+1)*len(v)/numCPU, u, c)
    }
    // Drain the channel.
    for i := 0; i < numCPU; i++ {
        <-c    // wait for one task to complete
    }
    // All done.
}
```

与其为numCPU创建一个常量值，不如直接在运行时查询可以采用CPU数做计算。标准库 `runtime.NumCPU`返回了硬件CPU数，所以我们可以这样写

```go
var numCPU = runtime.NumCPU()
```

同样还有个函数`runtime.GOMAXPROCS`，可以获取或设置用户指定的 内核数量以运行程序。其默认值与`runtime.NumCPU`相同，但是可以 通过设置类似名称的shell环境变量或通过调用具有正数的函数来覆盖。传入0代表只是查询， 所以如果我们使用用户的默认设置，可以这样写：

```go
var numCPU = runtime.GOMAXPROCS(0)
```

一定不要与并发（concurrency）混淆，并行（parallelism）将程序构建为独立的运行组件， 利用多个CPU并行执行计算以提高效率。虽然Go的并发特性可以使一些问题很容易构建为并行计算， Go是并发语言，不是并行语言，并不是所有的并行化问题都适合Go的模型。 有关这种区别的进一步讨论，请参阅[博文中的讨论](https://blog.golang.org/2013/01/concurrency-is-not-parallelism.html)。_原文是：Be sure not to confuse the ideas of concurrency—structuring a program as independently executing components—and parallelism—executing calculations in parallel for efficiency on multiple CPUs. Although the concurrency features of Go can make some problems easy to structure as parallel computations, Go is a concurrent language, not a parallel one, and not all parallelization problems fit Go's model. For a discussion of the distinction, see the talk cited in this blog post._

### A leaky buffer

并发编程的工具甚至可以使非并行的想法更容易表达。这里有一个取自rpc包的例子。 客户端程序循环接收来自某个源的数据，这个源可能来自网络。为了避免分配和释放缓冲区，我们保留一个空闲列表， 并使用一个缓冲channel来表示它。如果channel是空的，则分配一个新的缓冲区。一旦消息缓冲区准备就绪， 它就会被发送到serverChan。

```go
var freeList = make(chan *Buffer, 100)
var serverChan = make(chan *Buffer)

func client() {
    for {
        var b *Buffer
        // Grab a buffer if available; allocate if not.
        select {
        case b = <-freeList:
            // Got one; nothing more to do.
        default:
            // None free, so allocate a new one.
            b = new(Buffer)
        }
        load(b)              // Read next message from the net.
        serverChan <- b      // Send to server.
    }
}
```

服务端循环接收、处理来自客户端的消息，并返回至空闲列表。

```go
func server() {
    for {
        b := <-serverChan    // Wait for work.
        process(b)
        // Reuse buffer if there's room.
        select {
        case freeList <- b:
            // Buffer on free list; nothing more to do.
        default:
            // Free list full, just carry on.
        }
    }
}
```

客户端会尝试从freeList中检索缓冲区；如果没有可用的，它分配一个新的。服务器发送给freeList会将b放回空闲列表中，除非列表已满，在这种情况下， 缓冲区被丢弃，被垃圾收集器收回。select语句中的default子句在没有其他情况准备就绪时执行， 这意味着select不会被阻塞。这个实现只需几行就可以建立一个清单_leaky bucket free list_， 依靠缓冲的channel和垃圾收集器进行记录。

_这一节翻译的有点词不达意， [查看leaky bucket更详细的解释，](https://en.wikipedia.org/wiki/Leaky_bucket) 欢迎大家指正。_

_原文是：The client attempts to retrieve a buffer from freeList; if none is available, it allocates a fresh one. The server's send to freeList puts b back on the free list unless the list is full, in which case the buffer is dropped on the floor to be reclaimed by the garbage collector. (The default clauses in the select statements execute when no other case is ready, meaning that the selects never block.) This implementation builds a leaky bucket free list in just a few lines, relying on the buffered channel and the garbage collector for bookkeeping._

错误处理
----

在标准库的例子中，经常会返回一些错误提示给调用者。就像之前提到的，Go的多返回值机制可以 很容易的既返回操作结果也返回详细的错误信息描述。这是Go语言提倡的风格。例如，我们将看到， 标准库中`os.Open`不仅在失败时返回一个零指针，还会返回一个描述出错原因的值。

在标准库中，错误的类型是`error`，是个很简单的内置接口：

```go
type error interface {
    Error() string
}
```

任何人可以用更丰富的模型自由的实现这个接口，不仅可以查看错误，还可以提供丰富的其他信息。 如前所述，除了通常的`*os.File`返回值之外，`os.Open`还会返回一个错误值。 如果文件打开成功，错误将是零，但是当有问题时，它将持有一个`os.PathError`：

```go
// PathError records an error and the operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}
```

PathError内的Error方法生成了这样的字符串：

```
open /etc/passwx: no such file or directory
```

这种使用方式，包括有问题的文件名称，操作和触发的操作系统错误， 即使多层调用它的错误信息，在排查错误时也是极其有用的。它比普通的“没有这样的文件或目录”更具清晰明了。

在可行的情况下，错误字符串应该标识它们的来源，例如通过命名产生错误的操作或包的前缀。 例如，在标准库包`image`中，由于未知格式导致的解码错误的字符串表示是“image：未知格式”。

关心精确错误细节的调用者可以使用type switch或类型断言来查找特定错误并提取细节。 对于PathErrors，这可能包括检查内部Err字段是否存在可恢复的故障。

```go
for try := 0; try < 2; try++ {
    file, err = os.Create(filename)
    if err == nil {
        return
    }
    if e, ok := err.(*os.PathError); ok && e.Err == syscall.ENOSPC {
        deleteTempFiles()  // Recover some space.
        continue
    }
    return
}
```

第二个if使用了类型断言。如果断言失败，ok的值会是false，同时e会是nil。如果断言成功， ok的值是true，这意味着该错误就是`*os.PathError`类型。这样我们就可以使用e查看到有关错误的更多信息。

### Panic

向调用者报告错误的常用方法，是将错误作为额外的返回值返回。标准库中的`Read` 方法是一个众所周知的例子，它返回一个字节数和错误。但是如果错误非常严重导致程序无法继续下去呢？

系统内置函数`panic`就是为了解决这个问题而存在的，该函数接受任意类型的单个参数(通常是字符串)， 它会产生一个运行时错误，并停止程序。这是为了表明一种不可能的情况发生了，例如发生了无限循环。

```go
// A toy implementation of cube root using Newton's method.
func CubeRoot(x float64) float64 {
    z := x/3   // Arbitrary initial value
    for i := 0; i < 1e6; i++ {
        prevz := z
        z -= (z*z*z-x) / (3*z*z)
        if veryClose(z, prevz) {
            return z
        }
    }
    // A million iterations has not converged; something is wrong.
    panic(fmt.Sprintf("CubeRoot(%g) did not converge", x))
}
```

这只是个例子，真正提供给他人使用的库函数应该避免panic。如果问题可以被掩盖或者解决， 那么让事情继续运行而不是放弃整个程序总是更好的。一个可能的反例就是在初始化过程中： 如果库怎么都无法初始化起来，那真的可以说技术部恐慌业务部惊慌了。

```go
var user = os.Getenv("USER")

func init() {
    if user == "" {
        panic("no value for $USER")
    }
}
```

### Recover

当手动调用panic或出现隐式运行时错误，例如索引切片超出范围或失败类型断言时， 它立即停止当前函数的执行，并开始展开goroutine的堆栈，运行被defer修饰的延迟函数， 如果到达goroutine堆栈的顶部，程序就会死亡。 但是，可以使用内置函数`recover`来重新获得goroutine的控制权并恢复正常运行。

调用`recover`后停止展开goroutine的堆栈，并把参数传递给`panic` 函数。由于在展开时运行的唯一代码位于延迟函数内部，因此`recover`仅在延迟函数中有用。

`recover`的一个常见用法是关闭服务器内的失败的goroutine， 而不杀死其他正在执行的正常goroutines

```go
func server(workChan <-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```

在这个例子里，如果 do(work) 出现panic，结果会被 log 打印，该 goroutine 会干净的退出而 不干扰到其他的 goroutine 。你不需要做任何其他的事情，只需要考虑`recover` 接下来的处理逻辑。

除非直接从延迟函数中调用，否则`recover`总返回nil。延迟代码可以调用自己使用panic的库例程，并在不失败的情况下恢复。 例如，safeDo中的延迟函数在调用recover之前可能会调用日志记录函数，并且日志代码将不受panic状态的影响。

do函数（以及任何它所调用的）都可以通过调用`panic`来干净地摆脱任何不好的情况。 我们可以使用这个想法来简化复杂软件中的错误处理。 让我们看看`regexp`包的理想版本，它通过调用本地错误类型`panic` 来报告解析错误。

```go
// Error is the type of a parse error; it satisfies the error interface.
type Error string
func (e Error) Error() string {
    return string(e)
}

// error is a method of *Regexp that reports parsing errors by
// panicking with an Error.
func (regexp *Regexp) error(err string) {
    panic(Error(err))
}

// Compile returns a parsed representation of the regular expression.
func Compile(str string) (regexp *Regexp, err error) {
    regexp = new(Regexp)
    // doParse will panic if there is a parse error.
    defer func() {
        if e := recover(); e != nil {
            regexp = nil    // Clear return value.
            err = e.(Error) // Will re-panic if not a parse error.
        }
    }()
    return regexp.doParse(str), nil
}
```

如果doParse发生panic，恢复代码块块会将返回值设置为nil。 然后，它会检查，该问题是不是一个解析错误。 否则，断言类型将失败，导致运行时错误继续堆栈展开，就像没有任何中断它一样。 这种检查意味着出现了意想不到的情况，比如索引越界， 那么即使我们使用panic和恢复来处理分析错误，代码也会失败。

在处理错误的位置，可以很容易地报告解析错误，而不必担心需要手动解析堆栈：

```go
if pos == 0 {
    re.error("'*' illegal at start of expression")
}
```

虽然这种模式很有用，但只能在一个同包中使用。 上面的处理将内部的panic调用转化为错误值而不是将panic暴露给使用者。 这是一个值得遵循的规则。

顺便说一下，在实际应用中，这种操作方式会改变panic的价值。 但是，新旧故障都会在崩溃报告中展现，所以问题的根本原因仍然是可见的。 因此，这种简单的处理方法足够用了。 但如果你只想显示原始值，需要编写更多的代码来过滤意外情况，并处理原始错误。 这是留给你的一个练习。
