---
title: 在Linux(CentOS、Ubuntu等等)中安装Erlang和Elixir
date: 2024-07-17 20:44:56
cover: 
categories: Elixir
tags:
- Erlang
- Elixir
- Linux
---

> Erlang是一种开源编程语言，用于构建对高可用性有要求的大规模可扩展的软实时系统。它通常用于电信，银行，电子商务，计算机电话和即时消息中。Erlang的runtime系统具有对并发，分发和容错的内置支持。它是在爱立信计算机科学实验室设计的。

<!--more-->

## 检查旧版本

```
$ uname -a
Linux ws-ersyia-0 5.4.241-1-tlinux4-0017.10 #1 SMP Wed May 8 17:01:03 CST 2024 x86_64 x86_64 x86_64 GNU/Linux
➜  /workspace elixir --version
Erlang/OTP 24 [erts-12.3.2.17] [source] [64-bit] [smp:8:2] [ds:8:2:10] [async-threads:1] [jit]

Elixir 1.13.3 (compiled with Erlang/OTP 22)
```

## 卸载旧版本

```
$ where elixir
$ where erl
$ rm -rf /opt/elixir /opt/erlang 
```

## 下载安装包

```
$ wget https://github.com/erlang/otp/releases/download/OTP-27.0.1/otp_src_27.0.1.tar.gz
$ wget https://github.com/elixir-lang/elixir/archive/refs/tags/v1.17.2.zip
```

## 安装 erlang

```
$ tar zxvf otp_src_27.0.1.tar.gz
$ cd otp_src_27.0.1 
$ ./configure && make && make install
$ erl -s erlang halt                                                      
Erlang/OTP 27 [erts-15.0.1] [source] [64-bit] [smp:8:2] [ds:8:2:10] [async-threads:1] [jit:ns]
```

## 安装 elixir

```
$ unzip v1.17.2.zip 
$ cd elixir-1.17.2 
$ make clean compile
$ ./bin/elixir --version
Erlang/OTP 27 [erts-15.0.1] [source] [64-bit] [smp:8:2] [ds:8:2:10] [async-threads:1] [jit:ns]

Elixir 1.17.2 (compiled with Erlang/OTP 27)
```

## 配置 PATH

```
$ vim ~/.zshrc
```

添加路径到PATH

```
export PATH=$PATH:/workspace/elixir-1.17.2/bin
```

如果要配置文件立即生效，还需要source一下配置文件

```
$ source ~/.zshrc
```

查看版本：

```
$ elixir --version
Erlang/OTP 27 [erts-15.0.1] [source] [64-bit] [smp:8:2] [ds:8:2:10] [async-threads:1] [jit:ns]

Elixir 1.17.2 (compiled with Erlang/OTP 27)
```

## mix ok & install rebar3

配置mix hex和rebar3

```
$ mix local.hex -y
$ wget https://s3.amazonaws.com/rebar3/rebar3
$ chmod +x ./rebar3 
$ ./rebar3 local install

$ vim ~/.zshrc
export PATH=/root/.cache/rebar3/bin:$PATH

$ rebar3 -v           
rebar 3.23.0 on Erlang/OTP 27 Erts 15.0.1
```

如果要配置文件立即生效，还需要source一下配置文件

```
$ source ~/.zshrc
```
