# 什么是Semaphore？

## 概述

[Semaphore](https://github.com/semaphore-protocol/semaphore)是一个[零知识](https://z.cash/technology/zksnarks)底层协议，允许您作为可证明的群体成员发出信号（例如投票或背书），而无需透露您的身份。此外，它提供了一种简单的机制来防止双重信号。用例包括隐私投票、举报、匿名 DAO 和混合器。

## 功能

使用信号量，您可以允许您的用户执行以下操作：

1.  [创建Semaphore身份](https://semaphore.appliedzkp.org/docs/guides/identities)。
2.  [将他们的 Semaphore 身份添加到一个组中（即*Merkle树*）](https://semaphore.appliedzkp.org/docs/guides/groups)。
3.  [发送可验证的匿名信号（例如投票或认可）](https://semaphore.appliedzkp.org/docs/guides/proofs)。

当用户广播信号（例如：投票）时，Semaphore零知识证明可以确保用户已加入该组并且尚未使用其nullifier发出信号。

Semaphore 使用链上 Solidity 合约和链下 JavaScript 库协同工作。

-   链下，JavaScript 库可用于创建身份、管理组和生成证明。
-   链上，Solidity 合约可用于管理组和验证证明。

## 开发者

Semaphore 旨在成为以太坊上去中心化应用程序 (dApp)的通用隐私层。_它鼓励模块化应用程序设计，允许 dApp 开发人员选择和定制他们需要的链上和链下组件。

## 关于代码

协议的核心是[电路逻辑](https://github.com/semaphore-protocol/semaphore/tree/main/packages/circuits/scheme.png)。除了电路之外，Semaphore 还提供[Solidity 合约](https://github.com/semaphore-protocol/semaphore/tree/main/packages/contracts) 和[JavaScript 库](https://github.com/semaphore-protocol/semaphore#-packages)，允许开发人员生成零知识证明并以最小的努力对其进行验证。

### 可信设置[仪式](https://semaphore.appliedzkp.org/docs/introduction#trusted-setup-ceremony "直接链接到标题")

用于使用Semaphore电路生成有效证明的[安全参数是在](https://semaphore.appliedzkp.org/docs/glossary#trusted-setup-files)[Trusted Setup Ceremony](https://storage.googleapis.com/trustedsetup-a86f4.appspot.com/semaphore/semaphore_top_index.html)中生成的，该仪式于[ 2022 年 3 月 29 日](https://etherscan.io/tx/0xec6dbe68883c7593c2bea82f55af18b3aeb5cc146e026d0083a9b3faa9aa0b65#eventlog)由 300 多名参与者共同完成。

### 审计

| 版本   | 审计人                         | 报告                                                                                                                 | 范围                    |
| ------ | ------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| v2.0.0 | [PSE](https://appliedzkp.org/) | [Semaphore_2.0.0_Audit.pdf](https://github.com/semaphore-protocol/semaphore/files/9850441/Semaphore_2.0.0_Audit.pdf) | `circuits`,`contracts`  |
| v2.5.0 | [PSE](https://appliedzkp.org/) | [Semaphore_2.5.0_Audit.pdf](https://github.com/semaphore-protocol/semaphore/files/9845008/Semaphore_2.5.0_Audit.pdf) | `contracts`,`libraries` |
| v3.0.0 | [Veridise](https://veridise.com/)                               |    [Semaphore_3.0.0_Audit.pdf](https://github.com/semaphore-protocol/semaphore/files/10513776/Semaphore_3.0.0_Audit.pdf)                                                                                                                  |   `circuits`,`contracts`                      |

