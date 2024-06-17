---
title: Elixir语言之Phoenix框架安装
date: 2019-10-23 23:37:40
cover: 
categories: 默认分类
tags:
- Elixir
- Phoenix
---

## 简介

Phoenix是一个用Elixir编写的Web开发框架，它实现了服务器端模型视图控制器（MVC）模式。我们的其他人似乎对在其他Web框架（例如Ruby on Rails或Python的Django）中拥有丰富的经验感到熟悉。

Phoenix提供了两全其美的优势-高开发人员生产力和高应用程序性能。它还具有一些有趣的新特性，例如用于实现实时功能的通道和用于快速提高速度的预编译模板。

## 安装

如果我们是第一次安装Elixir，则还需要安装Hex软件包管理器。要使Phoenix应用程序运行（通过安装依赖项）并安装我们在此过程中可能需要的任何其他依赖项，必须使用Hex。

这是安装Hex的命令（如果已经安装了Hex，它将升级Hex到最新版本）：

```sh
$ mix local.hex
```

## 安装Phoenix

```sh
$ mix archive.install hex phx_new 1.4.10
```
## PostgreSQL

PostgreSQL是一个关系数据库服务器。Phoenix将应用程序配置为默认使用它，但是我们可以--database mysql在创建新应用程序时通过传递标志来切换到MySQL 。

## 我们的第一个Phoenix应用程序

现在我们已经安装了所有东西，让我们创建第一个Phoenix应用程序并启动并运行。

我们可以mix phx.new从任何目录运行，以引导我们的Phoenix应用程序。Phoenix将接受新项目目录的绝对路径或相对路径。假设我们的应用程序名称为hello，我们运行以下命令：

```sh
$ mix phx.new hello
```

- 在开始之前，有关Webpack的说明：Phoenix默认情况下将使用webpack进行资产管理。Webpack的依赖项是通过节点包管理器安装的，而不是混合安装的。Phoenix
- 将在mix phx.new任务结束时提示我们安装它们。如果那时我们说“ no”，并且以后不使用来安装那些依赖项npm install，则当我们尝试启动它时，我们的
- 应用程 > 序将引发错误，并且资产可能无法正确加载。如果我们根本不想使用webpack，可以将传递--no-webpack给mix phx.new。

关于Ecto的说明：Ecto允许我们的Phoenix应用程序与数据存储进行通信，例如PostgreSQL，MySQL等。如果我们的应用程序不需要此组件，则可以通过将--no-ecto标志传递给来跳过此依赖关系mix phx.new。该标志也可以与之组合--no-webpack以创建框架应用程序。

```sh
$ mix phx.new hello 

* creating hello/config/config.exs 
* creating hello/config/dev.exs 
* creating hello/config/prod.exs 
... 
* creating hello/assets/static/images/phoenix.png  
* creating hello/assets/static/favicon.ico  

Fetch and install dependencies? [Yn]  
```

Phoenix生成目录结构以及应用程序所需的所有文件。完成后，它将询问我们是否要它为我们安装依赖项。让我们对此表示肯定。

```sh
Fetch and install dependencies? [Yn] Y  
* running mix deps.get  
* running mix deps.compile  
* running cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development  
 
We are almost there! The following steps are missing: 

   $ cd hello  

Then configure your database in config/dev.exs and run:  

   $ mix ecto.create  

Start your Phoenix app with:  
 
   $ mix phx.server  

You can also run your app inside IEx (Interactive Elixir) as: 

   $ iex -S mix phx.server  
```

安装依赖项后，该任务将提示我们更改为项目目录并启动我们的应用程序。

Phoenix假设我们的PostgreSQL数据库将具有一个postgres具有正确权限的用户帐户，密码为“ postgres”。如果不是这种情况,请修改config目录下dev.ex文件中的数据库配置信息。

好的，让我们尝试一下。首先，我们cd进入hello/刚刚创建的目录：

```sh
$ cd hello
```

现在，我们将创建数据库：

```sh
$ mix ecto.create 

Compiling 13 files (.ex) 
Generated hello app 
The database for Hello.Repo has been created 
```

注意：如果这是您第一次运行此命令，Phoenix可能还会要求安装Rebar。由于Rebar用于构建Erlang软件包，因此请继续进行安装。


最后，我们将启动Phoenix服务器：

```sh
$ mix phx.server [info] Running HelloWeb.Endpoint with cowboy 2.5.0 at http://localhost:4000 
Webpack is watching the files… ...
```

如果在生成新应用程序时选择不让Phoenix安装依赖项，则该mix phx.new任务将提示我们在确实要安装依赖项时采取必要的步骤。

```sh
Fetch and install dependencies? [Yn] n  
 
We are almost there! The following steps are missing:  

   $ cd hello 
   $ mix deps.get  
   $ cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development  
 
Then configure your database in config/dev.exs and run:

   $ mix ecto.create  

Start your Phoenix app with:  

   $ mix phx.server  

You can also run your app inside IEx (Interactive Elixir) as:  

   $ iex -S mix phx.server  
```

默认情况下，Phoenix接受端口4000上的请求。如果我们将喜欢的Web浏览器指向http：// localhost：4000，则应该看到Phoenix框架欢迎页面。

如果您的屏幕看起来像上面的图片，那么恭喜！您现在有一个正在运行的Phoenix应用程序。如果看不到上面的页面，请尝试通过`http://127.0.0.1:4000`访问该页面，然后确保您的操作系统已将“ localhost”定义为“ 127.0.0.1”。
![welcome-to-phoenix.png][1]
在本地，我们的应用程序在iex会话中运行。要停止它，我们打了ctrl-c两次，就像我们iex正常停止一样。


  [1]: http://www.markhoo.com/276230293.png

