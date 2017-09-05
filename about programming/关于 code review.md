# 关于 code review

以前没有做过 code review ，没有什么思路，下面是整理的内容。

## code review 好处

- 改进代码质量

  可以让代码组织结构更清晰，提高代码的稳定性，有更高的可读性

- 提高代码的可维护性

  在 review 过程中，可以传递知识，可以让不熟悉代码的人知道作者的意图和想法，以后可以轻松维护代码

- 提高团队的整体水平

  在 code review 过程中，可以达到知识共享，团队内成员可以相互学习对方的长处和优点，团队成员对项目的熟悉程度也会比较均衡。

  > 代码不会只有个别人了解、熟悉，Bug谁都能改，新功能谁都能做。
    对公司来说避免了人员的风险，对个人来说比较轻松（谁都能来帮你），可以选自己喜欢的任务做。

- 减少 BUG 数量

  虽说，找 BUG 只是副产品，但是可以迅速减少 BUG 数量

- 改善团队的氛围

  > Review 的过程中会需要非常多的沟通，多沟通能拉近团队成员的距离。并且无论级别高低，大家的代码都是要经过Review的，可以在团队内营造一个平等的氛围。每个成员都可以审查别人的代码，这很容易激发他们的积极性。

## 失败的 code review

> 在功能失调的团队和组织中，对所有参与者来说它可以迅速变成一个令人不快的经历：
- 它成为评审人员来展示技能的平台。他们在别人的代码中指出“错误”，并强加自己没有价值的“意见”。
- 在代码完全就绪前，开发人员会非常抗拒别人审查他们的代码 ——可以说这可能是一件好事，但他没有真正理解评审的意义。
- 开发人员放弃代码所有权，并开始依赖他人查找问题。

内容出自：

[如何做有效的Code Review？我有这些建议](http://blog.jobbole.com/112050/?utm_source=blog.jobbole.com&utm_medium=relatedPosts)

里面有对管理层、个人、评审者、以及开发者的建议。

## 别人的经验

下面的内容来自于：[CODE REVIEW中的几个提示](https://coolshell.cn/articles/1302.html)

1. 经常进行 code review

  > 千万不要等大厦都盖好了再去 Reivew，而是在有了地基，有了框架，有了房顶，有了门窗，有了装修，的各个时候循序渐进地进行 Review，这样会更有效率，也更有帮助。
  - 要 Review 的代码越多，那么要重构、重写的代码就会越多。而越不被程序作者接受的建议也会越多，唾沫口水战也会越多。
  - 程序员代码写得时候越长，程序员就会在代码中加入越来越多的个人的东西。
  - 越接近软件发布的最终期限，代码也就不能改得太多。

  **总结：**code review 贯穿项目始终，要防止 code review 变成口水战

- Code Review不要太正式，而且要短

- 尽可能的让不同的人Reivew你的代码

  > 不同的人有不同的思考方式，有不同的见解，所以，不同的人可以全面的从各个方面评论你的代码，有的从实现的角度，有的从需求的角度，有的从用户使用的角度，有的从算法的角度，有的从性能效率的角度，有的从易读的角度，有的从扩展性的角度……当然，不要太多了，人多嘴杂反而适得其反，基本上来说，不要超过3个人，这是因为，这是一个可以围在一起讨论的最大人员尺寸。

下面的内容来自于：[从零开始 Code Review](http://blog.csdn.net/uxyheaven/article/details/49773619)

- Code Review 一个人看所有人的代码是不可取的
- Code Review 是团队的事情, 方案定了得拿出去溜溜
- Code Review 初期需要有标准. 让小伙伴们知道如何去review
- **tech leader 强压所有人开始 code review, 这是最重要的一步**
- 前期经常回顾, 这次的code review开展的怎样, 有哪些地方可以改善
- 每天不光可以review代码, 也可以安排整场的技术分享

## 前提

- 规范代码，统一代码风格
- 保持积极的正面的态度

  > 无论是代码作者，还是评审者，都需要一种积极向上的正面的态度，作者需要能够虚心接受别人的建议，因为别人的建议是为了让你做得更好；评审者也需要以一种积极的正面的态度向作者提意见，因为那是和你在一个战壕里的战友。记住，你不是一段代码，你是一个人！
- 良好的沟通

## 推行 code review 的困难

- 项目 deadline 已定，时间紧迫
- 团队认识不到其好处
- 团队人员结构搭配不合理
- 自信超强大的程序员

可以在这里：[Code Review 程序员的寄望与哀伤](http://www.cnblogs.com/mindwind/p/5639008.html) 找到解释

## code review 清单

> 在代码审查中，检查清单是一个非常好的工具——它们保证了审查可以在你的团队中始终如一的进行。它们也是一种保证常见问题能够被发现并被解决的便利方式。

代码审查清单包括下面几个大项：
- 常规项
- 安全
- 文档
- 测试

创建清单可以参考：[程序员必备的代码审查（Code Review）清单](http://blog.jobbole.com/83595/)

## 实施方案

大部分团队采用的方式：

Git Flow + Pull Request 模式来做 Code Review

过程：
> 1. 开发者在本地仓库中新建一个专门的分支开发功能。
- 开发者push分支修改到公开的Bitbucket仓库中。
- 开发者通过Bitbucket发起一个Pull Request。
- 团队的其它成员review code，讨论并修改。
- 项目维护者合并功能到官方仓库中并关闭Pull Request。

关于 Pull Request 详细使用可以看这里：
[Git工作流指南：Pull Request工作流](http://blog.jobbole.com/76854/)

这里有 gitlab 的 code review 实施流程： [如何用 Gitlab 做团队内的 Code Review](https://segmentfault.com/a/1190000006062488)

****

引用别人的一句话结尾：

> 即使在最差的环境下，完全没有人关心 Code Review 这件事。一个有追求的程序员依然可以做到一件事，自己给自己 review。

****

## 参考链接
- [我们是怎么做 Code Review 的](http://www.cnblogs.com/wenhx/p/How-We-Code-Review.html)
- [知乎-新手团队对于代码审查（Code Review）您有什么好的建议？](https://www.zhihu.com/question/21701366)
- [Code Review最佳实践](http://blog.csdn.net/u011570979/article/details/45951419)
- [CODE REVIEW中的几个提示](https://coolshell.cn/articles/1302.html)
- [从CODE REVIEW 谈如何做技术](https://coolshell.cn/articles/11432.html/comment-page-1#comments)
- [Code Review 程序员的寄望与哀伤](http://www.cnblogs.com/mindwind/p/5639008.html)
- [如何做有效的Code Review？我有这些建议](http://blog.jobbole.com/112050/?utm_source=blog.jobbole.com&utm_medium=relatedPosts)
- [谈一下我们是如何开展code review的](http://www.cnblogs.com/harlanc/p/6772394.html)
- [如何用 Gitlab 做团队内的 Code Review](https://segmentfault.com/a/1190000006062488)
