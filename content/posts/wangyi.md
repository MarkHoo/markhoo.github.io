---
title: 两种网易云音乐播放器调用代码 网站可直接调用
date: 2017-11-18 11:59:59
cover: 
categories: 默认分类
tags:
- 网易云音乐
- 音乐播放器
---

> 两种网易云音乐播放器调用代码

<!--more-->

### 第一种：

#### 效果：

<iframe src="//music.163.com/outchain/player?type=2&amp;id=4341314&amp;auto=1&amp;height=66" width="100%" height="86" frameborder="no" marginwidth="0" marginheight="0">&lt;span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start"&gt;&lt;/span&gt;</iframe>

#### 代码：

```
<iframe src="//music.163.com/outchain/player?type=2&amp;id=4341314&amp;auto=1&amp;height=66" width="100%" height="86" frameborder="no" marginwidth="0" marginheight="0">&lt;span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start"&gt;&lt;/span&gt;</iframe>
```

#### 参数说明：

播放器可修改参数：

width=100% #自适应宽度
height=86 #根据自己喜好修改
id=4341314 #为歌曲的ID http://music.163.com/#/song?id=4341314
auto=0 #0为不自动播放，1为自动播放

### 第二种：

#### 效果：

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=100% height=450 src="//music.163.com/outchain/player?type=0&id=967047669&auto=0&height=430"></iframe>

#### 代码：

```
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=100% height=450 src="//music.163.com/outchain/player?type=0&id=967047669&auto=0&height=430"></iframe>
```
