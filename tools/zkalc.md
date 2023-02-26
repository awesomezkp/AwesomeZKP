[zkalc](https://zka.lc/)帮助你计算在实际计算机上进行密码学运算所花费的时间。

创建 zkalc 是为了快速响应诸如 *“在AWS C5 机器上能以多快的速度计算规模为 $2^{18}$ 的 MSM，以及120次配对 ”* 或 *“ 哪条曲线可以在10微妙内执行DH变换” 之类的问题*。

快来[使用它](https://zka.lc/)！

## 为什么？

密码学家往往擅长密码学，但他们在估计计算机运行方案的时长方面可能相当糟糕。

我们希望 [zkalc](https://zka.lc/) 可以帮助缩小密码学与实践之间的差距：

-   密码学家可以使用简单的用户页面来了解他们的新方案在各种机器上的运行速度，而不会浪费计算时常和能源消耗；
-   协议设计者可以根据自己的需求轻松调整协议的参数

我们将 zkalc 设计为既易于使用又易于扩展。编写新的基准测试类型或向 zkalc 网站添加新数据[十分容易](https://zka.lc/about)。

## [zkalc](https://zka.lc/)是如何工作的？

现在让我们回顾一下我们的基准测试流程以及我们如何得到结果。简而言之：

1. 对每个支持的运算，我们运行衡量其性能的基准。我们使用 [criterion.rs](https://github.com/bheisler/criterion.rs) 获取多个样本数据（对于像 MSM 这样的大型计算至少取 10 个），然后选择平均值。    
2. 我们在 `perf/data/` [目录](https://github.com/asn-d6/zkalc/tree/main/perf/data)中收集基准测试结果，并免费提供给任何人使用。
3. 对于每个运算，我们都通过一个[函数](https://en.wikipedia.org/wiki/Curve_fitting)与其基准结果进行拟合。我们在基准边界内使用线性插值，在基准边界外使用最小二乘回归。
4.  当用户向 zkalc 查询规模为 $n$ 的运算，我们使用生成的函数估计它的运行时间。

在这篇博客中，我们将深入探讨上述过程。我们将主要关注函数拟合，如果你对我们的基准如何工作的整个流程感兴趣，或者如果你想查看如下图表的交互式版本，请访问 zkalc 的方法论[页面](https://zka.lc/methodology)。

### 运行基准测试

对于每个支持的运算，我们编写基准测试并在多个平台上运行它们。然后我们将结果存储在zkalc 的`perf/`[目录](https://github.com/asn-d6/zkalc/tree/main/perf/data)中。

### 回复用户查询

现在我们在`perf/`目录中有每个运算的基准数据。下一步是对每个运算拟合一个函数 $f(x)$ ，以便当用户向我们查询任意规模 $n$ 的运算，我们可以通过 $f(x)$ 求值来回答。

对于像基本的标量乘法和域加法（没有摊销时）这样的简单运算，我们认为它们是顺序计算的。也就是说，如果单个标量乘法需要 $x$ 秒，$n$ 个这样的运算将需要 $n⋅x$ 秒。这推导出一个简单的线性函数  $f(x)=n⋅x$ 

更复杂的运算（如 MSM 和配对）会被摊销，因此它们的性能不遵循简单的线性曲线。

对于此类运算，我们[收集](https://github.com/asn-d6/zkalc/blob/main/backend/arkworks/benches/bench_arkworks.rs#L52)各种规模的基准数据。例如下图，它显示了来自 $\mathbb{G}_1$ 规模从 $2$ 到 $2^{18}$ 的MSM 运算（两个坐标轴都是对数刻度）：

![https://crypto.ethereum.org/images/posts/zkalc/points.svg](https://crypto.ethereum.org/images/posts/zkalc/points.svg)

为了在基准范围内回复用户查询，我们对基准数据进行[多项式插值](https://www.youtube.com/watch?v=yQsDxOdn1hk)。

即对于每一对基准数据 $(x_i, f(x_i))$ 和 $(x_{i+1}, f(x_{i+1}))$ ，我们追踪 [穿过这两个点的](https://github.com/asn-d6/zkalc/blob/main/frontend/lib/estimates.js#L26)线。最终得到一个覆盖整个基准范围的分段函数，如下图所示：

![https://crypto.ethereum.org/images/posts/zkalc/interpolation.svg](https://crypto.ethereum.org/images/posts/zkalc/interpolation.svg)

对于基准测试范围之外的用户查询，我们通过非线性最小二乘法进行[推断](https://en.wikipedia.org/wiki/Extrapolation)。为了更进一步，我们决定浏览器中使用[Javascript](https://github.com/asn-d6/zkalc/blob/main/frontend/lib/estimates.js)来实现它。

在像 MSM 这样的，[众所周知](https://jbootle.github.io/Misc/pippenger.pdf) Pippenger 的复杂度是渐近的 $O\ (n/log\ n)$。 因此，我们使用最小二乘法将数据集拟合为函数 $h(x)=\frac{ax+b}{log\ x}$，并求解 $a, b$

如下是超出基准测试范围进行推断的例子，$\mathbb{G}_1$ 的规模大于 $2^{21}$ 的 MSM 运算

![https://crypto.ethereum.org/images/posts/zkalc/extrapolation.svg](https://crypto.ethereum.org/images/posts/zkalc/extrapolation.svg)

我们不期望推断能够严格地遵循基准。然而，我们相信这些估计提供了一个大致的时间概念，即计算这些算法需要多长时间。

最后，我们最终得到了每个运算的分段函数，我们可以在基准范围内外回复用户的查询。

一定要[试试 zkalc](https://zka.lc/)，让我们知道你的想法！

## 密码学计算性能的可视化

在 zkalc 网站上，你还可以找到[zcharts](http://zka.lc/charts)角，我们在其中可视化了我们在上一章节所使用的所有原始基准数据。

我们希望可视化能帮助你理解 zkalc 的基准数据，同时也能更好地理解不同实现/曲线之间的性能差异。

## 欢迎加入

[zkalc](https://zka.lc/) 的意义在于其所提供的数据，并且还有许多其他基准测试的空间。如果你可以在大型云提供商上运行基准测试吗，我们很乐意合作并收集 [zkalc](https://zka.lc/) 的基准。如果你有功能强大的 GPU 证明者的资源，我们很乐意帮助你运行 [zkalc](https://zka.lc/)。如果你刚刚设计了一条新的椭圆曲线吗，欢迎使用 [zkalc](https://zka.lc/)对其进行基准测试。如果你正在开发一个新的加密库吗，正如你所想的，向 [zkalc](https://zka.lc/) 添加基准测试实际上很容易，查看[我们的网站说明](https://zka.lc/about)！

未来，我们还希望扩展 [zkalc](https://zka.lc/) 以支持更高级别的原语。从 FFT 到 IPA，再到各种多项式承诺和查找参数方案。如果你想为其中任何一个编写基准测试，请查看我们的[TODO](https://github.com/asn-d6/zkalc/blob/main/TODO.md)文件，并与我们联系！

## 致谢

非常感谢[Patrick Armino](https://patrick.wtf/)和[Jonathan Xu](https://jonathanxu.com/)在 UX 方面的帮助。