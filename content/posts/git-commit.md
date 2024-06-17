---
title: 如何编写更好的 Git 提交消息——分步指南
date: 2019-09-18 02:01:58
cover: 
categories: 默认分类
tags:
- Git
---

#### *待完善*

如何编写更好的 Git 提交消息——分步指南



编写更好的提交消息的 5 个步骤
----------------

让我们总结一下建议的准则：

1.  大写和标点符号：第一个单词大写，不要以标点符号结尾。如果使用常规提交，请记住全部使用小写。
2.  语气：在主题行中使用祈使语气。示例 – `Add fix for dark mode toggle state`。命令式语气给出了您发出命令或请求的语气。
3.  提交类型：指定提交类型。建议使用一组一致的词来描述您的更改，这可能会更有益。示例：修正、更新、重构、凹凸等。有关其他信息，请参阅下面的常规提交部分。
4.  长度：理想情况下，第一行不应超过 50 个字符，正文应限制为 72 个字符。
5.  内容：直截了当，尽量消除这些句子中的填充词和短语（例如：虽然，也许，我认为，有点）。像记者一样思考。

### 如何找到你内心的记者

我从没想过我的新闻学辅修课程会有益于我未来的软件工程师职业生涯，但我们来了！  
  
记者和作家会问自己问题，以确保他们的文章详细、直截了当，并回答了读者的所有问题。

在写一篇文章时，他们希望回答_谁_、_什么_、_在哪里_、_何时_、_为什么_和_如何。_ 出于提交目的，最重要的是回答提交消息的内容和原因。

要提出深思熟虑的提交，请考虑以下事项：

*   为什么我做了这些改变？
*   我的更改有什么影响？
*   为什么需要改变？
*   参考有哪些变化？

假设读者不理解提交所针对的内容。他们可能无法访问解决更改详细背景的故事。

不要期望代码是不言自明的。这与上面的观点类似。

如果您要更新 CSS 样式之类的东西，因为它是可视的，这对程序员来说似乎很明显。您可能对当时为什么需要这些更改非常了解，但您不太可能回想起后来为什么要进行数百个拉取请求。

明确_说明_进行更改的_原因_，并注意它是否对功能至关重要。  
  
请参阅以下差异：

1.  `git commit -m 'Add margin'`
2.  `git commit -m 'Add margin to nav items to prevent them from overlapping the logo'`

很明显，哪些对未来的读者更有用。  
  
假设您正在撰写具有新闻价值的重要文章。给出标题，总结发生的事情和重要的事情。然后，以有组织的方式提供正文中的更多细节。  
  
在电影制作中，与口头解释正在发生的事情相比，使用视觉作为交流媒介经常引用“展示，不要告诉”。

在我们的例子中，“**告诉**，不要 \[只是\] 显示”——尽管我们有一些视觉效果可供我们使用，例如浏览器，但大部分细节来自阅读物理代码。  
  
如果您是 VSCode 用户，请下载[Git Blame](https://marketplace.visualstudio.com/items?itemName=waderyan.gitblame)扩展。这是有用的提交消息何时对未来的开发人员有帮助的一个主要例子。

此插件将列出进行更改的人员、更改日期以及内联注释的提交消息。

想象一下，这在排除错误或回溯所做的更改时会有多大用处。查看 Git 历史信息的其他荣誉提及是[Git History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)和[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)。

![](https://www.freecodecamp.org/news/content/images/2022/01/Screen-Shot-2022-01-03-at-10.45.49-AM.png)

常规提交
----

既然我们已经介绍了良好提交消息的基本提交结构，我想介绍常规提交以帮助提供有关创建可靠提交消息的一些细节。  
  
在 D2iQ，我们使用常规提交，这是工程团队的一个很好的实践。常规提交是一种格式约定，它提供了一组规则来制定一致的提交消息结构，如下所示：

    <type>[optional scope]: <description>
    
    [optional body]
    
    [optional footer(s)]

提交类型可以包括以下内容：

*   `feat` \- 引入了一个新功能的变化
*   `fix` – 发生了错误修复
*   `chore` – 与修复或功能无关且不修改 src 或测试文件的更改（例如更新依赖项）
*   `refactor` – 重构的代码既不修复错误也不添加功能
*   `docs` – 更新文档，例如自述文件或其他降价文件
*   `style` – 不影响代码含义的更改，可能与代码格式有关，例如空格、缺少分号等。
*   `test` – 包括新的或纠正以前的测试
*   `perf` – 性能改进
*   `ci` – 持续集成相关
*   `build` – 影响构建系统或外部依赖项的更改
*   `revert` – 恢复之前的提交

提交类型主题行应全部小写，并有字符限制以鼓励简洁的描述。

应使用可选的提交正文来提供无法符合主题行描述的字符限制的更多详细信息。

这也是一个很好的位置，可以`BREAKING CHANGE: <description>`用来记录提交中发生重大更改的原因。

页脚也是可选的。我们使用页脚链接JIRA故事，将这些变化，例如关闭：`Closes D2IQ-<JIRA #>`。

#### 完整的常规提交示例

    fix: fix foo to enable bar
    
    This fixes the broken behavior of the component by doing xyz. 
    
    BREAKING CHANGE
    Before this fix foo wasn't enabled at all, behavior changes from <old> to <new>
    
    Closes D2IQ-12345

为了确保这些提交约定在开发人员之间保持一致，可以在推送更改之前配置提交消息 linting。[Commitizen](https://commitizen-tools.github.io/commitizen/)是执行标准、同步语义版本控制以及其他有用功能的绝佳工具。

为了帮助采用这些约定，在您的项目中的贡献或 README 降价文件中包含提交指南是有帮助的。

常规提交特别适用于语义版本控制（在[SemVer.org 上](https://semver.org/)了解更多[信息](https://semver.org/)），其中提交类型可以更新适当的版本以进行发布。您还[可以在此处阅读有关常规提交的更多信息](https://www.conventionalcommits.org/en/v1.0.0/)。

提交消息比较
------

查看以下消息并查看他们在每个类别中核对了多少建议的指南。

#### 好的

*   `feat: improve performance with lazy load implementation for images`
*   `chore: update npm dependency to latest version`
*   `Fix bug preventing users from submitting the subscribe form`
*   `Update incorrect client phone number within footer body per client request`

#### 坏的

*   `fixed bug on landing page`
*   `Changed style`
*   `oops`
*   `I think I fixed it this time?`
*   空提交消息




[Git 手册](https://guides.github.com/introduction/git-handbook/)


