## 概览
**UniRep** ( **Uni**versal **Rep**utation) 是一个隐私并且不可否认的**信誉系统**。用户可以：

1.  从证明者那里获得正面和负面的声誉。
2.  证明他们至少有一定的声望，但不透露具体数字。
3.  用户不能拒绝接受证明者的声誉。

**UniRep**的目标是成为一个基础层，任何人都可以在其上轻松构建*自定义*但*可互操作*的信誉系统。例如，用户可以跨不同的社交媒体平台、消费应用程序或金融应用程序创建组合的零知识声誉证明，以便向他人提供关于他们自己的全面的、隐私的和可信赖的信息。

[在此处](https://mirror.xyz/privacy-scaling-explorations.eth/FCVVfy-TQ6R7_wavKj1lCr5dd1zqRvwjnDOYRM5NtsE)阅读最新的 UniRep 博客文章。

UniRep 最初由 BarryWhiteHat 在[这篇 ethresear.ch 帖子中提出](https://ethresear.ch/t/anonymous-reputation-risking-and-burning/3926)

## v2 更新

该协议的 v2 版本将用户证明的复杂度降低到常数时间。用户可以获得无限的证明，同时保持证明时间不变。

v2 还更改了树结构，完全删除了用户状态树。全局状态树和epoch树被每个证明者的状态树和epoch树所取代。因此，每个证明者都可以设置自己的epoch长度。用户还为每个证明者执行一次用户状态转换，而不是 1 个全局转换。

阅读[此处](https://github.com/unirep/unirep/issues/134)的说明。