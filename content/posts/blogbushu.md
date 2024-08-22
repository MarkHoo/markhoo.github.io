---
title: 使用Centos7.5+Nginx+Gunicorn+Django+Python3部署blog项目
date: 2018-08-14 18:46:15
cover: 
categories: Django
tags:
- Django
- Python
- Gunicorn
- Nginx
- CentOS7
---

> 本项目开发环境是 Python3.5.2+Django1.10.6+Sqlite3+Centos7.5+Nginx1.12.2+Gunicorn

<!--more-->

使用Centos7.5+Nginx+Gunicorn+Django+Python3部署blog项目

本项目开发环境是 Python3.5.2+Django1.10.6+Sqlite3+Centos7.5+Nginx1.12.2+Gunicorn

blog虽然已经上线测试有一段时间了(2018.8.7上线)，但是项目部署的笔记由于内容较多，没时间整理。今天抽空整理了一下午，发布出来供需要的同学借鉴参考。文中如有错误请多多指正


部署前的准备

## 服务器

要部署项目就要先购买服务器，如腾讯云，阿里云等等

注意：服务器在国内的话，必须要备案！不然无法使用。

阿里云优惠券：[点击前往领取](https://promotion.aliyun.com/ntms/yunparter/invite.html?userCode=od77opdu&utm_source=od77opdu)
腾讯云代金券：[点击前往领取](https://cloud.tencent.com/redirect.php?redirect=1025&cps_key=e45aa4e4a119335f28464633e4f42262&from=console)
腾讯云首页:[点击直达](https://cloud.tencent.com/redirect.php?redirect=1005&cps_key=e45aa4e4a119335f28464633e4f42262&from=console)
腾讯云西南区云产品促销链接：[点击直达](https://cloud.tencent.com/redirect.php?redirect=1014&cps_key=e45aa4e4a119335f28464633e4f42262&from=console)
腾讯云服务器购买链接：[点击直达](https://cloud.tencent.com/redirect.php?redirect=1001&cps_key=e45aa4e4a119335f28464633e4f42262&from=console)


## 域名

域名购买选择，可以到百度云，腾讯云，阿里云，GoDaddy，namecheap等等

购买域名后就是解析域名，打开域名解析面板，添加解析记录
- 记录类型：`A`
- 主机记录：`test.markhoo.cn`，这个`markhoo.cn`是你的一级域名，二级域名你可以设置为`www.markhoo.cn`
- 解析线路：可以默认也可以选别的
- 记录值：填写你的服务器IP地址
- TTL：域名生效时间，也就是多久生效
设置好以上内容，点击确定。解析完成

主机记录中这个markhoo.cn是你的一级域名，二级域名你也可以设置为 `www.markhoo.cn` 或者别的 `xxx.markhoo.cn` 也可以。
注意：域名是否在国内购买不重要，只要服务器不在大陆购买，即使未备案照样可以使用。要是要解析的服务器是大陆服务商则必须要备案。备案后一个域名可以同时接入多个服务商。

域名解析记录类型说明：
- A：将域名指向一个IPv4地址
- AAAA：将域名指向一个IPv6地址
- CNAME：将域名指向另外一个域名
- NS：将子域名指定其他DNS服务器解析
- MX：将域名指向邮件服务器地址
- SRV：记录提供特定的服务的服务器
- TXT：文本长度限制512，通常做SPF记录(反垃圾邮件)
- CAA：CA证书颁发机构授权校验
- 显性URL：将域名302重定向到另外一个地址
- 隐性URL：与显性URL类似，但是会隐藏真实目标地址


## xshell下载与安装

主要用于远程登录服务器进行相关操作。在官网注册后发送下载链接到你的注册邮箱，或者到搜索引擎搜索下载。
个人版免费，企业版收费。
下载后一路点击下一步，完成。

登录服务器步骤：
1. 点击左上角'文件'--'新建'
2. 然后弹出对话框
3. 常规：名称，这个你可以随便取什么名字
4. 协议：SSH，一般默认
5. 主机：空，填上你的服务器IP地址
6. 端口号：22，默认的就行
7. 填好以上选项内容，点击左侧的'用户身份验证'，然后看右侧，方法选择'Password'，在下面输入你的服务器登录用户名和密码，最后点击下面的'连接'。
8. 登录成功！进入界面。

百度网盘官网软件下载链接：
链接: https://pan.baidu.com/s/19K5q1JkmD9qnYaGRTALyLg 密码: r6hx

> 现在官网也是免费使用，也可以直接官网下载

## 安装Nginx

首先用xshell登录服务器
通过 yum 安装 Nginx

```sh
$ yum install nginx
```

启动 Nginx 服务

```sh
$ systemctl start nginx
```

或者

```sh
$ systemctl start nginx.service
$ systemctl enable nginx.service
```

访问服务器IP地址，可以看到 nginx 的欢迎界面

Nginx相关命令：
停止nginx

```sh
$ nginx -s stop
```

重启nginx

```sh
$ nginx -s reload
```

## Centos7.5安装Python3

由于centos7.5原本默认安装了Python2，而且这个Python2不能被删除，因为有很多系统命令，比如yum都要用到。
以下操作首先要用xshell登录到服务器
输入Python命令，查看可以得知是Python2.7.5版本

输入

```sh
$ which python
```

可以查看位置，一般是位于/usr/bin/python目录下。

下面介绍安装Python3的方法

首先安装依赖包

```sh
$ yum -y groupinstall "Development tools"
$ yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

然后根据自己需求下载不同版本的Python3，我下载的是Python3.5.2

注：如果速度不够快，可以直接去官网下载，利用WinSCP等软件传到服务器上指定位置。

我的存放目录是/usr/local/python3，使用命令：

```sh
$ mkdir /usr/local/python3 
```

建立好一个空文件夹，进入该目录

```sh
$ cd /usr/local/python3
```

使用一下命令下载压缩包

```sh
$ wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tar.xz
```

然后解压压缩包安装Python3

```sh
$ tar -xvJf  Python-3.5.2.tar.xz
$ cd Python-3.5.2
$ ./configure --prefix=/usr/local/python3
$ make && make install
```

最后创建软链接

```sh
$ ln -s /usr/local/python3/bin/python3 /usr/bin/python3
$ ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

在命令行中输入python3测试

升级pip3命令：

```sh
$ pip3 install --upgrade pip
```


## 部署前的项目配置

Django 项目中会有一些 CSS、JavaScript 等静态文件，为了能够方便地让 Nginx 处理这些静态文件的请求，我们把项目中的全部静态文件收集到一个统一的目录下，这个目录通常位于 Django 项目的根目录，并且命名为 static。为了完成这些任务，需要在项目的配置文件里做一些必要的配置：

```python
myblog/settings.py

# 其他配置...

STATIC_URL = '/static/'
# 加入下面的配置
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

STATIC_ROOT 指明了静态文件的收集目录，即项目根目录（BASE_DIR）下的 static 文件夹。

为了安全起见，在生产环境下需要关闭 DEBUG 选项以及设置允许访问的域名。打开 settings.py 文件，找到 DEBUG 和 ALLOWED_HOSTS 这两个选项，将它们设置成如下的值： 

```python
myblog/settings.py

DEBUG = False
ALLOWED_HOSTS = ['127.0.0.1', 'localhost ', '.markhoo.cn']
```

ALLOWED_HOSTS 是允许访问的域名列表，127.0.0.1 和 localhost 是本地访问的域名，.markhoo.cn 是访问服务器的域名（换成你自己的域名）。域名前加一个点表示允许访问该域名下的子域名，比如 www.markhoo.cn  test.markhoo.cn 等二级域名同样允许访问。如果不加前面的点则只允许访问 markhoo.cn。

项目还会依赖一些第三方 Python 库，为了方便在服务器上一次性安装，我们将全部依赖写入一个叫 requirements.txt 的文本文件中。激活本地的虚拟环境（如果你使用了虚拟环境的话），并进入项目的根目录，运行 pip freeze > requirements.txt 命令：

```sh
$ (myblog_env) D:\Djangoprojects\myblog>
$ pip freeze > requirements.txt
```

这时项目根目录下会生成了一个 requirements.txt 的文本文件，其内容记录了项目的全部依赖。由于有一些依赖包也有先后依赖关系，该调整的需要进去txt文件调整一下。

如果上传到服务器后修改，可以使用 'vi (文件名称)' 打开文件，然后点击键盘 'i' 键切换到输入模式。
修改完需要退出，先按键盘 'Esc' 键，然后输入 ':wq' 保存退出。如果不想修改又无法退出可以用 ':q!' 强制退出，这样不会保存你修改的内容
更多的vi、vim命令使用方法可以自行到搜索引擎搜索学习，这里就不一一讲解了。


## 将代码上传到 GitHub 、 Gitee 、 或者其他仓库

将代码上传到 GitHub 等代码托管平台，这样我们就可以方便地把代码拉取到服务器了。
如果访问慢或者打不开GitHub的话，可以上传到国内的Gitee或者Coding


## Git的安装和上传代码的简单方法

安装Git可以到官网 https://git-scm.com 下载，安装的话可以一直下一步，完成。

首先你需要一个GitHub 、 Gitee或者其他账号，所以还没有的话先去注册吧！

- https://github.com
- https://gitee.com

1.进入Github首页，点击`New repository`新建一个项目

2.填写相应信息后点击`create`即可 

- Repository name: 仓库名称

- Description(可选): 仓库描述介绍

- Public, Private : 仓库权限（公开共享，私有或指定合作者）

- Initialize this repository with a README: 添加一个`README.md`

- gitignore: 不需要进行版本管理的仓库类型，对应生成文件`.gitignore`

- license: 证书类型，对应生成文件`LICENSE`

一般可以填写项目仓库名称，点击`create`就可以了。

3.创建好项目仓库后，在项目仓库页面点击`Clone or dowload`会出现一个地址，copy这个地址备用。

4.接下来就到本地操作了，首先右键你的项目，如果你之前安装git成功的话，右键会出现两个新选项，分别为`Git Gui Here`,`Git Bash Here`,这里我们选择`Git Bash Here`进入界面，我的本地项目名为`myblog`。

5.接下来输入如下代码（关键步骤），把github上面的仓库克隆到本地

```sh
$ git clone (粘贴上你之前复制的地址)
```

6.操作步骤5以后你的本地项目文件夹下面就会多出个文件夹，该文件夹名即为你github上面的项目名，如我多出了个`myblogsite`文件夹，我们把本地项目文件夹下的所有文件（除了新多出的那个文件夹不用），其余都复制到那个新多出的文件夹下。

7.接着继续输入命令 ```cd myblogsite```，进入`myblogsite`文件夹

8.接下来依次输入以下代码即可完成其他剩余操作：

```sh
$ git add .        （注：别忘记后面的.，此操作是把myblogsite文件夹下面的文件都添加进来）
$ git commit  -m  "提交信息"  （注：“提交信息”里面换成你需要，如“first commit”）
$ git push -u origin master   （注：此操作目的是把本地仓库push到github上面，此步骤需要你输入GitHub帐号和密码）
```

> 注意：
> 数据库文件不要上传！要是SQLLITE3里面有你测试的重要数据，将可能会泄露。
> 配置里面的数据库密码，发送邮件等等配置里面的登录和密码隐私文件也记得删除

## 设置服务器目录结构

接下来需要把代码上传到服务器了。我服务器上存放代码的目录结构一般是这样的：

```
/home/markhoo/
    sites/
        markhoo.cn/
            myblog_env/
            myblog/

```

一台服务器可能部署多个网站，所有网站代码都放在 `sites/` 目录下。`markhoo.cn/` 这个文件夹以网站的域名命名，便于区分。`myblog_env` 是 python 虚拟环境目录。`myblog` 是 Django 博客项目目录。
创建这个目录结构，注意目录名替换为你自己的域名，以后涉及到 `markhoo.cn` 的地方通常都要替换你自己的域名，后面就不再一一指出了。

> 备注：上面的我的目录结构.可能会有新手看不懂，解释一下,就像下面这样一直创建下去

```
/home/markhoo/sites/markhoo.cn/
```

最后的 `myblog_env` 和 `myblog` 是同级目录位于`markhoo.cn`下，这里自己先不要创建。

> 创建目录的命令为： `mkdir (文件夹名称)`
> 删除目录的命令为： `rmdir (文件夹名称)`

# 安装virtualenv

为了不影响外界环境的清洁，所以我们使用虚拟环境来配置 Django 项目
首先用xshell登录到你的服务器，然后按一下步骤操作

```sh
$ pip3 install virtualenv
```

接下来创建虚拟环境，先进入到 markhoo.cn 目录下，然后运行 ```virtualenv``` 命令创建虚拟环境：

```sh
$ [root@instance-pkb22o0h]# cd /home/markhoo/sites/markhoo.cn
$ [root@instance-pkb22o0h markhoo.cn]# virtualenv -p /usr/bin/python3 –-no-site-packages myblog_env
```

命令说明：

- -p： 指定你要虚拟的Python版本，这里选择了本地的python3
- –-no-site-packages：表示在建立虚拟环境时不将原版本中的第三方库拷贝过来，这样就能获得一个纯净的Python环境。
- myblog_env:表明在该目录下，建立一个叫做myblog_env的虚拟环境，这样的命名方式，让你一眼就能看出这个虚拟环境是为谁建立的。

virtualenv的命令使用方法：

- 创建虚拟环境： $ virtualenv -p /usr/bin/python3.5 –-no-site-packages myblog_env
- 启动虚拟环境： $ source myblog_env/bin/activate　
- 退出虚拟环境： $ deactivate　
- 删除虚拟环境： $ rm –r myblog_env

检查一下虚拟环境是否创建成功，运行 ls 命令列出当前目录下的文件和文件夹，看到 myblog_env 这个文件夹说明虚拟环境创建成功。

```sh
$ [root@instance-pkb22o0h markhoo.cn]# ls
$ myblog_env
```

## venv的使用

由于我只是测试项目，所以使用了Python自带的venv。
先进入到 markhoo.cn 目录下，然后运行 ```python3 -m venv myblog_env``` 创建虚拟环境：

```sh
$ [root@instance-pkb22o0h]# cd /home/markhoo/sites/markhoo.cn
$ [root@instance-pkb22o0h markhoo.cn]# python3 -m venv myblog_env
```

检查一下虚拟环境是否创建成功，运行 ls 命令列出当前目录下的文件和文件夹，看到 myblog_env 这个文件夹说明虚拟环境创建成功。

```sh
$ [root@instance-pkb22o0h markhoo.cn]# ls
$ myblog_env
```

## 在虚拟环境中安装 django 以及依赖包并初始化项目

接着再从代码仓库把项目代码拉取过来，把 ```git clone``` 后的地址换成你自己的 GitHub 仓库地址！

```sh
$ [root@instance-pkb22o0h markhoo.cn]# git clone https://github.com/MarkHoo/myblog.git
```

运行 ls 命令检查一下是否拉取成功：

```sh
$ [root@instance-pkb22o0h markhoo.cn]# ls
$ myblog  myblog_env
```

多了 myblog 文件夹（文件夹名称由你的 GitHub 仓库名决定），说明拉取成功了。

接下来执行命令，启动虚拟环境

```sh
$ [root@instance-pkb22o0h markhoo.cn]# source myblog_env/bin/activate
$ (myblog_env) [root@instance-pkb22o0h markhoo.cn]# 
```

安装项目环境依赖

```sh
$ (myblog_env) [root@instance-pkb22o0h markhoo.cn]# cd myblog
$ (myblog_env) [root@instance-pkb22o0h myblog]# pip3 install -r requirements.txt
```

收集静态文件

运行 python manage.py collectstatic 命令收集静态文件到 static 目录下

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py collectstatic 
```

生成数据库

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py migrate
```

创建超级用户

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py createsuperuser
```

然后就会出现以下内容，第一个是用户名，第二个是邮箱，第三和第四是密码（输入时没有任何显示，实际上已经输入了）。最后显示创建成功！

```sh
$ Username (leave blank to use 'root'): admin
$ Email address: admin@markhoo.cn
$ Password: 
$ Password (again): 
$ Superuser created successfully.
```

> 备注：这里使用的是Django默认生成的sqlite3数据库，我使用了这个，简单写写博客够用了。

- 启动 Django

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py runserver 0.0.0.0:8000
```

如果没有报错，就说明 Django 已经安装成功了，打开浏览器输入你的服务器IP:8000，就可以看到你的网站了

- 退出 Django

按 ```ctrl+c``` 退出 Django 服务


## 安装 Mysql (使用MySQL数据库同学可以借鉴)

安装并启动 mariadb

因为 CentOS 7 之后的版本都不在提供 Mysql 安装源，这里我们使用 mariadb 代替 mysql，依次执行下列命令

```sh
$ yum install mariadb mariadb-server -y
$ yum install MySQL-python -y
$ systemctl start mariadb
```

对 mariadb 进行初始化设置

- 执行下面命令，根据提示操作
- 设置新密码为 test
- 默认密码为空，直接回车即可

```sh
$ mysql_secure_installation
```

使用设置的密码登陆 mariadb
- 登陆 db，这里假设密码被设置为 admin123

```sh
$ mysql -uroot -ptest
```

创建一个数据库

```sh
$ create database myblog;
```

成功后，输入 exit 命令退出 db

```sh
> exit
```

修改配置文件，与 Mysql 数据库相关联
编辑 myblog/settings.py


```python
# Database
# https://docs.djangoproject.com/en/1.11/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'myblog',
        'PASSWORD':'admin123',
        'USER': 'root',
        'HOST':'127.0.0.1',
        'PORT':'3306',
    }
}
```

创建 Django 数据库

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# cd /home/markhoo/sites/markhoo.cn/myblog
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py migrate
```

创建超级用户

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py createsuperuser
```

然后就会出现以下内容，第一个是用户名，第二个是邮箱，第三和第四是密码（输入时没有任何显示，实际上已经输入了）。最后显示创建成功！

```sh
$ Username (leave blank to use 'root'): admin
$ Email address: admin@markhoo.cn
$ Password: 
$ Password (again): 
$ Superuser created successfully.
```

启动 Django

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# python manage.py runserver 0.0.0.0:8000
```

如果没有报错，就说明 Django 已经安装成功了，并且跟 Mysql 的连接正常。打开浏览器输入你的服务器IP:8000，就可以看到你的网站了

退出 Django

按 ```ctrl+c``` 退出 Django 服务


## 使用 Gunicorn

Gunicorn 一般用来管理多个进程，有进程挂了Gunicorn 可以把它拉起来，防止服务器长时间停止服务，还可以动态调整 worker 的数量，请求多的时候增加 worker 的数量，请求少的时候减少。

在虚拟环境下，安装 Gunicorn：

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# pip3 install gunicorn
```

测试Gunicorn是否能启动你的项目服务

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# gunicorn --bind 0.0.0.0:8000 myblog.wsgi:application
```

注意：myblog.wsgi:application这里的myblog对应的是 /home/markhoo/sites/markhoo.cn/myblog/myblog, 根据自己的修改。浏览器输入域名，可以看到访问成功了！

访问ip地址看浏览器是否能正常查看内容（此时没有退出虚拟环境） 
完成测试后，按 ```CTRL-C``` 停止 Gunicorn 运行

退出虚拟环境

```sh
$ deactivate
```

创建一个 Gunicorn Systemd Service 文件，直接运行以下命令

```sh
$ vim /etc/systemd/system/gunicorn.service
```

修改内容如下：

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=root
Group=nginx
WorkingDirectory=/home/markhoo/sites/markhoo.cn/myblog
ExecStart=/home/markhoo/sites/markhoo.cn/myblog_env/bin/gunicorn --workers 3 --bind unix:/home/markhoo/sites/markhoo.cn/myblog/myblog.sock myblog.wsgi:application

[Install]
WantedBy=multi-user.target

```

一定要注意自己的项目路径和虚拟环境路径 
WorkingDirectory与ExecStart 修改为自己的路径

开启Gunicorn服务并开机自启,运行以下命令

```sh
$ systemctl start gunicorn
$ systemctl enable gunicorn
```

## 配置 Nginx

接下是配置 Nginx 来处理用户请求

配置nginx代理通过Gunicorn
依次运行以下命令

```sh
$ (myblog_env) [root@instance-pkb22o0h myblog]# cd /etc/nginx/
$ (myblog_env) [root@instance-pkb22o0h nginx]# ls
$ conf.d     fastcgi.conf          fastcgi_params          koi-utf  mime.types          nginx.conf          scgi_params          uwsgi
$ default.d  fastcgi.conf.default  fastcgi_params.default  koi-win  mime.types.default  nginx.conf.default  scgi_params.default  uwsgi
$ (myblog_env) [root@instance-pkb22o0h nginx]# vi nginx.conf
```

或者直接运行

```sh
$ vim /etc/nginx/nginx.conf
```

打开后默认的内容如下：


```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2 default_server;
#        listen       [::]:443 ssl http2 default_server;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        location / {
#        }
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}
```

找到server内容进行修改，具体内容如下：

```
server {
    listen 80;
    server_name  markhoo.cn 0.0.0.0;

        location = /favicon.ico { access_log off; log_not_found off; }
        location /static/ {
        root /home/markhoo/sites/markhoo.cn/myblog;
        }

        location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://unix:/home/markhoo/sites/markhoo.cn/myblog/myblog.sock;
        }

    }


```

注意修改自己的IP地址或域名，还有文件路径 
server_domain_or_IP 代表你的IP地址或域名

- 这里的域名为 markhoo.cn，其次一定要添加 0.0.0.0 否则无法访问，两个之间用空格隔开。
- 所有URL 带有 /static 的请求均由 Nginx 处理，alias 指明了静态文件的存放目录。
- 其它请求转发给 Django 处理。proxy_pass 后面使用了 unix 套接字，其作用是防止端口冲突，这里就不再详述。

修改所涉及到的vim命令使用方法：使用 'vi (文件名称)' 是打开文件，然后点击键盘 'i' 键切换到输入模式。修改完需要退出vim编辑器，先按键盘 'Esc' 键，然后输入 ':wq' 保存退出。如果不想修改又无法退出可以用 ':q!' 强制退出，这样不会保存你修改的内容。
更多的vi、vim命令的具体使用方法可以自行到搜索引擎搜索学习，这里也不一一讲解了。

修改nginx的权限

```sh
$ usermod -a -G root nginx
$ chmod 710 /home/
$ nginx -t
```

如果没有报错，就行下一步操作

开启nginx服务并开机自启

```sh
$ systemctl start nginx
$ systemctl enable nginx
```

现在，一切配置完成！你可以访问你的域名了！

