---
title: Go常见问题及解答
date: 2018-12-16 00:12:12
cover: 
categories: Go
tags:
- Go
---

起源
--

### Go语言项目的目标是什么？

近十年来程序开发语言没有重大进展，但环境已经发生了巨大的变化。有以下几个趋势：

*   软件开发速度并未跟上计算机硬件的发展速度。
*   依赖管理是当今软件开发的重要组成部分，但C语言中的语言“头文件”与清除依赖关系分析和快速编译背道而驰。
*   对Java和C++等繁琐的开发语言反思越来越多，人们逐渐靠向动态类型的语言，如Python和JavaScript。
*   垃圾回收(GC)和并行计算(parallel computation)等一些基本概念并未被流行语言很好的支持。
*   多核的出现引发了开发者的担忧和困惑。

我们认为在此等情况下建立一门新的开发语言是必要的，这门语言应该能够妥善的支持GC、 并行计算和快速编译：

*   Go能够在一台计算机上在几秒内编译一个大型的程序。
*   Go能提供良好的软件结构模型，使得依赖分析变得简单，同时避免像C一样产生的文件和库的开销。
*   Go的类型系统没有层次结构，所以不需要花费大量时间来定义类型之间的关系。尽管具有静态 类型，但开发感受比典型OO语言更轻松。
*   Go支持GC，从语言层面为并发执行和通信提供直接支持。
*   在设计上，Go提出了一种在多核机器上构建系统软件的方法。

