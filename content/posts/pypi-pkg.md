---
title: 发布包到pypi
date: 2022-11-14 23:51:32
cover: 
categories: Python
tags:
- Python
- pip
- pypi
---

> 很多人只用过别人写的包，那么有没有想过如何将自己的包发布出去让别人用

<!--more-->

首先到pypi.org注册账户，然后生成一个Token，注意生成Token的页面不要关闭不要刷新，因为只显示一次，在Token下面会有一个说明，就是在你的用户目录下创建一个文件，然后把那些内容复制粘贴进去保存就行了。这样做是为了在上传的时候不用输入用户名和密码，防止403报错等等。
提示：Windows的用户目录就是`C:\Users\zhangsan` 这个路径，打开步骤就是`C盘->用户->用户名`

项目目录结构：
```
porject_example/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── pkg_name/
│       ├── __init__.py
│       └── example.py
└── tests/
```

#### 构建工具

这里列出4个常见的构建工具，示例里面使用安装时常看到的setuptools，当然也根据个人喜好进行选择。
选择一个构建工具添加到pyproject.toml

setuptools
```
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"
```

Hatchling
```
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

Flit
```
[build-system]
requires = ["flit_core>=3.2"]
build-backend = "flit_core.buildapi"
```

PDM
```
[build-system]
requires = ["pdm-pep517"]
build-backend = "pdm.pep517.api"
```

requires是构建包所需的包列表。您不需要安装它们；像pip这样的构建前端会自动将它们安装在一个临时的、隔离的虚拟环境中，以便在构建过程中使用。
build-backend是前端将用于执行构建的 Python 对象的名称。


#### 配置元数据

打开pyproject.toml并输入以下内容。更改name 以包含您的用户名；这确保您有一个唯一的包名称，不会与其他人按照本教程上传的包发生冲突。

```
[project]
name = "pkg_name"
version = "0.0.1"
authors = [
  { name="Zhang San", email="admin@gmail.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.7"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
"Homepage" = "https://github.com/zhangsan/porject_example"
"Bug Tracker" = "https://github.com/zhangsan/porject_example/issues"
```

name是您的包的分发名称。这可以是任何名称，只要它只包含字母、数字、.、\_和-。它也不能已经在 PyPI 上使用。请务必使用您在本教程中的用户名更新此名称，因为这可确保您不会尝试上传与已存在名称相同的包。

version是包版本。有关版本的更多详细信息，请参阅版本说明符规范 。一些构建后端允许以其他方式指定它，例如从文件或 git 标签。

authors用于识别包的作者；您为每位作者指定姓名和电子邮件。您也可以maintainers以相同的格式列出。

description是包的简短单句摘要。

readme是包含包详细描述的文件的路径。这显示在 PyPI 的包详细信息页面上。README.md在这种情况下，描述是从（这是一种常见模式）加载的。项目元数据规范中还描述了一种更高级的表格形式 。

requires-python给出项目支持的 Python 版本。像pip这样的安装程序会回顾旧版本的包，直到找到一个具有匹配 Python 版本的包。

classifiers为 index 和pip提供一些关于您的包的额外元数据。在这种情况下，该包仅与 Python 3 兼容，根据 MIT 许可获得许可，并且独立于操作系统。您应该始终至少包括您的包适用于哪些 Python 版本、您的包在哪个许可下可用以及您的包将在哪些操作系统上运行。有关分类器的完整列表，请参阅 https://pypi.org/classifiers/。

urls允许您列出任意数量的额外链接以显示在 PyPI 上。通常这可能是源代码、文档、问题跟踪器等。


有关可以在 表中定义的这些字段和其他字段的详细信息，请参阅项目元数据规范。[project]其他常见字段keywords用于提高可发现性以及 dependencies安装包所需的字段。
https://packaging.python.org/en/latest/specifications/declaring-project-metadata/#declaring-project-metadata

重点：已经发布过得版本号不能再次使用！否则会400报错。创建并上传成功的包名，即使你在pypi管理页面删除了，也不能再次使用！所以不要随意删除你已占用到的名称的项目，不然就只能更换包名了！

pyproject.toml 文件最终配置完成的示例：

```
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"


[project]
name = "pkg_name"
version = "0.0.1"
authors = [
  { name="Zhang San", email="admin@gmail.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.7"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
"Homepage" = "https://github.com/zhangsan/porject_example"
"Bug Tracker" = "https://github.com/zhangsan/porject_example/issues"
```

#### 创建 README.md

这个文件是包的说明文档，使用Markdown语法(不会的话花几分钟看看)，内容包括项目介绍，安装，使用范例，开发人员，下载量，开源协议等等，不知道怎么写可以看看GitHub上其他项目是怎么写的，参考着写就行了。

#### 创建许可证

上传到pypi的每个包都包含一个许可证，这是告诉使用这个软件包的用户需要遵守那些条款。
但是既然发布一个包总想着长期维护吧，所以在GitHub上创建仓库的时候会有一个选择许可证的下拉框，一般常用的是MIT和Apache2.0，区别是使用的时候一个是不署名，一个要署名。


#### 生成分发档案

先升级PyPA到最新版本
```
py -m pip install --upgrade build
```


在项目根目录运行以下步骤的命令，也就是能看到pyproject.toml文件的位置

将项目进行打包
```
$ py -m build
```

#### 上传分发档案

将自己的项目上传到pypi
```
$ py -m twine upload dist/*
```

#### 安装你新上传的包

这个步骤和你安装其他包是一样的，打开pypi项目主页也会显示命令，复制粘贴安装就行了。
怎么导入包，使用包，这些都是基础知识，不多赘述了。


#### 重要提示

以上是首次发布的步骤，但是你要更新版本号的话需要注意：

1. 要在`pyproject.toml`里面修改版本号，必须是该项目在pypi上没有用过的版本号
2. 重新构建前必须删除之前构建生成的所有文件，比如：`dist`文件夹，`src`文件夹下的`*.egg-info`文件夹


参考链接：
- https://packaging.python.org/en/latest/tutorials/packaging-projects/
- https://python-poetry.org/

