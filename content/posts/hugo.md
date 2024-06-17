---
title: Hugo搭建博客教程
date: 2020-03-30 23:06:05
cover: 
categories: 默认分类
tags:
- Hugo
---

## 安装Hugo
----------------

#### 首先安装Git

在底部附近找到Windows文件（它们是按字母顺序排列，因此Windows是最后一个）–根据您使用的是32位还是64位Windows，下载32位或64位文件。（如果您不知道，请参见此处。）
将ZIP文件移动到您的`C:\Hugo\bin`文件夹中。
双击ZIP文件并提取其内容。确保将内容提取到同一`C:\Hugo\bin`文件夹中-Windows会默认执行此操作，除非您告诉它要提取其他位置。
现在，您应该有了三个新文件：hugo可执行文件（hugo.exe）LICENSE，和README.md。
现在，您需要将Hugo添加到Windows PATH设置中：


#### 安装

```sh
$ brew install hugo
```


#### 验证安装

```sh
$ hugo version
$ hugo help
```
#### 新建网站：

```sh
$ hugo new site quickstart
```

#### 添加内容

```sh
$ content/<CATEGORY>/<FILE>.<FORMAT>
$ hugo new posts/my-first-post.md
```

#### Hugo命令
命令`hugo`将您的站点渲染到`public/dir`中，并准备将其部署到Web服务器：

#### 启动Hugo服务器

```sh
$ hugo server -D
```

`http://localhost:1313`

#### 安装主题
config.toml

```yaml
baseURL = "https://example.org/"
languageCode = "en-us"
title = "My New Hugo Site"
theme = "ananke"        
```

#### 建立静态页面

```sh
$ hugo -D
```
`./public/`默认情况下，输出将位于目录中（-d/ --destination标志进行更改，或publishdir在配置文件中设置）。

#### 运行调试





#### 配置文件




#### 上线到GitHub, Gitee, Coding, Gitlab





