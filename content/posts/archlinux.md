---
title: ArchLinux之EndeavourOS
date: 2022-08-05 22:35:01
cover: https://s2.loli.net/2022/08/12/gD28qrO7XApi4Ut.jpg
categories: Linux
tags:
- ArchLinux
- EndeavourOS
- Linux
---

> ArchLinux是桌面版开发里最好的发行版之一，基于ArchLinux的EndeavourOS你值得拥有，至少值得一试

<!--more-->

## EndeavourOS

https://endeavouros.com
![endeavouros-preview.jpg](https://s2.loli.net/2022/08/12/gD28qrO7XApi4Ut.jpg)

## 为什么选择EndeavourOS

EndeavourOS 相比另一个衍生版Manjaro来说更干净，没有自带太多乱七八糟的东西，而且对我的电脑更友好。我的笔记本是ThinkPad的T系列，安装Manjaro后觉得不是很流畅，反应有些慢，自带很多软件我也不喜欢。

最早使用Linux的是Redhat系，但除了服务器上用的多，桌面版用的比较少；后来用的最多的就是Debian系的Debian，使用xfce桌面，但是软件源老旧，应该是追求稳定，而且有一个比较新的软件源直接没有，使用起来麻烦；Arch系最早滚来滚去嫌麻烦，只是体验了一下，现在正式转到Arch，嗯~ 真香！

## EndeavourOS安装

有友好的向导，安装简单。可以在一开始就更换到国内源，然后在线安装，强烈推荐使用Plasma桌面。

## 后记

在安装Sogou输入法的时候，卸载了 `ibus` ，用的 `pacman -Rc` 命令，而且平常会细心查看命令行提示的我，这次看都没看直接在出现 `（yes/no?）` 的时候快速输入 `y` 并回车，导致卸载了 `plasma` 以及其所依赖的 `libibus` 。 `plasma` 依赖输入法所依赖的 `libibus` 真的是非常奇葩！

重启系统后,输入密码登录到桌面的后，直接是黑屏，但是显示鼠标，可以用 `Alt + F2` 搜索快捷键，而且用这个打开其他任何软件都可以正常使用。最后通过 `Ctrl + Alt + F2` 切换到无界面模式，执行命令 `pacman -S plasma-meta` 重新安装 `plasma` 后恢复正常。

不管是安装和卸载，最好都仔细确认一下给出的提示。卸载应该使用 `pacman -Rs` ，而不是 `pacman -Rc` 或者 `pacman -Rcn` ，这样会卸载其依赖的所有软件包。

就在写文章的时候发现另一个衍生版，看着也不错：
官网： https://archcraft.io
![desktop.png](https://s2.loli.net/2022/08/12/HAebBcd8fkhmuD7.png)