更多有用的内容请查看[在Google内部，Go为软件开发设计提供强力支撑](https://talks.golang.org/2012/splash.article)

### Go语言项目现在处于什么状态？

2009.10.10起Go成为了一个开源项目。经过几年快速的迭代，2012.3.28日发布了1.0版本。 Go 1包括语言规范，标准库和定制工具，为创建可靠的软件产品提供了稳定的基础。 随着稳定性的确立，我们使用Go语言开发Go的程序，产品和工具，而不是主动地改变语言和库本身。 事实上，Go 1的目的是提供[长期的稳定性](https://golang.google.cn/doc/go1compat.html)。 向后不兼容的更改将不会对任何Go 1点发行版进行。我们希望让Go具有大众需要的特性，而不是止步不前。

当然，Go的新版本将继续围绕Go本身，但重点将放在性能，可靠性，可移植性以及增加新功能（如改进对国际化的支持）上。

某一天Go 2会到来，但不会很久。它会受到我们在Go 1中所学的到的经验影响。

_(2018-02-01 Go 1.9.3正式版已经发布，按照Go官方每半年一个版本的时间安排来算，Go 2即将到来。对此我既担忧又期待，虽然官方一再表示不会造成像Python2和3那样的割裂感，但谁知道呢。在网上有这样一张图，能表现出这样的忧虑。)_

![](/static/img/go2.jpg)

### 为什么把Go语言的徽标定为地鼠？

语言的logo由[Renée French](http://reneefrench.blogspot.com/)设计 ，她也设计了[Glenda](https://9p.io/plan9/glenda.html)——Plan 9的那只兔子。 [地鼠(gopher)](https://blog.golang.org/gopher)源自几年前她用于WFMU T恤设计中的一个。 徽标和吉祥物受Creative Commons Attribution 3.0许可证的保护。

### Go语言项目历史

Robert Griesemer, Rob Pike 和 Ken Thompson于2007年9月21日开始在白板上勾画新语言的目标。 诸神用业余时间在几天之内确定了基本计划和任务。到2008年1月，Ken已经开始编写一个编译器——它能生成C代码作为输出。 到了年中，语言已经变成了一个全职项目，编译器所需前置工作基本完成。2008年5月，Ian Taylor开始让GCC和Go一起干活， Russ Cox于2008年底加入，帮助语言和库脱离原型化。

Go于2009.11.10成为开源项目以来，社区贡献了大量的想法，讨论和代码。

### 你们为什么要创造一门新的开发语言？

Go源自对现有语言和编程环境的挫折感。编程变得太困难了，语言的选择是部分原因。 一个人不得不从有效的编辑、有效的执行、或简化编程中选一个——没有哪一门主流语言同时提供了这些。 开发者可以通过转向Python或JavaScript等动态类型而不是C++以提高开发速度，或在小范围内使用Java来提高安全性和效率。

Go尝试将动态类型语言的简便性与静态类型语言的效率及安全性相结合，同时提供对现代化编程、 网络开发和多核计算方面的支持。使用Go的目的是快速——在单个计算机上快速的编译。为了达到这些目标需要解决语言上的问题——具有表达性但轻量级的类型系统；并发和垃圾回收；严格的依赖规范等等。现有语言的库或工具无法很好的解决这些问题——Go因此而诞生。

[Go语言在Google](https://talks.golang.org/2012/splash.article)讨论了Go语言设计的背景和动机，并提供了本FAQ中许多答案的更多细节。

### Go语言在设计时参考了哪些语言？

主要是C语言（基本语法），来自Pascal/Modula/Oberon（声明，包）的输入方式， 以及由Tony Hoare的CSP（例如Newsqueak和Limbo（并发））启发的一些想法。 但是，这是一门全新的语言。 从各方面来说，语言都是表达了开发者的想法， 与其他语言相比至少我们所做的更有趣和高效。

### Go语言在设计时的指导原则是什么？

今天的编程涉及太多重复和文书工作。 正如Dick Gabriel所说的那样：“旧的程序读起来就像一个语音清晰的研究人员和一个 学习良好的机械同事之间的安静对话，而不是与编译器的辩论——谁会想到这些不必要的辩论所带来的杂音呢？“这种复杂性是值得的 - 没有人愿意回到以前的语言 - 但是有更加安静地实现吗？

在整个设计中，我们试图减少混乱和复杂性。 没有前向声明和头文件; 一切都被明确声明一次。 初始化是表达性的，自动的，且易于使用。关键字语法清晰明了。 通过简单类型派生结构减少类似于这样的（foo.Foo \*myFoo = new（foo.Foo））语句。 最根本的是，没有类型层次结构。 这些简化允许Go不牺牲表达性，而且语义强大。

另一个重要的原则是保持正交的概念。任何类型实现了接口内的方法就等于实现了接口；结构代表数据，接口代表抽象等等。正交性使得理解事物结合后会发生什么更容易。

使用
--

### Google内部在使用Go吗？

是的。现在Google内部有几个Go项目在进行。例如golang.org，它是在Google App Engine中运行的godoc文档服务器。。

其他的例子包括用于大规模SQL安装的Vitess系统和Google的下载服务器，dl.google.com，它提供了Chrome二进制文件和其他大型可安装软件，比如apt-get软件包。

### Go项目可以与C/C++项目连接吗？

有两个Go编译器实现，gc和gccgo。Gc使用了不同的调用约定和链接器，因此只能使用相同的约定与C程序链接。 Gccgo是一个GCC front-end，可以与GCC编译的C或C++程序链接。

cgo 提供了一个“外部函数接口”机制，允许使用Go代码安全地调用C库。如果你想用go调用C++，可以使用SWIG。

### Go语言支持Google的protocol buffers吗？

[github.com/golang/protobuf/](https://github.com/golang/protobuf)提供了对库和插件的支持。 [什么是Google的protocol buffers](https://developers.google.com/protocol-buffers/)。_protocol buffers由Google建立，是一种平台无关的，可扩展的序列化数据结构的机制——有点像XML，但更小，更快，更简单。可以定义一次数据的结构，然后使用专门生成的源代码轻松地将结构化数据写入各种数据流，或使用各种语言读取结构化数据。_

### 我能把Go语言官网页面转换为其他语言吗？

绝对可以。我们鼓励开发者把Go语言网站翻译为自己的母语。然而如果你要使用Google的商标或其他品牌标记(虽然并没有出现在golang.org官网上) ，你必须取得授权[www.google.com/permissions/guidelines.html](https://www.google.com/permissions/guidelines.html)。

语言设计
----

### Go有运行时吗？

Go有一个被广泛使用的标准库`runtime`。它实现垃圾收集，并发，堆栈管理以及Go语言的其他重要功能。Go的运行时间与C库（libc）类似。

但是，有一点很重要：Go的运行时不包含虚拟机（例如Java运行时提供的虚拟机）。Go程序会首先编译为本地机器码。因此，尽管这个术语通常用于描述程序运行的虚拟环境， 但是在Go中“运行时”是提供关键语言服务的库的名称。

### 关于Unicode编码

基于ASCII的范围扩展标识符对于我们来说非常重要。Go规定：标识符必须是由Unicode定义的字母或数字——这很容易理解和实现，但具有限制。结合字符(Combining characters)被因此排除在外。有一个商定的外部定义之前，为了确定标识符可能是什么，加上对标识符进行规范化的定义，最好不要使用混合字符。 因此，我们有一个简单的原则，可以在不破坏程序的情况下进行扩展，避免了因容许模糊标识符的规则而产生错误。

由于导出的标识符必须以大写字母开头，因此根据定义，某些语言中由“字母”创建的标识符不能导出。现在唯一的解决办法是使用`X中文`这种写法，这显然无法令人满意，我们正在考虑其他选择。但Go中的大写可见性规则不太可能改变， 这是我们最喜欢的功能之一。

### 为什么Go没有某某某特性？

每个语言都会有自己独有的特性同时遗漏掉某些"有意思"的特性。 Go的设计注重于程序开发的快捷性，编译速度，概念的正交性， 以及支持诸如并发和垃圾收集等需求。你喜欢的功能可能因为不适合而去除，因为它会影响编译速度 或语言的可读性，或使系统模型变得难以构建。

如果因缺少某种特性而使得你非常困扰，请原谅并和我们一起探讨Go未来的特性。你可能发现我们 以其他方式提供了某某某特性的实现。

### 为什么Go没有泛型？

泛型也许会在某个版本添加。尽快我们了解有些开发者非常想要这个特性，但并不觉得这一点非常紧迫。

泛型非常方便，但会使类型系统和运行时变得复杂，增大开销。我们并没有找到一种能够平衡使用价值与增大 复杂性相平衡的方案——但我们一直在考虑。并且，Go内置了map和slice结构，加上interface，可以在多数情况下替代泛型，虽然你觉得可能不是那么顺手。

关于泛型的话题讨论依然处于开放状态。你可以在这里看到[讨论过的几个Go的泛型解决方案](https://golang.org/issue/15292)。

### 为什么Go没有异常？

我们相信把`try-catch-finally`与控制结构耦合会使代码错综复杂，它倾向于鼓励开发者标记 太多的普通错误，比如没有打开文件等。

Go采用了不同的方法。对于普通的错误处理，Go的多值返回可以轻松地报告错误而不会重载返回值。 我们相信[规范的错误类型与Go的其他功能相结合使错误处理变得愉快](https://golang.google.cn/doc/articles/error_handling.html)，这一点与其他语言中的错误处理有很大不同。

Go也有一些内置的功能来发出信号并从特殊条件中恢复。恢复机制仅作为函数状态的一部分被执行，在错误发生之后被移除，这足以处理灾难情况，而且不需要额外的控制结构，如果使用得当可以产生干净的错误处理代码。

查看[Defer,Panic,Recover](https://golang.google.cn/doc/articles/defer_panic_recover.html)以了解更多。

### 为什么Go没有断言？

_注意：Go是有类型断言的。_

Go不提供断言。虽然断言很方便，但我们的经验是，开发者使用它们来偷懒，以避免考虑正确的错误处理和报告。 正确的错误处理意味着服务器在非致命错误时能够继续运行而不是崩溃。适当的错误报告意味着错误日志是清晰的，开发者不必像扒垃圾堆一样检查异常链。 当开发者看到错误而又不熟悉代码时，精准的错误日志是非常重要的。

我们明白这是一个争论点。Go语言中有很多不同于现代实践的东西，我们觉得有时尝试一种不同的方法是非常值得的。

### 为什么使用CSP的思想构建并发？

众所周知并发和多线程编程是很困难的。我们认为这是由于诸如pthreads之类的复杂设计导致， 部分原因在于过分强调底层细节（如互斥锁，条件变量和内存障碍）。高级别的接口可以在存在互斥体等情况下实现更简单的代码。

Hoare的交流顺序过程（CSP）是为并发提供高级语言支持的最成功模式之一。 Occam和Erlang是使用CSP的两个众所周知的语言。 Go的并发原语来源于CSP系语言的不同部分，其主要贡献是提出了channel这一强大概念。 前几种语言的经验表明，CSP模型非常适合程序语言框架。

### 为什么使用goroutine替代线程threads？

Goroutines是简化并发编程操作的一部分。这个想法是将独立执行的函数——coroutines——复用到一组线程上。 当一个coroutine(通过调用一个阻塞的系统调用)阻塞的时候，运行时会自动的将同一个操作系统线程上的其他coroutines移动到一个不同的、可运行的线程上去，这样他们就不会被阻塞。 开发者对这些是无感知的——这一点很重要。我们称之为goroutine的开销可能非常小：除了堆栈的内存外，只有几千字节(just a few kilobytes)。

为了使堆栈变小，Go的运行时使用可调整大小的有界堆栈。新建的goroutine有几千字节，运行时会自动增长（缩小）用于存储堆栈的内存，从而允许许多协程生存在适量的内存中。CPU平均每个函数调用大约三个开销很小的指令。在同一个地址空间中创建数十万个goroutines是可行的。相对于线程，goroutines使用的系统资源会更少。

### 为什么map操作没有被定义为原子的？

经过漫长的讨论后，我们决定map的典型使用场景不包含从多个例程安全地访问，因为map可能是已经同步的一些大型数据结构或计算的一部分。 要求所有map操作获取互斥锁将会减慢大多数程序的执行速度，并几乎没有增加安全性。 这不是一个容易的决定，尽管这意味着不受控制的map访问会使程序崩溃。

_注意：Go 1.9已经添加了`sync.Map`，算是弥补了这一缺憾。_

### 你们会接受我的语言改进方案吗？

很多人向我们提交了改进建议——[邮件列表](https://groups.google.com/group/golang-nuts)包含了这些讨论的丰富历史——但是这些建议只有很少一部分被接受了。

尽管Go是一个开源项目，但是语言和库受到[兼容性承诺](https://golang.google.cn/doc/go1compat.html)的保护， 可以防止破坏现有程序的更改。如果你的提案违反了Go 1规范，我们甚至不能接受这个想法，不管它的优点如何。 Go的未来主要版本可能与Go 1不兼容，但我们还没准备好开始讨论相关的内容。

即使你的提议与Go 1规范相兼容，也可能不符合Go的设计目标。发布于Google的文章[软件工程服务中的语言设计](https://talks.golang.org/2012/splash.article)解释了Go的起源和设计背后的思想。

类型
--

### Go是一门面向对象的语言吗？

即是也不是。虽然Go有类型和方法，并且允许面向对象的编程风格，但在语言层面没有类型层次结构支持。 Go中虽然也存在的"接口(interface)"概念，但与其他语言相比提供了不同的使用方法。另外还允许将类型嵌入到其他类型，以提供类似于子类化的东西——但 与其他面向对象语言的子类并不完全相同。而且在Go中函数相比C++和Java使用范围更广：函数可以被定义为任何类型 ，甚至是内置类型，例如“非装箱”整数型。它们并不受限于结构(类)。

此外，缺少类型层次结构使Go中的“对象”感觉比C++或Java等语言更轻量级。

### 如何获得动态的方法调度？

动态分派方法的唯一方法是通过接口。对结构或其他具体类型的方法总是静态解析的。

### 为什么没有类型继承？

面向对象的编程，至少在最著名的语言中，涉及到对类型之间的关系的讨论很多，这些关系往往可以自动导出。Go采用了不同的实现方式。

在Go中不需要开发者提前声明两种类型是相关的。类型可以同时满足多个接口，没有传统多重继承的复杂性。接口可以是非常轻量级的—— 一个甚至是没有任何方法的接口可以也可以表达一个有用的概念。接口可以在事后添加，如果出现新的想法或测试——不需要注释原始类型。 因为在类型和接口之间没有明确的关系，所以没有类型层次来管理或讨论。

使用这种方式，就能够构建类似于类型安全的Unix管道一样的东西。例如，`fmt.Fprintf` 可以将格式化打印输出到任何输出而不仅仅是一个文件，`bufio`包完全独立于文件I/O等等。所有这些实现都来自 代表单个方法(Write)的单个接口(io.Writer)。而这只是表面。Go的接口对整个程序的结构有深刻的影响。

它需要逐渐去习惯，但这种隐含的类型依赖风格是Go中最有成效的东西之一。

### 为什么len是个方法而不是函数？

_原标题为 Why is len a function and not a method_

我们就这个问题进行了辩论，但是决定将len和其他内置操作实现为方法，因为这样在实践中是最好的，并且不会使关于基本类型的接口（在Go类型意义上）问题复杂化。

### 为什么Go不支持函数和操作符重载？

在不需要进行类型匹配的情况下，函数的调度会被简化。其他语言的使用经验告诉我们，使用同一个名称但 签名不同的多种方法，偶尔有用，但在实践中可能令人困惑。只与名称匹配并要求类型一致是Go类型系统中一个重要的简化原则。

没有运算符重载的世界很美好。

### Go为什么没有"implements"关键字？

Go通过实现接口的方法来满足接口。这允许定义和使用接口而不必修改现有的代码。 它提供了一种结构化的类型，可以促进解耦，并提高代码重用性，能让你在产品的压迫下活的更舒坦。接口的语义是Go灵活轻便的主要原因之一。

### 怎样才能保证我的类型满足了接口？

可以要求编译器检查T类型能够通过尝试使用T的零值或指向T的指针来实现接口I（视情况而定）：

```go
type T struct{}
var _ I = T{}       // Verify that T implements I.
var _ I = (*T)(nil) // Verify that *T implements I.
```

如果T(或者\*T)并没有实现I接口，错误将在编译时被捕获。

如果希望接口的用户明确声明他们实现它，则可以向接口的方法集添加一个具有描述性名称的方法。例如：

```go
type Fooer interface {
    Foo()
    ImplementsFooer()
}
```

一个类型必须实现ImplementsFooer方法成为一个Fooer，godoc产生的输出会很清楚地记录该类型实现了它。

```go
type Bar struct{}
func (b Bar) ImplementsFooer() {}
func (b Bar) Foo() {}
```

大多数代码没有使用这样的约束，因为它们限制了接口思想的实用性。有时候，有必要解决类似接口之间的歧义。

### 为什么类型T不能满足Equal接口？

考虑这个简单的接口，表示可以执行与另一个值比较的操作：

```go
type Equaler interface {
    Equal(Equaler) bool
}
```

还有类型T

```go
type T int
func (t T) Equal(u T) bool { return t == u } // does not satisfy Equaler
```

与一些多态类型系统中的类似情况不同，T不执行Equaler。 T.Equal的参数类型是T，而不是字面上所需的类型Equaler。

在Go中，类型系统不支持Equal的愿意在于——这是开发者的责任，正如类型T2所说明的那样，它实现了Equaler：

```go
type T2 int
func (t T2) Equal(u Equaler) bool { return t == u.(T2) }  // satisfies Equaler
```

在Go中，任何满足Equaler的类型都可以作为参数传递给T2.Equal，并且在运行时我们必须检查参数是T2类型。有些语言会在编译时做出保证。

这里还有个例子

```go
type Opener interface {
   Open() Reader
}

func (t T3) Open() *os.File
```

在Go中，T3并不能满足Opener，尽管它在其他语言中可行。

虽然在这种情况下，Go的类型系统对于开发者来说确实少了一些，但缺乏子类型使得是否满足接口的规则很容易描述——函数的名称和签名是否与接口的名称和签名完全相符。Go的这一规则很容易实现。 我们觉得好处大于使用上的其他不足。如果有一天我们会采用某种形式的多态输入，我们希望能够有一种方法来表达这些例子的思想，并且对它们进行静态检查。

### 我能把`[]T`转换成`[]interface{}`吗？

不能直接这么做。因为它们在内存中没有相同的表现。必须逐个的将元素拷贝到目标切片中去。下面这个例子就是这样的：

```go
t := []int{1, 2, 3, 4}
s := make([]interface{}, len(t))
for i, v := range t {
    s[i] = v
}
```

### 如果T1和T2具有相同的基础类型，是否可以将`[]T1`转换为`[]T2`？

最后一行无法通过编译：

```go
type T1 int
type T2 int
var t1 T1
var x = T2(t1) // OK
var st1 []T1
var sx = ([]T2)(st1) // NOT OK
```

在Go中，类型与方法紧密相关，因为每个命名类型都有一个（可能是空的）方法集。一般的规则是你可以改变被转换类型的名称（因此可能改变它的方法集），但你不能改变组合类型元素的名字（和方法集合）。Go要求你明确地表达类型转换。

### 为什么我的nil错误值不等于nil？

在底层实现中，接口被实现为两个元素，一个类型和一个值。被称为接口的动态值的值是一个任意的具体值，类型是值的类型。对于int值3，可以认为接口实现为（int，3）。

只有在内部值和类型都未设置时（nil，nil），接口值才为nil。一个nil接口总是持有nil类型。如果我们在一个接口值中存储一个类型为\*int的零指针，那么无论指针的值是什么（\*int，nil），内部类型都将是\*int。因此即使里面的指针是nil，这样一个接口值也是非nil的。

这种情况可能会引起混淆，并且在一个接口值（如错误返回值）中存储一个零值时会出现这种情况：

```go
func returnsError() error {
	var p *MyError = nil
	if bad() {
		p = ErrBad
	}
	return p // Will always return a non-nil error.
}
```

这个函数返回一个nil的p，所以返回值是一个错误的接口值（\*MyError，nil）。

对于返回错误的函数来说，总是在签名中使用错误类型（如上所述）而不是像MyError这样的具体类型，以帮助保证正确创建错误。例如，`os.Open`返回一个错误，如果不是nil时，返回具体类型\*os.PathError。

无论何时使用接口，都会出现类似于这里描述的情况。请记住，如果接口中存储了具体的值，接口将不会为nil。更多信息请查看[反射的原则](https://golang.google.cn/doc/articles/laws_of_reflection.html)。

### 为什么没有类似于C的untagged unions？

Untagged unions会危害到Go的内存安全。

### 为什么Go没有variant type？

_原标题为 Why does Go not have variant types，此段翻译存疑，特将原文附上，欢迎讨论。_

_variant 是c++里面一种数据结构，变体，这种结构可以变成任何的对象或者函数指针。_

variant type，也称为代数类型，提供了一种方法来指定一个值可能采用一组类型中的一个。系统编程中的一个常见例子是将错误指定为网络错误，安全错误或应用程序错误，并允许调用者通过检查错误的类型来区分问题的根源。另一个例子是一个语法树，其中每个节点可以是不同的类型：声明，语句，赋值等等。

我们曾考虑加入这一特性，但经过讨论后我们移除了它——因为它与接口产生交叠并会引发混淆——如果改组类型的元素本身是接口该怎么办？

_Variant types, also known as algebraic types, provide a way to specify that a value might take one of a set of other types, but only those types. A common example in systems programming would specify that an error is, say, a network error, a security error or an application error and allow the caller to discriminate the source of the problem by examining the type of the error. Another example is a syntax tree in which each node can be a different type: declaration, statement, assignment and so on._

_We considered adding variant types to Go, but after discussion decided to leave them out because they overlap in confusing ways with interfaces. What would happen if the elements of a variant type were themselves interfaces?_

_Also, some of what variant types address is already covered by the language. The error example is easy to express using an interface value to hold the error and a type switch to discriminate cases. The syntax tree example is also doable, although not as elegantly._

### 为什么Go没有协变类型？

协变类型意味着像这样一个接口

```go
type Copyable interface {
	Copy() interface{}
}
```

会被这个方法所满足

```go
func (v Value) Copy() Value
```

因为 Value 实现了空的接口。但同时，在Go中，实现接口要求方法类型必须完全匹配，所以Value不会实现Copyable——是不是很矛盾。Go从类型的实现中分离出类型的概念。如果两个方法返回不同的类型，他们不会做同样的事情。希望使用协变类型的开发者经常试图通过接口表达类型层次结构。在Go中，接口和实现之间存在清晰的分离无疑是更加自然的。

值
-

### 为什么Go不提供隐式数字转换

C中的数字类型自动转换的方便性超小于它引发的问题。什么时候表达式是无符号的？ 值有多大？它溢出吗？结果是否可传递？与执行的机器有无关联？这使编译器复杂化。“通常的算术转换”在不同的架构上不易实现。 出于可移植性的原因，我们决定让转换的代价简单明了。

### 为什么map是系统内置的？

和内置字符串类型一样：它们是如此强大和重要的数据结构，为语法支持提供了一个优秀的实现，使编程更愉快。我们认为Go的map足够强大，可以满足绝大多数用途。如果一个特定的应用程序可以从一个自定义的实现中受益，那么可以编写一个，但是从语法上讲它不是那么方便。这似乎是一个合理的折衷办法。

### 为什么map不允许把切片作为键？

map查找需要一个等号运算符，切片无法实现。切片并没有实现是否相等的判断，因为对这种类型的相等没有很好的定义;在对比时有多个因素需要考虑——浅与深比较，指针与值比较，如何处理递归类型等等。我们可能在将来重新讨论这个问题。现在把它放在一边会让大部分问题变得简单。

在Go 1中，与以前的版本不同，为结构和数组定义了相等性，因此这种类型可以用作map的键。尽管如此，切片仍然没有相等的定义。

### 为什么map，slice和channel是引用而array是值？

这个话题已经有很长的历史。早期map和channel是指针，不允许声明或使用非指针实例。同时我们对数组应当如何工作争论不休。最终我们认定指针和值的严格分离使得语言更难以使用。将这些类型更改为对关联的共享数据结构的引用可解决这些问题。这种改变给语言增加了一些令人遗憾的复杂性，但对可用性影响很大：当它被引入时，Go变成了一种更高效，更舒适的语言。

代码编写
----

### 库文档如何使用？

Go有一个用Go语言编写的内置工具 godoc，它从源码中提取包文档，可以在命令行或网络上使用。 一个现成的例子就是[golang.org/pkg/](https://golang.google.cn/pkg/)。 事实上，godoc 在golang.org上实现了完整的网站。

godoc实例可以被配置为在其显示的程序中提供丰富的交互式静态符号分析;这里列出了[更多细节](https://golang.org/lib/godoc/analysis/help.html)。

为了从命令行访问文档，go工具有一个doc子命令，它提供了相同信息的文本接口。

### 有没有Go编程风格指南？

可能会有少量规则来指导命名，布局和文件组织等事情。在[Go进阶](/2018/12/16/go_2/)中包含了一些代码风格的建议。 gofmt自动为你提供更直接的帮助；它取代了通常的做法和不该做的解释。存储库中的所有Go代码都已经通过gofmt运行。

[Go Code Review Comments](https://golang.org/s/comments)是关于Go的非常短的文章的集合，这些文章经常被开发者遗漏。对于Go项目的代码审查人员来说，这是一个方便的参考。

### 如何为Go标准库提交补丁？

标准库源码在仓库 src 文件夹下。如果你想提交一个重大的修改，请在着手之前提交至邮件列表讨论。

查看[为Go项目做贡献](https://golang.google.cn/doc/contribute.html)以了解更多细节。

### 为什么"go get"克隆仓库的时候使用HTTPS？

大部分公司通常只允许在开放标准TCP端口80（HTTP）和443（HTTPS），阻止其他端口（包括TCP端口9418（git）和TCP端口22（SSH））。当使用HTTPS代替HTTP时，git默认强制执行证书验证，提供针对中间人，窃听和篡改攻击的保护。因此，get get命令使用HTTPS来确保安全。

如果你使用git，并且喜欢用你现有的密钥通过SSH推送更改，那很容易解决这个问题。对于GitHub，请尝试以下解决方案：

*   在软件包目录中手动克隆存储库：

```sh
    $ cd src/github.com/username
    $ git clone git@github.com:username/package.git
```  

*   通过添加以下两行代码至 \~/.gitconfig 强制 git push 使用SSH协议

```    
    [url "git@github.com:"]
    	pushInsteadOf = https://github.com/
```    

### 如何使用"go get"管理包版本？

"go get"没有任何明确的包版本的概念。版本控制是非常复杂的，特别是在大型代码库中，我们并不知道在多种情况下广泛适用于所有Go用户的大规模运行的方法。"go get"和更大的Go工具链提供的方案是，隔离具有不同导入路径的包来做区分。 例如，标准库的html/template和text/template共存，即使它们都是“包模板”。这可以作为包作者及相关使用者的参考。

供公众使用的软件包应该尽量保持向后兼容性。[Go 1 兼容性标准导读](https://golang.google.cn/doc/go1compat.html) 是一个很好的参考：不要删除导出的名称，鼓励标记的复合文字等等。如果需要不同的功能，请添加一个新名称，而不是更改旧名称。 如果有重大的修改调整，请使用新的导入路径创建新的包。

如果你使用的是外部提供的软件包，并担心提供者可能会以意想不到的方式进行更改，最简单的解决方案是将其复制到本地——这是Google在内部采取的方法——国内的各种SDK你懂的。 将拷贝存储在新的导入路径下并标识其为本地副本。例如，你可能会将“original.com/pkg”复制到“you.com/external/original.com/pkg”。[gomvpkg工具](https://godoc.org/golang.org/x/tools/cmd/gomvpkg)能帮你自动执行此过程。

Go 1.5版本为go命令添加了一个工具，通过将其“vendoring”到靠近依赖于它们的包的特定目录中，可以更容易地管理外部依赖关系。查看[govendor介绍](https://golang.org/s/go15vendor)以了解更多细节。

我们正在进行关于[dep](https://github.com/golang/dep)的工作——它是一个go的包管理工具。查看[dep FAQ](https://github.com/golang/dep)以了解更多。

指针和内存分配
-------

### 函数参数何时通过值传递？

和C系中的所有语言一样，Go中的一切都是通过值传递的。也就是说，函数总是获得被传递的值的副本，就像一个赋值语句赋值给参数一样。 例如，将一个int值传递给一个函数会生成一个int的副本，传递一个指针值会创建指针的副本，但不会复制它所指向的数据(_原文是：passing a pointer value makes a copy of the pointer, but not the data it points to_)。

map和切片类似与指针：它们是指向底层map或切片数据的描述符。复制地图或切片值不会复制指向的数据。复制一个接口值会复制存储在接口值中的东西：如果接口值包含一个结构体，则复制该接口值将生成该结构体的一个副本。如果接口值包含指针，则复制接口值会生成指针的副本，但不会复制指向的数据。

请注意，这里讨论的是关于操作的语义。实际的实现可以应用优化来避免复制，只要优化不会改变语义。

### 应该在什么时候使用接口的指针？

几乎从不。使用指向接口值的指针只出现在罕见的，棘手的情况下。

将一个指针传递给一个接口值的函数是一个常见的错误。编译器会报错，而这种情况很令人困惑，因为有时 需要一个指针来满足接口。尽管指向具体类型的指针可以满足接口，但是，指向接口的指针永远不能满足接口。_这段跟绕口令一样，实在是不通，原文：It is however a common mistake to pass a pointer to an interface value to a function expecting an interface. The compiler will complain about this error but the situation can still be confusing, because sometimes a pointer is necessary to satisfy an interface. The insight is that although a pointer to a concrete type can satisfy an interface, with one exception a pointer to an interface can never satisfy an interface._

考虑如下声明

```go
var w io.Writer
```

打印函数`fmt.Fprintf`将第一个参数作为满足io.Wirter类型的值——只要这个值实现了规范的Writer方法。所以可以这样：

```go
fmt.Fprintf(w, "hello, world\n")
```

如果我们传递 w 的指针，程序无法编译：

```go
fmt.Fprintf(&w, "hello, world\n") // Compile-time error.
```

任何值，甚至指向接口的指针，都可以分配给一个空接口类型的变量（interface{}）。即便如此，如果这个值是一个指向接口的指针，那几乎肯定是一个错误。结果可能会令人困惑。_原文：The one exception is that any value, even a pointer to an interface, can be assigned to a variable of empty interface type (interface{}). Even so, it's almost certainly a mistake if the value is a pointer to an interface; the result can be confusing._

### 应该把函数的接收者设置为值还是指针？

_原标题 Should I define methods on values or pointers_

```go
func (s *MyStruct) pointerMethod() { } // method on pointer
func (s MyStruct)  valueMethod()   { } // method on value
```

对于不熟悉指针的开发者来说，这两个例子之间的区别可能会令人困惑，但区别其实非常简单。**当在一个类型上定义一个方法时，接收者（在上面的例子中的s）的行为就像它是方法的参数一样**。无论是将接收者定义为值还是指针，都是同一个问题，那么函数参数是一个值还是一个指针。有下面几个因素需要考虑。

首先，最重要的是，该函数是否需要修改接收者的属性。如果需要的话，接收者必须是个指针。(切片和map 有类似与指针的表现，所以他们的使用情况有点微妙。但是如果需要改变一个方法中作为接收者的切片的长度，那么仍然需要是个指针。) 在上面的例子里，如果 pointerMethod 修改了 s 的属性，调用者在调用该函数后会看到这些改变，但是 valueMethod 被 s 的副本调用(值传递)，所以调用后 s 的属性不会发生改变。

顺便提一句，指针接收器和Java的情况是一样的，尽管在Java中指针是隐藏在底层。

其次是效率的考虑。如果接收器很大，例如一个大的结构，使用指针接收器会开销会小得多。

接下来是一致性。如果某些类型的方法必须有指针接收器，其余的也应该如此。(此处存疑，原文有点前后矛盾，另外实际操作中还得看情况：_If some of the methods of the type must have pointer receivers, the rest should too, so the method set is consistent regardless of how the type is used_)。

对于基本类型，切片和小体积结构体等类型，使用值接收开销是很小的，所以除非方法的语义需要指针，值接收是高效和清晰的。

### new和make的区别是什么？

一句话概括，new 分配内存，make 初始化切片，map和channel类型。

详见[Go进阶 new 关键字](effective#Allocation with new)和[Go进阶 make 关键字](effective#Allocation with make)。

### 一个int类型在64位机器上是多少位？

_原标题 What is the size of an int on a 64 bit machine_

int和uint的大小特定于实现的平台，但在给定平台上彼此相同。依赖于特定大小的值的代码应该使用明确大小的类型，如int64。在Go 1.1之前，64位Go编译器（包括gc和gccgo）都使用了32位的int表示。从Go 1.1开始，他们使用64位表示。

另一方面，浮点标量和complex类型的大小始终不变（因为没有浮点或complex 的基本类型），开发者在使用浮点数时应该注意精度。用于（无类型）浮点常量的默认类型是float64。因此，foo:= 3.0声明了一个float64类型的变量foo。 对于由（无类型）常量初始化的float32变量，必须在变量声明中明确指定变量类型：

```go
var foo float32 = 3.0
```

或者，常量必须被赋予一个具有转换的类型，如foo:= float32（3.0）。

### 如果判断一个变量分配在堆上还是栈上？

从某种角度上来说，你不需要考虑这个问题。在Go中只要变量还被引用着，它就会存在。实现存储位置与语言的语义无关。

但了解存储位置确实对编写高效的程序有益。在可能的情况下，Go编译器将为该函数的堆(function's stack frame)中的函数分配本地变量。但是，如果编译器不能在函数返回后证明变量未被引用，那么编译器必须在垃圾回收堆上分配变量，以避免错过指针错误。另外，如果局部变量非常大，将其存储在堆而不是栈可能更有意义。(_原文：The storage location does have an effect on writing efficient programs. When possible, the Go compilers will allocate variables that are local to a function in that function's stack frame. However, if the compiler cannot prove that the variable is not referenced after the function returns, then the compiler must allocate the variable on the garbage-collected heap to avoid dangling pointer errors. Also, if a local variable is very large, it might make more sense to store it on the heap rather than the stack._)。

在当前的编译器中，如果一个变量的地址被占用，那么这个变量就是在堆上分配的候选对象。然而，通过基本的逃逸分析我们会认识到，这些变量不会超过函数的返回，并且可以驻留在堆上(_原文：In the current compilers, if a variable has its address taken, that variable is a candidate for allocation on the heap. However, a basic escape analysis recognizes some cases when such variables will not live past the return from the function and can reside on the stack._)。

### 为什么Go进程使用这么多的虚拟内存？

Go内存分配器保留大量虚拟内存作为分配备用。该虚拟内存对于特定的Go进程是本地的;预留不会剥夺其他内存进程。_原文：The Go memory allocator reserves a large region of virtual memory as an arena for allocations. This virtual memory is local to the specific Go process; the reservation does not deprive other processes of memory._

要查找分配给Go进程的实际内存量，请使用Unix top命令并查阅RES（Linux）或RSIZE（Mac OS X）列。_原文：To find the amount of actual memory allocated to a Go process, use the Unix top command and consult the RES (Linux) or RSIZE (Mac OS X) columns._

并发
--

### 什么操作是原子的？互斥体是吗？

我们还没有完全定义这一切，但有关原子性的一些细节可以在[Go Memory Model规范](https://golang.google.cn/ref/mem)中找到。

在`sync`包中实现了互斥体。但是我们希望Go编程风格会鼓励一次只有一个goroutine负责某个特定的数据。

不要通过共享内存进行通信。相反，通过通信实现共享内存。

请参阅[Share Memory By Communicating](Share Memory By Communicating)和[相关文章](https://blog.golang.org/2010/07/share-memory-by-communicating.html)

### 为什么我的项目没有使用到多核CPU？

GOMAXPROCS shell环境变量控制着可用于执行goroutine的CPU数量。在Go的早期版本中，默认值为1，但从Go 1.5开始，默认值是系统可用的内核数量。 因此1.5之后编译的程序会默认多个goroutine的并行执行。如果想要更改这一行为，请设置环境变量或使用 标准库的`runtime.GOMAXPROCS`函数以使用不同的CPU数。

### 为什么设置GOMAXPROCS>1有时反而让程序变慢了？

这取决于你的项目。使用更多的goroutine并不能加速程序的表现。当问题的本质是并行时，扩大并发数也许是无效的。

实际上，在操作系统中使用多个线程时，花费更多时间在channel上进行通信可能会降低程序性能。 这是因为在线程之间发送数据涉及切换上下文，成本很高，因此增加GOMAXPROCS更有可能使速度变慢。

Go的goroutine调度程序并未达到我们预期的要求，尽管在最近的发行版中已经有所改进。 在接下来的版本中我们将持续优化。目前，如果存在性能问题，则应该根据每个应用程序来设置最佳的GOMAXPROCS数量。

想了解更多细节，请查看[Concurrency is not Parallelism.](https://blog.golang.org/2013/01/concurrency-is-not-parallelism.html)

方法和函数
-----

### 为什么T和\*T有不同的方法集？

在[Go开发规范](https://go.dev/ref/spec)中

任何其他命名类型T的方法集合由所有具有接收器类型T的方法组成。 相应的指针类型\*T的方法集是所有具有接收者\*T或T（也包含T的方法集合）的方法的集合。

如果一个接口值包含一个指针\*T，则方法调用可以通过解引用指针来获得一个值，但是如果一个接口值包含一个值T，那么方法调用就没有用于获取指针的有用方法。

即使在编译器可以将一个值的地址传递给方法的情况下，如果该方法修改了值，这些更改也将丢失。 例如，如果bytes.Buffer的Write方法使用了一个值接收器而不是一个指针，那么：

```go
var buf bytes.Buffer
io.Copy(buf, os.Stdin)
```

会将标准输入复制到buf的副本中，而不是复制到buf本身。

### 如果闭包并发运行会发生什么？

使用并发的闭包时可能会出现一些混淆。考虑下面的程序：

```go
func main() {
    done := make(chan bool)

    values := []string{"a", "b", "c"}
    for _, v := range values {
        go func() {
            fmt.Println(v)
            done <- true
        }()
    }

    // wait for all goroutines to complete before exiting
    for _ = range values {
        <-done
    }
}
```

你可能期望看到输出 a，b，c。但你很可能看到输出 c，c，c。这是因为循环的每个迭代使用变量v的同一个实例，所以每个闭包共享单个变量。 当闭包运行时，它会在执行fmt.Println时打印v的值，但是当goroutine启动v可能已被修改。 为了在这些问题发生之前检测到这些问题，请运行[go vet](https://golang.google.cn/cmd/go/#hdr-Run_go_tool_vet_on_packages)。

要将v的当前值绑定到每个闭包，必须修改内部循环以便在每次迭代中创建一个新变量。一种方法是将变量作为参数传递给闭包：

```go
for _, v := range values {
        go func(u string) {
            fmt.Println(u)
            done <- true
        }(v)
    }
```

可以看到v的值以参数的形式传递给匿名函数。这个值可以在函数里作为变量u访问。

有更简单的办法，虽然看起来有点奇怪，但在Go中是允许的：

```go
for _, v := range values {
        v := v // create a new 'v'.
        go func() {
            fmt.Println(v)
            done <- true
        }()
    }
```

流程控制
----

### Go有三元运算符?:吗？

没有。

包和测试
----

### 如何创建一个多文件包？

将包的所有源文件放在一个目录中。源文件可以随意引用来自不同文件的项目;不需要前向声明或头文件。除了被分割成多个文件之外，软件包将像单个文件包一样进行编译和测试。

### 如何写联合测试？

在与源码相同的目录中创建一个以`_test.go`结尾的新文件。在文件里导入`testing`包，然后这样写

```go
func TestFoo(t *testing.T) {
    ...
}
```

打开命令行，在该文件夹目录下运行`go test`。这个命令会找到测试函数，编译并执行它。

更多内容，查看[如何编写Go代码](/2018/12/16/go_3/)

### Where is my favorite helper function for testing？

Go的标准库`testing`使得编写测试很容易，但缺少其他语言测试框架（如断言）提供的功能。 在上面的章节我们说明了为什么没有断言的存在。正确的错误处理方式意味着调试失败的人可以得到完整的错误信息快速定位，而这一点会在时间和精力上给予你回报。

值得一提的是，测试框架倾向于发展自己的迷你语言，具有自己的条件和控制以及打印机制， 但是Go已经具备了所有这些功能，为什么还要用它们？

如果编写好的测试所需的额外代码量看起来十分让人难以忍受，那么如果表驱动测试可能会好点， 迭代在数据结构中定义的输入和输出列表（Go对此有很好的支持）。标准库有很多这样的例子，参见[fmt包测试源码](https://golang.google.cn/src/fmt/fmt_test.go)

### 为什么标准库没有X？

标准库的目的是支持运行时，连接到操作系统，并提供Go程序需要的关键功能，例如格式化的I/O和网络。 它还包含对web编程非常重要的内容，包括加密和对HTTP，JSON和XML等标准的支持。

没有明确的标准定义了标准库里必须有什么，虽然我们在不断添加新的标准，新的内容。

标准库新增的功能很少，包含的条目经过严格审查。包含在标准库中的代码需要 承担较大的持续维护成本（通常由原作者以外的人承担），受[Go 1兼容性承诺](https://golang.google.cn/doc/go1compat.html)以及[Go版本发布计划](https://golang.org/s/releasesched)的约束，防止因错误产生的修复分支被提供给开发者。

大多数新代码应该在标准库之外，并且可以通过go工具的get get命令来访问。这样的代码可以有自己的维护者，发布周期和兼容性保证。 用户可以在[godoc.org](https://godoc.org/)上找到软件包并阅读文档。

尽管标准库中有些与标准不符的东西，比如log/syslog，但根据Go 1兼容性承诺，我们继续保有库中的所有内容。

实现
--

### 编译器是如何构建的？

Gccgo有一个用C++编写的front end，递归下降解析器连接到标准的GCC back end。 Gc是使用递归下降解析器编写的，使用一个自定义的加载器，用Go编写，但是基于Plan 9加载器，生成ELF/Mach-O/PE二进制文件。

我们曾考虑使用LLVM作为gc，但是我们觉得这对于实现我们的性能目标来说太大而且太慢了。

旧的gc是用C编写的，但是引导困难——你需要一个Go编译器来设置Go环境。但从Go 1.5开始，编译器使用Go编写， 它使用自动翻译工具将C转到Go。因此编译器现在是自举(self-hosting)的，这意味着我们必须面对引导问题。 解决方案是要有一个正常工作的Go安装器，就像通常有一个正常工作的C安装器一样。有兴趣可以[看看这里](https://golang.google.cn/s/go15bootstrap)。

Go是实现Go编译器的较好的选择。尽管gc并没有使用它们(不确定)，但在go软件包中有一个本地的词法分析器和解析器([源码在这里](https://golang.google.cn/pkg/go/))，还有一个类型检查器([标准库的type包](https://golang.google.cn/pkg/go/types/))。

### 运行时如何支持编译？

还是因为引导问题，运行时代码最初主要是用C编写的(有一点点的汇编)，但是现在已经被翻译成了Go（还是有一点汇编）。 gccgo 的运行时支持使用 glibc。 gccgo 的编译器使用称为分段堆栈（segmented stacks）的技术来实现goroutine，最近对gold链接器进行了修改支持。

### 为什么我的代码很少生成的可执行程序却这么大？

gc工具链中的链接器默认创建静态链接的二进制文件。因此，所有Go二进制文件都包含Go运行时，以及支持动态类型检查，反射以及恐慌时间堆栈跟踪所需的运行时类型信息。

在Linux上使用gcc静态编译和链接的一个简单的C“hello，world”程序大约是750 kB，包括一个printf的实现。 使用fmt.Printf的等效Go程序大约为1.5 MB，但是包含更强大的运行时支持和类型信息。

### 我能禁掉未使用变量及导包的编译错误吗？

存在一个未使用的变量的可能表明一个错误，而未使用的包导入只会减慢编译速度，随着时间的推移，烂代码原来越多，这种影响可能会变得很大。 鉴于这些原因，Go拒绝使用未使用的变量或包导入来编译程序，我们在语言层面要求付出短期的便利，以实现长期的编译速度和程序清晰度。

尽管如此，在开发代码时，通常会暂时存在这些情况，在程序编译之前必须将它们编辑出来是很麻烦的。

一些人已经要求编译器选项关闭这些检查，或至少减少这些警告。但是，这样的选项还没有被添加，因为编译器选项不应该影响语言的语义——Go编译器不会报告警告，只会报告阻止编译的错误。

Go编译器不会报告警告有两个原因。首先，我们不想改代码，其次，让编译器生成警告，可能会导致噪音从而掩盖真正的错误。

解决这个问题很容易。使用空白标识符就能让在开发过程中不用的东西持续存在。

```go
import "unused"

// This declaration marks the import as used by referencing an
// item from the package.
var _ = unused.Item  // TODO: Delete before committing!

func main() {
    debugData := debug.Profile()
    _ = debugData // Used only during debugging.
    ....
}
```

现在，大多数Go开发者使用一个命令行工具[goimports](http://godoc.org/golang.org/x/tools/cmd/goimports)，它会自动重写一个Go源文件以获得正确的导入，并消除未使用的导入问题。 这个程序很容易连接到大多数编辑器，在源文件被写入时自动运行。

性能
--

### 为什么Go在benchmark上性能不佳？

_原文将benchmark比赛称为Benchmark games。_

Go的设计目标之一是为可比较的程序提供C的性能，但在一些基准测试中表现不佳，其中包括[golang.org/x/exp/shootout](https://go.googlesource.com/exp/+/master/shootout/)中的一些。 例如，pidigits.go 采用了多精度数学包，而C版本与Go不同，使用GMP（它是用优化的汇编器编写的）。基于正则表达式的基准测试（例如regex-dna.go）将Go的regexp软件包与PCRE等成熟，高度优化的正则表达式库进行了比较。

如果衡量可比较的C和Go程序（reverse-complement.go就是一个例子），你会看到这两种语言在原始性能方面比这个游戏所显示的更为接近。

不过还有改进的空间。编译器效率还可以更高，许多库需要调整性能，gc还不够快。

无论如何，Go非常有竞争力。随着语言和工具的发展，许多关键点的性能有了显着的提高。查看[剖析Go程序](https://blog.golang.org/2011/06/profiling-go-programs.html)以了解更多的内容

与C的不同
-----

### 为什么语法上与C差别这么大？

除了声明语法外，差异并不是非常大，这是出于两个诉求。首先， 语法应该感觉轻松，没有太多的强制性关键字，重复或难以理解。其次，这个语言被设计为易于分析， 并且可以在没有符号表的情况下被解析。这使得构建诸如调试器，依赖关系分析器，自动文档提取器， IDE插件等工具变得更加容易。C和它的后代在这方面实现起来非常困难。

### 为什么将类型声明后置？

在C中，这个概念是变量被声明为表示它的类型的表达式， 这是一个好主意，但是类型和表达式语法不能很好地混合， 结果可能会令人困惑。Go主要分离表达式和类型语法，并进行了简化。 在C，声明

```c
int* a, b;
```

声明了 a 是个指针而 b 不是，在Go中

```go
var a, b *int
```

声明了两者都是指针。这看起来更清晰。同样，:= 短声明表达式认为，完整的变量声明应该按照如下顺序

```go
var a uint64 = 1
```

和这种写法是一样的

```go
a := uint64(1)
```

语法分析也被简化为具有不仅仅是表达式语法的类型的独特语法; func和chan等关键字采用这样的方法保持含义清晰。

查看[Go声明语法](https://golang.google.cn/doc/articles/gos_declaration_syntax.html)了解更多。

### 为什么没有指针运算？

为了安全。如果没有指针运算，可能会创建一个永远不会导致错误地成功的非法地址。编译器和硬件技术已经发展到使用数组索引的循环与使用指针运算的循环一样有效。另外，抛弃指针运算可以简化垃圾收集器的实现。

### 为什么只有++和--后缀没有前缀？为什么它们是语句而不是表达式？

如果没有指针运算，前缀和后缀增量运算符的便利性会下降。通过将它们从表达式层次中全部删除，简化了表达式语法，并且围绕++和--（考虑f（i ++）和p \[i\] = q \[++i\]）的评估顺序的复杂性也被消除。至于后缀与前缀， 后缀版本更传统。

### 为什么没有分号？为什么不能把首个大括号放在下一行？

Go使用括号括起括号，这是一种熟悉C系列中任何语言的编程人员都熟悉的语法。 分号是给解析器看的，而不是人，我们想尽可能地消除分号。为了实现这个目标，Go从BCPL中借鉴了一个技巧： 分隔语句的分号存在于形式语法中，但是在任何可能是语句结束的行末尾，词法分析器都会自动注入分号，而不是向前。 这在实践中效果很好，但是写起来就必须符合一定的规则。 例如，函数的左括号不能单独出现在一行上。

有些人认为，词法分析器应该向前检查，以便大括号能够存在于下一行。 我们拒绝了这种思路。由于Go代码是由gofmt自动格式化的，因此必须选择一定的风格。 这种风格可能与你在C或Java中使用的风格不同。 最最重要的是，统一代码风格的优点大大超过了任何可察觉的缺点。 还要注意，Go的风格意味着Go的一个交互式实现可以一次使用一行标准语法而不需要制定特殊的规则。

### 为什么采用GC？

内存管理是系统程序中最大的记录消耗来源之一。我们认为消除开发者的心智负担是至关重要的， 过去几年里垃圾收集技术的进步使我们有信心能够以足够低的开销实现它，并且没有明显的延迟。

还有一点是并发和多线程编程的难点大部分是内存管理; 随着对象在线程之间传递，保证它们被安全地释放变得麻烦。自动垃圾收集使并发代码更容易编写。 当然，在并发环境中实现垃圾收集本身就是一个挑战，但是一次解决而不是在每个程序中都造轮子能帮助所有人。

最后，抛开并发，垃圾收集使得接口更简单，因为它们不需要指定如何管理内存。

目前的做法是实现一个并发的标记和扫描收集器。在[设计文档](https://golang.google.cn/s/go14gc)中 记录的最近的改进已经引入了有限的暂停时间并改善了并行性。未来版本可能会尝试新的方法。

关于性能的话题，请记住，Go让开发者对内存布局和分配有相当大的控制，远远超过垃圾收集语言的典型特征。 一个细心的开发者可以很好地使用语言来减少垃圾收集开销；请参阅关于对[Go程序进行性能分析](https://blog.golang.org/2011/06/profiling-go-programs.html)，其中包括Go的性能分析工具演示。
