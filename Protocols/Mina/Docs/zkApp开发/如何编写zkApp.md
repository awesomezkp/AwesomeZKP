**信息**
请注意，Mina Mainnet 上尚未提供 zkApp 可编程性，但现在可以将 zkApps 部署到 Berkeley Testnet。

# 如何编写zkApp

zkApp 由智能合约和与之交互的 UI 组成。首先，我们将安装 Mina zkApp CLI 并编写智能合约。

## 编写智能合约

您的智能合约将使用[Mina zkApp CLI](https://www.npmjs.com/package/zkapp-cli/)编写。

Mina zkApp CLI 通过提供包括 SnarkyJS、测试框架 ( [Jest](https://jestjs.io/) )、代码自动格式化 ( [Prettier](https://prettier.io/) )、代码检查 ( [ES Lint](https://eslint.org/) )等依赖项的项目脚手架，可以轻松遵循推荐的最佳实践。

### 安装 Mina zkApp CLI
```sh
npm install -g zkapp-cli
```

#### 依赖项：

- NodeJS 16+（或 14 使用`node --experimental-wasm-threads`）
- NPM 6+
- Git 2+

**提示**

如果您安装了旧版本，我们建议使用适用于您系统的包管理器安装更新版本：[Homebrew](https://brew.sh/) (Mac)、[Chocolatey](https://chocolatey.org/) (Windows) 或 apt/yum/etc (Linux)。在 Linux 上，您可能需要按照 NodeJS项目的建议，通过 NodeSource（ [deb](https://github.com/nodesource/distributions#debinstall)或[rpm ）安装最新的 NodeJS 版本。](https://github.com/nodesource/distributions#rpminstall)

### 开始一个项目

现在你已经安装了 Mina zkApp CLI，你可以从一个例子开始或者开始你自己的项目。

#### 选项 A：从示例开始（推荐）
示例基于标准项目结构，但`/src`目录中的附加文件是唯一的区别。

1. **安装：** 运行`zk example sudoku`。这将创建一个新项目并在项目目录中包含示例文件（即智能合约）`src/` 。键入`ls`并按 Enter 以查看创建的文件或在代码编辑器（例如 VS Code）中打开目录。
2. **运行测试：** 运行`npm run test`. 测试是使用[Jest](https://jestjs.io/)编写的。运行此命令后，您应该会看到所有测试都通过了。您还可以运行`npm run testw`以在监视模式下运行测试，因此它会在保存对代码的更改时自动重新运行测试。
3. **构建示例：** 运行`npm run build`. 这会将您的 TypeScript 编译成项目`/build`目录中的 JavaScript。
4. **Deploy to Testnet:** Run `zk config`，这将引导您将网络别名添加到项目的`config.json`. 对于 Berkeley Testnet，我们建议使用`berkeley`作为名称、`0.1`费用和 `https://proxy.berkeley.minaexplorer.com/graphql`url。然后运行`zk deploy`并按照提示操作。有关详细信息，请参阅[如何部署 zkApp](https://docs.minaprotocol.com/zkapps/how-to-deploy-a-zkapp)页面。

您可以[在此处查看所有可用示例](https://github.com/o1-labs/zkapp-cli/tree/main/examples/)的列表。

#### 选项 B：开始您自己的[项目](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#option-b-start-your-own-project)

1. **安装：** 运行`zk project <myproj>`。键入`ls`并按回车键以查看新创建的项目结构。
2. **运行测试：** 运行`npm run test`. 测试是使用[Jest](https://jestjs.io/)编写的。运行此命令后，您应该会看到所有测试都通过了。您还可以运行`npm run testw`以在监视模式下运行测试，因此它会在保存对代码的更改时自动重新运行测试。
3. **构建：** 运行`npm run build`。这会将您的 TypeScript 代码编译成项目中的 JavaScript `/build`。
4. **Deploy to Testnet:** Run `zk config`，这将引导您将网络别名添加到项目的`config.json`. 对于 Berkeley Testnet，我们建议使用`berkeley`作为名称、`0.1`费用和 `https://proxy.berkeley.minaexplorer.com/graphql`url。然后运行`zk deploy`并按照提示操作。有关详细信息，请参阅[如何部署 zkApp](https://docs.minaprotocol.com/zkapps/how-to-deploy-a-zkapp)页面。
5. **部署到主网：（**即将推出。）

### 编写您的智能[合约](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#writing-your-smart-contract)

本节的目的是解释您在编写基于零知识的智能合约时需要了解的概念。

如果您还没有阅读[zkApps 如何工作](https://docs.minaprotocol.com/zkapps/how-zkapps-work)页面，请先阅读它以便理解本节。

##### [SnarkyJS](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#snarkyjs)

zkApps 是使用 SnarkyJS 用 TypeScript 编写的。SnarkyJS 是一个 TypeScript 库，用于编写基于 Mina 协议的零知识证明的智能合约。使用 Mina zkApp CLI 创建新项目时会自动包含它。

要查看完整的 SnarkyJS 参考，请参阅[snarkyJS 参考](https://docs.minaprotocol.com/zkapps/snarkyjs-reference)。

##### [概念](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#concepts)

域元素是零知识证明编程中数据的基本单位。每个域元素最多可以存储一个 256 位大小的数字。您可以将其视为 Solidity 中的 uint256。

**注意**

对于密码倾斜，一个字段可以存储的确切最大值是：28,948,022,309,329,048,855,892,746,252,171,976,963,363,056,481,941,560,715,954,676,764,349,967,630,336

例如，在典型的编程中，您可能会使用：

`const sum = 1 + 3`.

在 SnarkyJS 中，你可以这样写：

```
const sum = new Field(1).add(new Field(3))
```

这可以简化为：

```
const sum = new Field(1).add(3)
```

请注意，3 会自动提升为字段类型以使其更清晰。

##### 原始数据[类型](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#primitive-data-types)

您可能使用的几种常见数据类型是：

```ts
new Bool(x);   // accepts true or false
new Field(x);  // accepts an integer, or a numeric string if you want to represent a number greater than JavaScript can represent but within the max value that a field can store.
new UInt64(x); // accepts a Field - useful for constraining numbers to 64 bits
new UInt32(x); // accepts a Field - useful for constraining numbers to 32 bits

PrivateKey, PublicKey, Signature; // useful for accounts and signing
new Group(x, y); // a point on our elliptic curve, accepts two Fields/numbers/strings
Scalar; // the corresponding scalar field (different than Field)

CircuitString.from('some string'); // string of max length 128
```



在 and 的情况下`Field`，`Bool`您也可以在没有 的情况下调用构造函数`new`：

```ts
let x = Field(10);
let b = Bool(true);
```



##### [条件句](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#conditionals)

SnarkyJS 尚不支持传统的条件语句：

```ts
// this will NOT work
if (foo) {
  x.assertEquals(y);
}
```



相反，使用 SnarkyJS 的内置`Circuit.if()`方法，它是一个三元运算符：

```ts
const x = Circuit.if(new Bool(foo), a, b); // behaves like `foo ? a : b`
```



##### [功能](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#functions)

函数在 TypeScript 中的工作方式与您预期的一样。例如：

```ts
function addOneAndDouble(x: Field): Field {
  return x.add(1).mul(2);
}
```



##### 常用[方法](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#common-methods)

您将经常使用的一些常用方法是：

```ts
let x = new Field(4); // x = 4
x = x.add(3); // x = 7
x = x.sub(1); // x = 6
x = x.mul(3); // x = 18
x = x.div(2); // x = 9
x = x.square(); // x = 81
x = x.sqrt(); // x = 9

let b = x.equals(8); // b = Bool(false)
b = x.greaterThan(8); // b = Bool(true)
b = b.not().or(b).and(b); // b = Bool(true)
b.toBoolean(); // true

let hash = Poseidon.hash([x]); // takes array of Fields, returns Field

let privKey = PrivateKey.random(); // create a private key
let pubKey = PublicKey.fromPrivateKey(privKey); // derive public key
let msg = [hash];
let sig = Signature.create(privKey, msg); // sign a message
sig.verify(pubKey, msg); // Bool(true)
```



有关完整列表，请参阅[SnarkyJS 参考资料](https://docs.minaprotocol.com/zkapps/snarkyjs-reference)。

##### 智能[合约](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#smart-contract)

现在我们已经介绍了编写 SnarkyJS 程序的基础知识，让我们看看如何创建智能合约。

智能合约是通过扩展基类编写的`SmartContract`：

```ts
class HelloWorld extends SmartContract {}
```



`constructor`a 的是`SmartContract`从基类继承的，不应该被重写。它以 zkApp 帐户地址（公钥）作为唯一参数：

```ts
let zkAppKey = PrivateKey.random();
let zkAppAddress = PublicKey.fromPrivateKey(zkAppKey);

let zkApp = new HelloWorld(zkAppAddress);
```
稍后，我们将向您展示如何将智能合约部署到链上帐户。

笔记

在 Mina 上，普通的“用户账户”和“zkApp 账户”之间没有明显的区别。一个 zkApp 账户只是一个部署了智能合约的普通账户——这本质上只是意味着账户上存储了一个验证密钥，它可以验证智能合约生成的零知识证明。

##### [方法](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#methods)

与智能合约的交互是通过调用其一种或多种*方法*进行的。`@method`您使用装饰器声明方法：

```ts
class HelloWorld extends SmartContract {
  @method myMethod(x: Field) {
    x.mul(2).assertEquals(5);
  }
}
```



在一个方法中，您可以使用 SnarkyJS 的数据类型和方法来定义您的自定义逻辑。

稍后，我们将向您展示如何...

- 运行一个方法（链下）
- 创建它成功执行的证明
- 将该证明发送到 Mina 网络，以触发状态更改或付款等操作

要了解“成功执行”的含义，请查看上面示例中的这一行：

```ts
x.mul(2).assertEquals(5);
```



`x`只有当输入满足等式时，才能为该方法创建证明`x * 2 === 5`。这就是我们所说的“约束”。神奇的是，可以在不看到的情况下检查证据`x`——这是一个*私人输入*。

上面的方法目前意义不大。为了让它更有趣，我们需要一种与帐户交互并在链上记录状态的方法。查看下一节了解更多信息！

关于私有输入的更多注意事项：上面的方法有一个输入参数，`x`类型为`Field`。通常，参数可以是您看到的任何内置 SnarkyJS 类型：`Bool`, `UInt64`,`PrivateKey`等。从现在开始，我们将这些类型称为[structs`](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#custom-data-types)。

信息

在引擎盖下，每个都`@method`定义了一个 zk-SNARK 电路。从密码学的角度来看，智能合约是电路的集合，所有电路都被编译成一个证明者和验证密钥。证据表明“我运行了其中一种方法，使用一些私人输入，它产生了这组特定的帐户更新”。在 ZKP 术语中，帐户更新是*公共输入*。只有在根据存储在帐户中的验证密钥进行验证时，该证明才会在网络上被接受。这确实确保了 zkApp 开发人员编写的相同代码也在用户的设备上运行——因此，帐户更新符合智能合约的规则。

提示

你会发现在 a 内部`@method`，事情有时表现得有点不同。例如，以下代码不能用于`x: Field`输入参数为 的方法中：

```ts
console.log(x.toString()); // don't do this inside a `@method`! 😬
```



这是行不通的，因为当我们将 SmartContract 编译成证明者和验证密钥时，我们将在方法输入没有附加任何具体值的环境中运行您的方法。它们就像数学变量`x`，用于构建抽象计算，例如`y`，只需运行您的方法代码。`z``x^2 + y^2`

因此，当执行您的代码并尝试读取 的值`x`以通过 将其转换为字符串时`x.toString()`，它会爆炸，因为找不到这样的值。另一方面，在证明生成期间，所有变量*都*附加了实际值（密码学家称它们为“证人”）；并且想要记录这些值以进行调试是非常有意义的。这就是为什么我们有一个特殊的函数来记录你的方法内部的东西：

```ts
Circuit.log(x);
```



API 类似于 的 API `console.log`，但它会自动处理以良好格式打印 SnarkyJS 数据类型。在 SmartContract 编译期间，它什么都不做。

##### 链上[状态](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#on-chain-state)

智能合约可以包含**链上状态**，它被声明为带有`@state`装饰器的类的属性：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  // ...
}
```



这里，`x`是类型`Field`。与方法输入一样，只有 SnarkyJS 结构可用于状态变量。在目前的设计中，状态最多可以包含 8 个字段，每个字段 32 个字节。这些状态存储在 zkApp 帐户中。有些结构占用了多个字段：例如，a`PublicKey`需要 8 个字段中的 2 个。状态是用函数初始化的`State()`。

一个方法可以通过使用修改链上状态`this.<state>.set()`：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  @method setX(x: Field) {
    this.x.set(x);
  }
}
```



作为一个 zkApp 开发者，如果你把这个方法添加到你的智能合约中，你就是在说：“任何人都可以调用这个方法，`x`在账户上设置他们想要的任何值。”

##### 阅读[状态](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#reading-state)

通常，我们还想*读取*状态——看看这个例子：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  @method increment() {
    // read state
    const x = this.x.get();
    this.x.assertEquals(x);

    // write state
    this.x.set(x.add(1));
  }
}
```



该`increment()`方法使用 获取当前链上`x`状态`this.x.get()`。稍后，它将新状态设置为`x + 1`using `this.x.set()`。简单的！

不过还有另一行，起初看起来很奇怪：

```ts
this.x.assertEquals(x);
```



要理解它，我们必须退一步，理解在链下执行期间“使用链上值”是什么意思。

当然，当我们使用链上值时，我们必须*证明*这是链上值。如果它是不同的值，验证必须失败！否则，恶意用户可以修改 SnarkyJS 并使其仅使用当前链上状态以外的任何其他值——破坏我们的 zkApp。

为防止这种情况，我们将“`x`在证明时”链接为与“`x`在验证时”相同。我们称之为*先决条件*——验证者（Mina 节点）在收到交易证明时检查的条件。这就是`this.x.assertEquals(x)`它的作用：它添加了先决条件`this.x`——验证时的链上状态——必须等于`x`——我们从客户端的链中获取的值。在 zkSNARK 语言中，`x`成为公共输入的一部分。

旁注：`this.<state>.assertEquals`比等同于当前值更灵活。例如，`this.x.assertEquals(10)`将 on-chain`x`固定为 number `10`。

笔记

为什么我们不`this.x.get()`自动添加前提条件，这样你就不用写了`this.x.assertEquals(x)`？好吧，我们喜欢保持明确。断言提醒我们，我们添加了可能导致证明失败的逻辑：如果`x`在验证时不相同，交易将被拒绝。因此，如果许多用户应该同时读取和更新状态，则必须小心读取链上值。它适用于某些情况，但在其他情况下可能会导致竞争，并需要解决方法。一种这样的解决方法是使用动作——请参阅[动作和 Reducer](https://docs.minaprotocol.com/zkapps/advanced-snarkyjs/actions-and-reducer)。

##### [断言](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#assertions)

让我们修改`increment()`接受参数的方法：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  @method increment(xPlus1: Field) {
    const x = this.x.get();
    this.x.assertEquals(x);

    x.add(1).assertEquals(xPlus1);

    this.x.set(xPlus1);
  }
}
```



在这里，在获取当前状态`x`并断言它等于链上值之后，我们进行另一个断言：

```ts
x.add(1).assertEquals(xPlus1);
```



如果断言失败，SnarkyJS 将抛出错误并且不提交事务。另一方面，如果它成功了，它就会成为在链上验证的证明的一部分。

正因为如此，我们的新版本`increment()`保证*与*以前的版本一样：它只能将状态更新`x`为`x + 1`。

提示

您可以将可选的失败消息添加到断言中，以使调试更容易。例如，上面的例子可以写成：

```ts
x.add(1).assertEquals(xPlus1, 'x + 1 should equal xPlus1');
```



断言对于约束状态更新非常有用。您可能使用的常见断言是：

```ts
x.assertEquals(y); // x = y
x.assertBoolean(); // x = 0 or x = 1
x.assertLt(y);     // x < y
x.assertLte(y);    // x <= y
x.assertGt(y);     // x > y
x.assertGte(y);    // x >= y
```



有关完整列表，请参阅[SnarkyJS 参考资料](https://docs.minaprotocol.com/zkapps/snarkyjs-reference)。

##### 公共和私人[投入](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#public-and-private-inputs)

我们已经谈到了这一点，但最好更深入地介绍它：

虽然 zkApp 的状态是**公开**的，但方法参数是**私有**的。

当调用智能合约方法时，它产生的证明使用零知识来隐藏输入和计算细节。

可以公开方法参数的唯一方法是在计算时显式公开它们，就像我们上一个示例中的输入直接存储在公共状态中一样：`this.x.set(xPlus1);`

让我们通过定义一个名为的新方法来展示一个不是这种情况的示例`incrementSecret()`：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  // ...

  @method incrementSecret(secret: Field) {
    const x = this.x.get();
    this.x.assertEquals(x);

    Poseidon.hash(secret).assertEquals(x);
    this.x.set(Poseidon.hash(secret.add(1)));
  }
}
```



这一次，我们的输入称为`secret`。我们检查我们的秘密的散列是否等于当前状态`x`。如果是这种情况，我们将秘密加 1 并设置`x`为它的哈希值。

成功运行时，它只是证明代码是使用散列为 的输入运行的*，*并且新的将设置为。然而，秘密本身仍然是私有的，因为它不能从它的散列中推断出来。`secret``x``x``hash(secret + 1)`

##### 初始化[状态](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#initializing-state)

我们还没有介绍如何初始化链上状态。这可以在`init()`方法中完成。

和构造函数一样，`init()`是在基`SmartContract`类上预定义的。当您第一次使用 zkApp CLI 部署 zkApp 时，它将被调用。如果您升级合同并进行第二次部署，则不会调用它。您可以覆盖此方法以添加链上状态的初始化：

```ts
class HelloWorld extends SmartContract {
  @state(Field) x = State<Field>();

  init() {
    super.init();
    this.x.set(Field(10)); // initial state
  }
}
```



请注意，我们必须调用`super.init()`，这会将您的整个状态设置为 0。

如果您没有任何状态可以初始化为 0 以外的值，则无需覆盖`init()`，您可以将其省略。然而，在上面的示例中，我们将状态设置`x`为`Field(10)`.

##### 编写[zkApps](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#composing-zkapps)

zkApps 的一个强大特性是它们是可组合的，就像以太坊智能合约一样。您可以简单地从其他智能合约方法调用智能合约方法：

```ts
class HelloWorld extends SmartContract {
  @method myMethod(otherAddress: PublicKey) {
    const calledContract = new OtherContract(otherAddress);
    calledContract.otherMethod();
  }
}

class OtherContract extends SmartContract {
  @method otherMethod() {}
}
```



当用户调用`HelloWorld.myMethod()`时，SnarkyJS 将创建两个单独的证明——一个用于`myMethod()`像往常一样执行，另一个*单独*用于执行`OtherContract.otherMethod()`. 该`myMethod()`证明将计算函数签名的适当散列`otherMethod()`加上该函数调用的任何参数和返回值，并保证此散列与由`callData`生成的帐户更新中的字段相匹配，该字段`otherMethod()`是`myMethod()`的公共输入的一部分。因此，当调用另一个 zkApp 方法时，您有效地证明：“我在这个 zkApp 帐户上用这个名称调用了一个方法，具有这个特定的参数和返回值。”

为确保其他方法可以使用您的 的返回值`@method`，您必须在 TypeScript 函数签名中注释返回值。下面是返回一个`Bool`called的例子`isSuccess`：

```ts
@method otherMethod(): Bool { // annotated return type
  // ...
  return isSuccess;
}
```



##### 自定义数据[类型](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#custom-data-types)

如前所述，智能合约方法参数可以是任何内置的 SnarkyJS 类型。

但是，如果您想定义自己的数据类型怎么办？

`Struct`您可以使用SnarkyJS 公开的函数为您的智能合约创建自定义数据类型。为此，创建一个扩展`Struct({ })`. 然后，在 object 内部`{ }`，定义要在自定义数据类型中使用的字段。

例如，假设您要创建一个名为`Point`表示网格上的二维点的自定义数据类型。该`Point`结构没有实例方法，仅用于保存有关`x`和`y`点的信息。您可以通过创建一个扩展该类的新类来创建这样的 Point 类`Struct`：

```ts
class Point extends Struct({
  x: Field,
  y: Field,
}) {}
```



现在您已经定义了 Struct，您可以在智能合约中将其用于任何 SnarkyJS 内置类型。

例如，以下智能合约使用`Point`上面定义的 Struct 作为状态和方法参数：

```ts
export class Grid extends SmartContract {
  @state(Point) p = State<Point>();

  @method init() {
    this.p.set(new Point({ x: Field(1), y: Field(2) }));
  }

  @method move(newPoint: Point) {
    const point = this.p.get();
    this.p.assertEquals(point);

    const newX = point.x.add(newPoint.x);
    const newY = point.y.add(newPoint.y);

    this.p.set(new Point({ x: newX, y: newY }));
  }
}
```



请注意，您的 Structs 可以包含 SnarkyJS 内置类型，例如`Field`, `Bool`,`UInt64`等，甚至您定义的其他基于`Struct`该类的自定义类型。这允许结构具有很好的可组合性和可重用性。

## 后续[步骤](https://docs.minaprotocol.com/zkapps/how-to-write-a-zkapp#next-steps)

现在你已经学会了如何编写一个基本的智能合约，你可以学习[如何测试你的 zkApp](https://docs.minaprotocol.com/zkapps/how-to-test-a-zkapp)。

[
](https://docs.minaprotocol.com/zkapps/how-zkapps-work)
