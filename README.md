<body>
	<div align="center">
	  <img src="assets/AwesomeZKP-Twitter%20Banner.png">
	</div>
</body>

- [术语表](Glossary.md)


# Popularizing｜科普
- [A Developer’s Guide to the zkGalaxy｜零知识证明开发人员指南](popularizing/zk_landscape/Blockchain%20Capital%20零知识证明开发人员指南.md)
- [An Introduction to Zero-Knowledge Machine Learning (ZKML) ｜零知识机器学习（ZKML）介绍](./protocols/Worldcoin/blogs/零知识机器学习（ZKML）介绍.md)


# Education｜教育
- [MIT IAP 2023](https://zkiap.com/)
- [ZKP Mooc, Spring 2023](https://zk-learning.org/)
- [Applied ZK Learning Group](https://learn.0xparc.org/)


# Technology Stacks｜技术栈

## Primitives｜原语
  - Math
	  - FFT
		  - [Fast Fourier Transforms](https://vitalik.ca/general/2019/05/12/fft.html)
	  - MSM
		  - [Multi-scalar Multiplication](https://hackmd.io/@tazAymRSQCGXTUKkbh1BAg/Sk27liTW9)
	  - Group Operation
		  - [Aztec emulated field and group operations](https://hackmd.io/@arielg/B13JoihA8)
  - Cryptography
	  - Elliptic Curve Pairings
		  - [Exploring Elliptic Curve Pairings](https://vitalik.ca/general/2017/01/14/exploring_ecp.html)
- IOP
	- PCP(Probabilistic Checkable Proof)｜概率可验证证明
	- Linear-PCP｜线性概率可验证证明
	- IPCP(Interactive PCP)｜交互式概率可验证证明
	- IOP(Interactive Oracle proof)｜交互式谕示证明
	- Poly-IOP(Polynomial Interactive Oracle proof)｜多项式交互式谕示证明
- PCS
	- [Dankrad Feist: PCS multiproofs using random evaluation](https://dankradfeist.de/ethereum/2021/06/18/pcs-multiproofs.html)
	- KZG
		- [Dankrad Feist: KZG polynomial commitments](https://dankradfeist.de/ethereum/2020/06/16/kate-polynomial-commitments.html)
	- FRI-based(Fast Reed-Solomon Interactive Oracle Proofs of Proximity)
	- IPA(Inner product argument)
	- DARK
	- Multilinear PCS

## Proving Systems｜证明系统

### SNARK
- Vigro
- deVirgo
- Placeholder
- Pickles
- Kimchi
- Marlin
- Groth 16
- Plonk
- Plonky2
- Halo
- Halo2
- dePlonk
- UltraPlonk
- HyperPlonk
- Bulletproofs
- Sonic
- SuperSonic
- FFLonk
- NOVA
### STARK
- [STARK 101](https://starkware.co/stark-101/)
- Anatomy of a STARK

## Acceleration｜硬件加速
- [PipeZK](https://www.microsoft.com/en-us/research/uploads/prod/2021/05/isca21_pizk-60a269dbb1310.pdf)
- [CycloneMSM](https://eprint.iacr.org/2022/1396.pdf)

## Library / Repo｜库
| Repo | Proving System | Team | Language |
| ---- | ------ | ---- | -------- |
| [bulletproof](https://github.com/dalek-cryptography/bulletproofs)|Bulletproof|dalek|Rust|
|[groth16](https://github.com/arkworks-rs/groth16)	|Groth16|arkworks	|Rust|
|[bellman](https://github.com/zkcrypto/bellman)|Groth16|zkcrypto|	Rust|
|[gnark](https://github.com/consensys/gnark)|Groth16, Plonk|Consensys|	Go|
|[snarkjs](https://github.com/iden3/snarkjs)|Groth16, Plonk|iden3|Javascript|
|[halo2](https://github.com/zcash/halo2)|Halo2(Zcash)|ZCash|	Rust|
|[snarkyjs](https://github.com/o1-labs/snarkyjs)	|Kimchi | O(1) Labs	|Javascript|
|[marlin](https://github.com/arkworks-rs/marlin)|Marlin	|arkworks	|Rust|
|[libsnark](https://github.com/scipr-lab/libsnark)|Plonk|	SCIPR Labs|	C++     |

## Tools | 工具
- [zkalc](zkalc.md)

## Papers｜论文
- The Knowledge Complexity of Interactive Proof-systems, 1985
- Non-interactive Zero-knowledge and Its Applications, 1988
- Short Pairing-based Non-interactive Zero-Knowledge Arguments, 2010
- From Extractable Collision Resistance to Succinct Non-Interactive Arguments of Knowledge, 2011
- Quadratic Span Programs and Succinct NIZKs without PCPs, 2012
- Pinocchio: Nearly Practical Verifiable Computation, 2013
- On the Size of Pairing-based Non-interactive Arguments? , 2016
- Bulletproofs: Short Proofs for Confidential Transactions and More, 2017
- Scalable, transparent, and post-quantum secure computational integrity, 2018
- Halo: Recursive Proof Composition without a Trusted Setup, 2019
- PlonK: Permutations over Lagrange-bases for Oecumenical Noninteractive arguments of Knowledge, 2019
- Sonic: Zero-Knowledge SNARKs from Linear-Size Universal and Updatable Structured Reference Strings, 2019
- Marlin: Preprocessing zkSNARKs with Universal and Updatable SRS, 2019
- SuperSonic: Transparent SNARKs from DARK Compilers, 2019
- PipeZK: Accelerating Zero-Knowledge Proof with a Pipelined Architecture, 2021
- HyperPlonk: Plonk with Linear-Time Prover and High-Degree Custom Gates, 2022
- SuperNova: Proving universal machine executions without universal circuits, 2022
- FPGA Acceleration of Multi-Scalar Multiplication: CycloneMSM, 2022
- PipeMSM: Hardware Acceleration for Multi-Scalar Multiplication, 2022


# Application Protocols｜应用协议
- Aztec
- Aleo
- zkSync
- PSE zkEVM
- Scroll
- Polygon zkEVM
- Consensys zkEVM
- Taiko
- Starknet
- Sismo
- SealCred
- Unirep
- Mina
- Risc Zero
- Renegade
- Slush
- Worldcoin
- Espresso System
- Ingonyama


# Reference｜参考资料
-   Geometry: https://geometry.xyz/
-   0xPARC: https://0xparc.org/blog
-   Ethereum Blogs: https://crypto.ethereum.org/blog
-   Ingnoyama: https://ingonyama-zk.github.io/ingopedia/
-   Matter Labs: https://github.com/matter-labs/awesome-zero-knowledge-proofs
-   Ventail: https://github.com/ventali/awesome-zk



# About Awesome ZKP
-   Dashboard: [awesomezkp.notion.site](http://awesomezkp.notion.site)
-   Twitter:[https://twitter.com/awesome_zkp](https://twitter.com/awesome_zkp)
-   Github: [https://github.com/awesomezkp](https://github.com/awesomezkp)
-   Telegram: [t.me/awesomezkp](http://t.me/awesomezkp)
-   Website(Soon): [awesomezkp.com](http://awesomezkp.com)
-   Foresight News: (https://foresightnews.pro/column/detail/572)[https://foresightnews.pro/column/detail/572](https://foresightnews.pro/column/detail/572) 