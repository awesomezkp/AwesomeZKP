Sovereign 是一个开放互联的 [rollup](https://www.sovereign.xyz/learn) 生态。我们的目标是让所有开发人员能够部署**在任何区块链上都可以运行的**，**可互操作且可扩容的**的 rollup。这就是我们构建 Sovereign SDK 的原因，这是用于创建安全且可互操作的主权 zk-rollup 的最简单框架。

## 当前的问题：区块链应用程序无法扩容

### 单体 L1 无法扩展

在探索扩展区块链应用程序的过程中，出现了三种主要范式：特定应用程序的Layer 1、optimistic rollups 和 zk-rollups。特定应用程序的 Layer 1最容易设计和实现——但它们都有一个明显的缺点。每个 Layer 1 都必须招募自己的验证者，并说服他们投入足够的资金来保护链免受攻击。因此需要大量的资金成本，这种方法只适用于少数资金充足的应用程序。

### Optimistic Rollups 打破可组合性

Rollups 试图通过允许开发人员使用新的逻辑扩容现有的区块链，而不是启动新的链来解决这个问题。这大大降低了应用链开发的准入门槛。

但 optimistic rollups（[如今主导市场的](https://l2beat.com/scaling/tvl)rollups ）并不是最好的解决方案。为了最大程度地减少共享验证者集的负担，optimistic rollups 依靠欺诈证明来防止不当行为。在攻击期间，这些欺诈证明会被检验 —— 因此 optimistic rollups 有很长的“最终确认延迟”，在此期间交易可能会因欺诈证明的出现而回滚。这使得从 optimistic rollups 跨链提现变得缓慢且代价高昂。

因此，optimistic rollups 的开发者必须做出艰难的选择：他们是否构建特定用途的 rollup 并期望用户在链之间频繁桥接？或者他们是否构建通用 rollup，试图在一个地方满足用户的所有需求？两种选择都不好。Optimistic rollups 链之间的桥接太慢、太昂贵，而且风险太大，无法满足日常操作。但是通用的 optimistic rollups 也存在单体 L1 的许多缺点：它们不仅存在自身的可扩展性问题，而且还无法支持应用链的全功能多样性。

### Zk-Rollups 是未来

出于所有这些原因，我们预计 zk-rollups 将成为主流的扩容范式。它们拥有 optimistic rollups 的优点，但没有长时间的最终确认延迟。用户无需等待数天查看是否出现欺诈证明，只需要创建有效性证明，用户就可以确信他们的交易被最终确认 —— 这个过程可能只需要几秒钟。

但是，如果 zk-rollups 是更好的扩容解决方案，为什么不是每个人都在使用它们呢？因为到目前为止，实践中构建 zk-rollups 是一项艰巨的任务——需要专业的密码学工程师多年的努力。

## 进入 Sovereign：Rollups 的互联网

### 什么是Sovereign SDK？

[Sovereign SDK](https://github.com/Sovereign-Labs/sovereign-sdk)旨在为 zk-rollups 实现 Cosmos SDK 在Layer 1 区块链上所做的事情。

它将提供进入区块链的所有通用模块 —— p2p 网络、数据库和 RPC 节点—— 让开发者专注于他们链的业务逻辑。此外，它将提供一组在零知识环境中高效运行的默认模块：代币实现，经验证的数据存储，以及跨链桥模块。开发者能够将这些原语组合到应用链中，或者通过实现一组标准 API，完全从头开始构建自己的状态转换函数。

更好的是，Sovereign SDK 将成为第一个从零知识细节中抽象出来的 rollup 框架。开发者无需成为密码学专家，就可以使用它的强大功能。相反，开发人员将能够使用常用的 Rust（或使用 C++）编写他们的应用程序，[SDK](https://github.com/Sovereign-Labs/sovereign-sdk/blob/research/specs/interfaces/zkvm.md)会自动将其在[高效的](https://github.com/nilfoundation/zkllvm) [zk 虚拟机](https://github.com/risc0/risc0)中编译。

![基于证明聚合的桥接](https://mirror.xyz/_next/image?url=https%3A%2F%2Fimages.mirror-media.xyz%2Fpublication-images%2F4XyEcbUdHCJR5xMhbszeI.jpg&w=3840&q=90)


### Sovereign SDK 将提供哪些属性？

1. Sovereign SDK 的 rollups 将提供 **无缝的互操作性**。我们使用一种基于聚合证明的新跨链桥技术，允许共享 L1 上的 Sovereign SDK rollups 在无需受信任的第三方的情况下来回桥接。由于零知识证明可以聚合，rollups 将能够以最小的成本维护尽可能多的跨链桥。链下中继器可以将所有 rollup 对的证明聚合成一个证明，并且可以在链上验证这个证明。而且由于状态转换被证明是有效的，因此无需向流动性提供者支付费用或等待一周的时间来完成交易。**实现无缺陷的即刻桥接**。
2. Sovereign SDK 的 rollups 将是**安全扩容的。** 通过 zk-rollups 中定制化构建每个组件，Sovereign SDK 将默认提供出色的性能。由于证明并行化的进步，我们的证明将可以以秒为单位来衡量延迟。借助零知识证明，链将能够在不牺牲终端用户可验证性的情况下进行扩容。
3. Sovereign SDK Rollups 可以**在任何区块链上运行**。***Sovereign SDK Rollups将验证证明委托给终端用户，而不是底层的 L1***（这一点和Mina设计相似）。这就是使它们成为 “sovereign”  rollup 而不是智能合约 rollup 的原因。由于数据可用性层不需要验证证明的能力，因此 Sovereign SDK rollups 可以自动移植到任何 L1。**一次编写，随处运行。**
    

### 我们现在在什么阶段？

我们目前正在努力开发 Sovereign SDK。我们当前的路线图分为三个阶段。

**第一阶段：研究（进行中）**

Sovereign SDK 目前正在研发中。我们正在设计默认存储模块、加密经济学和核心 API（如果您有兴趣解决这些问题，请查看我们的[工作看板](https://sovereign.xyz/join)！）。我们也在研究一个原型。尽管 SDK 将支持广泛的数据可用性层和证明系统，但我们当前的原型集成了 Celestia 的数据可用性和Risc0的证明。我们预计这一阶段将在 2023 年第二季度左右完成。

**第二阶段：初步实现**

Sovereign SDK 的初始实现将与研究同时进行。在此期间，我们将实现p2p网络、RPC 节点、核心 API 以及默认存储和排序模块。

**第三阶段：优化**

一旦 SDK 功能完善，我们将需要大量时间进行代码清理、测试、模糊测试和审计。在此期间，我们还将开始开发 SDK 的第一个用例（后续将公布更多相关的详细信息！）。在优化初步实现后大约六个月，我们希望看到主网链使用 SDK。

## 让扩容变得简单

在 Sovereign Labs，我们希望看到区块链支持可以支持数十亿用户。而这一切发生时，没有密码学博士学位的开发者也可以使用零知识技术，并且不是专业协议工程师的开发者也可以构建 rollup。

但是，如果以牺牲终端用户的可验证性为代价，无限扩容就毫无价值。如果构建、运行或查看区块链的状态需要某个中心化实体的合作，那么将是我们的我们。这就是为什么 Sovereign SDK 将始终完全免费和开源的原因 - 以及为什么我们致力于设计堆栈的每个组件以实现最大化的弹性。但不要仅仅信以为然，我们正在[开放](https://github.com/Sovereign-Labs/sovereign-sdk/)构建 - 亲自来看看吧。

扩容理应是容易的。有了 Sovereign SDK，它最终会成为现实。

### 资源

-   [加入我们的团队](https://www.sovereign.xyz/join)
-   [在推特上关注我们](https://twitter.com/sovereign_labs)
-   [加入我们的 Discord](https://discord.gg/kbykCcPrcA)
-   [查看我们的 Github](https://github.com/Sovereign-Labs/sovereign-sdk)
    

### 致谢

我们非常感谢我们的支持者，尤其是 Tim Beiko、James Prestwich、Mustafa Al-Bassam、Chris Ahn 和 Ekram Ahmed，感谢他们审阅了这篇文章的草稿，以及他们在我们的旅程中给予的指导。