---
title: Gitlab安装
date: 2019-12-10 22:34:18
cover: 
categories: 默认分类
tags:
- Gitlab
---

## 1.准备环境

在正式开始安装之前，先更新软件包并打开相关服务的权限。

更新软件包:

```sh
$ yum update -y
```

#### 安装 sshd

安装 sshd：

```sh
$ yum install -y curl policycoreutils-python openssh-server
```

启用并启动 sshd：

```sh
$ systemctl enable sshd

$ systemctl start sshd
```

#### 配置防火墙

打开vim /etc/sysctl.conf 文件，在文件最后添加新的一行并保存：

```
net.ipv4.ip_forward = 1
```

启用并启动防火墙：

```sh
$ systemctl enable firewalld

$ systemctl start firewalld
```

放通 HTTP：

```sh
$ firewall-cmd --permanent --add-service=http
```

重启防火墙：

```sh
$ systemctl reload firewalld
```

在实际使用中，可以使用 ```systemctl status firewalld``` 命令查看防火墙的状态。

#### 安装 postfix

GitLab 需要使用 postfix 来发送邮件。当然，也可以使用 SMTP 服务器，具体步骤请参考[官方教程](https://docs.gitlab.com/omnibus/settings/smtp.html)。
安装：

```sh
$ yum install -y postfix
```

打开 ```vim /etc/postfix/main.cf``` 文件，在第 119 行找到 ```inet_protocols = all```，将 ```all``` 改为 ```ipv4``` 并保存：


```sh
inet_protocols = ipv4
```

启用并启动 postfix：

```
$ systemctl enable postfix 

$ systemctl start postfix
```

#### 配置 swap 交换分区

由于 GitLab 较为消耗资源，我们需要先创建交换分区，以降低物理内存的压力。
在实际生产环境中，如果服务器配置够高，则不必配置交换分区。
新建 2 GB 大小的交换分区：

```sh
$ dd if=/dev/zero of=/root/swapfile bs=1M count=4096
```

格式化为交换分区文件并启用：

```sh
$ mkswap /root/swapfile

$ swapon /root/swapfile
```

添加自启用。打开 ```vim /etc/fstab``` 文件，在文件最后添加新的一行并保存：

```sh
/root/swapfile swap swap defaults 0 0
```


## 2.安装 GitLab

#### 将软件源修改为国内源

由于网络环境的原因，将 repo 源修改为清华大学
。
在 ```/etc/yum.repos.d``` 目录下新建 ```gitlab-ce.repo``` 文件并保存。内容如下：

```sh
$ vim /etc/yum.repos.d/gitlab-ce.repo
```

```sh
[gitlab-ce]
name=Gitlab CE Repository
baseurl=https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el$releasever/
gpgcheck=0
enabled=1
```

#### 安装 GitLab

刚才修改过了 yum 源，因此先重新生成缓存：
（此步骤执行时间较长，一般需要 3\~5 分钟左右，请耐心等待）

```sh
> yum makecache
```

安装 GitLab：
（此步骤执行时间较长，一般需要 3\~5 分钟左右，请耐心等待）

```sh
$ yum install -y gitlab-ce
````

## 3初始化 GitLab

#### 配置 GitLab 的域名（非必需）

打开 ```vim /etc/gitlab/gitlab.rb``` 文件，在第 23 行附近找到 ```external_url 'http://gitlab.example.com'```，将单引号中的内容改为自己的域名（带上协议头，末尾无斜杠），并保存。
例如：

```sh
external_url 'http://gitlab.markhoo.com'
```

记得将域名通过 A 记录解析到你的服务器IP。

#### 初始化 GitLab

特别重要！
使用如下命令初始化 GitLab：
（此步骤执行时间较长，一般需要 5\~10 分钟左右，请耐心等待）

```sh
$ sudo gitlab-ctl reconfigure
```

#### 开始使用吧！

至此，我们已经成功地在 CentOS 7 上搭建了 GitLab。 现在可以访问 GitLab 了。
- 在实际生产中，建议您使用 2 核 4 GB 或更高配置的 CVM。点击这里 可以查看 GitLab 官方推荐的配置和可承载人数对应表。

- 再次提醒您，定期执行 ```yum update -y``` 以保持各软件包的最新状态。
谢谢！





```sh
sudo docker run --detach \
    --hostname localhost \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```



sudo docker run --detach \
    --hostname localhost \
    --publish 1443:443 --publish 18080:8080 --publish 122:22 \
    --name gitlab \
    --restart always \
    --volume /home/gitlab/config:/etc/gitlab \
    --volume /home/gitlab/logs:/var/log/gitlab \
    --volume /home/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest




docker run --detach \
    --hostname 192.168.1.133 \
    --publish 7001:443 --publish 7002:80 --publish 7003:22 \
    --name gitlab --restart always \
    --volume /home/gitlab/config:/etc/gitlab \
    --volume /home/gitlab/logs:/var/log/gitlab \
    --volume /home/gitlab/data:/var/opt/gitlab 8e28c88b6a21

