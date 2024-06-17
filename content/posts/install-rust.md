---
title: Windows安装Rust（详细版）
date: 2024-04-20 13:54:27
cover: 
categories: Rust
tags:
- Rust
---

> Rust 速度惊人且内存利用率极高。由于没有运行时和垃圾回收，它能够胜任对性能要求特别高的服务，可以在嵌入式设备上运行，还能轻松和其他语言集成。

<!--more-->

1. 前置条件：安装C++环境


rust底层是依赖C环境的连接器，所以需要先安装C/C++编译环境, 有两种选择:安装微软的msvc或者安装mingw/cygwin。
如果使用msvc的Visual Studio，只需要安装好C/C++编译环境,然后一路默认就行了，缺点是体积比较大，下载安装都要好几个G，


[MinGW-w64](https://www.mingw-w64.org/downloads/)

[MingW-W64-builds](https://github.com/niXman/mingw-builds-binaries/releases)


64位下载地址
[mingw-builds-binaries](https://github.com/niXman/mingw-builds-binaries/releases/download/13.2.0-rt_v11-rev0/x86_64-13.2.0-release-posix-seh-ucrt-rt_v11-rev0.7z)


如果你是64位的系统，那就选择x86_64开头的，反之32位的选择i686开头的。

线程模型有posix和win32，如果你想在vscode里面搞断点调试，我推荐用posix。

然后是异常处理机制，我目前用的seh

● seh 零开销exception，64位系统可用
● sjlj 不是零成本，有较小的性能损失
● dwarf 只支持32位

MinGW-w64、UCRT 和 MSVCRT 是 Windows 平台上常用的 C/C++ 运行库，它们有以下不同点：

- MinGW-w64：是一个基于 GCC 的编译器套件，可以用于编译 Windows 平台上的 C/C++ 程序。MinGW-w64 使用的是 POSIX 标准的运行库，因此可以很好地兼容 Unix/Linux 程序。MinGW-w64 的默认运行库是 MSVCRT（Microsoft Visual C++ Runtime），但也可以使用其他运行库。
- UCRT（Universal CRT）：是微软提供的一个通用 C 运行库，可以用于编译 Windows 平台上的 C/C++ 程序。UCRT 提供了许多与标准 C 库兼容的函数，并支持新的 C11 和 C++11 标准。UCRT 适用于 Windows 10 或更高版本的操作系统。
- MSVCRT（Microsoft Visual C++ Runtime）：是微软提供的一个 C++ 运行库，可以用于编译 Windows 平台上的 C/C++ 程序。MSVCRT 提供了许多与标准 C 库兼容的函数，但不支持新的 C11 和 C++11 标准。MSVCRT 在 Windows 操作系统中已经内置，并且随着 Visual Studio 版本的升级而不断更新。

我选择的是：x86_64-13.2.0-release-posix-seh-ucrt-rt_v11-rev0.7z，下载到本地解压后，把bin文件夹路径配置到Path系统环境变量即可。



验证版本

gcc 和 g++ 都是 GNU 工具链中的编译器，它们的主要区别在于它们所编译的代码的语言不同。

gcc 是 GNU C 编译器，它主要用于编译 C 语言程序，可以将 C 语言源代码编译成可执行文件或动态链接库。

g++ 则是 GNU C++ 编译器，它主要用于编译 C++ 语言程序，可以将 C++ 语言源代码编译成可执行文件或动态链接库。

虽然 gcc 和 g++ 是两个不同的编译器，但它们都基于同一套 GNU 工具链，并共享许多相同的功能和选项。因此，除了语言不同外，它们的使用方式和命令选项也非常相似。

需要注意的是，C++ 语言相对于 C 语言来说增加了一些特性，例如类、继承、多态等。如果您想编译 C++ 代码，建议使用 g++ 编译器，因为它可以更好地支持这些特性。而如果您只是编译 C 代码，gcc 编译器就足够了。

```sh
gcc -v
gdb -v
mingw32-make -v
```

2. 安装Rust

安装之前，先想想要安装到什么地方，我不喜欢安装到C盘，可以设置一些系统环境变量，改变默认安装的位置

```
RUSTUP_HOME D:\rust\.rustup  // 默认路径 ~/.rustup 或 %USERPROFILE%/.rustup
CARGO_HOME  D:\rust\.cargo // 默认路径 ~/.cargo 或 %USERPROFILE%/.cargo
```

如果直接从官方网站下载，国内的环境复杂，下载会很慢，而且容易失败。推荐使用镜像加速安装，设置以下环境变量：

```
RUSTUP_DIST_SERVER  https://rsproxy.cn
RUSTUP_UPDATE_ROOT  https://rsproxy.cn/rustup
```

这一切准备工作做好之后，就可以用rustup-init来安装了
如果使用msvc环境的话，一切默认就行了
而mingw就需要手动选择gnu toolchain


```
msvc的toolchain：  stable-x86_64-pc-windows-msvc
mingw/cygwin:     stable-x86_64-pc-windows-gnu
```

运行rustup-init后出现如下提示：

```
Rust Visual C++ prerequisites

Rust requires a linker and Windows API libraries but they don't seem to be
available.

These components can be acquired through a Visual Studio installer.

1) Quick install via the Visual Studio Community installer
   (free for individuals, academic uses, and open source).

2) Manually install the prerequisites
   (for enterprise and advanced users).

3) Don't install the prerequisites
   (if you're targeting the GNU ABI).

>
```

它说rust需要windows API库和链接器，你可以选择下面3项的一项。

1、安装Visual Studio。
2、手动安装的预设条件。
3、无需预设条件。

我们这里选择3，mingw-w64也是C/C++编译器，就是 GCC 的 Windows 版本 。回车：


```
>3

Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  C:\Users\markh\.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  C:\Users\markh\.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  C:\Users\markh\.cargo\bin

This path will then be added to your PATH environment variable by
modifying the HKEY_CURRENT_USER/Environment/PATH registry key.

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-pc-windows-msvc
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with standard installation (default - just press enter)
2) Customize installation
3) Cancel installation
>
```


然后输入2，就是自定义安装，出现提示：

```
>2

I'm going to ask you the value of each of these installation options.
You may simply press the Enter key to leave unchanged.

Default host triple? [x86_64-pc-windows-msvc]

```

输入x86_64-pc-windows-gnu，（小提示，你先复制x86_64-pc-windows-gnu，在rustup右键可以直接粘贴文字，可以不用手写输入。出现提示：


```
Default toolchain? (stable/beta/nightly/none) [stable]
```

直接回车，默认选择是稳定版。出现提示：



```
Profile (which tools and data to install)? (minimal/default/complete) [default]
```

直接回车，默认选择默认。出现提示：

```
Modify PATH variable? (Y/n)
```
输入Y回车（不分大小写），表示修改环境变量，然后回到了第一步，提示：

```
Current installation options:


   default host triple: x86_64-pc-windows-gnu
     default toolchain: stable
               profile: default
  modify PATH variable: yes

1) Proceed with selected options (default - just press enter)
2) Customize installation
3) Cancel installation
>
```

输入1，然后出现：



```
info: profile set to 'default'
info: setting default host triple to x86_64-pc-windows-gnu
info: syncing channel updates for 'stable-x86_64-pc-windows-gnu'
info: latest update on 2022-12-15, rust version 1.66.0 (69f9c33d7 2022-12-12)
info: downloading component 'cargo'
  7.3 MiB /   7.3 MiB (100 %)   1.9 MiB/s in  5s ETA:  0s
info: downloading component 'clippy'
  3.6 MiB /   3.6 MiB (100 %)   1.7 MiB/s in  2s ETA:  0s
info: downloading component 'rust-docs'
 19.0 MiB /  19.0 MiB (100 %)   2.1 MiB/s in 10s ETA:  0s
info: downloading component 'rust-mingw'
  4.2 MiB /   4.2 MiB (100 %)   2.6 MiB/s in  1s ETA:  0s
info: downloading component 'rust-std'
 27.8 MiB /  27.8 MiB (100 %)   1.8 MiB/s in 14s ETA:  0s
info: downloading component 'rustc'
 74.3 MiB /  74.3 MiB (100 %)   1.5 MiB/s in 49s ETA:  0s
info: downloading component 'rustfmt'
  6.6 MiB /   6.6 MiB (100 %)   2.2 MiB/s in  3s ETA:  0s
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
 19.0 MiB /  19.0 MiB (100 %)   3.0 MiB/s in  5s ETA:  0s
info: installing component 'rust-mingw'
info: installing component 'rust-std'
 27.8 MiB /  27.8 MiB (100 %)   9.0 MiB/s in  3s ETA:  0s
info: installing component 'rustc'
 74.3 MiB /  74.3 MiB (100 %)  10.3 MiB/s in  7s ETA:  0s
info: installing component 'rustfmt'
info: default toolchain set to 'stable-x86_64-pc-windows-gnu'

  stable-x86_64-pc-windows-gnu installed - rustc 1.66.0 (69f9c33d7 2022-12-12)

Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload its PATH environment variable to include
Cargo's bin directory (D:\Programs\rust\cargo_home\bin).

Press the Enter key to continue.
```

等待它下载文件完毕，这里需要等待的时间有点长，但耐心就好，假如下载出现错误，就重设上面的设定，再来一次流程。

当出现最后一行，敲回车，然后窗口关闭。

打开命令提示符，也就是cmd。

分别输入每一行命令后回车，出现相关信息后，标明安装rust成功了。
```
rustc -V
rustup -V
cargo -V
rustup show 
```


其他相关命令

```
#安装其他类型的toolchain开发环境
rustup toolchain install <toolchain>

#例如
rustup toolchain install  stable-x86_64-pc-windows-gnu

#切换默认的toolchain
rustup default [toolchain]

#例如
rustup default stable-x86_64-pc-windows-gnu

#更新：
rustup update stable
rustup default stable
```




```
Rust Visual C++ prerequisites

Rust requires a linker and Windows API libraries but they don't seem to be
available.

These components can be acquired through a Visual Studio installer.

1) Quick install via the Visual Studio Community installer
   (free for individuals, academic uses, and open source).

2) Manually install the prerequisites
   (for enterprise and advanced users).

3) Don't install the prerequisites
   (if you're targeting the GNU ABI).

>3


Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  C:\Users\markh\.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  C:\Users\markh\.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  C:\Users\markh\.cargo\bin

This path will then be added to your PATH environment variable by
modifying the HKEY_CURRENT_USER/Environment/PATH registry key.

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-pc-windows-msvc
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with standard installation (default - just press enter)
2) Customize installation
3) Cancel installation
>2

I'm going to ask you the value of each of these installation options.
You may simply press the Enter key to leave unchanged.

Default host triple? [x86_64-pc-windows-msvc]
x86_64-pc-windows-gnu

Default toolchain? (stable/beta/nightly/none) [stable]


Profile (which tools and data to install)? (minimal/default/complete) [default]


Modify PATH variable? (Y/n)
y


Current installation options:


   default host triple: x86_64-pc-windows-gnu
     default toolchain: stable
               profile: default
  modify PATH variable: yes

1) Proceed with selected options (default - just press enter)
2) Customize installation
3) Cancel installation
>1
```






参考：
- https://blog.csdn.net/Mr_Roki/article/details/136213972




报错信息：

```sh
error: linker `link.exe` not found
  |
  = note: program not found

note: the msvc targets depend on the msvc linker but `link.exe` was not found

note: please ensure that Visual Studio 2017 or later, or Build Tools for Visual Studio were installed with the Visual C++ option.

note: VS Code is a different product, and is not sufficient.

error: aborting due to 1 previous error
```


解决方案：

1. 

如果cmd运行 rustc -version 显示rust版本，则运行

```
rustup default stable-x86_64-pc-windows-gnu
```

如果没有则运行：

```
rustup toolchain install stable-x86_64-pc-windows-gnu

rustup default stable-x86_64-pc-windows-gnu
```



2. 下载安装依赖

[visualcppbuildtools_full.exe](https://download.microsoft.com/download/5/f/7/5f7acaeb-8363-451f-9425-68a90f98b238/visualcppbuildtools_full.exe)



[Microsoft C++ 生成工具](https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/)









