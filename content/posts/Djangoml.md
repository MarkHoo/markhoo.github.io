---
title: Django使用基础常用命令
date: 2017-11-20 10:35:15
cover: 
categories: Django
tags:
- Python
- Django
---

> Django使用基础常用命令

<!--more-->

- 查看django命令：`django-admin`即可显示django的可用命令
- 创建项目命令：`django-admin startproject [项目名称]`若没有报错则创建项目成功
- 查看manage.py常用命令：`python manage.py`即可显示manage中的可用命令
- 启动项目服务器命令：`python manage.py runserver`启动服务器
- 启动项目服务器并修改端口：`python manage.py runserver 9999`使用9999端口来启动服务器
- 创建django应用需要进入与`manage.py`的同级目录进行创建
- 创建命令为：`python manage.py startapp [项目名称]`
- 创建完成后将应用名称添加到`settings.py`中的`INSTALLED_APPS`中完成应用的创建
- models模型创建完毕后使用`manage.py`来生成数据表，models模型创建需要在应用目录中的`models.py`中进行创建
- 创建数据迁移命令：`python manage.py makemigrations [应用名称]`进行数据迁移的准备步骤，输入以下命令完成数据迁移操作
- 开始数据迁移命令：`python manage.py migrate`

> 注：如果不输入应用名称则默认对django中的所有应用进行数据迁移操作

数据迁移完成后使用以下命令进行SQL语句查询

- 命令为：`python manage.py sqlmigrate [应用名称] [文件id]`

给django创建一个超级用户
- 命令为：`python manage.py createsuperuser`

浏览器中访问：`localhost:8000/admin`进行访问admin页面

- 在应用下`admin.py`中引入自身的models模块，使用编辑`admin.py`添加代码：`admin.site.register(models.Article)`
- 将admin管理后台进行配置升级，创建admin配置类`class ArticleAdmin(admin.ModelAdmin)`
- 注册：`admin.site.register(Article,ArticleAdmin )`
