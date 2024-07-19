---
title: 更换pip源到国内镜像加速python pypi包的安装
date: 2018-07-28 10:55:37
cover: 
categories: Python
tags:
- Python
- pip
- Django
---

> 安装Django的时候总是安装到一半就报错，而且速度很慢，问题主要是国外的源连接不稳定会中断所致。

<!--more-->

昨晚安装Django的时候总是安装到一半就报错，而且速度很慢，问题主要是国外的源连接不稳定会中断所致。后来就想到了切换到国内源。想到可能还会有同学碰到类似的问题，特此记录分享一下

#### pip国内的一些镜像

- 公网 http://e.pypi.python.org/simple
- 教育网 http://pypi.tuna.tsinghua.edu.cn/simple
- 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 
- 豆瓣 http://pypi.doubanio.com/simple/ 
- 阿里云 http://mirrors.aliyun.com/pypi/simple/ 
- 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
- 华中理工大学 http://pypi.hustunique.com/simple
- 山东理工大学 http://pypi.sdutlinux.org/simple  

## 修改源方法：

### 临时使用：

可以在使用 pip 的时候在后面加上 `-i` 参数，指定 pip 源

```sh
$ pip install scrapy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

指定需要安装的库 requirements.txt

```sh
$ pip install -r "/home/admin/$APP_NAME/conf/requirements.txt" -i  "http://mirrors.aliyun.com/pypi/simple" --trusted-host "mirrors.aliyun.com" && \ #通过阿里云镜像安装三方库
```

### 永久修改： 

#### Linux: 

修改 \~/.pip/pip.conf (没有就创建一个)， 内容如下：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

#### Windows: 

直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

参考：https://pip.pypa.io/en/latest/configuration/