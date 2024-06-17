---
title: NeoVim使用PackerSync失败问题解决（初稿待完善）
date: 2022-11-06 20:12:23
cover: 
categories: 默认分类
tags:
- Vim
- NeoVim
- Lua
---

> 使用PackerSync安装失败的解决方法

<!--more-->

## 问题

在NeoVim配置好插件后需要运行`PackerSync`进行配置的更新，但是由于国内访问GitHub不稳定，所以非常容易导致下载失败。下面推荐几个办法。

## 方法

#### 使用代理（梯子）

最简单粗暴的办法就是使用代理来提高对GitHub的访问稳定性以及速度，简单快速实用，但是梯子不是每个人都有。
对于梯子不便透露，自己找。

#### 使用镜像

虽然有人做过插件的一些镜像，但是都已经失效，当然也可以自己来制作镜像，但是意义不大，谁没事整天配置nvim玩，再说了这是用来干活的，还有镜像也就用的时候起点作用，要是给别人用，那服务器也是得花钱，也没人愿意用爱发电，当然也是那些镜像失效的原因。

使用镜像操作简单，修改配置  packe  里面的  xxx 为 `https://xxxx.com:` 就可以了。 `xxx.com` 就是镜像地址。

#### 使用ssh方式

首先要更改配置里面的访问方式，pack.nvim 里面的 修改为 `git@github.com:`  `[xxx/xxx]` 修改为 `[git@github.com:xxx/xxx.git]`
或者修改为 `git@gitee.com:`  ，毕竟是在国内，肯定快，当然还有把  `[xxx/xxx]`  修改为  `[git@gitee.com:xxx/xxx.git]`

完成上面的配置后在命令行运行 `nvim +PackerSync` 或者进入nvim后运行 `:PackerSync` ，就可以看到nvim能进行正常安装了。

注意：如果使用Gitee的话，需要先把用到的插件导入到Gitee，而且是公开的。
配置ssh到GitHub或者Gitee的步骤：生成key，在GitHub或者Gitee的配置里面添加key。

```
git config --global user.name "name"
git config --global user.email "email"
git config --global --list

ssh-keygen -t rsa -C "email"
```

以上就是如何生成key的步骤，把真实的 `name`和`email`替换进去就行了，最后生成的时候有一些选项，比如设置密码什么的，这不是必需的，一路回车就行，然后就创建好了。

#### 总结

根据自己的具体情况来做选择，有梯子就直接上梯子，没梯子想折腾镜像就折腾镜像，以上都没有或者懒得搞，那就ssh，这个除了改的时候麻烦点，其他都好，把改好的配置文件最好在GitHub或者Gitee上保留一份，用的时候clone下来就行了。

