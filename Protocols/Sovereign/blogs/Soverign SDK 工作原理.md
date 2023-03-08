  
在我们的[介绍博客](https://mirror.xyz/sovlabs.eth/V-nUycehpu-cwCRaStz7984mYGOKpRGMyC-tTDaQIsk)中，我们勾画了一个未来愿景，构建 zk-rollup 就像启动 dapp 一样容易。这篇博文旨在解释我们将如何实现这一愿景。在我们深入之前，一个小提醒 - 这篇文章将偏技术性但并不完全严谨。如果您想要 Sovereign SDK 的非技术介绍，请参阅[此处](https://www.sovereign.xyz/learn)。如果您想要完全严谨的技术性文档，[您可以在 GitHub 上找到 Sovereign SDK 的规范和实现](https://github.com/Sovereign-Labs/sovereign)，或者[访问 Discord](https://discord.gg/kbykCcPrcA)。有了这些，让我们开始吧！

## 为什么使用 ZK-Rollups？

在本节中，我们将尝试从基本原理出发解释为什么我们认为 zk-rollups 是区块链的“终局”。

### 原子可组合性的代价

我们从以太坊的杀手级特性不是 EVM 本身，而是原子可组合性这一论点开始。任何智能合约都可以与任何其他智能合约交互，并且系统保证调用者不会卡在某个中间状态 —— 即使在调用堆栈的上方出现问题。这极大地简化了编程模型，使以太坊成为对开发者最友好的环境之一。但是原子可组合性是有代价的。为了原子化执行调用，全节点需要实时访问调用堆栈中所有合约的相关状态，这意味着他们必须将所有相关状态存储在本地，并且他们需要能够非常快速地加载它。换句话说，全节点需要购买足够多的SSD来存储整个链的状态。

为了最大化可扩展性，像 Near 这样的系统试图放宽这些要求。Near 不是原子可组合性，而是只允许异步跨合约调用。这种设置允许更多的可扩展性，因为每个合约的状态只能存储在特定子集的全节点上。但这给程序员带来了巨大的代价—— 他们必须推理代码路径的组合。

### 两全其美

我们的愿景是这两个极端的结合。我们相信，没有一个完全原子性的系统可以扩展到满足整个世界的需求。但与此同时，我们认识到原子性在开发简单且可组合的智能合约中发挥的核心作用。因此，我们追求两全其美：在生态系统中，每条优化后的链都提供原子可组合性，通过跨生态系统的快速（但异步）跨链桥连接。

在单个链中，开发人员保留原子可组合性。合约设计可以相对简单，用户可以近乎实时地知道他们的交易结果。但与此同时，没有一组全节点负责存储和更新整个世界的状态。

### 构建 rollups，而不是区块链

我们当然不是第一个按照这些思路思考的人。据我们所知，最早尝试创建集成多链系统的是 Cosmos。与我们的愿景非常相似，Cosmos 是一个自下而上的异构链生态系统，具有快速可靠的跨链桥功能。Cosmos 的不足之处在于创建新链的能力。在一条链变得可用之前，它需要一个“验证者集”来为其操作赋予有意义的经济权重。产生的资金成本，在链有足够的使用量来创造可观的收入之前，很难将验证者吸引到链上。但当然，没有用户愿意在不安全的链上冒险。这导致了先有鸡还是先有蛋的情况，并使得推出新链成为一项艰巨的任务。

此外，Cosmos 链在实施跨链桥方面面临根本性的阻碍。他们的轻客户端桥 (IBC) 维护起来很昂贵，因为区块头必须在链上处理。这会导致稀疏的跨链桥拓扑图，从而带来糟糕的用户体验。更糟糕的是，如果一条链遭受远程攻击或其验证者集执行无效的状态转换，则可能会损失资金。

我们对这些问题的解决方案很简单：与许多其他链共享一个验证者集。换句话说，*不构建区块链；构建 rollups。*

这最后引导我们构建了我们的产品：Sovereign SDK。

## 什么是 Sovereign SDK？

Sovereign SDK 是一个用于构建经过异步证明的主权 zk-rollup 的工具包。它囊括的内容很多，让我们一点一点地看。

首先，*经过异步证明*的交易是什么意思？简单来说，它意味着原始交易数据由排序器实时发布到 L1，然后生成证明。这与像 StarkNet 这样的 rollup 形成对比，在 StarkNet 中，证明是在任何数据发布到链上之前在链下生成的。异步证明的优点是它允许实时确认交易，从而使 rollup 具有与 Optimistic Rollup 类似的响应能力。

其次，rollup 的*主权* 意味着什么？与“智能合约” rollup 相比，这是最容易理解的。在智能合约 rollup 中，L2 状态只有在被 L1 上的智能合约接受时才是最终状态（在轻客户端看来）。因为今天的 L1 链上如此有限的吞吐量，智能合约 rollup 被迫相对不频繁地更新其规链上状态。相对来说，主权 rollups 的轻客户自行负责决定是否应该接受特定区块。这使得 sovereign rollups 可以更快地完成，因为它们不必限制更新频率来适应拥挤的 L1。

## 它是如何工作的？

Sovereign SDK 通过将 zk-rollup 的功能抽象为不同层次的接口来工作。在层次结构的每一层，开发人员都可以自由使用 SDK 提供的预打包实现之一，或者从头开始构建自己的功能。通过将逻辑封装在定义好的接口后面，我们能够提供可插拔的组件，在不牺牲灵活性的情况下提供简单的开发体验。

### 核心 API

在最高的抽象层次上，每个 Sovereign SDK 链都结合了三个不同的元素：

1.  提供 DA 和共识的 L1 区块链
2.  一个状态转换函数（用 Rust 编写），实现链的“业务逻辑”
3.  一个零知识证明系统，可以 (1) 递归和 (2) 运行 Rust 的子集

```typescript
interface DaLayer {
  // Gets all transactions from a particular da layer block that are relevant to the rollup
  // Used only by full-nodes of the rollup
  function get_relevant_txs(header: DaHeader) -> Array<DaTxWithSender>
  // Gets all transactions from a particular da layer block that are relevant to the rollup,
  // along with a merkle proof (or similar) showing that each transaction really was included in the da layer block.
  // Depending on the DA layer, may need to include auxiliary information to show that no 
  // relevant transactions were omitted.
  // Used by provers
  function get_relevant_txs_with_proof(header: DaHeader) -> (Array<DaTxWithSender>, DaMultiProof, CompletenessProof>
  // Verifies that a list of Da layer transactions provided by an untrusted prover is both 
  // complete and correct.
  // Used by the "verifier" circuit in the zkVM
  function verify_relevant_tx_list(txs: Array<DaTxWithSender>, header: DaHeader, witness: DaMultiProof, completenessproof: CompletenessProof)
}

// The interface to a state transition function, inspired by Tendermint's ABCI
interface StateTransitionFunction {
	// Called once at rollup Genesis to set up the chain
  function init_chain(config: Config) 
  // A slot is a DA layer block, and may contain 0 or more rollup blocks
  function begin_slot(slotnumber: u64) 
  // Applies a batch of transactions to the current rollup state, returning a list of the
  // events from each transaction, or slashing the sequencer if the batch was malformed
  function apply_batch(batch: Array<Transaction>, sequencer: Bytes): Array<Array<Event>> | ConsensusSetUpdate
  // Process a zero-knowledge proof, rewarding (or punishing) the prover
  function apply_proof(proof: RollupProof): Array<ConsensusSetUpdate>
  // Commit changes after processing all rollup messages
  function end_slot(): StateRoot
}

interface ZkVM { 
  // Runs some code, creating a proof of correct execution
  function run(f: Function): Proof
  // Verifies a proof, returning its public outputs on success
  function verify(p: Proof): (Result<Array<byte>>)
}
```

### 通用全节点

使用我们刚刚所描述的核心接口，Sovereign SDK 将提供一个通用的全节点实现，能够在任何数据可用性层上运行几乎任何状态转换函数。一般而言，全节点的工作原理是将 rollup 功能封装在我们刚刚描述的核心接口后面，并将 rollup 数据视为可以存储和传输的 opaque byte strings。为了让您了解全节点的运行方式，如下粗略地说明了核心事件循环的流程。

```typescript
// A pseudocode illustration of block execution. Plays fast and loose
// with types for the sake of brevity.
function run_next_da_block(self, prev_proof: Proof, db: Database) {
  // Each proof commits to the previous state as a public output. Use that state 
  // to initialize the state transition processor
  let prev_state = deserialize(prev_proof.verify());
  let current_da_header = db.get_da_header(prev_state.slot_number + 1);
  let processor = stf::new(db, current_da_header, prev_state);
  
  // Fetch the relevant transactions from the DA layer
  let (da_txs, inclusion_proof, completeness_proof) = da::get_relevant_txs_with_proof(current_header);
  da::verify_relevant_tx_list(da_txs, inclusion_proof, completeness_proof)
  
  // Process the relevant DA transactions 
  processor.begin_slot(prev_state.slot_number + 1)
  for da_tx in da_txs { 
    let msg = parse_msg(da_tx);
    if msg.is_batch() { 
      processor.apply_batch(msg)
    } else {
      processor.apply_proof(msg)
    }
  }
  processor.end_slot();
}
```

### 可插拔组件

因为全节点实现是完全通用的，开发人员可以轻松地更换模块来改变他们链的特性。例如，非常关心 calldata 成本的开发者可能会使用[Jupiter](https://github.com/Sovereign-Labs/Jupiter)，我们与 Celestia 区块链的集成。另一方面，主要关心获得流动性的开发者可能会在以太坊或 EigenDA 之上构建（我们也将为此提供一个可插拔模块，但要到原型阶段之后才会开发）。

同样，开发者可以自由选择他们自己的 zkVM——只要它支持 Rust 的 no_std 和一些 shim API。喜欢使用标准编译器工具链和 VM 接口的开发者可能会选择 [Risc0](https://github.com/risc0/risc0)，而那些看重小证明大小的开发者可能会选择另外的 VM，例如[=nil；基金会](https://nil.foundation/)。

### 默认模块

Sovereign SDK 在下一层抽象中，还旨在简化创建状态转换函数的过程。为此，我们将提供一个受 Cosmos SDK 启发的可组合的模块化系统。尽管我们会随着时间的推移继续构建功能，但我们计划至少推出以下模块：存储、跨链桥、（同质化）代币和证明/排序。

在撰写本文时，模块化系统的设计仍处于早期阶段 — 因此您在本节中阅读的所有内容都可能会发生快速变化。但是，我们仍然认为勾勒出我们一直在实现的一些设计是帮助的，让你可以知道即将发生的事情。

**跨链桥模块**

首先是跨链桥。与不同链之间的桥接（众所周知，如果没有强大的信任假设[是不可能的](https://spiral.imperial.ac.uk/bitstream/10044/1/75810/6/2019-1128.pdf)）不同，共享 DA 层上的 rollups 之间的桥接从根本上来说并不困难。在 Sovereign SDK 中，我们计划充分利用这一事实，默认提供低延迟最小化信任的跨链桥。

它是这样工作的：假设您有 50 个 rollups 在共享 DA 层上运行。在标准范式中，每个 rollup 之间需要运行最小化信任的跨链桥，您需要每个 rollup 上运行其他每个 rollup 的链上轻客户端。算一算，需要 50 * 49 = 2450 个链上轻客户端。由于实际执行起来成本高得令人望而却步，因此您会转而使用多跳 (multi-hop) 跨链桥。你会让每条链连接到（比如）其他 3 条链，并且 —— 假设你的网络具有合理的拓扑结构 - 你将能够使用 3 或 4 跳在任何一对链之间传递消息。

借助 zk 的力量，我们可以做得更好。回想一下，任何两个零知识证明（在 VM 模型中）都可以聚合成第三个证明，并且这个新证明的验证成本并不比原始证明高。利用这种能力，我们可以在链下递归地聚合所有 50 个 rollup 证明，然后在每个 rollup 上验证该聚合证明。因此，我们可以进行一次链下证明聚合和 50 次链上证明验证，而不是维护 2450 个轻客户端。换句话说，我们已经将全链接的跨链桥的通信复杂度从 O(n^2) 降低到 O(n)。

**存储模块**

高效的 zk-rollups 的一个关键组成部分是状态管理。在设计经验证的数据存储时，开发者必须兼顾几个需要取舍的问题。一方面，他们想要一些能够可以用电路有效地表示的东西，因为每次对状态的读写都必须在零知识证明中进行验证。另一方面，他们想要在可以再本地执行快速且轻量级的东西，这样它就不会成为全节点和排序器的瓶颈。

在 Sovereign SDK 中，我们计划提供一个默认存储模块来平衡这两个设计目标。为了提高 zkVM 外的效率，我们将使用最初由 Diem 开发的[Jellyfish Merkle Tree](https://developers.diem.com/papers/jellyfish-merkle-tree/2021-01-14.pdf) (JMT) 来存储经验证的状态数据。为了可以快速访问，我们还将维护一个扁平的数据结构来保存原始状态。为了提高电路效率，我们将对哈希函数进行 JMT 通用化，以便开发人员可以轻松插入根据他们选择的 zkVM 定制的哈希。

### 组合在一起：区块生命周期

将区块添加到主权链分三步。首先，排序器将新的交易数据块发布到 L1 链上。一旦 blob 在 L1 上被确认，新逻辑的 rollup 状态也将被确认。

当每个 L1 区块被最终确认时，rollup 的完整节点会扫描它并识别与 rollup 的状态转换函数相关的 blob。然后，他们按顺序将汇总的 STF 应用于这每一个 blob， 计算新的 rollup 状态根。至此，从全节点的角度来看，主观上该区块已经被最终确认。

接下来，证明者节点（在 zkVM 内部运行的全节点）执行与全节点相同的过程 —— 扫描 DA 区块并按顺序处理所有 blob 数据。然而，与全节点不同的是，证明者相互竞争来生成证明并将其发布到链上。第一个创建有效证明的节点将获得 rollup 的 gas费（的一部分）相对应的代币奖励。一旦给定 batch 的证明被发布到链上，对于轻客户端来说，主观上该 batch 就被最终确认了。

最后，中继者可以选择将最新的 rollup 证明提交给另一条链上的跨链桥智能合约 ——  DA 层或另一个 rollup。为了提高效率，中继器可能会在提交更新之前使用证明聚合将许多 rollup 的有效性证明聚合成一个证明。

### 结论

如果您看到这里，那么您现在应该对 Sovereign SDK 有一个大致的理解。如果您有兴趣了解更多信息，[请深入我们的 github](https://github.com/Sovereign-Labs/sovereign)（欢迎贡献！），[联系 Twitter](https://twitter.com/sovereign_labs)，或[加入 Discord](https://discord.gg/kbykCcPrcA)！另外，[我们也在招幕更多团队成员！](https://www.sovereign.xyz/join)