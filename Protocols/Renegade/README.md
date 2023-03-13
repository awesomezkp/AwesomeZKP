- 全面的交易隐私
- 结合多方计算 （MPC）和零知识证明（ZKP），多方计算决定交易的匹配，零知识证明用来结算匹配后的交易。通过 collaborative SNARKs 实现 MPC-ZKP的架构，Renegade 实现了原子化和交易前后完全隐私的去中心化交易所。
- 做到端到端加密的交易所，暗池
- 共识和安全性依托于Starknet，节点和订单匹配依赖于自身的p2p网络


## 基本概念

### 什么是暗池（Dark Pool）
传统的订单簿存在暴露交易池的信息，和交易方的订单信息，带来如下的问题
- MEV：验证人对巨额交易的抢跑
- 自动跟单：对链上地址的自动跟单
- 价格歧视：可以根据分析链上行为，对地址标记，从而进行价格歧视
- 跨交易所的套利：流动性割裂带来的，交易割裂，从而带来交易所间的套利机会
- 统计套利：对TWAP等下单模式进行分析，套利机器人进行套利
在链上合约的背景下，信息暴露问题更为严重，通过归档节点，任何人可以知晓过去的交易状态，通过内存池，任何人可以知道交易成功后的未来状态。
而在暗池交易模式下，交易者无法知晓其他人的挂单信息，只能知道自己的订单和成交情况。对于交易量大的巨鲸，需求尤为强烈。

### 什么是多方计算（MPC）

多方计算的核心思想是在不需要信任中心化一方的情况下，多方可以在隐私输入下，计算出输出。最典型的例子就是姚期智教授的百万富翁问题。
应用在暗池中，交易对手方匿名地完成了交易，下单信息就是隐私输入，MPC的输出就是订单的交割。但 MPC 本身无法验证订单信息的有效性，即隐私输入的正确性。因此需要借助零知识证明的力量。

## MPC-ZKP 架构

### Commit-Reveal 机制
交易者的钱包（Wallet）包含余额和订单列表，钱包和随机数的哈希承诺上链，类似于UTXO集
交易者发送给智能合约的信息包括：对新钱包的承诺，旧钱包的“nullifiers”，承诺和“nullifiers”正确计算的证明

### 网络架构
中继器（Relayer）是 Renegade 的节点，每个中继器管理一到多个钱包，负责和其他中继器进行多方计算。集群（Cluster）是管理相同钱包的中继器群。公共网关（Public Gateway）是特殊的中继器集群，可供不跑节点的群体使用。
当一笔交易进入中继器，中继器会传送 handshake 元组，包含对订单数据的承诺和相应的零知识证明。其他所有中继器会监听网络中的 handshake 元组，并通过MPC计算匹配引擎。

## Collaborative zk-SNARKs
Renegade 使用了 Ozdemir and Boneh 最新[collaborative SNARK](https://eprint.iacr.org/2021/1530) 研究成果，将零知识证明生成打包进了 MPC 的算法当中，使得中继器们可以合作证明一个 NP 问题。
当合作生成的证明产生后，任意方都可以递交到链上的智能合约，从而完成实际的代币交换。


## Fundraising | 融资

### 种子轮
- 融资 340 万美元，由 Dragonfly 和 Naval 领投，包括其他天使投资人：[Balaji Srinivasan](https://twitter.com/balajis) (Coinbase), [Tarun Chitra](https://twitter.com/tarunchitra) (Gauntlet), [Marc Bhargava](https://twitter.com/marcbhargava) (Tagomi), [Lily Liu](https://twitter.com/calilyliu) (Osmosis, Solana), and [Lev Livnev](https://lev.liv.nev.org.uk/) (Symbolic Capital Partners)