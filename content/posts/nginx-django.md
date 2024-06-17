---
title: nginx django 的配置步骤
date: 2017-12-25 10:22:33
cover: 
categories: Django
tags:
- Python
- Django
---

> nginx django 的配置步骤

<!--more-->

#### 升级并安装 pip

```sh
$ sudo apt-get update
$ sudo apt-get install python-pip
```

#### 安装虚拟环境

```sh
$ sudo pip install virtualenv virtualenvwrapper
```

加入到环境变量中
当前用户目录下的 Env为虚拟环境存放的目录

```sh
$ echo "export WORKON_HOME=~/Env" >> ~/.bashrc
$ echo "source /usr/local/bin/virtualenvwrapper.sh" >> \~/.bashrc
```

#### 激活配置

```
$ source \~/.bashrc
```

#### 创建虚拟环境

环境名为:`firstsite`

在用户当前目录下创建 Django工程

```
$ cd ~
$ django-admin.py startproject firstsite
```

#### django 的初始化

```
$ cd \~/firstsite
$ ./manage.py migrate
$ ./manage.py createsuperuser
```

#### Django静态文件配置

```sh
$ nano firstsite/settings.py
```

```
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
```

```sh
$ ./manage.py collectstatic
```

#### 安装 uWSGI

```
$ sudo apt-get install python-dev
$ sudo pip install uwsgi
```

#### 测试是否能够正常访问 django 项目

- `http` 指定进程端口`8080`
- `home`指定虚拟环境目录
- `chdir`指定 项目工程所在目录
- `uwsgi --http :8080 --home /home/pi/Env/firstsite --chdir /home/pi/firstsite -w firstsite.wsgi`

#### 创建 uwsgi 的配置文件

```
$ sudo mkdir -p /etc/uwsgi/sites

$ cd /etc/uwsgi/sites
```
#### uwsgi 的配置相关内容(重点)

```
[uwsgi]
project = firstsite
base = /home/pi

chdir = %(base)/%(project)
home = %(base)/Env/%(project)
module = %(project).wsgi:application

master = true
processes = 5

socket = %(base)/%(project)/%(project).sock
chmod-socket = 666
vacuum = true
```

- 启动守护进程

```sh
$ vim /etc/systemd/system/uwsgi.service
```

```
[Unit]
Description=uWSGI Emperor service
After=syslog.target

[Service]
ExecStart=/usr/local/bin/uwsgi --emperor /etc/uwsgi/sites
Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```

- 重载守护进程的配置信息

```sh
$ sudo systemctl daemon-reload
```

- 保证系统重启后uwsgi守护进程自动重启

```sh
$ sudo systemctl enable uwsgi
```

- 启动uwsgi进程

```sh
$ sudo systemctl start uwsgi
```

- nginx 的安装

```sh
$ sudo apt-get install nginx
```
- nginx的配置

```sh
$ sudo nano /etc/nginx/sites-available/firstsite
```

```
server {
listen 80;
server_name firstsite.com www.firstsite.com;

location = /favicon.ico { access_log off; log_not_found off; }
location /static/ {
root /home/pi/firstsite;
}

location / {
include uwsgi_params;
uwsgi_pass unix:/home/pi/firstsite/firstsite.sock;
}
}
```

- nginx配置生效

```sh
$ sudo ln -s /etc/nginx/sites-available/firstsite /etc/nginx/sites-enabled
```

- 检查配置

```sh
$ sudo service nginx configtest
```

- 重启nginx

```sh
$ sudo service nginx restart
```

非 debug 模式

- 非 debug 模式需要重启uwsgi 服务

```sh
$ sudo systemctl restart uwsgi
```
