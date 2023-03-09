## Sovereign: zkRollups as a service

>Sovereign 是一个开放互联的 [rollup](https://www.sovereign.xyz/learn) 生态。目标是让所有开发人员能够部署**在任何区块链上都可以运行的**，**可互操作且可扩容的** rollup。Sovereign SDK 是用于创建的主权 zk-rollup 的最简单框架。

在过去 L1 公链竞争时，出现了 Blockchain-as-a-service (BaaS)。Cosmos 抽象出了 Tendermint，Cosmos SDK 和 IBC，Polkadot抽象出了 Substrate，这些都是为了方便开发者专注于链上逻辑，而无需太过关注链底层的设计。
现如今，L2 竞争日趋白热化阶段，也涌现出了 Rollup-as-a-service服务。Optimistic Rollup赛道，Optimism就率先推出了Op Stack，方案也获得了Coinbase的采纳。其他也在做这一赛道的包括 Altlayer，Eclipse 等。而zkRollup赛道，目前各大 zkEVM 仍然在积极推进主网上线，每个都在兼容性和性能方面做了自己的取舍，并且因为 zkRollup 本身构建难度要远高于 Optimistic Rollup，因此目前每个项目方的zkRollup都有所不同。而 Sovereign 抽象出了zkRollup 的结构，并提出了 zkRollup 模块化的概念，并将推出对应的 SDK。

## 模块化设计

从披露的信息看，Sovereign 将 zkRollup 拆分成多个模块：存储，跨链桥，证明系统，代币，排序器等。
存储粗略来看可以对应模块化区块链中的数据可用性（DA）层，Sovereign 的初始版本将提供 Celestia 作为 DA，通过 Sovereign SDK 构建的主权 Rollup 将共享数据可用层。对于状态存储收费，目前Sovereign的设计是拆分计算和存储两块，计算价格根据需求快速调整，存储价格根据长期趋势调整，并存在地板价做支撑。
不同主权 Rollup 之间的通信可以依赖于 Sovereign 所提供的跨链桥模块。Sovereign 的跨链桥采用结合 Mutil-Hop 和聚合证明的方案。Multi-Hop 通过将每个 Rollup 连接至若干个 Rollups，使得多次 Hop 可以将 Rollup 连接至所有的 Rollups。为了解决多次 Hop 带来的证明问题，Sovereign 使用聚合证明，通过链下聚合多个证明，链上验证一次证明，进一步压缩了跨链成本。
Sovereign 是第一个从零知识细节中抽象出来的 Rollup 框架。开发者无需成为密码学专家，就可以使用零知识证明的强大功能。初始版本中将提供 Risc0 作为默认的证明系统。开发者仅需关注 Rollup 本身的状态转换，Sovereign 将解决余下的证明生成和验证。
代币模块和排序器目前官方没有过多介绍，预计同 BaaS 相似，将提供默认原生代币和节点的实现。

## Sovereign SDK

对比目前最成熟的 Cosmos SDK，Sovereign SDK 无需验证人集合，因此也就没有 Cosmos SDK 中的 Tendermint 共识模块，取而代之的是 Layer 1 DA层的API接口。而 Sovereign 因为增加了零知识证明来验证可信问题，因此会增加证明系统的核心API。相同的部分在于核心API都会提供状态转换函数，实现链的“业务逻辑”。
核心 API 包含三个部分
-   Layer 1 DA 层，负责确保 rollups 的数据可用性，对所有共享 DA 层 rollups 的 batch 进行整体排序， 并提供抗审查的后盾。
    ```tsx
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
    ```
-   状态转换函数，负责将交易按出现在 DA 层上的顺序进行处理。
    ```tsx
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
    ```
-   证明系统，没有定义任何关于证明大小，验证时长，延迟的规范。SDK 支持任何可靠的证明系统。
    ```tsx
    interface ZkVM { 
      // Runs some code, creating a proof of correct execution
      function run(f: Function): Proof
      // Verifies a proof, returning its public outputs on success
      function verify(p: Proof): (Result<Array<byte>>)
    }
    ```
具体 Sovereign SDK 可参考 https://github.com/Sovereign-Labs/sovereign/tree/research/sdk


## 主权 zkRollups

主权这一概念最早也是在 Cosmos 的讨论中出现，主权强调了自主不依赖的特性。对比之前的主权区块链，Sovereign 的主权 Rollups 中，主权主要是由上面的模块化设计和 Soveregin SDK 所带来。现在 ZK-Rollups 中的zkEVM赛道选手，大多数与以太坊深度绑定。但因为缺少去中心化排序器，安全性假设基本直接继承以太坊。Sovereign 目前设计的方案里，ZK-Rollups 可以灵活在多个 L1 DA 层间迁移，每个 zkRollup 可以自主选择其应用场景适合的 DA 层。除了摆脱对特定 DA 层的依赖，Sovereign 所提供的节点实现中，完全可以建立 zkRollup 自己的网络。但是 Sovereign 又同 Cosmos 一样，建立了一套可以将这些 zkRollups 连接在一起的规范。


## Rollups 网络

回过头看 Layer 2 的竞争，无论是 zkRollups 还是 Optimistic Rollups，也无论是通用型 Rollups，特定应用程序 Rollups。我们没有办法确定谁会是最终的赢家，但是可以确定的是未来会出现一个 Rollups 的网络，就如同现在EVM范式占据了 Layer 1 网络中大壁江山的格局。毫无疑问，Rollup网络中也肯定会出现一个范式标准。从长远的角度看，zkRollup在有零知识证明的加持下，会比目前没有完善欺诈证明系统的Optimisitic Rollup更有竞争力。至于是开源的 zkRollup 会成功构建自己的范式网络，还是 Sovereign 这样提供模块化方案能快速吸引项目方崛起，又或是两者分庭抗礼，都需要时间给出答案。
