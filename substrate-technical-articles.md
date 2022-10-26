# 前言

本译文原文全部来自于[Substrate官方文档](https://docs.substrate.io/v3/getting-started/overview/)，旨在还原原文意思、促进技术交流，但是由于本人才疏学浅，如有错误或不当之处，还请各位见谅并指出

# 1 开始

## 1.1 总览

这里是Substrate 开发者的官方文档中心，这里资源的目的主要是帮助读者理解使用Substrate 进行区块链开发的难点。这篇指导文章分为几个部分，主要内容是介绍Substrate的一些构建原则和设计初衷以及成为一名高效的区块链开发者所需要的特殊技能

必备知识提示：

为了更充分的理解Substrate，你应该拥有扎实的计算机科学知识和了解基本的区块链概念。你应该熟悉区块头（header）、block（区块）、client（客户端）、哈希（hash）、交易（transaction）和签名（signature）等术语。Substrate是用Rust语言构建的，这是因为Rust语言具有优秀的设计模式能够实现安全、快速的代码开发。如果你不了解Rust，你仍然可以利用Substrate进行开发工作，但是对于Rust有比较好的理解可以让你变成一个优秀的Substrate开发者。可以通过Rust社区提供的[资源](https://www.rust-lang.org/learn)来提升你的Rust使用技巧

Substrate 采用模块化的方法进行区块链开发，它定义了一系列丰富的原语，这能够让开发者使用强大、熟悉的编程语言

**使用方法**

**Substrate主要有以下三种使用方式：**

**使用Substrate Node**：你可以运行预先设计的[Substrate节点]( https://github.com/paritytech/substrate/tree/master/bin/node )和[配置](https://github.com/paritytech/substrate/blob/master/bin/node/cli/src/chain_spec.rs)创世区块。在这种情况下，你只需要用一个JSON文件就可以发布你自己的区块链。JSON文件可以让你配置构建Substrate Node运行时模块的初始状态，比如：余额、 质押和超管权限等。你可以通过[创建你的第一条Substrate链教程](https://docs.substrate.io/tutorials/v3/create-your-first-substrate-chain/)和[创建私有网络教程](https://docs.substrate.io/tutorials/v3/private-network/)了学习更多关于运行一个Substrate Node的知识

**使用Substrate FRAME**：你可以使用[FRAME](https://docs.substrate.io/v3/runtime/frame/)（Framework for Runtime Aggregation of Modularized Entities) 轻松地创建自定义的运行时，FRAME用于构建Substrate 节点。当你构建区块链逻辑时它能给你很大的自由，它可以让你配置数据类型、从模块库中选择模块，甚至往模块库中添加自定义的模块。当你要开始一个项目时，推荐使用[Substrate 开发者中心节点模版](https://github.com/substrate-developer-hub/substrate-node-template)，这是一个好的起点。更多内容，请参考[创建一个dApp](https://docs.substrate.io/tutorials/v3/proof-of-existence/)和[添加一个模块](https://docs.substrate.io/tutorials/v3/add-a-pallet/)

**使用Substrate Core**：如果使用Substrate core，可以忽略整个FRAME框架，因为运行时可以从头开始设计和实现。任何编译目标是[WebAssembly](https://webassembly.org/)的语言都可以实现。如果让runtime和Substrate节点抽象的区块创作逻辑兼容，你就能够轻易的从Wasm blob构建一个新的创世区块并且使用现有的以Rust为基础的Substrate 客户端发布你的链。如果不兼容，你需要改变客户端的区块创作逻辑，甚至是改变区块头和区块序列化格式。就开发工作而言，使用Substrate Core尽管很难，但是更困难的方式也意味着更大的创新自由

Substrate可以让开发者在设计决策的每一步都能够按照他们自己的偏好来选择是技术上更自由还是开发上更容易。下图说明了这种灵活性的本质

![Technical Freedom vs Development Ease](https://docs.substrate.io/static/440b4e8d7160f904e672e5b5803f2e44/c1b63/technical-freedom.png)

**后续**

**了解更多**

参考开发者文档，[用于运行时开发的FRAME系统](https://docs.substrate.io/v3/concepts/runtime/)

使用[Polkadot-JS](https://docs.substrate.io/v3/integration/polkadot-js/)系列库为任何基于substrate的链创建丰富多样的客户端应用

深入研究高级主题，比如Substrate的[SCALE 编码](https://docs.substrate.io/v3/advanced/scale-codec/), [共识机制](https://docs.substrate.io/v3/advanced/consensus/), [密码学](https://docs.substrate.io/v3/advanced/cryptography/) 和 [存储](https://docs.substrate.io/v3/advanced/storage/)

**例子**

通过[教程](https://docs.substrate.io/tutorials/v3/)学习使用Substrate和FRAME构建和运行区块链

参考[指导文档](https://docs.substrate.io/how-to-guides/v3/)中的代码示例，它们包含了一些常见问题的解决方法

参考

查看[Rust 参考文档](https://docs.substrate.io/rustdocs/)，了解Substrate代码基础

## 1.2 Substrate客户端架构

![Substrate Client Architecture](/Users/qinjianquan/Desktop/substrate/substrate-arch.png)

Substrate客户端是一个运行了基于Substrate的区块链节点的应用，它的组成部分包括但不限于如下内容：

**存储：**用于持续更新Substrate区块链状态。区块链网络允许参与者就存储达状态达成[共识](https://docs.substrate.io/v3/advanced/consensus/)。Substrate附带了一种简单而高效的[键值对存储机制](https://docs.substrate.io/v3/advanced/storage/)

**运行时**：定义了如何处理区块的逻辑，包括状态转换逻辑。在Substrate中，运行时代码被编译成[Wasm](https://docs.substrate.io/v3/getting-started/glossary/#webassembly-wasm)，并且变成了区块链存储状态的一部分。它实现了以Substrate为基础区块链特的性之一：[无分叉运行时升级](https://docs.substrate.io/v3/runtime/upgrades/#forkless-runtime-upgrades)。Substrate客户端也可能包含一个原生运行时，该运行时用于编译与客户端相同的平台（和Wasm相反）。 [executor](https://docs.substrate.io/v3/advanced/executor/)是客户端的组件，它可以调度对运行时的调用，它作用是在原生代码和Wasm之间作出选择。尽管原生运行时可能会提供更出色的性能优势，但是如果它执行了[更新的版本](https://docs.substrate.io/v3/runtime/upgrades/#runtime-versioning)，executor将会选择Wasm运行时

**点对点网络**：它可以让客户端与其他网络参与者进行交流。Substrate使用了[libp2p network stack]( https://libp2p.io/)的Rust实现来构建点对点网络

**共识**：是网络参与者就区块链的状态达成一致的逻辑。Substrate让提供自定义的共识引擎成为了可能并且还附带了几种基于[Web3基金会研究](https://research.web3.foundation/en/latest/index.html)构建的共识机制

**RPC（远程进程调用）**：让区块链用户与网络进行交互，Substrate提供了HTTP和WebSocket PRC服务器

**Telemetry**：嵌入式[Prometheus](https://prometheus.io/)服务器公开的客户端指标

## 1.3 安装 Substrate

本节内容将会通过两个步骤来指导你搭建Substrate开发环境。因为Substrate是基于[Rust](https://www.rust-lang.org/)构建的，所以第一件事情是在电脑上搭建Rust开发环境，这些步骤随计算机操作系统的不同而略有差别，Rust配置完成之后，你就可以使用工具链与Rust项目进行交互，Rust工具链的命令在所有以Unix为基础的操作系统上都是相同的

### 1.3.1 构建依赖

Substrate早期开发基于Unix操作系统，比如macOS 和 Linux. [Substrate教程](https://docs.substrate.io/tutorials/v3/)和[指导](https://docs.substrate.io/how-to-guides/v3/)中的例子使用的是Unix风格的终端展示如何在命令行中与Substrate交互

**Ubuntu/Debian** 

在终端中执行下面的命令

```
sudo apt update
# May prompt for location information
sudo apt install -y git clang curl libssl-dev llvm libudev-dev
```

**Arch Linux **

在终端中运行下面的命令

```
pacman -Syu --needed --noconfirm curl git clang
```

**Fedora**

在终端中运行下面的命令

```
sudo dnf update
sudo dnf install clang curl git openssl-devel
```

**OpenSUSE**

在终端中运行下面的命令

```
sudo zypper install clang curl git openssl-devel llvm-devel libudev-devel
```

**macOS**

如果你使用的是M1芯片的Mac,请确保你通过“softwareupdate --install-rosetta”安装了Apple Rosetta 2。它只需要再构建期间运行‘protoc'工具，该工具本身和二进制目标文件都是原生的

打开终端程序执行下面的命令

```
# Install Homebrew if necessary https://brew.sh/
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

# Make sure Homebrew is up-to-date, install openssl
brew update
brew install openssl
```

**Windows**

**提醒：**Substrate本地开发环境目前并没有很好的支持所有平台，强烈推荐按照 [Ubuntu/Debian](https://docs.substrate.io/v3/getting-started/installation/#ubuntudebian)指令使用[Windows Subsystem Linux](https://docs.microsoft.com/en-us/windows/wsl/install )(WSL)。如果是Windows系统的话，请参考[原生Windows开发环境指导](https://docs.substrate.io/v3/getting-started/windows-users/)

### 1.3.2 搭建Rust 开发环境

参考[安装文档]( [https://rustup.rs](https://rustup.rs/))安装Rust和使用rustup工具管理Rust工具链。先安装和配置rustup

```
# Install
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# Configure
source ~/.cargo/env
```

配置Rust 工具链，默认会安装最新的稳定版本，添加nightly和nightly wasm 目标

```
rustup default stable
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
```

**检查开发环境是否成功搭建**

现在确定成功搭建好Substrate开发环境的最佳方式是按照教程[创建第一条Substrate区块链](https://docs.substrate.io/tutorials/v3/create-your-first-substrate-chain/)

有关为什么要使用这些依赖的详细信息，以及如何排除构建模板时遇到的错误，请继续阅读

**Substrate构建疑难解答**

有时候你无法让Substrate 节点模板开箱就可以编译。这儿有一些小窍门可以帮助你解决它

**检查Rust配置**

通过运行如下命令，你可以看到你目前正在使用的Rust工具链

```
rustup show
```

下图是Ubuntu系统的显示样例

```
Default host: x86_64-pc-windows-msvc
rustup home:  C:\Users\jianquanqin\.rustup

installed toolchains
--------------------

stable-x86_64-pc-windows-msvc (default)
nightly-x86_64-pc-windows-msvc

active toolchain
----------------

stable-x86_64-pc-windows-msvc (default)
rustc 1.58.1 (db9d1b20b 2022-01-20)
```

如上所见，默认的工具链是稳定版的，nightly-x86_64-unknown-linux-gnu和wasm32-unknown-unknown也被安装了，你也可以看到nightly-2020-10-06-x86_64-unknown-linux-gnu被安装了，除非在[指定nightly版本](https://docs.substrate.io/v3/getting-started/installation/[)中被明确定义，否则它不会被使用

**WebAssembly 编译**

Substrate使用[WebAssembly]( https://webassembly.org/)（Wasm）产生适配的区块链运行时。你需要配置你的Rust 编译器以使用[nightly build](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html)，这能够让你把Substrate运行时代码编译为Wasm

**提示：**在Substrate可以稳定地使用Rust 工具链之前所有的问题都需要先被解决，如果你好奇为什么以及这些问题是如何被解决的，这儿是[问题追踪](https://github.com/paritytech/substrate/issues/1252)

**Substrate master 最新的nightly版本**

构建Substrate本身的开发者总是会使用Rust无错误的稳定版和nightly版。这是因为Substrate 代码库遵循Rust nightly的提示，这意味着Substrate的改变取决于Rust nightly 编译器的改变，为了确保你的Rust编译器总是最新的，你应该运行如下代码：

```
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
```

**注意：**如果上游Substrate代码库的更改依赖于Rust 编译器的新功能，你必须偶尔要运行rustup update，让nightly和稳定版的工具链都更新到最新版本，对于nightly，我们通常不会期待它编译WASM时不出任何错误（事实上它通常不会出错）。如果你使用rustup 构建WASM时遇到了错误，请[指定nightly版本](https://docs.substrate.io/v3/getting-started/installation/#specifying-nightly-version)，并根据需要选择合适的[版本](https://docs.substrate.io/v3/getting-started/installation/#downgrading-rust-nightly)

**Rust nightly toolchain**

如果想要确保当你更新Rust和其它依赖时，你的构建工作仍然能够在你的计算机上运行。你应该指定一个具体的nightly版本，它应该和Substrate使用的nightly版本相兼容。这个版本会因项目而异。不同项目可能会使用不同的机制与开发者交流，例如波卡客户端在其[发布摘要](https://github.com/paritytech/polkadot/releases)中就指明了此信息

```
# Specify the specific nightly toolchain in the date below:
rustup install nightly-<yyyy-MM-dd>
```

**Wasm toolchain**

现在，配置nightly版本以编译Wasm

```
rustup target add wasm32-unknown-unknown --toolchain nightly-<yyyy-MM-dd>
```

**指定nightly 版本**

使用WASM_BUILD_TOOLCHAIN环境变量指定Rust nightly版本，以让Substrate项目能使用其编译Wasm

```
WASM_BUILD_TOOLCHAIN=nightly-<yyyy-MM-dd> cargo build --release
```

**注意：**指定的nightly只用于构建运行时，其余的项目仍然用默认的工具链编译，即最新安装的稳定工具链

Downgrading Rust nightly

如果你的计算机配置了最新的Rust nightly，但是你想要回到某个具体的nightly版本，请按照以下步骤：

```sh
rustup uninstall nightly

rustup install nightly-<yyyy-MM-dd>

rustup target add wasm32-unknown-unknown --toolchain nightly-<yyyy-MM-dd>
```

## 1.4 在Windows上开始

**提醒：**Substrate本地开发环境目前并没有很好的支持所有平台，强烈推荐按照 [Ubuntu/Debian](https://docs.substrate.io/v3/getting-started/installation/#ubuntudebian)指令使用[Windows Subsystem Linux](https://docs.microsoft.com/en-us/windows/wsl/install )(WSL)。如果是Windows系统的话，请参考[原生Windows开发环境指导](https://docs.substrate.io/v3/getting-started/windows-users/)。Substrate早期开发基于Unix操作系统，比如macOS 和 Linux. [Substrate教程](https://docs.substrate.io/tutorials/v3/)和[指导](https://docs.substrate.io/how-to-guides/v3/)中的例子使用的是Unix风格的终端展示如何在命令行中与Substrate交互

如果你决定尝试使用Windows计算机去在本地构建Substrate，请遵从下面的流程：

**1.下载和安装Build Tools for Visual Studio**

- 下载链接： [https://aka.ms/buildtool](https://aka.ms/buildtools)

- 运行安装文件：vs_buildtools.exe

- 确保安装 Visual C++ 构建工具时包含Windows 10 SDK组件

- 重启计算机


**2.安装Rust**

-  [Rust Book](https://doc.rust-lang.org/book/ch01-01-installation.html#installing-rustup-on-windows)提供了详细的安装步骤，快速安装请参考 [https://rustup.rs](https://rustup.rs/)

- 下载链接: https://www.rust-lang.org/tools/install

- 运行适用于32位或者64位系统的安装程序：rustup-init.exe

  - 它不应提示你安装vs_buildtools,因为你在第一步安装了它

- 选择默认安装

- 在开始之前，需要在环境变量中添加Cargo bin目录（%USERPROFILE%\.cargo\bin），以后的应用会自动配置正确的环境变量，重启你的shell


**3.在终端中运行如下命令配置Wasm 构建环境**

```
rustup update nightly
rustup update stable
rustup target add wasm32-unknown-unknown --toolchain nightly
```

**4.安装 LLVM**： https://releases.llvm.org/download.html

**5.使用PowerShell安装带vcpkg的OpenSSL**

```
mkdir C:\Tools
cd C:\Tools
git clone https://github.com/Microsoft/vcpkg.git --depth=1
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg.exe install openssl:x64-windows-static
```

**6.使用PowerShell将 OpenSSL 添加至系统变量**

```
$env:OPENSSL_DIR = 'C:\Tools\vcpkg\installed\x64-windows-static'
$env:OPENSSL_STATIC = 'Yes'
[System.Environment]::SetEnvironmentVariable('OPENSSL_DIR', $env:OPENSSL_DIR, [System.EnvironmentVariableTarget]::User)
[System.Environment]::SetEnvironmentVariable('OPENSSL_STATIC', $env:OPENSSL_STATIC, [System.EnvironmentVariableTarget]::User)
```

**7.安装cmake**： [https://cmake.org/download](https://cmake.org/download/)

**8.安装make**：

​	它可以通过Chocolatey来安装，首先你需要安装Chocolatey包管理器: https://chocolatey.org/install

​	一旦Chocolatey安装完成，就可以安装make:

```
choco install make
```

## 1.5 常见术语

这些术语定义和解释了区块链和Substrate生态系统中常见的概念

**公投人数自适应调整 (adaptive quorum biasing (AQB))**

公投人数自适应调整允许更灵活的治理，它取消了为投票目的而设置的任意法定人数的要求。而这种要求会导致不良的治理机制。公投人数自适应调整机制在[Democracy pallet](https://docs.substrate.io/v3/runtime/frame/#democracy)中实现。Democracy pallet为链上实体（如[集体](https://docs.substrate.io/v3/getting-started/glossary/#council)或者个人代币持有者）提供了一个接口，他们可以以积极、消极或者中立的态度召集公投

如果针对某一项提议的投票率偏正，则提议被通过的门槛会随着参与投票的人数增加而降低，因此较高的投票率会增加公投通过的可能性。如果投票率偏负有时候也被称为默认同意，因为当投票者不参与时会算作默认通过。投票率偏中性时，则是简单的哪方投票多，哪方获胜

**aggregation**

常用在和FRAME相关的上下文中，aggregation或者pallet aggregation指的是把来自于多个运行时模块中相似的类型聚合成一个单个类型的过程。Pallet aggregation可以代表每个类型的相似类型。对包含了聚合类型的调用有时候叫做外部调用或者对外部对象的调用。目前，有六种数据类型可以被聚合

- Call ：使用一些参数调用的已发布函数
- Error：函数调用失败的描述信息

- Event：描述状态改变的pallet- emitted事件信息

- Log：可扩展的header 项目

- Metadata：用于检查上述信息

- Origin：函数调用的来源


**赞成投票（approval voting）**

投票系统支持投票者投票给多个候选人（项目），到期得票数最多的人（项目）会获胜。在投赞成票时，需要注意以下几点：

- 给所有候选人（项目）投票等同于未投票
- 可以通过把票投其他所有候选人的方式来反对你不赞成的候选人

[FRAME Elections Phragmen pallet](https://docs.substrate.io/v3/runtime/frame/#elections-phragmén) 在一些以Substrate为基础的链上使用同意投票作为[管理委员会](https://docs.substrate.io/v3/getting-started/glossary/#council)

**author**

指负责创建区块的节点，区块author也叫区块生产者，在工作量证明的区块链中，这些节点叫做矿工

**authority**

指的是节点的集合体，[管理](https://docs.substrate.io/v3/getting-started/glossary/#consensus)区块链网络的[共识](https://docs.substrate.io/v3/getting-started/glossary/#blockchain)。在[权益证明](https://docs.substrate.io/v3/getting-started/glossary/#nominated-proof-of-stake-npos)的区块链中，例如，在一条使用[FRAME](https://docs.substrate.io/v3/getting-started/glossary/#frame)的[Staking pallet](https://docs.substrate.io/v3/runtime/frame/#staking) 的区块链中，权限通过代币加权提名和投票系统决定 

```
authorities和validators有时候指的是同一件事，但是，实际上validators是一个更广泛的概念，它包含了链维护的其它方面，例如平行链的验证。一般而言，validators包含authorities（并不是很严格），也就意味着许多validators是authorities
```

**authority round (Aura)**

它是一个确定的[共识](https://docs.substrate.io/v3/getting-started/glossary/#consensus)协议，在这个协议中，只有在轮转列表里的[authorities](https://docs.substrate.io/v3/getting-started/glossary/#authority)才能够创建区块。在authority round (Aura)的共识中，大多数在线authorities都被认为是诚实的

有关于Aura 共识算法的更多内容请参考官方[wiki文章]( [the official wiki article](https://openethereum.github.io/Aura))

Aura协议通常和GRANDPA 被用于[混合共识](https://docs.substrate.io/v3/getting-started/glossary/#hybrid-consensus)协议中。Aura 被用于区块的产生和短期的[probabilistic finality](https://docs.substrate.io/v3/getting-started/glossary/#probabilistic-finality),而[GRANDPA](https://docs.substrate.io/v3/getting-started/glossary/#grandpa)用于提供[deterministic finality](https://docs.substrate.io/v3/getting-started/glossary/#deterministic-finality) 

**区块链扩展盲分配 (blind assignment of blockchain extension (BABE))**

一个和Aura是相似的[区块生产](https://docs.substrate.io/v3/getting-started/glossary/#author)协议。但是，在区块链扩展盲分配协议下，authorities可以基于可验证的随机函数赢得插槽，而不是通过轮流选择的方式。获胜者可以选择一条链并为其提交一个新区块

更多关于区块链扩展盲分配的信息请参考[Web3 基金会的官方研究文档](https://research.web3.foundation/en/latest/polkadot/block-production/Babe.html)

**block**

区块是区块链的单个组成元素，它会把一系列[extrinsic](https://docs.substrate.io/v3/getting-started/glossary/#extrinsic)数据以[加密的](https://docs.substrate.io/v3/getting-started/glossary/#cryptographic-primitives)的方式绑定到区块的[头部](https://docs.substrate.io/v3/getting-started/glossary/#header)，区块通过父指针排列成树结构，指向父区块的是父区块的哈希，使用[fork-choice rule](https://docs.substrate.io/v3/advanced/consensus/#fork-choice-rules)和可选的[finality](https://docs.substrate.io/v3/getting-started/glossary/#finality)将树修剪成列表

**blockchain**

是一个分布式计算网络，它使用了密码学让参与者就系统的状态随着时间的推移达成共识。组成区块链网络的计算机被称为节点

**拜占庭容错 (byzantine fault tolerance (BFT))**

指的是在一定比例的节点或者[authorities](https://docs.substrate.io/v3/getting-started/glossary/#authority) 不可信或者存在恶意行为时分布式计算机网络仍然能够保持正常运行的能力。通常情况下，如果分布式网络可以在多达三分之一的节点是有缺陷的、离线的、恶意的并且充当攻击的一部分的情况下仍旧能保持正常运行，则该网络被认为具有拜占庭容错性

**byzantine failure**

节点故障超过达成共识所需的节点比例而导致的网络服务不可用

**practical byzantine fault tolerance (pBFT)**

实现拜占庭容错的早期方法，pBFT系统可以容忍多达1/3的参与者不靠谱。此系统的通信开销是n^2，n是系统中节点（参与者的）的数量

**consensus**

在 [区块链](https://docs.substrate.io/v3/getting-started/glossary/#blockchain)上下文中，共识是一个节点就链的规范分叉达成一致的过程。共识由[authorship](https://docs.substrate.io/v3/getting-started/glossary/#author), [finality](https://docs.substrate.io/v3/getting-started/glossary/#finality),和[fork-choice rule](https://docs.substrate.io/v3/advanced/consensus/#fork-choice-rules)组成

在Substrate生态系统中，这三个要素彼此独立，并且共识通常特指authorship。在Substrate节点的上下文中，共识引擎指的是为共识任务担保的节点子系统

另见[hybrid consensus](https://docs.substrate.io/v3/getting-started/glossary/#hybrid-consensus)

**consensus algorithm**

它是一个能确保参与者在彼此不互信的情况下就计算结果的状态达成一致意见的算法。因为大多数共识算法都假定达到1/3的参与者或者节点不诚实网络仍旧能正常运行是拜占庭容错的

共识算法主要关注两个重要的方面：

- 安全性：所有诚实节点最终都同意链的状态

- liveness：链具有持续更新的能力


关于 [Polkadot network](https://docs.substrate.io/v3/getting-started/glossary/#polkadot-network) 共识策略的更多详细信息请参考[Polkadot Consensus](https://polkadot.network/polkadot-consensus-part-1-introduction/)系列博客

也可以了解[hybrid consensus](https://docs.substrate.io/v3/getting-started/glossary/#hybrid-consensus)

**cryptographic primitives**

密码学原语通常是用来描述基本加密概念的术语，如签名方案和哈希算法。密码学原语对于Substrate生态系统中的很多概念来说非常重要。例如：

- 哈希算法产生哈希数据 [blocks](https://docs.substrate.io/v3/getting-started/glossary/#block)，并且每个区块引用它的父区块

- 使用哈希加密 [state](https://docs.substrate.io/v3/getting-started/glossary/#state) 并且构成[trie](https://docs.substrate.io/v3/getting-started/glossary/#trie-patricia-merkle-tree)数据结构，以实现有效验证

- 数字签名方案用来确保不同 [consensus](https://docs.substrate.io/v3/getting-started/glossary/#consensus) 模型的安全，如[authorities](https://docs.substrate.io/v3/getting-started/glossary/#authority)

- 密码学方案用于识别和验证在Substrate运行时执行[交易](https://docs.substrate.io/v3/getting-started/glossary/#transaction)的 [accounts](https://docs.substrate.io/v3/concepts/account-abstractions/)

**council**

如果Collective pallet是网络基于FRAME运行时的一部分，那council大多数情况下指的是基于Substrate网络的Collective pallet实例，如[Kusama](https://docs.substrate.io/v3/getting-started/glossary/#kusama) or [Polkadot](https://docs.substrate.io/v3/getting-started/glossary/#polkadot) 。council主要的工作是优化和平衡更具包容性的公投系统

**database backend**

指的是当区块链[节点](https://docs.substrate.io/v3/getting-started/glossary/#node)应用程序被调用时保持区块链网络[状态](https://docs.substrate.io/v3/getting-started/glossary/#state)的方式，基于Substrate的链如何实现以及使用database backend，请参考 [Advanced storage](https://docs.substrate.io/v3/advanced/storage/)

**dev phrase**

故意公开的助记词，一些常见的用于开发的账户[Alice, Bob, Charlie, Dave, Eve, and Ferdie](https://docs.substrate.io/v3/tools/subkey/#well-known-keys)都来源于相同的助记词，它是：

```
bottom drive obey lake curtain smoke basket hold race lonely fit walk
```

Substrate生态系统中的许多工具，比如 [subkey](https://docs.substrate.io/v3/tools/subkey/),允许用户仅指定路径比如//Alice就可以隐式地确定开发短语

**digest**

区块[头](https://docs.substrate.io/v3/getting-started/glossary/#header) 中的可扩展字段，编码了多个区块链网络中的参与者需要的信息，包括：

- 用于链同步的轻[客户端](https://docs.substrate.io/v3/getting-started/glossary/#light-client)

- 用于区块验证的共识引擎
- pre-runtime摘要就是runtime 本身

**dispatch**

执行带有一组预定义参数的函数。在使用FRAME进行运行时开发的上下文中，dispatch采用纯数据，该类型就是之前所讲的的Call，并且使用该数据用预定义的参数调用运行时模块中已发布的函数，已发布的函数使用了额外的参数，如[origin](https://docs.substrate.io/v3/getting-started/glossary/#origin) 。它允许函数安全的确定其执行的来源

**equivocating**

一种错误或者恶意的行为，涉及在[consensus](https://docs.substrate.io/v3/getting-started/glossary/#consensus)机制中支持多个互斥选项

**ethash**

一些 [proof-of-work](https://docs.substrate.io/v3/getting-started/glossary/#proof-of-work) [consensus](https://docs.substrate.io/v3/getting-started/glossary/#consensus) 系统中所使用的一个功能。比如以太坊区块链。它由Tim Hughes带领的团队开发

**events**

它是一种记录链下世界利益的方法，尤其是一些[state](https://docs.substrate.io/v3/getting-started/glossary/#state) 交易的发生。在FRAME上下文中，events是组合数据类型，每个pallet可以自定义，在F RAME中，events被作为一系列暂存项目执行，在区块执行后会立即检查这些临时存储项，并且在区块初始化的时候重置

**executor**

在给定的运行时中执行函数调用的方法，这些运行时有一系列依赖。Substrate有两个[executor](https://docs.substrate.io/v3/advanced/executor/)实现：WebAssembly 和 native

- native executor 使用嵌入在节点中原生编译的运行时来执行调用，最新的节点可以使用它来优化性能
- WebAssembly executor使用Wasm 二进制文件和Wasm 编译器来执行调用，无论区块链节点的版本如何，二进制文件都是最新的，因为它的更新是基于Substrate链的

**extrinsic**

包含在区块链中的外部数据，通常有两类extrinsics:

- [已签名的](https://docs.substrate.io/v3/concepts/extrinsics/#signed-transactions) 或 [未签名的](https://docs.substrate.io/v3/concepts/extrinsics/#unsigned-transactions) transactions.
- [区块创建者](https://docs.substrate.io/v3/getting-started/glossary/#author)嵌入的在区块中的[inherents](https://docs.substrate.io/v3/concepts/extrinsics/#inherents) 

**existential deposit**

在余额模块中，账户的最低余额。不能创建存款金额最低余额的账户，如果存款金额低于此金额，Balances pallet会使用[FRAME System API](https://docs.substrate.io/rustdocs/latest/frame_system/pallet/struct.Pallet.html#method.dec_ref)丢弃账户的引用，如果一个账户的所有引用都被丢弃，可以重新[获取](https://docs.substrate.io/rustdocs/latest/frame_system/pallet/struct.Pallet.html#method.allow_death)

**finality**

区块链持续进程不可逆转共识的一部分。当一个区块完成后，在不硬分叉的情况下，它封装的所有改变都是不可逆的。共识算法必须保证最后完成的区块不需要逆转，但是不同的共识算法可以定义不同的区块完成方式

在一个使用deterministic finality的共识协议中，协议保证区块链中的每个块都是规范块。在完整的链无法获得的情况下，使用deterministic finality是可取的，例如[轻客户端](https://docs.substrate.io/v3/getting-started/glossary/#light-client)，[GRANDPA](https://docs.substrate.io/v3/getting-started/glossary/#grandpa)是[波卡网络](https://docs.substrate.io/v3/getting-started/glossary/#polkadot-network)使用的deterministic finality协议

在使用probabilistic finality的共识协议中，finality 被用概率术语p表示，由B表示的提议块将保留在规范链中，随着B上区块的增加，p接近1

在使用instant finality的共识协议中，finality在区块产生后立即会得到保证。这种类型的非概率共识倾向于使用[practical byzantine fault tolerance (pBFT)](https://docs.substrate.io/v3/getting-started/glossary/#practical-byzantine-fault-tolerance-pbft)并且它的通信要较高

**fork**

表明区块链可以选择的不同路径，如果两个或者两个以上的区块的父块相同但是它们本身状态却不同，在这个问题没有[解决](https://docs.substrate.io/v3/advanced/consensus/#fork-choice-rules)之前，区块链无法继续更新。分叉不解决将会把链分裂成两条独立的链，但通过解决分叉问题，可以保证只有一条规范的链存在

**Flaming Fir**

一个基于Substrate的区块链测试网络，主要用来开发和测试Substrate区块链开发框架，更多信息请参考Substrate网络和Flaming Fir， [Polkadot wiki](https://wiki.polkadot.network/docs/maintain-networks#flaming-fir)

**FRAME**

它是Framework for Runtime Aggregation of Modularized Entities的首字母缩写，可以让开发者用一系列叫做 pallets的组件创建区块链运行时环境

运行时开发人员可以使用像下面的宏来与FRAME交互

- \#[pallet::event],
- \#[pallet::error],
- \#[pallet::storage],
- \#[frame_support::pallet]

宏让定义个性化的pallet变得很容易。可以使用[construct_runtime!](https://docs.substrate.io/v3/runtime/macros/#construct_runtime)宏构建pallet以创建可用的运行时并且将其部署在基于Substrate的区块链上

在[the Substrate codebase](https://github.com/paritytech/substrate/tree/master/frame)中的使用惯例是在核心模块前面加上pallet_*。例如之前的宏都定义在[frame_support](https://docs.substrate.io/v3/runtime/frame/#support-library)模块中并且所有基于FRAME的运行时必须包含在[frame_system](https://docs.substrate.io/v3/runtime/frame/#system-library)模块中，在frame_support::construct_runtime宏被用作来创建包含frame_system模块的运行时后，可以使用其它pallets比如Balances pallet来扩展运行时的核心功能

**full client**

一个可以以安全的方式同步区块链的节点，这种安全的方式是执行和验证所有逻辑，全节点客户端和 [轻客户端](https://docs.substrate.io/v3/getting-started/glossary/#light-client)正好相反

**genesis configuration**

指定区块链初始状态的一种机制，初始状态或者第一个区块通常被称为创世状态或者创世区块。基于Substrate的链的genesis configuration是通过[chain specification](https://docs.substrate.io/v3/runtime/chain-specs/) 文件完成的，chain specification文件让使用单个的Substrate 代码库作为多个独立配置的链变得很容易

**GRANDPA**

区块链的 [deterministic finality](https://docs.substrate.io/v3/getting-started/glossary/#deterministic-finality)机制，由Rust语言来实现，The [formal specification](https://github.com/w3f/consensus/blob/master/pdf/grandpa-old.pdf)由[Web3基金会](https://web3.foundation/)维护

**header**

汇总区块信息的结构，header主要包含轻客户端使用的加密信息，它虽然获得是最小的安全性，但能够实现链的高效同步

**hybrid consensus**

它是一个共识协议，由用于生成区块和确定finality的独立或者耦合的机制构成，Hybrid consensus既可以让链像概率共识协议一样快速发展，比如 [Aura](https://docs.substrate.io/v3/getting-started/glossary/#aura-aka-authority-round)，又可以让链保持像[deterministic finality](https://docs.substrate.io/v3/getting-started/glossary/#deterministic-finality) 共识协议那样的安全性,比如[GRANDPA](https://docs.substrate.io/v3/getting-started/glossary/#grandpa).通常而言，区块生成算法比 finality 机制更快。将区块的生成和最终化分开让Substrate 开发者可以更好的控制其链的性能

**JSON-RPC**

以JASON格式编码的无状态的、轻量级的远程调用协议。JSON-RPC提供了一种使用JavaScript 对象符号的标准方式在远程系统上调用函数，对于Substrate，这个协议通过 [Parity JSON-RPC](https://github.com/paritytech/jsonrpc) 库实现

**keystore**

它是Substrate上的一个子系统，用于管理用来产生新区块的健

**Kusama**

[Kusama](https://kusama.network/)是一条基于Substrate的区块链，它采用了和波卡网络相似的设计。它是一个 *[canary](https://en.wiktionary.org/wiki/canary_in_a_coal_mine)*网络，被称为波卡网络的”野表亲“， 作为canary网络，Kusama预计会比像Westend这样的测试网络更加稳定，但是不会像波卡这样的生产网络稳定

作为canary网络，Kusama由它的[网络参与者](https://docs.substrate.io/v3/runtime/frame/#democracy)控制，主要为了让其变得更稳定以促进有意义的实验

**libp2p**

一个点对点的网络堆栈，允许使用多种传输机制，包括WebSockets（在Web浏览器中使用）。Substrate使用的是**libp2p**网络堆栈的Rust实现

**light client**

一种不会存储链状态和产生区块的区块链节点。一个轻客户端能够验证加密原语并暴露一个远程调用（RPC）服务器，能够让区块链用户和区块链进行交互

**macro**

编程语言的一种特性，能够让开发者编写一系列可以一起被命名和执行的命令。FRAME开发环境为Rust提供了几种[宏](https://docs.substrate.io/v3/runtime/macros/)，可以用来编译一个运行时

**metadata**

提供了关于系统多个方面的信息，metadata展示了关于Substrate 区块链的信息，它能够让你和系统交互

**node**

区块链客户端运行的实例，每个客户端都是对等网络的一部分，它能够让区块链参与者彼此交流，Substrate节点可以在区块链网络中扮演很多角色，比如，产生区块的节点可以充当区块链验证者的角色。运行轻客户端的节点能够在资源受限制的环境中如 [user interfaces](https://github.com/paritytech/substrate-connect)或在嵌入式设备中促进交互

**nominated proof-of-stake (NPoS)**

一个确定[validators](https://docs.substrate.io/v3/getting-started/glossary/#validator) or *[authorities](https://docs.substrate.io/v3/getting-started/glossary/#authority)* 的方式，基于他们stake的意愿，这能够维持一个或者多个区块链节点正常运行

**origin**

一种FRAME原语，用来标记调用进运行时的函数来源，FRAME系统模块定义了[三个内置的来源](https://docs.substrate.io/v3/runtime/origins/#raw-origins)来源，作为一个pallet开发者，你可以自定义origin，就像在[Collective pallet](https://docs.substrate.io/rustdocs/latest/pallet_collective/enum.RawOrigin.html)定义的那些origin一样

**pallet**

一个可用于扩展基于FRAME运行时的能力的模块，它和特定领域的逻辑和运行时原语绑定在一起，比如[events](https://docs.substrate.io/v3/getting-started/glossary/#event)和[storage items](https://docs.substrate.io/v3/getting-started/glossary/#storage-items)

**parachain**

平行链，它从[中继链](https://docs.substrate.io/v3/getting-started/glossary/#relay-chain)派生而来，并且与之共享安全性和基础设施，你可以通过波卡[维基百科](https://wiki.polkadot.network/docs/learn-parachains)了解更多与之相关的信息

**Polkadot networ****k**

波卡是一个区块链网络，充当异构区块链网络的中心枢纽，作为中继链，它通过提供基础设施和安全性来支持其它链—如平行链

**proof-of-finality**

可用于证明特定区块已完成的数据

**proof-of-work**

一种共识机制，通过要求网络参与者的工作量来阻止攻击。例如，一些工作量证明系统要求参与者使用Ethash函数计算一个哈希值作为已完成工作量的证明

**relay chain**

多个区块链异构网络的中心枢纽，中继链是在网络中为其它链提供基础设施和安全性的区块链，尤其是为了提供共识，中继链可以让平行链相互交流和交换数字资产而无需彼此信任

**remote procedure call (RPC)**

一种和计算机程序交互的机制，remote procedure call能够让开发者请求远程计算机程序和用他们提供的参数调用程序逻辑，Substrate节点在HTTP和WebSocket端点上暴露了一个RPC服务器

**rhododendron**

即instant finality，byzantine fault tolerant (BFT) 共识算法，众多采用pBFT共识协议的区块链采用的共识算法之一，请参考它在GitHub上的实现

**rococo**

一个波卡网络平行链测试网络，基于Substrate，它还在不断地发展，用于测试异构区块链网络的性能

**runtime**

区块链中区块的执行逻辑，运行时为节点提供了状态转换功能，在Substrate中，在连状态中运行时被作为ebAssembly二进制文件存储

**slot**

像Aura 和 BABE等共识引擎使用固定的、相等的时间间隔。在每个插槽中， 对于创建区块可以或者必须创建权限子集

**stake-weighted voting**

一种民主投票系统，每个Token一票而不是一人一票

**state**

在块与块之间长久存在的加密数据，作为块状态转换函数的一部分，可以用来创建新区块。在以Substrate为基础的区块链中，状态存放在支持高效创建增量摘要的trie数据结构中。这个trie作为一种简单的键值map暴露给了运行时，其中健和值都可以是任意字节数组

**state transition function (STF)**

区块链的逻辑决定了在处理区块时state会如何变化。在Substrate中，状态转换函数实际上等同于运行时

**storage item**

FRAME primitives为运行时提供了类型安全数据持久性化的能力。请在runtime storage中了解更多关于storage item的信息

**Substrate**

用于构建模块化、高效和可升级区块链的灵活框架，它由Rust语言构建，由Parity Technologies维护

**transaction**

一种可以在网络节点间安全传播的extrinsic，可以被签名和签名扩展来验证

**transaction era**

一段被定义的时间，用于描述一系列区块的数字。在此期间，交易包含在区块中，transaction eras用来防止交易重复攻击，这种情况下账户会被重置，防止双重支付的随机数会被重置为0

**transaction pool**

一系列虽然还没包括在区块中但是已经被认定有效的交易集合。标记交易池是一个交易池的实现。它允许运行时指定给定的交易是否有效、应该如何确定它的优先级以及它与池中的其他事务在依赖性和互斥性方面的关系。标记交易池实现被设计为可扩展的和通用的，足以表达未花费的交易支出UTXO模型和基于账户的交易模型

**trie (Patricia Merkle Tree)**

一种表示键值对的数据结构

The Patricia Merkle trie数据结构能够让使用加密哈希存储和检索数据组合中的项目。由于数据集中增加的改变会产生新的哈希，所以即使数据集非常大，数据的检索也非常的有效，通过使用这种方式，你可以不用检索整个数据集就能证明数据集是否包含了特定的键值对

**validator**

帮助维护区块链网络的半可信或者不可信的参与者，他们在激励机制下会做出有益于整个网络的行为，在Substrate中，validators 大致等同于运行在共识系统中的authorities，在polkadot中，validators 也管理着一些其它事务，比如保证数据的可用性和验证平行链的候选区块

**WebAssembly (Wasm)**

一种执行架构，可以高效、跨平台的表示确定的、机器可以执行的逻辑。WebAssembly 可以用很多语言编译，包括Rust语言，基于Substrate的区块链使用WebAssembly 二进制文件提供可移植的运行时，这些运行时可以作为链状态的一部分

**Westend**

一个由 Parity维护的，基于Substrate的区块链，并且作为一个测试网络服务于波卡网络

# 2.主要概念

## 2.1 运行时

**Runtime**

区块链的运行时是定义它自身行为的商业逻辑。在基于Substrate的链中，运行时指的是”[state transition function](https://docs.substrate.io/v3/getting-started/glossary/#state-transition-function-stf)"；运行时是一个Substrate开发者定义storage items的地方，这些storage items通常代表区块链的状态和功能，并且区块链用户可以改变这些状态

![substrate-runtime-client.png](https://docs.substrate.io/static/165c3188f678dcd2cbd3999cbab04b7c/ea64c/substrate-runtime-client.png)

每个Substrate节点都包含运行时，运行时包含了链的商业逻辑。它定义了什么交易是有效的，什么交易是无效的，也决定了链的状态是如何响应交易的，外部节点，除了运行时之外的所有东西，都不会编译为Wasm，只会编译为原生，外部节点负责处理对等的发现、交易池、区块和交易传递、共识以及响应外界的RPC调用。在执行这些任务时，外部节点有时候需要向运行时查询信息，或者向运行时提供信息。外部节点和运行时之间的交流通过runtime API实现

**Runtime APIs**

在Substrate中，sp_api crate 提供了一个接口实现运行时API。它的设计让开发者能够使用[impl_runtime_apis](https://docs.substrate.io/rustdocs/latest/sp_api/macro.impl_runtime_apis.html) macro定义个性化的运行时API.但是每个运行时必须实现[Core](https://docs.substrate.io/rustdocs/latest/sp_api/trait.Core.html) 和 [Metadata](https://docs.substrate.io/rustdocs/latest/sp_api/trait.Metadata.html) 运行时 APIs。除了这些之外，一个基本的Substrate节点还实现了以下运行时API

[BlockBuilder](https://docs.substrate.io/rustdocs/latest/sp_block_builder/trait.BlockBuilder.html)：提供了创建区块的功能

[TaggedTransactionQueue](https://docs.substrate.io/rustdocs/latest/sp_transaction_pool/runtime_api/trait.TaggedTransactionQueue.html)：处理交易队列中的验证交易

[OffchainWorkerApi](https://docs.substrate.io/rustdocs/latest/sp_offchain/trait.OffchainWorkerApi.html)：处理[链下事项](https://docs.substrate.io/v3/concepts/off-chain-features/)

[AuraApi](https://docs.substrate.io/rustdocs/latest/sp_consensus_aura/trait.AuraApi.html)：使用[Aura consensus](https://docs.substrate.io/v3/advanced/consensus/#aura)处理区块权限

[SessionKeys](https://docs.substrate.io/rustdocs/latest/sp_session/trait.SessionKeys.html)：生成和解码[会话密钥](https://docs.substrate.io/v3/concepts/session-keys/)

[GrandpaApi](https://docs.substrate.io/rustdocs/latest/sp_finality_grandpa/trait.GrandpaApi.html):集成[GRANDPA](https://docs.substrate.io/v3/advanced/consensus/#grandpa) finality小工具至运行时中

[AccountNonceApi](https://docs.substrate.io/rustdocs/latest/frame_system_rpc_runtime_api/trait.AccountNonceApi.html)：处理查询事务索引

[TransactionPaymentApi](https://docs.substrate.io/rustdocs/latest/pallet_transaction_payment_rpc_runtime_api/trait.TransactionPaymentApi.html): 处理交易的查询信息

[Benchmark](https://docs.substrate.io/rustdocs/latest/frame_benchmarking/trait.Benchmark.html): 提供一种测试FRAME 运行时的方法

为了提供其定义的无分叉运行时升级的功能，Substrate运行时被构建为[WebAssembly (Wasm)](https://docs.substrate.io/v3/getting-started/glossary/#webassembly-wasm) 字节码，Substrate也定义了运行时必须执行的核心原语

**Core primitives**

Substrate框架对运行时必须向其他层提供的项目做了最少的要求。但是仍旧有一些数据类型需要被定义和满足特定的接口才能够在Substrate框架内实现

它们是：

Hash：一种对一些数据的加密摘要编码的类型，通常是256bit

DigestItem：一种必须能够编码众多硬线替代方案之一的类型，这些方案与共识和改变追踪相关，也和一些软编码的变量有关，而这些变量又和运行时中具体的模块相关

Dingest：一系列DigestItems，这编码了与轻客户端相关的所有信息，以便在块内拥有

Extrinsic：一种类型，代表区块链外部的单个数据片，它会被区块链识别。它通常会涉及一到多个签名和一组编码指令（例如，用于转移资金所有权或者调用智能合约）

Header：代表与块相关的所有信息类型（加密或其它方式），包括父节点的哈希、存储根和extrinsics trie 根、摘要和区块编号

Block：本质上是Header和和一系列 Extrinsics的结合，是一个哈希算法使用实例

BlockNumber：一种对所有有效区块进行编号的类型，通常为 32 位

**FRAME primitives**

核心Substrate代码库附带FRAME，它是Parity用于开发Substrate运行时的系统，这些运行时被像 [Kusama](https://github.com/paritytech/polkadot/blob/master/runtime/kusama/src/lib.rs) 和[Polkadot](https://github.com/paritytech/polkadot/blob/master/runtime/polkadot/src/lib.rs)这样的链使用。FRAME定义了额外的运行时原语和提供了一个框架，者能够轻易的通过组合模块（又称pallets）来构建运行时。每个pallet都封装了特定域的逻辑，这些逻辑被表示为一系列存储项目、事件、错误和可调用函数。FRAME开发者可以创建他们自己的pallet和重复使用现有的pallets，这包超过50个Substrate附带的pallets

![Runtime Composition](https://docs.substrate.io/static/64b2fcb61748ae77f4dd4c9ce63872b1/c1b63/frame-runtime.png)

还有一些别的原语假设运行时是以Substrate 框架构建的，它们是：

Call：可以通过外部调用的调度类型

Origin：代表调用来源。例如签名信息（一笔交易），未签名的信息（an inherent extrinsic）或者来自于运行时本身的调用（一个根调用）

Index：账户索引（又名随机数），存储了与发送者账户相关的之前交易的数量

Hashing：运行时中使用的哈希算法（例如Blake2）

AccountId：在运行时中用于表示账户类型

Event：在运行时中出现的事件类型

Version：代表运行时版本的类型

尽管FRAME及其相关的原语可以用于运行时的开发， 但是FRAME并不是开发基于Substrate区块链的唯一系统

**Next steps**

了解更多

- 了解更多关于[Substrate FRAME](https://docs.substrate.io/v3/runtime/frame/)


- 参考[tutorial to develop your first Substrate chain](https://docs.substrate.io/tutorials/v3/create-your-first-substrate-chain/)

- 参考a [tutorial to add a pallet to your Substrate runtime](https://docs.substrate.io/tutorials/v3/add-a-pallet/)


**References**

-了解 [primitive types defined in `node-primitives`](https://docs.substrate.io/rustdocs/latest/node_primitives/index.html)

-了解 [traits defined in sp-runtime](https://docs.substrate.io/rustdocs/latest/sp_runtime/traits/index.html)

## 2.2 Extrinsics

Extrinsic 是包含在区块中的链外信息，extrinsics 分为三类，inherents,签名交易和未签名的交易

注意：events不是extrinsics，是链本身发出的事件。例如，质押奖励是事件，不是extrinsics，因为奖励是由链内部的循环逻辑引发的

**Block structure**

在Substrate中的区块是由header和一组extrinsics,header包含区块高度，前一个节点的哈希，extrinsics root，状态root和摘要。本节点只会关注 extrinsics root

Extrinsics被绑定在一个区块中，是一系列要执行的事件，被定义在运行时中。extrinsics root是这些extrinsics的加密摘要。它服务于两个目标，首先，在header被构建和发布之后，阻止对于这些extrinsics的改变。第二，它提供了一种方式，可以让轻客户端在只知道区块头的情况下就能够轻易地验证任何给定的extrinsic是否的确存在于区块中

- [Block Reference](https://docs.substrate.io/rustdocs/latest/sp_runtime/traits/trait.Block.html)

**Inherents**

Inherent是一片没有被签名的、只是被块作者嵌入进区块中的信息片段。它们不会在网络上传播或者存储在交易队列中。从技术上讲，没有什么能够组织在Substrate链上传播inherents，但是不会有基于费用的垃圾邮件预防机制

Inherents虽然是用自定义的方式去描述的信息，但仍然是有效的，它们被认为是可靠的，因为已经有足够多的验证者同意它们是合理的

举个例子，区块作者可能会在区块中嵌入一个时间戳inherent。没有办法证明时间戳是真实的，就像用签名证明发送资金一样。相反，验证者根据通过其它验证者发现时间戳的合理程度来拒绝或者接受块。这意味着时间戳在他们自己时钟系统里是在接受范围内的

- [Inherents Reference](https://paritytech.github.io/substrate/master/sp_inherents/index.html)

**Signed transactions**

签名交易包含了账户的签名，这个账户发出交易并且需要支付一定的费用才能将交易包含在链上。因为可以在执行之前识别在链上包含签名交易的价值。所以他们可以在网络中的节点上传播，并且风险很低、

签名交易概念在以太坊和比特币中仍然适用

**Unsigned transactions**

有些情况需要未签名的交易，请谨慎使用未签名的交易，因为它们的验证逻辑非常难

因为交易未被签署，因此无需支付费用。因为没有费用，所以交易队列也没有经济逻辑阻止作恶，未签名的交易也缺少随机数，这也让避免重复交易变的困难。很少交易使用未签名的变体，但是它们需要某种形式的基于签名扩展的自定义实现对作恶的预防，这可以存储在未签名的交易中

在Substrate中未签名交易的一个例子是authorities发送 [I'm Online](https://docs.substrate.io/v3/runtime/frame/#im-online) heartbeat 交易。交易包含来自会话的签名，该密钥不控制资金因此也无法支付费用，交易池会通过在会话中检查 heartbeat是否已经被提交来预防作恶

**Further reading**

- [Reference Documentation](https://docs.substrate.io/rustdocs/latest/sp_runtime/traits/trait.Extrinsic.html)
- [Runtime Execution](https://docs.substrate.io/v3/concepts/execution/)
- [Transaction Fees](https://docs.substrate.io/v3/runtime/weights-and-fees/)
- [Transaction Pool](https://docs.substrate.io/v3/concepts/tx-pool/)

## 2.3 账户抽象

Substrate使用多组公钥和私钥对来代表网络中的参与者，包括验证者、提名者和普通用户。例如，Substrate节点使用提名权益证明算法选择验证人，验证人和提名人可能持有大量的资金，因此Substrate质押pallet引入了账户抽象，以尽可能地保证资金安全

这些抽象是：

Stash Key：Stash账户用于存放大量资金，它的私钥在冷钱包中以尽可能地保证安全

Controller Key: 控制账户代表Stash账户发出信号，例如支付偏好，但是只持有非常少的资金以支付交易费，它的私钥必须是安全的因为它会影响验证人的设置，定期使用它来维护验证人

Session Keys：这是保存在验证器中的热密钥，常用于签署某些验证人的操作，它们不会持有资金

了解更多更多关于Substrate提名权益共识的内容中的验证人和提名人的信息，请参考Staking pallet

**Account keys**

一个密钥对可以代表账户和控制资金，就像其它区块链中的普通账户一样，在Substrate的Balance pallet的上下文中，这些账户必须有一个最小的金额（现有存款）

账户密钥定义是概括的，在运行时中会具体化

继续我们的Stash 和 Controller账户示例，这些账户的密钥随着它们的使用目的而有所区别，而不是底层加密技术的差异。所有可以是普通账户密钥对的都可以作为Stash和Controller 密钥

**Stash keys**

Stash 密钥是定义Stash账户的公钥、私钥对，这种账户像“Saving account”，你不应该频繁的使用它进行交易。因此，它的私钥应该被以最大的安全性来对待。比如保存在保险箱或者硬件中

因为Stash密钥保持离线状态，所以它指定了一个Controller账户使用Stash 账户的资金权重做出非支出性决策。它也可以指定一个代理账户在治理中代表投票

**Controller keys**

Controller 密钥是公钥/私钥对用来定义Controller账户。在Substrate的提名权益证明模型的上下文中，Controller密钥将会表明验证者人或者提名人的意图

Controller密钥用来设置像奖励目的等偏好，就验证人而言，是用来设置会话密钥的。Controller账户需要交易费，但是交易费用很少

Controller密钥永远不能用于从其Stash账户中花费资金。但是Controller的行为会导致Stash账户金额削减, 因此仍然需要被安全的保护

**Next steps**

了解更多

- 了解更多会话密钥this article
- 了解Substrate中的密码学https://docs.substrate.io/v3/advanced/cryptography/

例子

- 参考tutorial to create a local network and generate keys

参考

- 参考文档session keys runtime API
- 查看session keys in the Substrate node runtime

## 2.4 交易池

交易池包含了广播到整个网络的所有交易（签名的和未签名的），它们都已被本地节点接收和验证

**Validity**

交易池会检查交易的有效性。注意，交易的有效性的验证不是直连到交易池的，而是在运行时中被定义。如下是有效性检查的示例：

- 检查交易索引（随机数）是否正确

- 检查账户是否有足够的金额支付相关费用

- 检查签名是否有效


交易池也会定期检查已有交易的有效性。一旦发现交易无效或者过期则会将它从交易池中删除

[validate_transaction](https://docs.substrate.io/rustdocs/latest/sp_transaction_pool/runtime_api/trait.TaggedTransactionQueue.html#method.validate_transaction)被从运行时中调用以检查签名和随机数是否有效（或UTXO链的输出）并返回结果。它是单独执行的，因此它不会捕获错误，例如双重支付

尽管它是有可能的，validate_transaction不会检查对pallet的调用是否会成功，它是一个潜在的Dos vector，因为网络中的所有交易都会发送给validate_transaction

validate_transaction函数应该专注于为交易池提供必要信息来对交易进行排序和优先级处理，并且快速拒绝无效或过期交易。函数会被频繁的调用，同一笔交易可能会执行多次。validate_transaction验证一笔单独的交易可能会失败，如果该交易以正确的顺序执行，它会被传递给execute_block

**Sorting**

如果交易有效，交易队列将会分为两组：

Ready Queue ：包含了可以被打包进新区块的交易，对于用FRAME创建的运行时而言，交易必须按照就绪队列中的确切顺序

Future Queue：包含了将来可能是有效的交易。举个例子，一笔交易可能有一个对于账户来说过高的随机数。该交易会在队列中等待直到前面的交易都被包含在链中

尽管可以通过设计一个自定义的运行时去移除严格的交易队列等待要求。这将允许全节点在交易广播和区块打包方面执行不同的策略

**Transaction dependencies**

[ValidTransaction struct](https://docs.substrate.io/rustdocs/latest/sp_runtime/transaction_validity/struct.ValidTransaction.html)定义了要求的参数和提供的参数形式并构建了交易依赖关系图，连同优先级一起可以让交易池产生有效的线性交易序列

对于用FRAME构建的运行时，节点以账户为基础的系统对交易进行排序。每个签名交易都需要包含一个随机数。随着新的交易完成，这个随机数会增加1。例如一个新账户的第一笔交易随机数是0第二笔交易的随机数是1

FRAME交易至少有一个`encode(sender ++ nonce)` 提供参数标签和一个 `encode(sender ++ (nonce -1)) if nonce > 0`需求参数标签. 如果随机数是0，交易没有任何要求。因此，所有的交易都来自单个发送者，并且它们会形成一个交易队列

Substrate支持多个提供参数和需求参数标签，因此自定义的运行时可以创建可选的依赖（排序）方案

**Transaction priority**

在ValidTransaction 结构中的交易优先级决定了就绪队列中交易的顺序。如果一个节点是下一个块的作者，它将会在下一个区块中对交易从高到低按优先级排序，并且直到区块的大小或者长度限制

在多个依赖交易未锁定的情况下priority 定义了图的线性顺序。例如我们有两起交易并且它们的依赖都满足，那我们会优先为它们选择顺序

使用FRAME构建的运行时，priority被定义为交易将要支付的费用，如：

- 如果我们从不同的发送者要接收两笔交易（随机数=0），我们可以决定哪笔交易更重要并且把它先放入区块中

- 如果我们从相同的发送者那里收到两笔nonce一样的交易，那么只有一笔能够被包含在链中。我们可以选择交易费更高的交易并且把它存储在交易池中


注意：交易池并不知道费用，账户和签名。它只处理交易的有效性和优先级、需求和供给参数的抽象概念。其它细节是运行时通过validate_transaction函数定义的

**Transaction lifecycle**

一笔交易可以按照如下两条路径：

区块通过节点产生：

1.节点会监听网络中的交易

2.每笔交易都会被确认并且有效的交易会被存放在交易池中

3.交易池负责对交易进行排序并且返回可以被包含在区块中的就绪交易，在就绪队列中的区块通常会用来构建区块

4.交易的执行和状态改变会存储在本地内存中。就绪队列中的交易会被广播到整个网络中。当区块的产生因为队列前面的交易优先级更高并且有着更高的可能性被包含在区块中而等待，我们会使用准确的顺序

5.新建构好的区块会发布到整个网络，网络中所有节点都能够接收和执行区块

注意：在创建区块时，交易不会从就绪队列中移除，只有在块导入时才会删除，这是因为最近创建的区块可能不会包含在合法链上

**Block received from network**

某个块被执行时，要么执行成功要么执行失败

**Signed extensions**

[SignedExtension](https://docs.substrate.io/rustdocs/latest/sp_runtime/traits/trait.SignedExtension.html)是一种一项交易可以用额外数据或者逻辑延展的trait。在交易执行之前,Signed extensions可以用在任地方，它可以让你获取到相关信息，它在交易池中大量的被使用

运行时可以使用一些这部分的数据，例如Call会被调度，用来计算交易费用 . Signed extension也包括了可以持有任何可编码数据的AdditionalSigned类型，因此这可以让你在包括或者调用一次交易之前执行自定义的逻辑。交易队列会周期性的调用来自于SignedExtension的函数在区块创建之间验证交易以避免区块包含失败交易

尽管名字是SignedExtension，但它也可以用来验证未签名的交易，*_unsigned方法集可以封装交易池所需的验证、作恶和重复支付保护逻辑

- [Signed Extension API](https://docs.substrate.io/rustdocs/latest/sp_runtime/traits/trait.SignedExtension.html)

**更多内容**

- [Extrinsics](https://docs.substrate.io/v3/concepts/extrinsics/)
- [Transaction Fees](https://docs.substrate.io/v3/runtime/weights-and-fees/)

## 2.5 Session Keys

Substrate提供了 Session pallet，验证者可以使用它来管理Session Keys

Session Keys是热密钥，验证人通常使用它签署与共识相关的信息

它们不应该用于管理资金账户，而应该适用于特定的目的。它们可以被定期地改变。你的Controller只需要通过签署会话公共密钥并且通过extrinsic广播这个证书就可以创建新的会话密钥

会话密钥也被通用的定义并在运行时中被具体化

为了创建一个会话密钥，验证操作者应该证明密钥的行为代表Stash账户和提名人。为此，他们通过用他们的Controller密钥签署来创建新的密钥，接着他们通过在链上交易中发布会话密钥告知链这个密钥代表了他们新的会话密钥

**Implementation**

会话密钥通常被验证人用来签署与共识相关的信息。SessionKeys是一个通用的、可索引的类型并会在运行时中被具体化

你可以声明任何数量的会话密钥，例如，默认的substrate节点使用四个。其它链可能会更多也可能更少，这取决于链期望它的的验证者执行什么样的操作

在实际中，验证者会将所有会话公共密钥合并为一个对象，用Controller账户签署一系列公共密钥，并在链上通过提交交易注册密钥

链上注册链接了一个验证节点，这个节点有一个持有资金的账户，因此，该账户基于节点的行为会获得奖励和惩罚

运行时声明了会话密钥，并且在宏下执行，例如：

```
impl_opaque_keys! {
  pub struct SessionKeys {
      pub grandpa: Grandpa,
      pub babe: Babe,
      pub im_online: ImOnline,
      pub authority_discovery: AuthorityDiscovery,
  }
}
```

用于获取每个密钥的加密曲线在都定义在原语中，如BABE's key uses sr25519

```
mod app {
    use sp_application_crypto::{app_crypto, key_types::BABE, sr25519};
    app_crypto!(sr25519, BABE);
}
```

通常，这些密钥在初始状态中就被预先配置，以使用预先设置的验证器启动链。你可以在private network tutorial中看到这一点

**Strongly typed wrappers**

尽管来自于Substrate节点使用了相同的加密技术，但是在运行时逻辑中服务的目标却不同。为了阻止错误的密钥用于错误的操作，强大的Rust类型封装了这些密钥，使它们彼此不兼容，确保只用于特定的目的

**Generation and use**

作为一个节点操作者，你可以使用RPC 调用author_rotateKeys来生成密钥，接着你需要在链上使用session.setKeys注册一个新的密钥

注意事项：

为了增加安全性，每个会话的会话密钥都应该被更新，这可以通过创建新的会话密钥来完成，把这个会话公共密钥放入 extrinsic,并在链上申请这个 extrinsic

因为会话密钥时必须保持在线的热密钥，个人密钥不应该用于控制资金。所有处理会话密钥的逻辑都在Substrate客户端、原语和Session Pallet中。如果Session keys其中之一泄露，攻击者就可能盗走资金

**Examples**

Follow our tutorial to create a local network and generate keys

**References**

- 查阅参考文档 session keys runtime API
- 查看默认session keys in the Substrate node runtime
- 查看sp_application_crypto，crate，用于构建特定应用的强类型加密封装

## 2.6 Transaction Weight

对于链来说，资源是有限的。这些资源包括内存使用、存储I/O、计算、交易区块的大小和状态数据库大小。Substrate提供了几种不同的机制去管理对资源的访问，以防止链上的单个组件过多的占用某种资源，Weight用来管理验证区块的时间。通常而言，这受限于存储 I/O和计算

```
注意：
Weight不限制对其它资源的访问，比如存储本身或内存占用。为此必须实现其它机制
```

一个区块大小是有限的，一个可选的Weight消耗通常通过经济措施来限制（例如weight既不需要作为区块的初始化或者完成的一部分被部署也不需要在inherent extrinsics强制使用）。或者简单而言，通过交易费用限制。Weight系统费用的影响被包含在[交易费用文件中](https://docs.substrate.io/v3/runtime/weights-and-fees/)

在固定参考硬件上（Intel Core i7-7700K CPU with 64GB of RAM and an NVMe SSD）, Substrate定义Weight单元为1picosecond的执行时间。10的12次方picosecond=1s,或者1000weight = 1nanosecond

基于参考硬件的基准测试使weight在运行时之间具有可比性，这让不同来源的软件原件有了组合性。为调整运行时以适应不同的硬件要求，你可以设置不同的最大的区块权重。例如，为了让验证人参与的速度只有参考机器速度的一半，最大块的weight应该设置为默认值的一半，保持默认的块时间

最大块weight应该等于目标块时间的三分之一，分配三分之一用于块构建、三分之一用于网络传播、三分之一用于导入和验证。将区块时间加倍会让最大区块weight加倍。这些调整选项给运行时开发者提供了一个方法，可以针对他们的用例平衡每秒最佳交易和硬件需求。这些平衡可以通过运行时的更新进行调整，以匹配硬件和软件更新

**Weight fundamentals**

weight代表区块链验证区块的有效时间。这包括计算周期和存储I/O。自定义实现使用复杂的结构描述这一点，Substrate weight是一个简单的[numeric value](https://docs.substrate.io/rustdocs/latest/frame_support/weights/type.Weight.html)

weight计算应该：

- 在调度之前计算，区块生产者应该在接受与否之前检查可调度的weight

- 本身消耗很少的资源，当花费费用使用它时再去计算交易weight就没必要了。因此weight计算量应该比调度小的多

- 无需咨询链上状态即可确定使用的资源。weight比较适用于表示固定的测量值或者仅基于可调度函数的参数，不需要昂贵的I/O。如当花费取决于链的状态时，weight就没那么有用了


在可调度的weight严重依赖链的状态的情况下，有两种选择：

- 确定或者引入一个调度能够承受的weight的强制上限。如果调度能够承受的weight的强制上限和下限之间的差异很小，那么可以视作总是处于weight上限而无需咨询状态。但是如果差异很大的话，交易量小时交易成本可能会很大，这将扭曲激励机制并且会导致较低的吞吐量

- 要求将有效的weight作为参数传递给调度（或者precursors可用于有效的计算）。weight支付不仅应该基于这些参数还应该基于在调度时确认所花费的时间。必须执行验证以确保weighing参数正确的响应了链的状态，如果不正确，则操作应该出错


**Weight factors**

几个因素会影响执行时间，从而影响权重计算。一个很大的原因是执行调度访问数据库的次数。因为访问数据库的成本主要取决于数据库的后端和存储硬件，因此weight在数据库的读写上计算是参数化的。这些成本取决于参考硬件上可用数据库的基准测试，这允许在不改变权重计算的情况下切换数据库后端

除了仅使用常量进行预调度weight计算外，开发者还可以考虑给给定的调度输入参数。例如，当执行时间取决于一个参数的长度时非常有用，重要的是这些计算本身意义不大。预调用最大weight应该通过一些基本算法从输入参数中轻松的计算出来

The [System pallet ](https://docs.substrate.io/rustdocs/latest/frame_system/pallet/index.html)负责累加每个块的weight以确保没有超过限制，[Transaction Payment pallet](https://docs.substrate.io/rustdocs/latest/pallet_transaction_payment/index.html)负责解释这些weights和基于它们扣费。The weighing 功能是运行时的一部分，因此如果需要，它可以升级

**Post dispatch weight correction**

在一些情况下，调度的weight无法从它的输入计算出来。举个例子，weight可能依赖调度的逻辑路径。我们会高估并在随后收取这些调度更多的费用，因为我们在此之前必须假设最坏的调度情况以确保链的安全

预调度weight纠正可以让任何调度在执行后都返回实际weight。这个weight必须小于或者等于预调度最坏情况的weight。对于允许包含extrinsic的用户，他们仍然必须能够支付最大的weight，尽管最终的支付费用是基于实际的weight

**Block weight and length limit**

除了影响费用之外，weight系统的主要目的是阻止区块被过多交易填满以至于太长而不能执行。当区块中的交易执行时，系统pallet会累加区块长度（用字节编码的交易总数）和区块weight。如果这些数字中的任何一个超出了限制，区块就不能接收新的交易了，这些限制被定义在[`MaximumBlockLength`](https://docs.substrate.io/rustdocs/latest/frame_system/limits/struct.BlockLength.html)和[`MaximumBlockWeight`](https://docs.substrate.io/rustdocs/latest/frame_system/limits/struct.BlockWeights.html)中

关于这些限制的一个重要的说明是它们之中的一部分是为Operational调度类保留的。这个规则适用于限制和比率，可以在[`AvailableBlockRatio`](https://docs.substrate.io/rustdocs/latest/frame_system/limits/struct.BlockLength.html#method.max_with_normal_ratio)查看它们

举个例子，如果块长度限制为1兆字节并且比率设置为80%，那么所有的交易可以填充块的前800千字节，后200千字节只能由操作类填充

还有一个Mandatory 调度类可以用于确保extrinsic始终包含在块中，无论其对weight的影响如何。请参考文章[Transaction Fees](https://docs.substrate.io/v3/runtime/weights-and-fees/) 学习更多不同的调度类和什么时候使用它们

学习更多

- [Example](https://github.com/paritytech/substrate/tree/master/frame/examples/basic) pallet
- [Transaction Payment pallet](https://github.com/paritytech/substrate/blob/master/frame/transaction-payment/src/lib.rs)
- [Weights](https://github.com/paritytech/substrate/blob/master/frame/support/src/weights.rs)

## 2.7 执行

Substrate运行时的执行由[Executive module](https://docs.substrate.io/v3/runtime/frame/#executive-module)模块构建

与FRAME中的其它模块不同，这不是一个运行时模块。它是Rust中的一个普通的模块，它调用区块链中各种运行时模块

The Executive module公开了execute_block函数：

- [Initializes the block](https://docs.substrate.io/v3/concepts/execution/#initializing-a-block)
- [Executes extrinsics](https://docs.substrate.io/v3/concepts/execution/#executing-extrinsics)
- [Finalizes the block](https://docs.substrate.io/v3/concepts/execution/#finalizing-a-block)

**Validating transactions**

在块开始执行之前，检查签名交易的有效性。这并不会产生副作用。它仅仅会检查交易被包含或者不包含时是否会恐慌。因此，对存储所做的更改会被丢弃

**Executing a block**

一旦出现有效的交易序列，执行模块就开始执行

**Initializing a block**

为了初始化区块，系统模块和其它包含在运行时中的模块会调用on_initialize函数。这个函数会在交易执行之前执行这些模块定义的任何商业逻辑。模块会以它们被定义在construct_runtime!宏中的顺序执行。但是系统模块总是最先执行

接着，在区块的头部的父哈希中会进行初始化检查以确认正确，the extrinsics trie root代表了the extrinsics

**Executing extrinsics**

在区块被初始化后，每个extrinsic都会以交易的优先级被执行。Extrinsics 必须在运行时逻辑中不得导致恐慌，否则系统会被容易攻击，攻击位置是用户不会收到惩罚就能够引发计算执行

当执行一个extrinsic时，状态在执行前不会被缓存，存储的变量会直接在存储上操作。因此，运行时开发者应该执行所有必要的检查，以确保外部存储成功。如果extrinsic在执行过程中失败，之前的存储变量则不会被保存

由extrinsic发出的events也会被写入存储中，因此，不应该在执行补充的操作前发出event。如果extrinsic在event发出后失败了，event也不会恢复

**Finalizing a block**

在队列中所有的extrinsics被执行完毕后，the Executive模块会调用每个模块的on_idle 和 on_finalize 函数在区块末尾执行任何应该执行的商业逻辑。模块会以它们在construct_runtime!宏中被定义的顺序再次执行，但是在这种情况下，系统模块最后执行

最后再检查区块头中的摘要和storage root是否与计算的匹配

on_idle将会传递剩余的区块weight以根据区块链的使用情况执行

**Next steps**

- 在 [runtime tests](https://docs.substrate.io/v3/runtime/testing/)中了解如何模拟执行模块的构建

**Reference**

- [FRAME executive](https://docs.substrate.io/rustdocs/latest/frame_executive/index.html)
- [`#[pallet::event\]` macro](https://docs.substrate.io/rustdocs/latest/frame_support/attr.pallet.html#event-palletevent-optional)
- [`#[pallet::storage\]` macro](https://docs.substrate.io/rustdocs/latest/frame_support/attr.pallet.html#storage-palletstorage-optional)
- [`construct_runtime!` macro](https://docs.substrate.io/rustdocs/latest/frame_support/macro.construct_runtime.html)
- [`OnInitialize` trait](https://docs.substrate.io/rustdocs/latest/frame_support/traits/trait.OnInitialize.html)
- [`OnIdle` trait](https://docs.substrate.io/rustdocs/latest/frame_support/traits/trait.OnIdle.html)
- [`OnFinalize` trait](https://docs.substrate.io/rustdocs/latest/frame_support/traits/trait.OnFinalize.html)
- [`Hooks` trait](https://docs.substrate.io/rustdocs/latest/frame_support/traits/trait.Hooks.html)

## 2.8 Off-Chain Features

将数据包含在链上状态之前通常需要查询或者处理线下数据。传统方法是通过 [oracles](https://docs.substrate.io/v3/getting-started/glossary/#oracle)，虽然这种方法奏效，但是在安全性、可扩展性和基础设施效率方面仍然有一些缺点

为了使链下数据集成更加的安全和高效，Substrate提供了以下线下功能：

- Off-Chain Worker (OCW) 子系统可以执行长时间运行和可能性不确定的任务（例如：Web请求、数据加密/解密、数据签名、随机数生成、CPU密集型计算、链上数据的枚举和集成等），完成这些任务可能需要的时间可能比生成区块的时间更长

- Off-Chain Storage为Substrate节点提供了本地存储，链下工作者（既可以读也可以写）和链上逻辑（通过链下索引写但是不可以读）都可以访问。这让不同的工作线程能够在无需整个网络共识的情况下相互通信、存储用户或者某个节点的数据

- Off-Chain Indexing 允许运行时，（如果选择加入）独立于OCW直接写入链下存储。它可以作为本地/临时存储服务于链上逻辑和补充链上状态


Off-chain features在Substrate运行时之外的自己的Wasm执行环境中运行。这种分离确保了块的生产不受链下较长工作任务运行的影响。然而，由于Off-chain features在与运行时相同的代码中声明，所以它们可以轻松的访问链上状态以进行计算

![Off-chain Workers](/Users/qinjianquan/Desktop/substrate/off-chain-workers-v2.png)

**Off-chain workers**

Off-chain workers可以访问扩展API以与外部世界进行通信

- 能够向链提交交易（签名的或未签名的）并且发布运算结果

- 一个功能齐全的HTTP客户端可以让工作者从外部访问和获取数据

- 访问本地密钥库以签名和确认声明或交易

- 在链下[key-value database](https://docs.substrate.io/rustdocs/latest/sp_runtime/offchain/trait.OffchainStorage.html) 之间共享附加的、键值数据库

- 用于生成随机数的安全的、本地的熵源

- 获取节点精确的本地时间

- 可以休眠，也可以恢复工作


注意：来自于链下工作者的结果不受定期交易验证的约束。应该实施验证机制（例如：投票、求均值、检查发送中签名或简单的“信任”）以确定哪些信息进入链中

**Off-chain storage**

顾名思义，存储不是存储在链上。它可以被链下工作线程（读和写）和链上逻辑（只能写，参考如下线下索引）访问。此存储没有填充在区块链网络中，也不需要对其进行共识计算

由于在每个区块导入时都会产生一个脱链工作线程，因此在任何给定时间内，可能有多个脱链线程在运行，因此，与任何多线程编程环境类似，还有一些实用程序可以在访问存储时进行互斥锁以实现数据的一致性

链下存储可以作为一个桥梁为各种链下工作者线程提供链下和链上逻辑通信服务。它也可以通过远程过程调用（RPC）读取，因此它适用于存储无限增长的数据用例，而不会过度消耗链上的存储

**Off-chain indexing**

在区块链的上下文中，存储主要是关于链上状态的。但是它很昂贵（因为它被嵌入到了网络中的每一个节点上），并不建议存随着时间无限增长的历史数据或者用户数据

为此，我们有线下存储，除了OWC可以访问外，Substrate也包含了一个功能叫“off-chain indexing”，允许运行时独立于OCW直接写入链下存储。节点必须通过--enable-offchain-indexing 标志选择加入数据的持久性

与在初始区块链同步期间不执行的OCW不同，链下索引会在每次处理区块时更新，因此数据始终是一致的，并且对于启用索引的每个节点来说都是完全相同的

**Learn more**

要查看链下工作者的具体示例和在运行时开发时如何使用它们，请参考如下Substrate的内容：

- [Substrate's inclded example-offchain-worker](https://github.com/paritytech/substrate/tree/master/frame/examples/offchain-worker)
- [Recipe to Submit signed and unsigned transactions from off-chain workers back on-chain](https://github.com/JoshOrndorff/recipes/blob/master/text/off-chain-workers/transactions.md)
- [Recipe to fetch external data using HTTP requests and parse JSON responses](https://github.com/JoshOrndorff/recipes/blob/master/text/off-chain-workers/http-json.md)

- [Recipe to store result in off-chain worker local storage](https://github.com/JoshOrndorff/recipes/blob/master/text/off-chain-workers/storage.md)

- [Recipe for off-chain indexing](https://github.com/JoshOrndorff/recipes/blob/master/text/off-chain-workers/indexing.md)

# 3.运行时开发

## 3.1 FRAME

模块化实体运行时聚合框架 (FRAME) 是一组简化运行时开发的模块和支持库。在Substrate中，这些模块叫做Pallets，每个都包含了特定的逻辑，这些逻辑会在链上运行时中

FRAME也提供了一些帮助模块与重要的Substrate Primitives进行交互，这些Primitives为核心客户端提供了接口

下图展示了FRAME架构总览和所支持的库

![FRAME Architecture](https://docs.substrate.io/static/3499b4465d746bd0dcb22e34779d5546/c1b63/frame-arch.png)

**Pallets**

当用FRAME构建时，Substrate运行时由几个叫做pallets的组件组成。一个pallet包含一组类型、存储项和函数。它们为运行时定义了一组特性和功能

![FRAME Runtime](https://docs.substrate.io/static/64b2fcb61748ae77f4dd4c9ce63872b1/c1b63/frame-runtime.png)

小建议：

如果你想以Substrate runtime development为开始,建议你使用教程[creating your first Substrate chain](https://docs.substrate.io/tutorials/v3/create-your-first-substrate-chain/)

FRAME 不仅提供了一个常用的Substrate pallet库，还提供了一个构建自定义pallet的框架，让运行时工程师可以根据他们的目标用例灵活的定义运行时的行为，结果就是每个pallet都有自己独特的逻辑，可以用于修改链状态转换函数的特性和功能

举个例子，包含在FRAME中的[Balances pallet](https://github.com/paritytech/substrate/tree/master/frame/balances)，能定义加密货币。更具体而言，它定义了

- **存储项目：**追踪用户拥有的token
- **功能：**用于用户转移和管理tokens
- **API:** 可以让其它pallets使用这些tokens和它们的特性
- **Hooks：**允许当用户余额发生变化时可以让其它pallets触发函数调用

Substrate运行时工程师可以通过编写自己的pallet和封装区块链期望的功能（通过组合个性化的pallet和已经存在的FRAME pallets或者Substrate类似的模块）为区块链定义个性化的逻辑。下面的文件将会展示如何操作：

**Skeleton of a pallet**

一个FRAME pallet由七个部分组成

1. Imports and Dependencies
2. Declaration of the Pallet type
3. Runtime Configuration Trait
4. Runtime Storage
5. Runtime Events
6. Hooks
7. Extrinsics

下面是一个例子

```
// 1. Imports and Dependencies
pub use pallet::*;
#[frame_support::pallet]
pub mod pallet {
    use frame_support::pallet_prelude::*;
    use frame_system::pallet_prelude::*;

    // 2. Declaration of the Pallet type
    // This is a placeholder to implement traits and methods.
    #[pallet::pallet]
    #[pallet::generate_store(pub(super) trait Store)]
    #[pallet::generate_storage_info]
    pub struct Pallet<T>(_);

    // 3. Runtime Configuration Trait
    // All types and constants go here.
    // Use #[pallet::constant] and #[pallet::extra_constants]
    // to pass in values to metadata.
    #[pallet::config]
    pub trait Config: frame_system::Config { ... }

    // 4. Runtime Storage
    // Use to declare storage items.
    #[pallet::storage]
    #[pallet::getter(fn something)]
    pub MyStorage<T: Config> = StorageValue<_, u32>;

    // 5. Runtime Events
    // Can stringify event types to metadata.
    #[pallet::event]
    #[pallet::generate_deposit(pub(super) fn deposit_event)]
    pub enum Event<T: Config> { ... }

    // 6. Hooks
    // Define some logic that should be executed
    // regularly in some context, for e.g. on_initialize.
    #[pallet::hooks]
    impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> { ... }

    // 7. Extrinsics
    // Functions that are callable from outside the runtime.
    #[pallet::call]
    impl<T:Config> Pallet<T> { ... }

}
```

注意：

Pallets可以按需构建，这在基本的架构之上为运行时工程师提供了更大灵活性。参考 the [Substrate Runtime Macros](https://docs.substrate.io/v3/runtime/macros#substrate-runtime-macros) 了解添加功能至FRAME pallet

**System crate**

The [FRAME System crate frame_system](https://docs.substrate.io/rustdocs/latest/frame_system/index.html)为区块链提供了基本的类型、存储和功能。其它运行时pallet都以此为基础

系统crate定义了Substrate运行时所有的核心类型

- Origin
- Block Number
- Account Id

- Hash

- Header

- Version

- etc...


它还具有许多对系统至关重要的存储项目，例如：

- Account Nonce

- Block Hash

- Block Number

- Events
- etc...

最后，它定义了一些低级函数，它们可以访问区块链存储，验证外部数据的来源等

**Support crate**

The [FRAME Support Crate frame_support](https://docs.substrate.io/rustdocs/latest/frame_support/index.html)是Rust宏、类型、traits和模块的集合，它们可以简化Substrate pallet的开发

在编译时为pallet编译常见的代码结构时，支持宏能够扩展并且可以让开发者无需重复编写

**Executive pallet**

The [FRAME Executive Pallet frame_executive](https://docs.substrate.io/rustdocs/latest/frame_executive/index.html)充当运行时的编排层，它将传入的外部调用分派到运行时的各个pallet

**Runtime**

运行时汇集了这些组件和pallets。它定义了包含在运行时中的pallets，并将它们配置到一起组成最终运行时。当调用运行时它使用the Executive pallet把这些调用分配给各自的pallets

**Prebuilt pallets**

一些pallets被设计为通用的目的，并且在区块链中重复使用。任何人都能轻松编写和分享有用的pallets。Substrate提供了一系列流行的pallets，我们一起来看看：

**Assets**

一个Assets pallet是一个简单的、安全的用于处理同质化资产的模块

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_assets/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/assets/src/lib.rs)

**Atomic Swap**

一个可以自动将资金从某一地址转移至目标地址的模块。证明机制可以让target同意交换，如果交换在特定的期间没有被声明，发送者会取消的它

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_atomic_swap/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/atomic-swap/src/lib.rs)

**Aura**

The Aura pallet通过管理离线报告扩展 Aura共识

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_aura/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/aura/src/lib.rs)

**Authority Discovery**

core/authority-discovery使用The Authority Discovery pallet检索当前的authorities集合，获取它自己的authority ID,也会签名和验证来自其它authorities的信息

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_authority_discovery/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/authority-discovery/src/lib.rs)

**Authorship**

The Authorship pallet 能够追踪当前区块的作者和附近的叔区块

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_authorship/index.html)

- [Source](https://github.com/paritytech/substrate/blob/master/frame/authorship/src/lib.rs)

**BABE**

The BABE pallet extends 通过从VRF输出收集链上随机数和管理epoch 交易来扩展BABE共识

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_babe/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/babe/src/lib.rs)

**Balances**

The Balances pallet提供了处理账户和余额的功能

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_balances/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/balances/src/lib.rs)

**Benchmark**

是一个以隔离的方式包含了常见的运行时模式。这个pallet不用于生产区块链，仅用于基准测试和测试目的

- [Docs](https://docs.substrate.io/rustdocs/latest/frame_benchmarking/trait.Benchmark.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/benchmarking/src/lib.rs)

**Collective**

The Collective pallet可以让一系列Account ID通过从特定的来源调度calls从而发出集体信号

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_collective/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/collective/src/lib.rs)

**Contracts**

The Contracts pallet 为运行时提供了部署和执行 WebAssembly 智能合约的功能

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_contracts/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/contracts/src/lib.rs)

**Democracy**

The Democracy pallet 提供了一个民主系统，可以用于处理代币持有人一般投票事物

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_democracy/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/democracy/src/lib.rs)

**Elections Phragmén**

The Phragmén Elections pallet是一个基于[sequential Phragmén](https://wiki.polkadot.network/docs/en/learn-phragmen)的选举模块

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_elections_phragmen/index.html)

- [Source](https://github.com/paritytech/substrate/blob/master/frame/elections-phragmen/src/lib.rs)

**Elections**

> 提醒： NOT ACTIVELY MAINTAINED The Elections pallet 是一个用于代币加权了的成员选举的模块

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_elections/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/elections/src/lib.rs)

**Example Offchain Worker**

链下工作者例子，一个简单的pallet展示了大多数链下工作者共有的概念，API和结构 

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_example_offchain_worker/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/examples/offchain-worker/src/lib.rs)

**Example**

一个简单的pallet展示了大多数链下工作者共有的概念，API和结构 

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_example_offchain_worker/index.html)
- [Source]

**GRANDPA**

The GRANDPA pallet 通过管理为原生代码准备的The GRANDPA authority 来扩展GRANDPA 共识

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_grandpa/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/grandpa/src/lib.rs)

**Identity**

一个联合命名系统，允许从某个特定来源添加多个注册商。注册商可以设置费用来提供身份验证服务。任何人都可以提议确认fixed deposit，并要求一定数量的注册商审查（通过支付给他们一定的费用）。注册商的审阅结果以枚举的方式给出，允许复杂的、多层次的意见

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_grandpa/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/identity/src/lib.rs)

**I'm Online**

The I'm Online pallet允许验证人在每个会话中传递一个heartbeat transaction，以表明节点在线

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_im_online/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/im-online/src/lib.rs)

**Indices**

The Indices pallet可以为新建的账户分配索引，索引是地址的简短形式

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_indices/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/indices/src/lib.rs)

**Membership**

The Membership pallet允许控制一组账户ID的成员身份，这对于管理集体的成员身份很有用

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_membership/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/membership/src/lib.rs)

**Multisig**

用于进行多重签名调度的模块

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_multisig/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/multisig/src/lib.rs)

**Nicks**

Nicks 是一个简单的模块，用于保持追踪链上账户名称，它无需努力创建账户层级、替代DNS或者反向查找

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_nicks/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/nicks/src/lib.rs)

**Offences**

The Offences pallet 追踪报告恶意行为

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_offences/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/offences/src/lib.rs)

**Proxy**

A module 允许账户给其它账户从其签名来源分配调用类型的权限

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_proxy/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/proxy/src/lib.rs)

**Randomness Collective Flip**

The Randomness Collective Flip pallet 提供了一个随机函数，可以用来测试和基于之前81个区块的哈希产生低影响的随机值。此 pallet不用于生产

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_randomness_collective_flip/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/randomness-collective-flip/src/lib.rs)

**Recovery**

The Recovery pallet是一个M-of-N社交恢复工具，如果私钥或者其它身份验证机制丢失丢失，则可以使用这个工具访问他们的账户。通过这个pallet，用户可以用恢复的账户执行调用。恢复过程受原始账户拥有者选择的可信任的朋友保护。M是N个朋友的临界值数，只有达到临界值，才能让另一个账户访问可恢复的账户

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_recovery/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/recovery/src/lib.rs)

**Scheduler**

这个模块暴露了一个特性，可以让调度发生在具体编号的区块中或特定时期。这些调度处理可以是命名的、匿名的以及可以被取消的

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Scored Pool**

The Scored Pool pallet 维持了一个评分会员池，其中最高得分实体会成为会员

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Session**

The Session pallet 允许验证人管理他们的私钥，管理会话长度和处理会话轮转

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Society**

The Society module是一个经济游戏，它激励用户参与和维护成员所在的社区

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Staking**

The Staking pallet 用于管理网络维护者质押的资金

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Sudo**

The Sudo pallet允许让单个账户（ 称为“sudo”密钥）执行需要root权限才能执行的调度或者指定新账户为sudo key

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Timestamp**

The Timestamp pallet 提供了能够获取和设置链上时间的功能

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Transaction Payment**

The Transaction Payment pallet 提供了一个计算预调用交易费用的基本逻辑

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Treasury**

The Treasury pallet 提供了一个资金池，它在系统中被代币持有者者管理，可以通过提出花费提议来花费资金池中的余额

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Utility**

一个无状态的模块用于管理调度

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Vesting**

一个简单的模块，提供了一种在账户锁定余额上放置线型曲线的方式，该模块可以确保账户余额锁定，防止余额因除了交易费支付以外的任何原因跌至某个限度之下

- [Docs](https://docs.substrate.io/rustdocs/latest/pallet_scheduler/index.html)
- [Source](https://github.com/paritytech/substrate/blob/master/frame/scheduler/src/lib.rs)

**Next steps**

**Learn more**

参考 [tutorial to add a pallet to your FRAME runtime](https://docs.substrate.io/tutorials/v3/add-a-pallet/)

了解 [implement a pallet to lock user funds](https://docs.substrate.io/how-to-guides/v3/pallet-design/lockable-currency/)

了解  [write a simple crowd funding pallet](https://docs.substrate.io/how-to-guides/v3/pallet-design/crowdfund/)

**References**

参考 [System library](https://docs.substrate.io/rustdocs/latest/frame_system/index.html)

参考 [Executive pallet](https://docs.substrate.io/rustdocs/latest/frame_executive/index.html)

参考 [FRAME support library](https://docs.substrate.io/rustdocs/latest/frame_support/index.html)https://docs.substrate.io/rustdocs/latest/frame_support/index.html)

## 3.2 Macros

2021年1月，基于pallets的FRAME升级了宏的使用，参考[文档](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#upgrade-guidelines)了解从v1版本迁移到v2版本和FRAME版本的[变化](https://hackmd.io/@fscJ0GEvRb2rqALLZB1kfA/SkSerkamt)

Substrate使用[Rust宏](https://doc.rust-lang.org/book/ch19-06-macros.html)来整合在运行时中执行的来自于各个pallets的逻辑。这些运行时宏能够让开发者聚焦于运行时逻辑开发而不是编码和解码链上变量或者编写大量实现区块链基本要素的代码块。这能极大的减轻区块链的开发负担和移除重复的代码块

这篇文章的目的是介绍Rust宏和运行时开发工程师的使用最频繁的Substrate宏

**Macro basics**

简单而言，宏是编写代码的代码，它能够进行抽象而无需显示声明复杂的数据结构，Rust有四种宏

- [declarative macros](https://doc.rust-lang.org/book/ch19-06-macros.html#declarative-macros-with-macro_rules-for-general-metaprogramming)：最常用的

- [custom derive](https://doc.rust-lang.org/book/ch19-06-macros.html#how-to-write-a-custom-derive-macro)：过程宏的一种类型，只能用于结构体和枚举
- [attribute-like macros](https://doc.rust-lang.org/book/ch19-06-macros.html#attribute-like-macros)：过程宏的一种，可以创建新的属性

- [function-like macros](https://doc.rust-lang.org/book/ch19-06-macros.html#function-like-macros)：过程宏的一种，类似于macro_rules!，比函数更灵活，可以接受未知数量的参数

大多数Substrate运行时宏是声明宏或者类函数宏的一种，在FRAME v2 版本的pallets中也使用类属性宏

了解更多关于Substrate 运行时宏的小建议

- 阅读Substrate常用的宏[文档](https://paritytech.github.io/substrate/master/frame_support/index.html#macros)
- 使用[cargo expand](https://github.com/dtolnay/cargo-expand)查看扩展代码和底层逻辑
- 阅读[macro rules of expression pattern matching](https://danielkeep.github.io/tlborm/book/pim-README.html)

**Substrate runtime macros**

如下内容大多数运行时工程师看到的关于宏的综述，如想了解细节，可以通过子目录进一步查看

Substrate Primitives 和 FRAME 都依赖于各种宏，接下来会详细解释，先看看总览

**Macros in the FRAME [Support Library](https://docs.substrate.io/v3/runtime/frame/#support-library):**

- in frame_support：
  - [function-like](https://paritytech.github.io/substrate/master/frame_support/index.html#macros) macros
  - [derive](https://paritytech.github.io/substrate/master/frame_support/index.html#derives) macros
  - [attribute](https://paritytech.github.io/substrate/master/frame_support/index.html#attributes) macros

**Macros in the Substrate [System Library](https://docs.substrate.io/v3/runtime/frame/#system-library):**

- in sp_core:
  - [function-like](https://paritytech.github.io/substrate/master/sp_core/index.html#macros) macros
  - [derive RuntimeDebug](https://paritytech.github.io/substrate/master/sp_core/index.html#derives) macro
- in sp_runtime:
  - [function-like](https://paritytech.github.io/substrate/master/sp_runtime/index.html#macros) macros
  - [derive](https://paritytech.github.io/substrate/master/sp_runtime/index.html#derives) macros
- in sp_api: [function-like](https://paritytech.github.io/substrate/master/sp_api/index.html#macros) macros
- in sp_std: [function-like](https://paritytech.github.io/substrate/master/sp_std/index.html#macros) macros
- in sp_version: [function-like](https://paritytech.github.io/substrate/master/sp_version/index.html#macros) macros

注意：可以参考Cargo.toml文件中的[依赖](https://github.com/substrate-developer-hub/substrate-node-template/blob/main/runtime/Cargo.toml#L21)了解这些宏

**FRAME macros and attributes**

如下是FRAME有的一些不同的宏，请参考[structure of a pallet](https://docs.substrate.io/v3/runtime/frame/#pallets) 理解Substrate pallet的组成

**#[frame_support::pallet]**

1. 使用时机

需要声明一个pallet，组成一系列类型、函数和trait实现，后续construct_runtime会将其聚合构建运行时

2. 用途

它是属性宏，可以让pallet用在construct_runtime中， 该宏由一系列属性组成，能够识别pallet要求的特定的项

与派生宏类似，此宏仅通过读取输入来扩展类型和实现trait，并且几乎不会修改输入。 宏修改其输入的唯一例外（与派生宏相反）是：

- 当泛型替换为具体类型：例如，在 pallet::pallet 中项 `pub struct Pallet<>(_)`的内部类型被`PhantomData<T>`替换和项`pallet::strage`中第一个通配符`-`被实现了`StorageInstance` trait 的类型替换
- 当一些项被更改时：例如`pallet::type_value`更改函数为结构体或者trait实现
- 没有任何提供时用户自己添加了文档：例如没有文档提供宏`pallet::pallet`时将会通过在项上面添加`struct Pallet<T>(_) `将会修改输入

**#[pallet::config]**

1. 使用时机

需要定义pallet通用属性的时候

2. 用途

提供了[`Config` trait](https://paritytech.github.io/substrate/master/frame_system/pallet/trait.Config.html)  的一部分常量，用于指定运行时中pallet的配置，它可以让开发者调整运行时的行为

**#[pallet::constant]**

1. 使用时机

从配置trait中传递常量类型给pallet 元数据时，这些类型的值可以被外部工具和第三方使用。注意：这个属性只有在#[pallet::config]内部的项才可用

2. 用途

提供运行时所需的`Config trait` 的属性和类型以产生元数据

添加`#[pallet::constant]`属性将会导致宏添加常量元数据，包括：常量名称，常量关联类型的名称，常量值，值由`Get::get()`返回

例如：我们可以使用`#[pallet::constant] `把`type MyGetParam`转为元数据

```
#[pallet::config]
pub trait Config: frame_system::Config {
    #[pallet::constant] // puts attributes in metadata
    type MyGetParam: Get<u32>;
}
```

3. 查看[#[pallet::config\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#config-trait-palletconfig-mandatory)在pallet中的使用

**#[pallet::extra_constants]**

1. 使用时机

它是一个可选项，为和pallet 配置不相关的额外常量传递元数据

2. 用途

可以定义一些额外常量并将其转为元数据，例如，可以声明一个函数，该函数可以返回一个将会添加到元数据中的值

```
#[pallet::extra_constants]
impl<T: Config> Pallet<T> {
  //Example function using extra_constants
  fn example_extra_constants() -> u128 { 4u128 }
}
```

3. 查看[documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#extra-constants-palletextra_constants-optional)

**#[pallet::pallet]**

1. 使用时机

声明pallet 结构体占位符，在随后的`construct_runtime` 宏中被使用

2. 用途

如果提供了属性，则会生成`Storage`trait，它包含了每个存储项的关联类型。它采用了显式声明的Rust 结构体形式，可以被写作为`pub struct Pallet<T>(_)`, 使用合适的`PhantomData`替换以让它变得更加通用

它定义的例子：

```
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
pub struct Pallet<T>(_);
```

3. 查看 [macro expansion](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#macro-expansion-1) 添加在`struct Pallet<T>`中的定义

**#[pallet::generate_storage_info]**

1. 使用时机

该属性不会长期存在，`generate_storage_info`现在时默认的，所有pallet 存储项都是约束的（如有固定大小）

2. 用途

所有存储项都要求实现`traits::StorageinfoTrait`,就像所有的键和值都必须受`pallet_prelude::MaxEncodeLen`Trait约束，个别的storage可以选择性的使用`#[pallet::unbounded]`取消这种约束。查看`#[pallet::storage]`文档

在不可能事件中，整个pallet的存储项目需要默认为不受约束，可以想像下面一样为pallet添加`pallet::without_storage_info`属性

```
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
#[pallet::without_storage_info]
pub struct Pallet<T>(_);
```

（但是在具体存储项上放置`#[pallet::unbounded]`通常更常用）

3. 查看 [macro expansion](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#macro-expansion-1) 添加在`struct Pallet<T>`中的定义

**#[pallet::hooks]**

1. 使用时机

需要声明pallet hooks时使用

2. 用途

Hooks可以通过使用`Hooks` trait来启用

例如

```
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
  // Hooks functions and logic goes here.
}
```

3. 参考文档

- See the [documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#hooks-pallethooks-mandatory)
- See the [macro expansion](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#macro-expansion-2)

**#[pallet::call]**

1. 使用时机

需要为pallet实现调度时，每个调度：

- 使用`#[pallet::weight($expr)]`属性定义weight
- 像`origin:OriginFor<T>`一样有第一个参数
- 返回`DispatchRusltWithPostInfo`或`DispatchResult`
- 必须使用`#[pallet::compat]`编码参数

2. 用途

Extrinsics可以调用触发特定的逻辑。Calls也可以用于链上治理，在可以投票的democracy pallet中展示。`#[pallet::call]`能够创建调度函数，它们会从`impl`代码块中产生关联类型的。换句话说，它使用[`Call`](https://paritytech.github.io/substrate/master/frame_system/pallet/enum.Call.html) 枚举集成了调度逻辑，这将会把可调度的calls集成为单个的运行时call

3. 参考文档

- [documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#hooks-pallethooks-mandatory)

**#[pallet::error]**

1. 使用时机

可选项，定义调度的错误类型

2. 用途

将错误变量文档转为元数据

3. 参考文档

- [documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#error-palleterror-optional)

**#[pallet::event]**

1. 使用时机

可选项，定义pallet的event 类型

2. 用途

和错误相似但持有更多信息，可以从event的元数据和add_derive.使用`#[pallet::metadata(..)]`属性定义从events生成什么元数据

例如：

```
#[pallet::event]
#[pallet::metadata(u32 = "SpecialU32")]
pub enum Event<T: Config> {
    Proposed(u32, T::AccountId),
}
```

3. 参考文档

-  [documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#event-palletevent-optional)

**#[pallet::storage]**

1. 使用时机

可选项，可以被多次使用

2. 用途

定义抽象的存储到运行时存储中

`[pallet::storage]`使用类Rust HashMap 存储。但它还包含Key，Value, Hasher, Prefix,QueryKind 和 Onempty参数。prefix通常定义了存储`(key,value)`对（例如trie）地方。定义pallet 和 storage prefix如下：

`concat(twox_128(pallet_prefix),twox_128(storage_prefix`,用pallet和存储名称产生的prefix实现代替`_`

3. 参考文档

- See [macro documentation](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#storage-palletstorage-optional)
- [`StorageMap` implementation](https://paritytech.github.io/substrate/master/frame_support/pallet_prelude/struct.StorageMap.html#implementations)
- [Rust HashMap syntax](https://doc.rust-lang.org/book/ch08-03-hash-maps.html)

**其他属性**

其他FRAME 宏包含的属性

- [#[pallet::genesis_config\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#genesis-config-palletgenesis_config-optional)
- [#[pallet::type_value\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#type-value-pallettype_value-optional)
- [#[pallet::genesis_build\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#genesis-build-palletgenesis_build-optional)
- [#[pallet::inherent\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#inherent-palletinherent-optional)
- [#[pallet::validate_unsigned\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#validate-unsigned-palletvalidate_unsigned-optional)
- [#[pallet::origin\]](https://paritytech.github.io/substrate/master/frame_support/attr.pallet.html#origin-palletorigin-optional)

**特殊的 FRAME 宏**

**construct_runtime!**

1. 使用时机

构建substrate运行时和将各种pallets集成进运行时

2. 用途

该宏声明和实现了各种结构体和枚举，例如Runtime，Event，Origin，Call，GensisConfig 以及各种这些类型的帮助trait。宏也添加了逻辑从总的运行时到特定的pallet匹配不同的事件和调度calls

3. 参考文档

- [API Documentation](https://paritytech.github.io/substrate/master/frame_support/macro.construct_runtime.html)
- `Runtime` 代表Substrate运行时的结构体类型
- `Event` 各种pallet和emit events变体的枚举类型,它实现了helper traits和encoding/deconding traits. 在各种conversion traits中, `Event` 也实现了 `TryInto<mypallet::Event<Runtime>>` trait ，它会将event转换为在具体pallet中声明的event
- `Origin` 使用 helper traits定义的枚举类型, 如实现`PartialEq`, `Clone`, `Debug` . 这个枚举定义了是谁在调用extrinsic: `NONE`, `ROOT`, or 某个特定的账户. 这三种 primitive origins 在  FRAME 系统模块中定义, 但是其他可选的 FRAME pallets 也可能定义了 origins.
- `Call` 由所有集成的pallets的变体定义的枚举类型. 它包含了每个集成pallet的数据和元数据, 并且可以把calls重定向到具体pallet，这些 pallet实现可`frame_support::dispatch::Dispatchable` 和 `frame_support::dispatch::UnfilteredDispatchable` traits
- `GenesisConfig` 实现可用于构建存储初始配置的 `sp_runtime::BuildStorage` trait 结构体类型
- 宏影响了每个pallet实现 `frame_support::unsigned::ValidateUnsigned` trait。如果任何已包含的pallet没有实现ValidateUnsigned` trait，未签名的交易将会被拒绝

**parameter_types!**

1. 使用时机

在构建运行时时声明和pallet相关的参数类型

2. 用途

该宏使用`get()`函数代替每个参数的具体类型并返回它的具体值。每个参数的结构体都实现了 `frame_support::traits::Get<I>` trait以便能够转为具体值

3. 参考文档

- [API Documentation](https://paritytech.github.io/substrate/master/frame_support/macro.parameter_types.html)

**Substrate system library macros**

**impl_runtime_apis!**

1. 使用时机

该宏通过`RuntimeApi` and `RuntimeApiImpl` 结构体类型未客户端实现了API 

2. 用途

定义了 `RuntimeApi` and `RuntimeApiImpl` 并暴露给客户端节点。它也提供了 RuntimeApiImpl实现的细节，这些细节基于在  `RuntimeApiImpl`中设置和添加用户至特定的实现

3. 参考文档

- [API Documentation](https://paritytech.github.io/substrate/master/sp_api/macro.impl_runtime_apis.html)
- `RuntimeApi` and `RuntimeApiImpl` 结构体被声明 ，宏为 `RuntimeApiImpl`实现了各种帮助traits
- 开发者使用`impl_runtime_apis!` 宏添加在`RuntimeApiImpl` 实现的末尾
- 为了广播运行时版本的信息, 定义了RUNTIME_API_VERSIONS常量，包含运行时 core `ID`/`VERSION`, 元数据 `ID`/`VERSION`, SessionKeys `ID`/`VERSION`等等
- 使用 `dispatch()` 函数定义公共模块的`api`实现了各种类型的字符串怎样匹配元数据以及链上的循环调用

**app_crypto!**

1. 使用时机

指定pallet管理的密码学密钥对和签名算法

2. 用途

宏声明了三种结构体类型, `Public`, `Signature`, and `Pair`. 这三种类型实现了各种帮助trait之外, `Public` 类型的实现能够让它generating 产生密钥对、签名和验证签名。 `Signature` 类型持有给定签名的所有权，如 (e.g. SR25519, ED25519 etc);  `Pair` 类型能够从给定种子值生成公钥和私钥对

3. 参考文档

- [API Documentation](https://paritytech.github.io/substrate/master/sp_application_crypto/macro.app_crypto.html)
- `Public` 结构体类型, 实现了 `sp_application_crypto::AppKey` trait 可以定义公钥类型, 实现了`sp_application_crypto::RuntimeAppPublic` trait ，可以产生密钥对、签名和验证签名
- `Signature` 结构体类型, 实现了`sp_application_crypto::AppKey `和`corehash::Hash` traits ，决定了这些签名数据如何被哈希加密
- `Pair` 结构体类型，封装了密钥对. 实现了`sp_application_crypto::AppKey` 和 `sp_application_crypto::Pair` trait，定义了如何从词汇或者种子值生成公钥和私钥对

**References**

- [The Rust Programming Language ch 19.5 Macros](https://doc.rust-lang.org/book/ch19-06-macros.html)
- [The Little Book of Rust Macros](https://danielkeep.github.io/tlborm/book)

## 3.3 Metadata

![image-20220611182602089](/Users/qinjianquan/Library/Application Support/typora-user-images/image-20220611182602089.png)

## 3.4 测试

可以通过模拟substrate 运行时环境来测试运行时模块的逻辑

**单元测试**

通过Rust提供的单元测试框架来测试

```
cargo test <optional: test_name>
```

**模拟运行时环境**

配置类型Test作为Rust枚举已经被定义，并且在每个pallet配置trait中被实现，它就是用来模拟运行时环境的

```
frame_support::construct_runtime!(//定义
    pub enum Test where
        Block = Block,
        NodeBlock = Block,
        UncheckedExtrinsic = UncheckedExtrinsic,
    {
        System: frame_system::{Pallet, Call, Config, Storage, Event<T>},
        TemplateModule: pallet_template::{Pallet, Call, Storage, Event<T>},
    }
);

impl frame_system::Config for Test {//实现
    // -- snip --
    type AccountId = u64;
}
```

if Test实现了pallet_balances::Config，Balance可能是u64

```
impl pallet_balances::Config for Test {
    // -- snip --
    type Balance = u64;
}
```

通过将pallet_balances::Balance 和frame_system::AccountId 分配给u64，模拟运行时环境减轻了我们的测试负担，只需要追踪(AccountId: u64, Balance: u64)map 就可以推断账户和余额

**模拟运行时存储**

```
sp-io crate 暴露了一个 TestExternalities实现，它是在内存中的、基于 hashmap 的外部性实现的类型别名
```

如下示例定义了一个名为 ExtBuilder 的结构来构建 TestExternalities 的实例，并将块编号设置为 1

```
pub struct ExtBuilder;

impl ExtBuilder {
    pub fn build(self) -> sp_io::TestExternalities {
        let mut t = system::GenesisConfig::default().build_storage::<TestRuntime>().unwrap();
        let mut ext = sp_io::TestExternalities::new(t);
        ext.execute_with(|| System::set_block_number(1));
        ext
    }
}
```

为了在单元测试中创建测试环境，调用 build 方法以使用默认的 genesis 配置生成 TestExternalities

```
#[test]
fn fake_test_example() {
    ExtBuilder::default().build_and_execute(|| {
        // ...test logics...
    });
}
```

Externalities 的自定义实现可以让开发人员构建运行时环境以让外部节点的访问。 另一个例子可以在 offchain 中找到，它维护了自己的 Externalities 实现

**Genesis config**

前面展示了方法 ExtBuilder::build()，它用于模拟运行时环境时的默认配置，很多情况下在在测试之前设置storage很方便

一个在测试之前测试种子账户的例子

在 frame_system::Config 的实现中，AccountId 设置为 u64，就像上面显示的 Balance 一样。 将 (u64, u64) 对放在余额 vec 中，作为账户余额的种子 (AccountId, Balance) 对

```
impl ExtBuilder {
    pub fn build(self) -> sp_io::TestExternalities {
        let mut t = frame_system::GenesisConfig::default().build_storage::<Test>().unwrap();
        pallet_balances::GenesisConfig::<Test> {
            balances: vec![
                (1, 10),
                (2, 20),
                (3, 30),
                (4, 40),
                (5, 50),
                (6, 60)
            ],
        }
            .assimilate_storage(&mut t)
            .unwrap();

        let mut ext = sp_io::TestExternalities::new(t);
        ext.execute_with(|| System::set_block_number(1));
        ext
    }
}
```

账户1的的余额为10，账户为2的余额为20，以此类推

如何定义某个pallet的创世配置的确切结构取决于pallet GenesisConfig 结构定义。 对于 Balances Pallet，如 rustdocs 所示，它被定义为：

```
pub struct GenesisConfig<T: Config<I>, I: 'static = ()> {
    pub balances: Vec<(T::AccountId, T::Balance)>,
}
```

**Block production**

模拟区块的产生以验证预期行为在块的生成中是否成立非常有用

一个简单的方法是在所有模块的 on_initialize 和 on_finalize 调用之间增加 System 模块的块编号， System::block_number() 作为唯一输入。 虽然运行时代码缓存对存储或系统模块的调用很重要，但测试环境脚手架应优先考虑可读性以促进未来的维护

```
fn run_to_block(n: u64) {
    while System::block_number() < n {
        if System::block_number() > 0 {
            ExamplePallet::on_finalize(System::block_number());
            System::on_finalize(System::block_number());
        }
        System::set_block_number(System::block_number() + 1);
        System::on_initialize(System::block_number());
        ExamplePallet::on_initialize(System::block_number());
    }
}
```

on_finalize 和 on_initialize 仅在pallet trait 实现 frame_support::traits::{OnInitialize, OnFinalize} trait时从 ExamplePallet 调用，以分别在每个块之前和之后执行运行时方法中编码的逻辑

然后按以下方式调用此函数

```
#[test]
fn my_runtime_test() {
    with_externalities(&mut new_test_ext(), || {
        assert_ok!(ExamplePallet::start_auction());
        run_to_block(10);
        assert_ok!(ExamplePallet::end_auction());
    });
}
```

**Testing Events**

测试发出的事件是否符合某些期望可能很有用。

我们可以使用 events() 方法访问从 frame_system pallet发出的事件

```
#[test]
fn subtract_extrinsic_works() {
    new_test_ext().execute_with(|| {
        assert_ok!(<test_pallet::Pallet<Test>>::subtract(Origin::signed(1), 42, 12));
        let event = <frame_system::Pallet<Test>>::events().pop()
            .expect("Expected at least one EventRecord to be found").event;
        assert_eq!(event, mock::Event::from(test_pallet::Event::Result(42 - 12)));
    });
}
```

**了解更多**

了解如何为pallet设置测试：https://docs.substrate.io/how-to-guides/v3/testing/basics/

## 3.11 测试Pallet

1. 在mock.rs文件中添加如下代码

```
use crate as pallet_template;
use sp_core::H256;
use frame_support::parameter_types;
use sp_runtime::{
	traits::{BlakeTwo256, IdentityLookup}, testing::Header,
};
use frame_system as system;

type UncheckedExtrinsic = frame_system::mocking::MockUncheckedExtrinsic<Test>;
type Block = frame_system::mocking::MockBlock<Test>;

// Configure a mock runtime to test the pallet.
frame_support::construct_runtime!(
	pub enum Test where
		Block = Block,
		NodeBlock = Block,
		UncheckedExtrinsic = UncheckedExtrinsic,
	{
		System: frame_system::{Module, Call, Config, Storage, Event<T>},
		TemplateModule: pallet_template::{Module, Call, Storage, Event<T>},
	}
);

parameter_types! {
	pub const BlockHashCount: u64 = 250;
	pub const SS58Prefix: u8 = 42;
}

impl system::Config for Test {
	type BaseCallFilter = ();
	type BlockWeights = ();
	type BlockLength = ();
	type DbWeight = ();
	type Origin = Origin;
	type Call = Call;
	type Index = u64;
	type BlockNumber = u64;
	type Hash = H256;
	type Hashing = BlakeTwo256;
	type AccountId = u64;
	type Lookup = IdentityLookup<Self::AccountId>;
	type Header = Header;
	type Event = Event;
	type BlockHashCount = BlockHashCount;
	type Version = ();
	type PalletInfo = PalletInfo;
	type AccountData = ();
	type OnNewAccount = ();
	type OnKilledAccount = ();
	type SystemWeightInfo = ();
	type SS58Prefix = SS58Prefix;
}

impl pallet_template::Config for Test {
	type Event = Event;
}

// Build genesis storage according to the mock runtime.
pub fn new_test_ext() -> sp_io::TestExternalities {
	system::GenesisConfig::default().build_storage::<Test>().unwrap().into()
}
```

# 4.Offchain Features

## Offchain Worker (上)

时常感叹时间流逝的速度，转眼间距离上次写文章（6.25）已经过去快四个月了。好吓人，怎么这么快！

这四个月里发生了太多的事情，于个人而言，认识了很多有趣的小伙伴，接触了不同的生态和技术，离开了待了近十年的上海 .....。哎，人生，真是充满了各种未知和可能呢

好了，废话不多说，今天依然是学习局，关于substrate offchain-worker 的更详细介绍，本系列内容会分为上中下三篇内容与大家分享，今天是 “上”

### 1 为什么要引入Offchain Features

区块链上的存储和计算资源都非常有限，这无法满足我们对性能的要求。Offchain Features 的引入可以在一定程度上解决这种问题，把需要长时间计算的、耗费大量存储的业务放在链下

那以太坊是怎么解决的呢，它通过Offchain Daemo对链上信息进行监听，然后基于监听结果进行各种逻辑处理，最后再将结果通过rpc传至链上

substrate 的offchain worker 在监听逻辑上其实与以太坊非常相似，区别在于以太坊的Offchain Daemo是独立的应用程序，代码的维护、管理、分发不会经过链上共识，而substrate的offchain worker逻辑在runtime中，这可以让链下业务逻辑共享链上逻辑升级方案

总结而言，substrate offchain worker：

1. 集成度更高，链上链下代码管理统一，在一个节点内，维护性更好
2. 链下代码经过共识验证，分发可靠性有保证，防止潜在的作恶行为
3. 链下代码和链上代码共享同一份forkless升级策略
4. 从offchain worker向链上发送交易，而不是通过外部的rpc通道，交易处理更加灵活和高效

### 2 Offchain Features 三大组件

Offchain Worker

Offchain Storage

Offchain Indexing

关于上面的三大组件详细内容可以参考之前的翻译以及原文：

原文链接：https://docs.substrate.io/fundamentals/offchain-operations/

**注意：**

1. Offchain Worker可以直接读链上的数据（状态），但不能写
2. 链上代码可以向Offchain Storage中写数据，但不能读
3. 外层的node和rpc可以直接读链上存储的数据以及Offchain Storage中的数据
4. 外层的node和rpc可以直接向Offchain Storage中写数据，但不可以向链上存储写数据
5. 在链上存储数据或者更改数据只能通过发交易的形式（经过共识验证数据）

### 3 Offchain worker 功能

1. 提交签名和未签名的交易（计算后的数据或者fetch到的信息）
2. 实现了标准的HTTP客户端，可以发送GET/POST请求等
3. 可以读取本地数据库并在offchain workers之间共享数据
4. 读取本地账户信息，签名和验证信息或者交易
5. 产生真随机数
6. 访问本地精确的unix时间戳
7. 可以暂停和恢复工作

## Offchain Worker (中)

本节内容我们将会分享一些案例，以展示offchain 在实际业务场景中到底是如何使用，主要是涉及存储以及http请求。在最后一节，我们将会使用offchain worker 执行一些交易操作

### Case 1 : 引入 Offchain Worker

**目标**

1. 给pallet 添加 offchain worker hook 

2. 在offchain worker中打印日志信息，并在终端查看日志

所有代码都会在substrate-node-template上构建，可以通过如下方式获取substrate-node-template

```
git clone https://github.com/substrate-developer-hub/substrate-node-template
```

clone repo 后编写代码，具体有两处，一是lib.rs，二是Cargo.toml, 如下：

**substrate-node-template/pallets/template/src/lib.rs**

```
#[frame_support::pallet]
pub mod pallet {
	--
  // offchain workers 通过hooks实现
	#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);
		}
	}
}
```

**substrate-node-template/pallets/template/Cargo.toml**

```
[dependencies]
--
log =  { version = "0.4.17", default-features = false } // 引入log依赖，打印offchain worker信息
```

Ok，Offchain worker引入非常简单，现在可以编译并运行

```
cargo build // 编译 （在这里也可以使用cargo build --release）
./target/debug/node-template --dev // 运行 （也可以使用./target/release/node-template --dev）dev代表单节点出块
```

查看终端

![image](https://github.com/shiyivei/substrate/blob/main/static/offchain-worker.png)

**重点**

1. Offchain worker 在每次块导入之后执行
2. 每导入一个区块，就会执行一次

### Case 2 :  Offchain worker 的 lifecycle （1）

**目标**

1. 同时打开多个hooks, on_initialize, on_finalize,on_idle
2. 在各个hooks中打印信息，观察日志出现的时机，理解offchain worker的执行

现在我们在case1 的基础上增加更多函数

```
#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);
		}

		fn on_initialize(_n: T::BlockNumber) -> Weight {
			log::info!("in on_initialize!");
			let weight: Weight = Default::default();
			weight
		}
		fn on_finalize(_n: T::BlockNumber) {
			log::info!("in on_finalize!");
		}

		fn on_idle(_n: T::BlockNumber, _remaining_weight: Weight) -> Weight {
			log::info!("in on_idle!");
			let weight: Weight = Default::default();
			weight
		}
	}
```

继续编译运行

![image]()

**重点**

1. 以上四个hooks函数执行顺序确定，其中offchain worker最后执行
2. 这四个hooks函数和substrate的共识相关

### Case 3 :  Offchain worker 的 lifecycle （2）

**目标**

1. 在offchain worker 中sleep一段时间，观察offchain worker 跨块执行

跨块执行的结果是会在整个substrate中跑多个offchain worker，实现并发。实际上底层使用的是**tokio task**实现

**substrate-node-template/pallets/template/src/lib.rs**

```
#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			let timeout =
				sp_io::offchain::timestamp().add(sp_runtime::offchain::Duration::from_millis(8000));

			sp_io::offchain::sleep_until(timeout);

			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}
}
```

**substrate-node-template/pallets/template/Cargo.toml**

```
[dependencies]
--
sp-io = { version = "6.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
sp-runtime = { version = "6.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
```

继续编译运行

![image](https://github.com/shiyivei/substrate/blob/main/static/cross-block-fn.png)

我们可以看到，第一个块中引入的offchain worker在第二个块中才退出，同样的第二个块引入的offchain worker在第三个块中退出。正如我们之前所言，offchain worker对于执行一些需要不确定计算时间的业务需求非常有用

**重点**

1. offchain worker的编程模式是定时器的流模式（定时重入）
2. 与响应式Reaction模式不同，Noed.js就是这种模式

### Case 4 : 向Local Storage 写入并读取数据

Offchain Storagte（Local storage）的功能

1. Offchain Worker可以直接读写 Local Storage
2. 链上代码可以通过Indexing功能直接向Local Storage写数据，但是不能读
3. 可用于Offchain Workers tasks 之间的通信和协调，注意由于可能同时存在多个Offchain worker，因此并发访问时需要加锁

**目标**

1. 在奇数块向Local Storage写数据，偶数块读取数据，并检查

2. 学习：获取链下随机数；对BlockNumber类型进行数学运算；获取链下时间；生成存储key；写链下存储，读链下存储；清理存储key

**substrate-node-template/pallets/template/src/lib.rs**

引入相关的类型和Trait（注意在mod pallet外或者内引入都可，但是再外引入的话，内部要再使用use super::* 引入到模块内）

```
use sp_runtime::{offchain::storage::StorageValueRef, traits::Zero};
#[frame_support::pallet]
pub mod pallet {
	use super::*;
	use frame_support::inherent::Vec;
	--
	}
```

在mod pallet中继续编写

```
#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			// odd
			if block_number % 2u32.into() != Zero::zero() {
				let key = Self::derive_key(block_number);
				let val_ref = StorageValueRef::persistent(&key);

				// get local random value
				let random_slice = sp_io::offchain::random_seed();

				// get a local timestamp
				let timestamp_u64 = sp_io::offchain::timestamp().unix_millis();

				// combine to a tuple and print it
				let value = (random_slice, timestamp_u64);
				log::info!("in odd block, value to write {:?}", value);

				// write or mutate tuple content to key
				val_ref.set(&value);
			} else {
				// even
				let key = Self::derive_key(block_number - 1u32.into());
				let mut val_ref = StorageValueRef::persistent(&key);

				// get from db by key
				if let Ok(Some(value)) = val_ref.get::<([u8; 32],u64)>() {
					// print values
					log::info!("in even block, value read:{:?}", value);
					// delete key
					val_ref.clear();
				}
			}
			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}

	impl<T: Config> Pallet<T> {
		#[deny(clippy::clone_double_ref)]
		// 由 drive_key 获取key
		fn derive_key(block_number: T::BlockNumber) -> Vec<u8> {
			block_number.using_encoded(|encoded_bn| {
				b"node-template::storage::"
					.iter()
					.chain(encoded_bn)
					.copied()
					.collect::<Vec<u8>>()
			})
		}
	}
```

编译运行

![image](https://github.com/shiyivei/substrate/blob/main/static/local-storage.png)

通过终端可以看到，在奇数块，我们成功的存储了随机数据，并且在偶数块准确的读到了数据。所有的学习目标也都完成了！

### Case 5 :  使用mutate 方法向Local Storage 写入数据

**目标**

1. 在 case 4的基础上使用mutate 方法对数据进行原子更改
2. 学习新的原子操作修改方法（不再使用之前手动锁的方式），学习配套的错误处理模式

**substrate-node-template/pallets/template/src/lib.rs**

新增加两种错误类型

```
use sp_runtime::{offchain::storage::{StorageValueRef,MutateStorageError,StorageRetrievalError}, traits::Zero};
```

使用mutate更改数据

```
#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			// odd
			if block_number % 2u32.into() != Zero::zero() {
				let key = Self::derive_key(block_number);
				let val_ref = StorageValueRef::persistent(&key);

				// get local random value
				let random_slice = sp_io::offchain::random_seed();

				// get a local timestamp
				let timestamp_u64 = sp_io::offchain::timestamp().unix_millis();

				// combine to a tuple and print it
				let value = (random_slice, timestamp_u64);
				log::info!("in odd block, value to write {:?}", value);

				struct StateError;

				// 通过一种方式实现get and set操作
				let res = val_ref.mutate(|val:Result<Option<([u8;32],u64)>,StorageRetrievalError>| -> Result<_,StateError> {
					match val {
						Ok(Some(_)) => Ok(value),
						_ => Ok(value),
					}
				});
				match res {
					Ok(value) => {
						log::info!("in odd block, mutate successfully {:?}", value);
					},
					Err(MutateStorageError::ValueFunctionFailed(_)) => (),
					Err(MutateStorageError::ConcurrentModification(_)) => (),
				}
			} else {
				// even
				let key = Self::derive_key(block_number - 1u32.into());
				let mut val_ref = StorageValueRef::persistent(&key);

				// get from db by key
				if let Ok(Some(value)) = val_ref.get::<([u8; 32], u64)>() {
					// print values
					log::info!("in even block, value read:{:?}", value);
					// delete key
					val_ref.clear();
				}
			}
			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}

	impl<T: Config> Pallet<T> {
		#[deny(clippy::clone_double_ref)]
		// 由 drive_key 获取key
		fn derive_key(block_number: T::BlockNumber) -> Vec<u8> {
			block_number.using_encoded(|encoded_bn| {
				b"node-template::storage::"
					.iter()
					.chain(encoded_bn)
					.copied()
					.collect::<Vec<u8>>()
			})
		}
	}
```

编译并运行

![image](https://github.com/shiyivei/substrate/blob/main/static/mutate-storage.png)

通过日志我们可以看到，使用mutate方法成功的存储了数据，这种方式的好处是应付并发处理

### Case 6 :  获取外部Http 接口的数据

**目标**

1. 在Offchain Worker中发起https请求，获取数据
2. 使用serde_json解析获取到的json数据
3. 学习serde的类型转换和调试相关的知识

**substrate-node-template/pallets/template/src/lib.rs**

引入crate

```
[dependencies]
--
serde = {version = "1.0.147", default-features = false, features = ['derive']}
serde_json = {version = "1.0.87",default-features = false, features = ['alloc']}
sp-std = {version ="4.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
```

引入相关的类型和Trait

```
use sp_runtime::{
	offchain::{http,Duration}
};
use serde::{Deserialize,Deserializer}
```

定义数据类型和方法

这种逻辑和我们日常处理http请求是一样的，也就是先定义类型以及解析方法，另外，注意为了打印结构体，我们手动为其实现了Debug Trait

```
pub mod pallet {
	--
	#[derive(Deserialize, Encode, Decode)]
	struct GithubInfo {
		#[serde(deserialize_with = "de_string_to_bytes")] // 使用指定的方法解析
		login: Vec<u8>,
		#[serde(deserialize_with = "de_string_to_bytes")] // 使用指定的方法解析
		blog: Vec<u8>,
		public_repos: u32,
	}

	pub fn de_string_to_bytes<'de, D>(de: D) -> Result<Vec<u8>, D::Error>
	where
		D: Deserializer<'de>,
	{
		let s: &str = Deserialize::deserialize(de)?;
		Ok(s.as_bytes().to_vec())
	}
	
	use core::{convert::TryInto, fmt};
	impl fmt::Debug for GithubInfo {
		fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
			write!(
				f,
				"{{ login: {}, blog: {}, public_repos: {} }}",
				sp_std::str::from_utf8(&self.login).map_err(|_| fmt::Error)?,
				sp_std::str::from_utf8(&self.blog).map_err(|_| fmt::Error)?,
				&self.public_repos
			)
		}
	}
}
```

offchain worker

```
pub mod pallet {
	--
	#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			if let Ok(info) = Self::fetch_github_info() {
				log::info!("Github Info: {:?}", info);
			} else {
				log::info!("Error while fetch github Info");
			}
			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}
}
```

辅助函数

```
pub mod pallet {
	--
	// 辅助函数
	impl<T: Config> Pallet<T> {
		fn fetch_github_info() -> Result<GithubInfo, http::Error> {
			// prepare for send request
			let deadline = sp_io::offchain::timestamp().add(Duration::from_millis(8_000));
			let request = http::Request::get("https://api.github.com/orgs/substrate-developer-hub");
			let pending = request
				.add_header("User-Agent", "Substrate-Offchain-Worker")
				.deadline(deadline)
				.send()
				.map_err(|_| http::Error::IoError)?;
			let response =
				pending.try_wait(deadline).map_err(|_| http::Error::DeadlineReached)??;
			if response.code != 200 {
				log::warn!("Unexpected status code: {}", response.code);
				return Err(http::Error::Unknown)
			}
			let body = response.body().collect::<Vec<u8>>();
			let body_str = sp_std::str::from_utf8(&body).map_err(|_| {
				log::warn!("No UTF8 body");
				http::Error::Unknown
			})?;

			// parse the response str
			let gh_info: GithubInfo =
				serde_json::from_str(body_str).map_err(|_| http::Error::Unknown)?;

			Ok(gh_info)
		}
	}
}
```

编译并运行

![image](https://github.com/shiyivei/substrate/blob/main/static/image-20221023163658315.png)

OK，我们成功的实现了通过offchain worker 从http请求获取数据

今天的分享就到这里，我们下期再会

### Case 7 :  向链上发起签名交易

**目的**

1. 修改 Node, runtime, pallet 文件
2. 大部分都是样板代码
3. 签名交易需要有账户，交易的执行会从这个账户收取tx fee

本节内容重点内容在于配置，现在我们就来设置一些必要的配置项

**1.substrate-node-template/node/src/service.rs**

```
--
let client = Arc::new(client);

+++
// 生成账户放入keystore，这样pallet就可以获取该账户了，offchain使用
if config.offchain_worker.enabled {
        let keystore = keystore_container.sync_keystore();
        sp_keystore::SyncCryptoStore::sr25519_generate_new(
            &*keystore,
            node_template_runtime::pallet_template::KEY_TYPE,
            Some("//Alice"), //添加的是Alice账户，测试用的，本身有很多余额，也是Sudo
            ).expect("Creating key with account Alice should succeed.");
    }
```

ubstrate-node-template/node/Cargo.toml

```
sp-keystore = {version ="0.12.0",git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
```

**2.substrate-node-template/runtime/src/lib.rs**

注意：branch = "polkadot-v0.9.30" 版本 的Call 变为了RuntimeCall

```
use codec::Encode;
use sp_runtime::SaturatedConversion; //在此处引入两个trait

impl pallet_sudo::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type RuntimeCall = RuntimeCall;
}

// 当offchain worker调用pallet中的函数时，在此处产生真正的交易
impl<LocalCall> frame_system::offchain::CreateSignedTransaction<LocalCall> for Runtime
where
	RuntimeCall: From<LocalCall>,
{
	fn create_transaction<C: frame_system::offchain::AppCrypto<Self::Public, Self::Signature>>(
		call: RuntimeCall,
		public: <Signature as sp_runtime::traits::Verify>::Signer,
		account: AccountId,
		nonce: Index,
	) -> Option<(
		RuntimeCall,
		<UncheckedExtrinsic as sp_runtime::traits::Extrinsic>::SignaturePayload,
	)> {
		let tip = 0;

		let period =
			BlockHashCount::get().checked_next_power_of_two().map(|c| c / 2).unwrap_or(2) as u64;
		let current_block = System::block_number().saturated_into::<u64>().saturating_sub(1);
		let era = generic::Era::mortal(period, current_block);
		let extra = (
			frame_system::CheckNonZeroSender::<Runtime>::new(),
			frame_system::CheckSpecVersion::<Runtime>::new(),
			frame_system::CheckTxVersion::<Runtime>::new(),
			frame_system::CheckGenesis::<Runtime>::new(),
			frame_system::CheckEra::<Runtime>::from(era),
			frame_system::CheckNonce::<Runtime>::from(nonce),
			frame_system::CheckWeight::<Runtime>::new(),
			//pallet_asset_tx_payment::ChargeAssetTxPayment::<Runtime>::from(tip, None),
			pallet_transaction_payment::ChargeTransactionPayment::<Runtime>::from(tip),
		);
		let raw_payload = SignedPayload::new(call, extra)
			.map_err(|_| {
				//log::warn!("Unable to create signed payload: {:?}", e);
			})
			.ok()?;
		let signature = raw_payload.using_encoded(|payload| C::sign(payload, public))?;
		let address = account;
		let (call, extra, _) = raw_payload.deconstruct();
		Some((call, (sp_runtime::MultiAddress::Id(address), signature.into(), extra)))
	}
}

impl frame_system::offchain::SigningTypes for Runtime {
	type Public = <Signature as sp_runtime::traits::Verify>::Signer;
	type Signature = Signature;
}

impl<C> frame_system::offchain::SendTransactionTypes<C> for Runtime
where
	RuntimeCall: From<C>,
{
	type Extrinsic = UncheckedExtrinsic;
	type OverarchingCall = RuntimeCall;
}

/// Configure the pallet-template in pallets/template.
/// Configure the pallet-template in pallets/template.
impl pallet_template::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type AuthorityId = pallet_template::crypto::OcwAuthId;
}
```

**3.substrate-node-template/pallets/template/src/lib.rs**

```
use frame_system::{
    offchain::{
        AppCrypto, CreateSignedTransaction, SendSignedTransaction,
        Signer,
    },
};
use sp_core::crypto::KeyTypeId;

pub const KEY_TYPE: KeyTypeId = KeyTypeId(*b"ocwd");
pub mod crypto {
    use super::KEY_TYPE;
    use sp_core::sr25519::Signature as Sr25519Signature;
    use sp_runtime::{
        app_crypto::{app_crypto, sr25519},
        traits::Verify,
        MultiSignature, MultiSigner,
    };
    app_crypto!(sr25519, KEY_TYPE);

    pub struct OcwAuthId;

    impl frame_system::offchain::AppCrypto<MultiSigner, MultiSignature> for OcwAuthId {
        type RuntimeAppPublic = Public;
        type GenericSignature = sp_core::sr25519::Signature;
        type GenericPublic = sp_core::sr25519::Public;
    }

    impl frame_system::offchain::AppCrypto<<Sr25519Signature as Verify>::Signer, Sr25519Signature>
        for OcwAuthId
        {
            type RuntimeAppPublic = Public;
            type GenericSignature = sp_core::sr25519::Signature;
            type GenericPublic = sp_core::sr25519::Public;
        }
}

pub mod pallet {
	use super::*;
	use frame_support::pallet_prelude::*;
	use frame_system::pallet_prelude::*;
	use sp_std::{vec, vec::Vec};
	--
	
	#[pallet::config]
	pub trait Config: frame_system::Config + CreateSignedTransaction<Call<Self>> {
		/// Because this pallet emits events, it depends on the runtime's definition of an event.
		type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;
		type AuthorityId: AppCrypto<Self::Public, Self::Signature>;
	}
	
	#[pallet::call]
	impl<T: Config> Pallet<T> {
		--
		// 在链上定义函数,提交变量，如上面的函数一样，该怎么写就怎么写
		#[pallet::weight(0)]
		pub fn submit_data(origin: OriginFor<T>, payload: Vec<u8>) -> DispatchResultWithPostInfo {
			let _who = ensure_signed(origin)?;

			log::info!("in submit_data call: {:?}", payload);

			Ok(().into())
		}
	}
	
	#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			// 通过offchain worker获得要提交的变量,可以是从外部获取
			let payload: Vec<u8> = vec![1, 2, 3, 4, 5, 6, 7, 8];

			// 执行辅助函数
			_ = Self::send_signed_tx(payload);

			log::info!("Leave from offchain workers!: {:?}", block_number);
		}

		fn on_initialize(_n: T::BlockNumber) -> Weight {
			log::info!("in on_initialize!");
			let weight: Weight = Default::default();
			weight
		}
		fn on_finalize(_n: T::BlockNumber) {
			log::info!("in on_finalize!");
		}

		fn on_idle(_n: T::BlockNumber, _remaining_weight: Weight) -> Weight {
			log::info!("in on_idle!");
			let weight: Weight = Default::default();
			weight
		}
	}

	impl<T: Config> Pallet<T> {
		fn send_signed_tx(payload: Vec<u8>) -> Result<(), &'static str> {
			let signer = Signer::<T, T::AuthorityId>::all_accounts();
			if !signer.can_sign() {
				return Err(
					"No local accounts available. Consider adding one via `author_insertKey` RPC.",
				)
			}

			// 在辅助函数中调用 链上函数
			let results = signer
				.send_signed_transaction(|_account| Call::submit_data { payload: payload.clone() });

			for (acc, res) in &results {
				match res {
					Ok(()) => log::info!("[{:?}] Submitted data:{:?}", acc.id, payload),
					Err(e) => log::error!("[{:?}] Failed to submit transaction: {:?}", acc.id, e),
				}
			}

			Ok(())
		}
	}
}
```

**3.substrate-node-template/pallets/template/Cargo.toml**

```
[dependencies]
--
sp-core = { version ="6.0.0",default-features = false,git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
```

编译运行

![image-20221024224723809](/Users/qinjianquan/Career/substrate/static/offchain-tx.png)

重点：

1. 配置offchain worker三个文件，node,runtime,template
2. Offchain 逻辑：链上交易函数 - 分装在辅助函数中调用 - offchain 传递参数并调用辅助函数
3. 当前块发起的交易在下一个块中执行

### Case 8 : 向链上发送不签名交易

**目的**

1. 无需修改 Node（不配置账户用于签名支付交易费用）, 只需修改runtime, pallet 文件
2. 大部分都是样板代码
3. 学习宏 #[pallet::validate_unsigned],TransactionValidity,ValidTransaction,ensure_none等
4. 每个块的不签名交易不建议发送太多

更改配置

**substrate-node-template-case/runtime/src/lib.rs**

```
++++
use super::*;

impl<C> frame_system::offchain::SendTransactionTypes<C> for Runtime
where
	RuntimeCall: From<C>,
{
	type Extrinsic = UncheckedExtrinsic;
	type OverarchingCall = RuntimeCall;
}
```

**substrate-node-template-case/pallets/template/Cargo.toml**

```
[dependencies]
--
sp-runtime = { version = "6.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
log =  { version = "0.4.17", default-features = false }

[features]
default = ["std"]
std = [
	--
	"sp-runtime/std",
]
```

**substrate-node-template-case/pallets/template/src/lib.rs**

```
use frame_system::offchain::{
    SubmitTransaction,
};
use sp_runtime::{
    transaction_validity::{InvalidTransaction, TransactionValidity, ValidTransaction},
};
```

```
#[pallet::config]
	pub trait Config:
		frame_system::Config + frame_system::offchain::SendTransactionTypes<Call<Self>>
	{
		/// Because this pallet emits events, it depends on the runtime's definition of an event.
		type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
	}
```

```
#[pallet::call]
	impl<T: Config> Pallet<T> {
		--
		#[pallet::weight(0)]
		pub fn submit_data_unsigned(origin: OriginFor<T>, n: u64) -> DispatchResult {
			ensure_none(origin)?;

			log::info!("in submit_data_unsigned: {:?}", n);

			// Return a successful DispatchResultWithPostInfo
			Ok(())
		}
	}

	#[pallet::hooks]
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			let value: u64 = 42;

			let call = Call::submit_data_unsigned { n: value };

			_ = SubmitTransaction::<T, Call<T>>::submit_unsigned_transaction(call.into()).map_err(
				|_| {
					log::error!("Failed in offchain_unsigned_tx");
				},
			);

			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}
	
	#[pallet::validate_unsigned]
	impl<T: Config> ValidateUnsigned for Pallet<T> {
		type Call = Call<T>;

		fn validate_unsigned(_source: TransactionSource, call: &Self::Call) -> TransactionValidity {
			if let Call::submit_data_unsigned { n: _ } = call {
				//let provide = b"submit_xxx_unsigned".to_vec();
				ValidTransaction::with_tag_prefix("ExampleOffchainWorker")
					.priority(10000)
					.and_provides(1)
					.longevity(3)
					.propagate(true)
					.build()
			} else {
				InvalidTransaction::Call.into()
			}
		}
	}

	impl<T: Config> Pallet<T> {
		// 暂未写业务逻辑
	}
```

编译并运行

![image-20221025213640309](/Users/qinjianquan/Career/substrate/static/unsigned-tx.png)

可以看到，我们已经成功的提交了不签名交易

更多案例可以参考官方的完整实现

https://github.com/paritytech/substrate/blob/master/frame/examples/offchain-worker/src/lib.rs

### Case 9 : 使用Offchain Indexing特性实现从链上向Offchain Storage中写入数据

本节内容我们使用一个较为完整的案例实现，具体见代码：

https://github.com/shiyivei/substrate-advanced-course/tree/master/lesson4/backend/pallets/kitties

branch = "polkadot-v0.9.30" 版本和 branch = "polkadot-v0.9.28" 配置有些许不一样，但是逻辑是一样的，这部分内容，挖坑后补。最近又要开始很忙了。OK，关于Offchain worker的介绍我们要再告一段落了。下个主题见



**substrate-node-template/node/src/service.rs**

```
let client = Arc::new(client);
// +++ 配置账户
if config.offchain_worker.enabled {
		let keystore = keystore_container.sync_keystore();
		sp_keystore::SyncCryptoStore::sr25519_generate_new(
			&*keystore,
			node_template_runtime::pallet_kitties::KEY_TYPE,
			Some("//Alice"),
		)
		.expect("Creating key with account Alice should succeed.");
	}
```

**Runtime**

```
impl<LocalCall> frame_system::offchain::CreateSignedTransaction<LocalCall> for Runtime
where
	RuntimeCall: From<LocalCall>,
{
	fn create_transaction<C: frame_system::offchain::AppCrypto<Self::Public, Self::Signature>>(
		call: RuntimeCall,
		public: <Signature as sp_runtime::traits::Verify>::Signer,
		account: AccountId,
		nonce: Index,
	) -> Option<(Call, <UncheckedExtrinsic as sp_runtime::traits::Extrinsic>::SignaturePayload)> {
		let tip = 0;

		let period =
			BlockHashCount::get().checked_next_power_of_two().map(|c| c / 2).unwrap_or(2) as u64;
		let current_block = System::block_number().saturated_into::<u64>().saturating_sub(1);
		let era = generic::Era::mortal(period, current_block);
		let extra = (
			frame_system::CheckNonZeroSender::<Runtime>::new(),
			frame_system::CheckSpecVersion::<Runtime>::new(),
			frame_system::CheckTxVersion::<Runtime>::new(),
			frame_system::CheckGenesis::<Runtime>::new(),
			frame_system::CheckEra::<Runtime>::from(era),
			frame_system::CheckNonce::<Runtime>::from(nonce),
			frame_system::CheckWeight::<Runtime>::new(),
			//pallet_asset_tx_payment::ChargeAssetTxPayment::<Runtime>::from(tip, None),
			pallet_transaction_payment::ChargeTransactionPayment::<Runtime>::from(tip),
		);
		let raw_payload = SignedPayload::new(call, extra)
			.map_err(|_| {
				//log::warn!("Unable to create signed payload: {:?}", e);
			})
			.ok()?;
		let signature = raw_payload.using_encoded(|payload| C::sign(payload, public))?;
		let address = account;
		let (call, extra, _) = raw_payload.deconstruct();
		Some((call, (sp_runtime::MultiAddress::Id(address), signature.into(), extra)))
	}
}

impl frame_system::offchain::SigningTypes for Runtime {
	type Public = <Signature as sp_runtime::traits::Verify>::Signer;
	type Signature = Signature;
}

impl<C> frame_system::offchain::SendTransactionTypes<C> for Runtime
where
	RuntimeCall: From<C>,
{
	type Extrinsic = UncheckedExtrinsic;
	type OverarchingCall = RuntimeCallCall;
}
/// Configure the pallet-template in pallets/template.
impl pallet_template::Config for Runtime {
	type RuntimeEvent = RuntimeEvent;
	type Randomness = RandomnessCollectiveFlip;
	type KittyIndex = u32;
	type Currency = Balances;
	type MaxKittyIndex = ConstU32<512>;
	type KittyPrice = ConstU128<512>;
	type AuthorityId = pallet_template::crypto::KittiesAuthId;
}
```

template

```

#[frame_support::pallet] //开发pellet所用的宏
pub mod pallet {

	//引入类型或函数
	use frame_support::traits::{Randomness, ReservableCurrency};
	use frame_support::{log, pallet_prelude::*, traits::Currency}; /* Currency,固定引入方法 */
	use frame_system::pallet_prelude::*; /* 比如一些方便签名和验证的方法 */
	use sp_io::hashing::blake2_128;

	use frame_support::inherent::Vec;
	use frame_system::offchain::{
		AppCrypto, CreateSignedTransaction, SendSignedTransaction, Signer,
	};
	use sp_io::offchain_index;
	use sp_runtime::{offchain::storage::StorageValueRef, traits::Zero};

	//定义新类型，并想为其实现一些必要的trait时，可以直接引用现成的类型，无需重新定义trait
	use sp_runtime::traits::{AtLeast32Bit, Bounded, CheckedAdd}; //引入trait

	//定义一个类型别名,使用Currency这个trait，先得引入，如下也是常见写法，凡是涉及到金钱的，
	// 这个类型必不可少
	type BalanceOf<T> =
		<<T as Config>::Currency as Currency<<T as frame_system::Config>::AccountId>>::Balance;

	//定义类型(类型别名，在业务中易于识别),本来这个类型我们是定义在trait外部的，
	// 但是现在改为定义在trait内部，然后用的时候指定 type KittyIndex = u32;

	//定义函数
	//函数要使用trait中的关联类型，必须先为定义trait为泛型约束
	#[pallet::type_value]
	pub fn GetDefaultValue<T: Config>() -> T::KittyIndex {
		//取0
		0_u8.into()
	}

	//定义业务数据
	#[derive(Encode, Decode, Clone, PartialEq, Eq, Debug, TypeInfo, MaxEncodedLen)]
	pub struct Kitty {
		pub dna: [u8; 16],
		pub asset: u32,
	}

	//定义接口
	#[pallet::config]
	pub trait Config: frame_system::Config + CreateSignedTransaction<Call<Self>> {
		type Event: From<Event<Self>> + IsType<<Self as frame_system::Config>::Event>;
		type Randomness: Randomness<Self::Hash, Self::BlockNumber>;

		//定义关联类型 Currency
		type Currency: ReservableCurrency<Self::AccountId>;

		//在config中定义类型，并为其增加必须的trait，然后在runtime中指定
		//例子：无符号整数类型定义模版
		type KittyIndex: Parameter + AtLeast32Bit + Default + Copy + Bounded + MaxEncodedLen;

		//定义常量
		#[pallet::constant]
		type MaxKittyIndex: Get<u32>;

		#[pallet::constant]
		type KittyPrice: Get<BalanceOf<Self>>;

		type AuthorityId: AppCrypto<Self::Public, Self::Signature>;
	}

	//定义Pallet结构体
	#[pallet::pallet]
	#[pallet::generate_store(pub(super) trait Store)] //生成包含所有存储项的接口
	pub struct Pallet<T>(_);

	//定义存储
	#[pallet::storage]
	#[pallet::getter(fn next_kitty_id)]
	//同样的，不管是在函数中使用trait中的关联类型还是在新的类型中使用，都需要将trait作为泛型约束
	pub type NextKittyId<T: Config> =
		StorageValue<_, T::KittyIndex, ValueQuery, GetDefaultValue<T>>;

	//定义存储
	#[pallet::storage]
	#[pallet::getter(fn kitties)]
	pub type Kitties<T: Config> = StorageMap<_, Blake2_128Concat, T::KittyIndex, Kitty>;

	//定义存储
	#[pallet::storage]
	#[pallet::getter(fn kitty_owner)]
	pub type KittyOwner<T: Config> = StorageMap<_, Blake2_128Concat, T::KittyIndex, T::AccountId>;

	//定义存储
	#[pallet::storage]
	# [pallet::getter(fn all_kitties)]
	pub type AllKitties<T: Config> = StorageMap<
		_,
		Blake2_128Concat,
		T::AccountId,
		BoundedVec<T::KittyIndex, T::MaxKittyIndex>,
		ValueQuery,
	>;

	//定义执行成功事件
	#[pallet::event]
	#[pallet::generate_deposit(pub(super) fn deposit_event)]
	pub enum Event<T: Config> {
		KittyCreated(T::AccountId, T::KittyIndex, Kitty),
		KittyBred(T::AccountId, T::KittyIndex, Kitty),
		KittyTransferred(T::AccountId, T::AccountId, T::KittyIndex),
	}

	//定义执行失败错误
	#[pallet::error]
	pub enum Error<T> {
		InvalidKittyId,
		KittyIdOverflow,
		NotOwner,
		SameKittyId,
		NotEnoughBalance,
		OwnTooManyKitties,
	}

	const ONCHAIN_TX_KEY: &[u8] = b"kitty_pallet::indexing01";

	#[derive(Debug, Encode, Decode, Default)]
	// struct IndexingData<T: Config>(T::KittyIndex);
	struct IndexingData<T: Config>(T::KittyIndex);

	#[pallet::hooks]
	// 沉睡8000ms，根据block number 奇偶性确定kitty id
	impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
		fn offchain_worker(block_number: T::BlockNumber) {
			log::info!("Hello World from offchain workers!: {:?}", block_number);

			// 获取key
			let key = Self::derived_key(block_number);

			// log::info!("the new store key is {:?}", key);

			// 读取存储内容
			let storage_content = StorageValueRef::persistent(&key);

			//读取内容并更新
			if let Ok(Some(data)) = storage_content.get::<IndexingData<T>>() {
				// if let Some((Some(data)) = storage_ref.get::<IndexingData>() {
				// Sleep 8000ms to simulate heavy calculation for kitty asset index.
				log::info!("It needs a lot of time to create a kitty {:?}", block_number);
				let timeout = sp_io::offchain::timestamp()
					.add(sp_runtime::offchain::Duration::from_millis(8000));
				sp_io::offchain::sleep_until(timeout);

				log::info!("new kitty has been created and stored both onchain and offchain");

				let kitty_id = data.0.into();

				log::info!("get kitty index from offchain {:?}", kitty_id);

				if block_number % 2u32.into() != Zero::zero() {
					_ = Self::send_signed_tx(kitty_id, 100);
					log::info!("now the block number is odd, let's update kitty's asset as 100, and kitty id is {:?}",kitty_id);
				} else {
					_ = Self::send_signed_tx(kitty_id, 200);
					log::info!("now the block number is even, let's update kitty's asset as 200, and kitty id is {:?}",kitty_id);
				}
			} else {
				log::info!("No off-chain indexing data retrieved.");
			}

			log::info!("Leave from offchain workers!: {:?}", block_number);
		}
	}

	//定义执行逻辑
	#[pallet::call]
	impl<T: Config> Pallet<T> {
		#[pallet::weight(10_000)]
		//创建kitty
		pub fn create(origin: OriginFor<T>) -> DispatchResult {
			//验证签名
			let who = ensure_signed(origin)?;

			//获取kitty价格
			let kitty_price = T::KittyPrice::get();

			//判断创建者的钱包余额是否足够，这里是锁仓create
			ensure!(T::Currency::can_reserve(&who, kitty_price), Error::<T>::NotEnoughBalance);

			//获取 kitty_id
			let kitty_id = Self::get_next_id().map_err(|_| Error::<T>::InvalidKittyId)?;

			//获取dna
			let dna = Self::random_value(&who);
			//创造新的 kitty
			let kitty = Kitty { dna, asset: 0 };

			//质押
			T::Currency::reserve(&who, kitty_price)?;

			//存储kitty和id
			Kitties::<T>::insert(kitty_id, &kitty);

			//存储kittyId和所有者
			KittyOwner::<T>::insert(kitty_id, &who);

			//获取下一个kitty的id
			let next_kitty_id = kitty_id
				.checked_add(&(T::KittyIndex::from(1_u8)))
				.ok_or(Error::<T>::KittyIdOverflow)
				.unwrap();

			//设置下一个kitty_id
			NextKittyId::<T>::set(next_kitty_id);

			AllKitties::<T>::try_mutate(&who, |ref mut kitties| {
				kitties.try_push(kitty_id).map_err(|_| Error::<T>::OwnTooManyKitties)?;
				Ok::<(), DispatchError>(())
			})?;

			// 同时把数据存到链下存储中
			Self::store_kitty_to_indexing(kitty_id);

			Self::deposit_event(Event::KittyCreated(who, kitty_id, kitty));

			Ok(())
		}

		//繁殖kitty
		#[pallet::weight(10_000)]
		pub fn breed(
			origin: OriginFor<T>,
			kitty_id_1: T::KittyIndex,
			kitty_id_2: T::KittyIndex,
		) -> DispatchResult {
			let who = ensure_signed(origin)?;

			//获取kitty价格
			let kitty_price = T::KittyPrice::get();

			//判断钱包余额是否足够
			ensure!(T::Currency::can_reserve(&who, kitty_price), Error::<T>::NotEnoughBalance);

			// 确保不是相同的kitty
			ensure!(kitty_id_1 != kitty_id_2, Error::<T>::SameKittyId);

			//通过kitty_id先获取两只kitty
			let kitty_1 = Self::get_kitty(kitty_id_1).map_err(|_| Error::<T>::InvalidKittyId)?;
			let kitty_2 = Self::get_kitty(kitty_id_2).map_err(|_| Error::<T>::InvalidKittyId)?;

			//获取新的kitty的id
			let kitty_id = Self::get_next_id().map_err(|_| Error::<T>::InvalidKittyId)?;

			// 得到随机的dna
			let selector = Self::random_value(&who);

			// 定义数据
			let mut data = [0u8; 16];

			for i in 0..kitty_1.dna.len() {
				data[i] = (kitty_1.dna[i] & selector[i]) | (kitty_2.dna[i] & selector[i]);
			}

			// 生成新kitty
			let new_kitty = Kitty { dna: data, asset: 0 };

			//质押
			T::Currency::reserve(&who, kitty_price)?;

			//保存id
			Kitties::<T>::insert(kitty_id, &new_kitty);
			//保存所有者
			KittyOwner::<T>::insert(kitty_id, &who);

			//获取新的id
			let next_kitty_id = kitty_id
				.checked_add(&(T::KittyIndex::from(1_u32)))
				.ok_or(Error::<T>::KittyIdOverflow)
				.unwrap();

			NextKittyId::<T>::set(next_kitty_id);

			AllKitties::<T>::try_mutate(&who, |ref mut kitties| {
				kitties.try_push(kitty_id).map_err(|_| Error::<T>::OwnTooManyKitties)?;
				Ok::<(), DispatchError>(())
			})?;

			Self::store_kitty_to_indexing(kitty_id);

			Self::deposit_event(Event::KittyBred(who, kitty_id, new_kitty));

			Ok(())
		}

		#[pallet::weight(10_000)]
		pub fn transfer(
			origin: OriginFor<T>,
			kitty_id: T::KittyIndex,
			new_owner: T::AccountId,
		) -> DispatchResult {
			let who = ensure_signed(origin)?;

			//获取kitty价格
			let kitty_price = T::KittyPrice::get();

			//判断钱包余额是否足够
			ensure!(T::Currency::can_reserve(&who, kitty_price), Error::<T>::NotEnoughBalance);

			//获取要转移的kitty，验证kitty_id有效性
			Self::get_kitty(kitty_id).map_err(|_| Error::<T>::InvalidKittyId)?;

			//确保是拥有者
			ensure!(Self::kitty_owner(kitty_id) == Some(who.clone()), Error::<T>::NotOwner);

			//释放和锁定金钱
			T::Currency::unreserve(&who, kitty_price);
			T::Currency::reserve(&new_owner, kitty_price)?;

			//更改owner，键相同，值覆盖
			KittyOwner::<T>::insert(kitty_id, &new_owner);

			//移除某个account下的kitties信息
			AllKitties::<T>::try_mutate(&who, |ref mut kitties| {
				let index = kitties.iter().position(|&r| r == kitty_id).unwrap();
				kitties.remove(index);
				Ok::<(), DispatchError>(())
			})?;

			//更改新的owner
			AllKitties::<T>::try_mutate(&new_owner, |ref mut kitties| {
				// print!("### before push kitty to Kitties");
				kitties.try_push(kitty_id).map_err(|_| Error::<T>::OwnTooManyKitties)?;
				// print!("### after push kitty to Kitties");
				Ok::<(), DispatchError>(())
			})?;

			Self::deposit_event(Event::KittyTransferred(who, new_owner, kitty_id));

			Ok(())
		}
		#[pallet::weight(0)]
		//更新链上数据
		pub fn update_kitty(
			origin: OriginFor<T>,
			kitty_id: T::KittyIndex,
			asset: u32,
		) -> DispatchResultWithPostInfo {
			let _who = ensure_signed(origin)?;

			let kitty = Self::get_kitty(kitty_id).map_err(|_| Error::<T>::InvalidKittyId)?;

			let new_kitty = Kitty { dna: kitty.dna, asset };

			Kitties::<T>::insert(kitty_id, &new_kitty);

			Ok(().into())
		}
	}

	//定义辅助性的函数,这些函数不需要weights，但是实际上他们会被需要weights的函数在其内部所调用，
	// 间接的也说明并不是无成本的
	impl<T: Config> Pallet<T> {
		fn random_value(sender: &T::AccountId) -> [u8; 16] {
			let payload = (
				T::Randomness::random_seed(),
				&sender,
				<frame_system::Pallet<T>>::extrinsic_index(),
			);
			payload.using_encoded(blake2_128)
		}

		fn get_next_id() -> Result<T::KittyIndex, ()> {
			let kitty_id = Self::next_kitty_id();
			match kitty_id {
				_ if T::KittyIndex::max_value() <= kitty_id => Err(()),
				val => Ok(val),
			}
		}

		fn get_kitty(kitty_id: T::KittyIndex) -> Result<Kitty, ()> {
			match Self::kitties(kitty_id) {
				Some(kitty) => Ok(kitty),
				None => Err(()),
			}
		}

		// 辅助函数
		fn derived_key(block_number: T::BlockNumber) -> Vec<u8> {
			block_number.using_encoded(|encoded_bn| {
				ONCHAIN_TX_KEY
					.clone()
					.into_iter()
					.chain(b"/".into_iter())
					.chain(encoded_bn)
					.copied()
					.collect::<Vec<u8>>()
			})
		}

		// 写入存储
		fn store_kitty_to_indexing(kitty_id: T::KittyIndex) {
			let key = Self::derived_key(frame_system::Pallet::<T>::block_number());

			// log::info!("the store key is {:?}", key);

			let data: IndexingData<T> = IndexingData(kitty_id);
			offchain_index::set(&key, &data.encode());
			log::info!("kitty id has been stored in offchain storage:{:?}", kitty_id);
		}

		fn send_signed_tx(kitty_id: T::KittyIndex, payload: u32) -> Result<(), &'static str> {
			let signer = Signer::<T, T::AuthorityId>::all_accounts();
			if !signer.can_sign() {
				return Err(
					"No local accounts available. Consider adding one via `author_insertKey` RPC.",
				)
			}

			log::info!("updating kitty asset, {:?}", kitty_id);
			// update_kitty info
			let results = signer.send_signed_transaction(|_account| Call::update_kitty {
				kitty_id,
				asset: payload,
			});

			for (acc, res) in &results {
				match res {
					Ok(()) => log::info!("[{:?}] Submitted data:{:?}", acc.id, (kitty_id, payload)),
					Err(e) => log::error!("[{:?}] Failed to submit transaction: {:?}", acc.id, e),
				}
			}

			Ok(())
		}
	}
}
```

添加依赖

```
sp-core = { version = "6.0.0", git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
sp-io = { version = "6.0.0", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v0.9.30" }
```

