# Livepeer 白皮书

**去中心化视频流媒体网络-协议和经济激励**

Doug Petkanics <doug@livepeer.org>  
Eric Tang <eric@livepeer.org>

## 摘要 ###########################################

Livepeer项目旨在提供完全去中心化、高度可扩展和加密代币激励的实时视频流网络协议，并产生可作为分散式开发（web3）架构中的实时媒体层的解决方案。此外，Livepeer旨在为现有的Broadcaster提供经济高效的集中广播解决替代方案。在本文档中，我们描述了一种基于股权的委托协议 — Livepeer协议，用于以游戏理论上安全的方式激励实时视频广播网络中的参与者。此外，我们还提出了分散工作的可放大验证和防止无用工作的解决方案，试图在通货膨胀系统中确定最佳代币分配方式。


## 目录 ###########################################

* [介绍和背景](#介绍和背景)
    * [实时视频架构](#实时视频架构)
* [Livepeer 协议](#livepeer-协议)
    * [视频片段](#视频片段)
    * [Livepeer 代币](#livepeer-代币)
    * [协议角色](#协议角色)
    * [共识](#共识)
    * [绑定+委托](#绑定+委托)
    * [转码器()交易](#转码器()交易)
    * [广播+转码工作](#广播+转码工作)
        * [预处理](#预处理)
        * [工作](#工作)
        * [结束工作](#结束工作)
    * [工作验证](#工作验证)
        * [关于Truebit的注释](#关于Truebit的注释)
    * [代币生成](#代币生成)
    * [削减](#削减)
    * [代币分布](#代币分布)
    * [治理](#治理)
* [攻击](#攻击)
    * [共识攻击](#共识攻击)
    * [DDoS](#ddos)
    * [无用或自处理转码器](#无用或自处理转码器)
    * [转码器恶意破坏](#转码器恶意破坏)
    * [连锁重组](#连锁重组)
* [实时视频分发](#实时视频分发)
* [用例](#用例)
    * [即用即付内容消费](#即用即付内容消费)
    * [自动扩展社交视频服务](#自动扩展社交视频服务)
    * [不受审查的直播新闻](#不受审查的直播新闻)
    * [带视频功能的DApps](#带视频功能的DApps)
* [总结](#总结)
* [附录](#附录)
    * [Livepeer协议参数参考](#Livepeer协议参数参考)
    * [Livepeer协议交易类型](#Livepeer协议交易类型)
* [参考](#参考)

*注意：本文最初发布于2017年4月。2018年12月提出了一个名为“Streamflow”的扩展提案，其中概述了以下一些想法的迭代和增强理念。在这里阅读 [Streamflow Proposal](https://github.com/livepeer/wiki/blob/master/STREAMFLOW.md).* 

## 介绍和背景 ###########################################

在过去几年中，随着[以太坊](http://ethereum.org)等网络的出现，分布式网络的愿景开始得以实现，以推动无信任计算，并通过[Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/)和[IPFS/Filecoin](http://ipfs.io) 启用去中心化存储和内容分发、比特币和各种代币项目，以促进p2p价值转移，以及分散名称注册 比如 [Blockstack](http://blockstack.org)和[ENS](http://ens.readthedocs.io/en/latest/introduction.html) ，为内容和身份提供人类可访问的名称。这些元素构成了去中心化应用程序（DApps）的基础，这些应用程序以大部分静态或不经常更新的Web或移动内容的形式构建，但目前DApps仍然缺乏以开放和去中心化方式纳入流媒体和数据的能力。Livepeer项目的目标是通过互联网去中心化实时视频广播。

[Livepeer Project Overview](https://github.com/livepeer/wiki/wiki/Project-Overview)提供了对互联网上直播视频当前状态的精彩介绍。本白皮书将主要关注Livepeer的加密经济协议细节，而不是业务案例，但总的来说，本文件中描述了实时流式传输的快速增长，集中化和昂贵的现状。另一方面，由于可以服务的连接数量不受限制，在完全分散的P2P解决方案中，节点能够自行进行计算且用于数据流直播视频的带宽将更加开放和可扩展。

这种技术确定具有一定程度的可用性，但到目前为止，还没有动力让用户运行提供此功能的节点，也没有适当的资金来通过以下方式开发协议：使整个互联网受益，而不是一个集中的公司。然而，随着最近出现的加密代币供电协议[[2, 3](#参考)]，现在有机会同时激励用户为实时视频广播贡献计算和带宽，其方式与融资方式一致。开发一种开放式媒体服务器解决方案，能够根据达到所有设备所需的所有最新标准和格式提供实时视频流。此外，传统上被认为是代币供电协议的结果的经济行为表明，为了使用Livepeer网络，Broadcaster的成本可能比任何集中式解决方案的成本更便宜。

随着Livepeer技术和协议的交付，它将使用户能够参与以下流程：

1.	通过相机、手机、屏幕或网络摄像头捕获视频，然后将其发送到Livepeer网络。
2.	在网络中运行的节点将其编码为所有必要的格式，以到达每个支持的设备。通过ETHbroadcaster支付的费用对运行这些节点的用户进行激励，并通过协议代币建立声誉，以获得未来执行更多工作的权利。
3.	网络上的所有用户可以请求查看流数据，并自动地、近乎实时地分发给他们。

<img src="https://s3.amazonaws.com/livepeerorg/LPExample.png" alt="Livepeer Network Example" style="width: 750px">

### 实时视频架构

用于直播视频广播的技术栈已经发展了很多年并且包含许多层。Broadcaster需要在源头捕获视频，在媒体服务器接口处理视频并将其转码为多种不同格式，通过网络分发视频，然后最终消费者可以高感知质量来播放视频。当人们通过这个架构思考时会导致经济问题，broadcaster例如应该是broadcaster还是由消费者来问转移视频而支付带宽费用。

目前，一个典型的流媒体直播平台需要同时支持RTMP、HLS、Mpeg-Dash视频格式的H.264和VP8编解码器。像H.265/HEVC、VP9和AV1这样的新编解码器将在不久的将来变得更受欢迎，因为消费者更倾向于高质量的视频。 仅对于HLS，[Apple建议](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) 位速率范围为 145kb/s一直到7800kb/s，以便在不同的条件下为不同类型的设备提供服务。所有这些都会增加视频直播的复杂性和成本。

现有的分散式开发架构(web3)包含了实时视频平台所需的一些层级的解决方案，比如文件传输和支付，但是目前还没有针对实时视频的捕捉、接口、代码转换和处理以及服务层的解决方案。为此，Livepeer引入了 [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) — 一种媒体服务器开源执行程序，它提供了DApp开发人员和现有Broadcaster在其应用程序中构建live功能所需的所有实时视频的特定功能。 [详情请点击这里](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server).

作为一个独立的应用程序，任何开发人员都可以在LPMS之上构建一个活动的应用程序，但是它仍然是集中式的，并且需要通过传统的方法进行扩展。然而，当Livepeer网络上的每个节点都运行LPMS时，该协议的经济激励机制将确保这些节点发挥它们的处理能力和带宽，为实时视频的编码和分发服务。此外，**开发人员还可以使用自放大、即付即用的网络，它们只需将实时数据流发送到网络中，就可以获得可放大、支付和媒体托管的实现细节信息**。

## Livepeer协议 ###########################################

Livepeer协议定义了直播流生态系统中的各个参与者如何以安全和经济合理的方式参与直播进程。协议中需要解决的两个主要领域是以高性能和可扩展的方式从源到大量消费者的实时视频的实际分发，以及经济激励方式，从而鼓励消费者以符合游戏理论的安全方式参与网络过程。虽然本白皮书将探讨实时视频发布本身，并涉及经济协议，但它将主要关注后者，以证明其安全性和经济一致性。在最高级别，该协议旨在：

- 允许任何节点将实时视频发送到网络，并可选择付费以将其转码为各种格式和码率。
- 允许通过任何节点从网络上请求获取视频。
- 允许参与者为视频的转码和分发发挥其处理能力和带宽，并相应地进行补偿。

在去中心化的网络中，参与者将按照他们贡献的工作量按比例获得奖励，其中确保安全性需要解决的两大挑战是：

- 是否可以验证已正确完成节点所做的工作？
- 节点获得奖励的原因是对于为网络贡献价值的实际工作，还是为了不公平地获得代币分配而进行虚假工作？

Livepeer协议旨在解决工作验证和防止虚假工作的问题，同时还提供网络自动可放大性的解决方案，以及长期范围内的协议进化集成治理。

### 视频段

我们将Livepeer中媒体的核心单位称为 `视频段`. 视频段 是时间长度为`t`的多路复用音频和视频的时间切块。Livepeer网络中的每个段都是唯一的，并包含加密证据，以验证Broadcaster是否为此特定网段提供此特定数据。每个流由许多连续的段组成，每个段中均包含标识其正确排序的序列号。段中包含以下字段：

| 视频片段字段 | 描述 |
|--------|--------|
| **StreamID** | 标识此段所属的源节点和流。 |
| **SequenceNumber** | 此片段所属原始流的顺序。 |
| **DataPayload** | 二进制元数据和表示此段中音频/视频的数据。 |
| **DataHash** | 数据有效负载的哈希值。 |
| **BroadcasterSignature** | 来自广告公司的`Priv(StreamID, SequenceNumber, hash(StreamID, SequenceNumber, DataHash))`签名，可用于证明并验证这是否是该Broadcaster唯一段的真实数据。 |

Livepeer协议通常使用段作为转码、分发和支付的工作单元。

### Livepeer代币

Livepeer Token（LPT）是Livepeer网络的协议代币，但它不是交换代币的媒介。Broadcaster使用以太坊的以太网（ETH）在网络上播放视频。进行处理和提供带宽的节点以向broadcaster收费的形式获得ETH。LPT是一种股份代币，对于希望在网络上工作的参与者，它可以协调在网络上分配工作的方式，并提供安全性，确保能够如实、正确地完成工作。LPT具有以下用途：

- 用作委托证明收益系统中的绑定机制，其中收益被委托给参与协议的Transcoder（或verifier）以转码视频和进行验证工作。此外，由于协议违规而发生的代币和潜在的削减是必要的，以便保护网络免受攻击。更多信息，请阅读下文。
- 它按照股份和委托代币数量的比例路由工作，本质上起到协调机制的作用。
- 它是一个特定于Livepeer生态系统的帐户单元，构成SectorCoin概念的基础，适用于将来引入的其他功能 [[4](#references)]。DVR、隐藏式字幕、广告插入/货币化和分析等服务都可以插入Livepeer生态系统中，并可利用LPT提供的安全性。

将分发Livepeer代币的初始分配，以便收益相关者可以在网络中履行各种角色并使用网络，然后根据随时间推移的算法编程来发布附加代币。请参阅 [代币分发](#token-distribution) 部分。

遵循以太坊和许多流行的ERC20代币 [[16](#references)]的惯例，LPT将被10 ^ 18整除，如LPT本身等的较大面额旨在用于用户级交易，而较小面额则用于协议核算目的。

### 协议角色

在继续之前，我们需首先定义网络中的角色，以便讨论协议时有一个共同的词汇表。 Livepeer节点是运行Livepeer软件的任何计算机。

| 节点角色 | 描述 |
|--------|-----------|
| **Broadcaster** | 发布原始流的Livepeer节点 |
| **Transcoder** | 执行将流转码为另一种编解码器、码率或打包格式工作的Livepeer节点。 |
| **Relay Node** | 参与实时视频分发和协议消息传递的Livepeer节点，但不一定执行转码工作。 |
| **Consumer** | 请求streamLivepeer节点，可能通过网关查看或通过其应用程序或DApp为用户提供服务。 |

除了运行Livepeer节点的用户所扮演的上述角色之外，该协议还将参考以下系统。虽然在谈及可能实现的目标时我们会使用某些特定系统，但如果它们提供类似的功能和加密经济保证，也可以交换替代系统：

| 系统的作用 | 描述 |
|-------|----------|
| **Swarm** | 内容寻址存储平台。可以通过SWEAR协议 [[7, 12](#references)]保证数据在验证过程中暂时可用。 *(注意，在本文档中，我们指的是Swarm，但是如果数据可用性能够得到保证，则可以替换其他内容寻址存储平台)。* |
| **Livepeer Smart Contract** | 在以太网络上运行的智能合约 [[1](#references)]。 |
| **Truebit** | 黑盒验证协议，保证了链上计算的正确性（成本很高） [[6](#references)]。 (<http://truebit.io>) |

下面是对各种角色的可视化概述，同时描述了它们在以下描述的工作验证过程中相互通信的方式。

<img src="https://livepeer-dev.s3.amazonaws.com/docs/lpprotocol.png" alt="Protocol Visual Overview" style="width: 750px">  

*视频片段从Broadcaster流向Transcoder，最终流向消费者。Transcoder能够确保视频有签名和工作证明，以便参与工作验证的过程。*

**Transcoder注释：** Transcoder在Livepeer生态系统中扮演着最重要的角色。它们可以采用输入流并将其及时转换为多种不同格式，以实现低延迟分发。因此，高可用性、高效、强大的硬件（可能具有GPU加速转码），以及高带宽连接和可靠的DevOps实践在Transcoder中至关重要。与其他网络参与程序相比，Transcoder数据流失得更少，因为当它们承担转码流的工作时，如果从网络中退出，则最终效果就会不尽人意。虽然网络可以扩展以支持许多参与者扮演Transcoder的角色（并获得必要的代币分配），但这是大多数网络参与者所委派的特殊角色，从而确保维持对于Broadcaster具有很高价值的可靠网络。以下是关于委托的详细信息。

### 共识

Livepeer有一个两层共识系统。LPT分类账和交易由底层区块链（如以太坊）保护。任何LPT代币的转移或系统中的交易都可以被认为已经确认具有与基础工作证明或股权区块链证明相同的安全性。然而，第二层决定了新生成的LPT的分布。这由Livepeer智能合约管理，并由各方参与协议。虽然没有达成共识，但在接受和验证以前的区块方面，该协议规定了参与者的规则以及参与者因未能履行其职责而受到惩罚（削减）的条件。

管理新生成的代币的第二级共识将基于收益委托证明（DPOS）(参考Bitshares、Steem、Tendermint和Casper [[5, 9, 10, 11](#references)]等系统）。 verifier在网络中的作用由Transcoder来实现。任何用户都可以将他们的收益委托给Transcoder，然后Transcoder需要在网络中执行转码作业，参与工作验证协议，并以特定的时间间隔在链上调用函数来验证这项工作。该协议将分配费用和新生成的代币，并削减行为不端者的收益。在执行验证后将通过Truebit在链上记录验证结果，因此Broadcaster与Transcoder之间不存在任何冲突。

### 绑定+委托

在Livepeer中，为了表明网络中的收益，节点必须绑定一定数量的LPT。他们将通过`Bond()` 绑定交易来做到这一点，这将把他们在智能合约中的收益绑定起来，直到他们解绑`Unbond()`，此时他们将进入解绑状态，并持续一段时间（解绑时间`UnbondingPeriod`）。“解绑时间`UnbondingPeriod`”结束后，他们可以撤回他们的LPT。

绑定金额用于将收益委托给Transcoder。网络在任何时候都支持`N`个活动的Transcoder，这是一个可移动的网络参数。任何节点都可以指示其希望成为具有`Transcoder()`交易功能的Transcoder，并且协议将在每轮开始时选择具有最多累积收益（他们自己的+从其他节点委托）的`N`个Transcoder，以及来自等候名单的一个随机Transcoder。

Livepeer中新生成的代币以与其绑定的工作量（减去费用）相对的比例分配给绑定节点，只要它们被委派给符合协议行为的Transcoder。如果他们委派的节点不表现并违反其中一个削减条件，则可以削减收益（减少一定百分比）。已经绑定并委托给transcoder的节点也会收到Transcoder通过网络转码作业生成的部分费用。从本质上讲，执行工作的节点可以获得Broadcaster为该工作支付的费用。

展望未来，当本文档使用术语“委托人”时，指的是已将其收益委托给Transcoder候选者的绑定节点，而不是将其作为Transcoder委托给自己。

总之，参与者因为下列原因选择保留他们的收益：

- 参与委派有效的Transcoder，为网络提供优质服务，确保其对Broadcaster的价值。
- 建立声誉并以分配代币的形式按照收益比例来分配未来工作。
- Transcoder赚取transcoder产生的费用。
- 它们可能希望成为Transcoder。

### Transcoder() 交易

节点通过提交`Transcoder()`交易信息来表明它们希望成为Transcoder，这将公开节点的以下三个属性：

- `PricePerSegment`：他们愿意接受转录视频段的最低价格
- `BlockRewardCut`：绑定节点将为转码服务支付的区块奖励百分比。（例如2％，如果一个绑定节点在块奖励中接收100个LPT，那么2个LPT将被分配给Transcoder）。
- `FeeShare`： Transcoder愿意与委托给它的绑定节点共享的广播任务的费用百分比。 （例如25％，如果Transcoder要收取100 ETH的费用，它们将向绑定节点支付25 ETH）。

Transcoder可以在下一轮转码前的`RoundLockAmount`时间之前更新它们的可用性和信息。它以一轮的百分比形式呈现。（示例10％= = 2.4小时。它们可以将此信息更改为下一个转码循环前的2.4小时，持续时间为`RoundLength` 1天）。这使得绑定节点有机会审查相对于其他Transcoder的费用和代币奖励分割，以及基于它们收费和网络需求的费率的预期费用，此外还可根据需要移动他们的委托收益。在转码循环开始时（通过调用`InitializeRound()`交易触发），该轮的有效Transcoder根据委托给每个Transcoder的收益来确定，收益和利率的锁定时间为该轮的持续时间。

在`RoundLockPeriod`轮锁定期间允许进行一项更改：任何候选Transcoder的最低提供价格/段都被锁定并且无法移动，但其他Transcoder候选者可以向下调其价格/段。这使他们能够匹配网络上的最低报价，以保证它们在网络上的股份加权份额。在此期间，他们不得上调他们的报价。

以下是delegator在决定委派方时可以查看的Transcoder选项的示例。

| Transcoder ID | 每段价格 | 阻止奖励削减 | 收费 |
|----|----|----|----|
| 1 | 22 wei | 1% | 25% |
| 2 | 30 wei | 2% | 40% |
| 3 | 10 wei | 4% | 1% |
| ... | ... | ... | ... |
| N | 14 wei | 0% | 2% |

*价格说明：在本文档中，我们列出价格/段详情。实际上，Livepeer计划使用一种gas accounting启发模型，其中包含一个段的某些工作参数所需的gas单位的概念，如码率、编码、帧大小等。价格/段是一个备选功能，其中 激励措施是相同的，但实际上它们可能会传达价格/gas的信息。*

### 广播+转码任务

在网络上执行开放业务的Transcoder可以通过提交一个`TranscodeAvailability()`交易申请来投入到代码转换工作中。这表示它们的可用性，并将它们投入可用于接受新提交的任务的Transcoder池中。

当Broadcaster向Livepeer网络提交它们的数据流时，它将被赋予一个`StreamID`。这既是一个惟一的标识符，也包含源节点地址，以便节点明确如何请求和路由消费该来源数据流的请求。数据流中包含许多连续的段`Segments`，定义在[Video Segments](#video-segments)视频段小节中。如果Broadcaster希望网络负责将它们的流转换成所有必要的格式和码率，并发送给每个设备上的每个用户，那么第一步是提交一个链上的转换工作交易。任务也有唯一的ID，任务的输入数据包括：

`Job(StreamID, TranscodingOptions, PricePerSegment)`

`TranscodingOptions`定义输出码率、格式、编码等，`PricePerSegment` 将列出Broadcaster所提供的价格。

一旦挖掘了这项交易，下一个blockhash将随机地确定为此作业选择的Transcoder。所有价格低于或等于所提供价格的Transcoder都将被纳入考虑范围，而blockhash模数候选Transcoder的数量（根据它们的权重）将决定所选Transcoder的索引。

此时，Broadcaster可以开始向Transcoder传输视频段，之后Transcoder将将参与以下协议。该协议还使用了一个持久存储解决方案，例如Swarm，作为工作验证过程的一部分。

#### 预处理

1.  **Broadcaster**  -> **Livepeer Smart Contract**：在链上提交保证金，以支付整个代码转换工作的成本。这可以在以后的任何时候补充，但如果保证金耗尽，随着它们所做的工作逐渐兑现，Transcoder可能会停止工作。

#### 任务

2. **Broadcaster** -> **Livepeer Smart Contract**: Job(streamID, options, price/segment)
    - 在链上创建任务请求，并在第三方托管中放置一些ETH来支付工作费用。
3. 该协议可以使用下一个块哈希来确定地为该任务选择正确的Transcoder。
4. **Transcoder** -> **Broadcaster**：发送输出streamID以及表示任务已被接受的收据。
5. **Broadcaster** -> **Transcoder**：发送包含验证输入数据的签名的流片段。
7. **Transcoder** 执行代码转换，使新的输出流在网络上可用
9. **Transcoder**：存储每个代码转换工作段的代码转换收据。代码转换收据有以下字段。

| 转码收据的字段 | 描述 |
|-------|------------|
| **StreamID** | 标识此段所属的源节点和流。 | 
| **Sequence Number** | 该片段所属原始流的顺序。 |
| **Input Data hash** | 输入段数据有效负载的哈希值。 |
| **Transcoded Data hash** | 转码后输出数据的哈希值。 |
| **Broadcaster segment signature** | 由Priv(StreamID, Seq#, Dhash)产生的一个签名，可用于证明和验证broadcaster这个独特段是真实数据。 |
| **Transcoder segment signature** | Transcoder上述所有字段的签名，证明是在此特定输入上执行特定输出代码转换。 |

每当Transcoder发现不再接收段时，就可以调用`ClaimWork()`来声明它们的工作状态。

#### 结束任务

10. **Transcoder** -> **Livepeer Smart Contract**：调用`ClaimWork(JobID, StartSegmentSeq#, EndSegmentSeq#, MerkleRoot)`。transcoder在链上声明它们已经对所声明的段范围执行了相关工作，所有转码接收数据的merkle根都已提交到这些编码段的内容中。
11. 11. 等待挖掘这项交易，并观察下一个blockhash。然后，协议可以根据`VerificationRate`确定将验证哪些段。
12. **Transcoder** -> **Swarm**：为将验证受到挑战的片段编写输入数据有效负载，使用SWEAR params确保数据足够长（`VerificationPeriod`时间）。 
13. **Transcoder** -> **Livepeer Smart Contract**：为需要验证的每个片段提供链上的代码转换声明，以及代码转换声明中每个片段收据的merkle证明。智能合约可以验证Broadcaster和**Transcoder** 的签名，以确保所有必要的数据都可用来进行验证，并且可以根据`ClaimWork()`中提交的merkle根来验证merkle证明。 
14.  **Transcoder** -> **Truebit**: `Verify()` 这是对Truebit智能合约的一个链上调用，其中，Transcoder为受挑战的片段提供群输入散列。（更多关于验证的信息见下一节）
15. **Truebit** -> **Livepeer Smart Contract**：这项工作的结果将被写在链上，并将之与Transcoder提供的转码声明结果进行比较。
16.  **Livepeer Smart Contract**：在这一点上，Livepeer智能合约拥有它所需要的所有信息，以确定是否验证了Transcoder的工作。
    - 如果验证正确，则使用作为输入的代币分配算法和发放托管费用。
    - 如果不正确，那么Transcoder和它的利益相关方将被削减`FailedVerificationSlashAmount`、Broadcaster将得到退款。

Broadcaster可以在任何时候停止发送片段，这实际上是一个`EndJob()`交易。 

此时已经执行了代码转换，链上已经提交了工作的证明，并且已经报告工作验证的失败或成功。所有的信息都在链上，以确定向Transcoder和委托程序分配的费用和代币分配，或者在验证失败的情况下进行削减。让我们来看看实际是如何进行验证工作的。

### 验证工作

为了将费用分配给声称已经执行了代码转换工作的Transcoder，协议必须能够确定该工作实际上是高概率正确执行的。为此，Livepeer扩展了[Truebit协议](http://truebit.io) [[6](#references)]的研究并利用了它。

Truebit的工作方式是让一个参与者(Solver)执行实际工作，并收取一定的费用，在本例中是代码转换，然后让其他参与者(Verifier)验证工作，以检测错误、误差或作弊。该任务被分解为非常小的步骤，验证者检查solver的工作，以找到与它们期望的不同的第一步。然后，只有这一个非常小的步骤需要由一个智能合约 (judge)来执行，该合约能知道哪一方做的工作是正确的。经济激励，包括强制误差，以激励验证者的检查，以确保作弊或错误挑战不是有利可图的，但执行检查工作是有利可图的。

这个协议的缺点是，为了验证所有的工作，它的成本是原始工作成本的5 -50倍。Livepeer使用Truebit作为黑盒子来验证段，段但它只随机验证一小部分段，并在验证失败的情况下使用，从而避免了支付高昂的验证税。Livepeer中的`VerificationRate`集决定了在Truebit中选择一个特定分段进行挑战的频率，而在工作提交到区块链之后，未来区块哈希的随机性决定了具体选择哪些段。

如果工作是通过调用区块`N`中的`ClaimWork()`提交的，那么：

如果 `Sha3(N, BlockHash(N), Seg#) % VerificationRate == 0` 那么必须验证分段号码。

Transcoder通过调用`Verify()`交易为候选段提供链上的代码转换声明。Livepeer智能合约合约可以使用内部签名和提供merkle证明来验证这些声明的真实性，然后调用Truebit来只验证这些段。

Truebit solver和verifier访问来自持久内容寻址存储系统(如Swarm)的分段的输入数据。Transcoder负责验证段数据在Swarm中可用，并且可以选择从SWEAR协议[[5](#references)] 中查找保证一段时间持久性的收据，而这段时间足够Truebit运行。此外，它们可以自己运行一个群集节点，确保数据可用于Truebit验证。如果它们有理由相信群集中没有可用的数据，它们可以提供这些数据，或者对以前可用的数据调用`ClaimWork()`。

Truebit将把计算结果(成功或失败)写回Livepeer智能合约合约，然后该合约可以在协议内用于奖励和削减计算。代码转换节点不能预先预测哪些段将被验证，如果作弊或不能正确地进行代码转换，将会受到以下惩罚：

- `FailedVerificationSlashAmount` 如果它们没有通过Truebit的验证，就会被削减。
- `MissedVerificationSlashAmount` 如果它们不能提供代码转换声明，并且不能在要求它们这样做的段上调用Truebit，那么它们将被删除。
- 自Broadcaster损失的费用。
- 不仅Transcoder将被削减，它们所有的delegator 也将被砍掉。它们会把这一点纳入它们决定委托给谁的考虑中，而Transcoder可能会失去它们所拥有的有利可图的工作。

重要的是，将LPT押注于一个有效的、如实执行的代码转换者比作弊和接受严厉的惩罚、同时为不诚实的工作收取费用和代币分配更有利可图。仔细选择削减参数和验证率可以确保这一点。

#### 关于Truebit的一个说明

*虽然协议使用Truebit来提供工作的完全不可信验证，但是在实践中可能有必要使用可用的解决方案来提供验证，而不像Truebit在开发和测试期间所提供的不可信程度。根据不信任程度排序的选项包括：*

*1. 基于Livepeer API的Oracle -信任Livepeer来验证计算。非常集中，不适合测试之外的任何东西。*  
*2. Oraclize计算服务——信任一家提供计算证明的公司，这家公司的全部声誉依赖于将外部数据与未被篡改的证明放在链上。*  
*3. 安全硬件飞地——像Intel SGX或TownCrier这样的服务提供可信的计算环境。相信它们的硬件实现是正确和安全的。它可以被分布和审计。*


### 代币生成

Livepeer的通货膨胀原因在于，随着时间的推移，新的代币将根据[代币分发](#token-distribution)中传递的时间表生成和分配。如果Livepeer中的所有角色都按照协议行事，那么新生成的代币将按其绑定的股份(减去费用)的比例分配给用户。Transcoder的作用是调用 `Reward()`函数，以触发新的代币分配或削减，这可以从链上的所有可用数据计算出来。

每个转码器将被要求每轮调用`Reward()`一次。

- 确保一个活跃的转码器正在调用`Reward()`。
- 确保编译器在这轮还没有调用`Reward()` 。 
- 根据通货膨胀率`InflationRate`计算要铸造的代币数目。铸造这许多代币。
- 计算代码转换的削减基于它们的区块奖励削减`BlockRewardCut`。 
- 分配到编码器的绑定股份。
- 将剩余的分配到delegator奖励池中。
- 将绑定的代币数量更新到此转码器。

调用`Reward()`失败的直接后果是丢失了一部分代币分配，并且在由delegator为该角色选出时，会对Transcoder的声誉造成损害。

### 削减

如前所述，削减的条件是：

- 验证失败
- 在需要时未能调用验证
- 根据委托的股权，不在平台内按比例分配所需的工作

在以太坊生态系统中构建的一个好处是，您可以在其他协议（如Truebit和Swarm / SWEAR）之上构建网络效果。 不幸的是，依赖于这些外部系统本身具有外部依赖性和激励性，其中一个协议的缺陷或弱点可能会导致Livepeer中的削减。

例如，如果Truebit验证作业在其队列中长时间没有任何solver或verifier声明它，Livepeer将无法在调用`Reward()`之前及时看到该验证的结果。 或者，如果Swarm网络遭遇分区并且无法及时将文件传播到Truebit验证程序，那么这也可能会产生问题。

这些风险可以通过激励Livepeer协议中的参与者在内部执行这些角色来缓解，它们可能会发现它们最适合作为Truebit verifier或Swarm节点。 但是还有另一种方法是在削减参数上引入概率阈值的概念。 可以设置可选的协议变量，例如`VerificationFailureThreshold`，以指示只要节点通过99％的验证，它们就不会被削减。 这将是网络部署之前需要研究的另一个研究领域。

任何Livepeer协议参与者都可以检查和调用无法调用验证削减条件。 有一个`FinderFee`指定查找器将收到的削减量的百分比，作为成功调用此削减条件的奖励。

根据协议的治理机制，剩余的大幅削减资金将进入`CommonPool`，它们可以被烧毁或分配给诸如进一步的生态系统发展等共同用途。

### 代币分发

作为表示通过DPoS标记算法参与和执行网络工作的能力的标记，初始Livepeer标记分布将遵循需要广泛分布的创世状态的其他DPoS系统模式。

代币的初始分配将在创世和网络的早期阶段分发给社区。接受者可以使用它来担任Transcoder或Delegator的角色。 一部分将分配给在创世之前为协议提供预先工作和资金的小组，并且一部分将被赋予核心项目的长期发展。

在网络启动时，代币发布将根据通货膨胀计划继续进行，其中代币以每轮`InflationRate` ，相对于代币的未清偿浮动而生成。 由于代币是按照议定书中所有参与者的股份比例发放的，因此可以激励积极参与。 由于获得了比例股份，参与者受到这种通货膨胀的“保护”。 只有不活跃的参与者才会不管其代币上，而不会将其与参与者绑定起来，他们会看到他们的比例网络所有权因这种通货膨胀而减少。

`InflationRate`的初始目标将被设置为其目的是激励LPT的大约`ParticipationRate`被绑定并积极参与[[19](#references)]。 例如，如果`ParticipationRate`为50％，则存在激励以使一半的优秀代币被绑定。 通货膨胀率将在每轮按算法移动以提升参与目标。 较高的通货膨胀率会增加更多的保护标记，较低的税率将导致更多的人选择流动性而不是参与。这是流动性偏好与网络所有权百分比之间的权衡，由于网络中的许多经济因素，它应该找到均衡。

### 治理

Livepeer协议中的治理角色有三个方面：

1. 确定从行为不当的节点中削减的共同资金的消耗或挪用。
2. 调整网络参数以确保健康、蓬勃发展的网络，这对Broadcaster来说很有价值。
3. 以去中心化的方式调用提议的协议更新。

本文档中引用的许多网络参数，如`UnbondingPeriod`、 `RoundLength`、 `ParticipationRate` 和 `VerificationRate` 都是可调整的。 可以提交对这些参数进行调整的提议，并且治理过程（包括代码转换器按其委托的比例进行投票）将确定在协议内自动采用这些更改。 治理的详细规范留给另一个文档。 [详见此处](https://github.com/livepeer/wiki/wiki/Governance)。 

## 攻击

本节包含对恶意行为者可能试图攻击Livepeer网络的各种方式的调查。 我们使用理性攻击者模型，攻击者根据自己的经济利益做出决策。 让攻击变得无利可图，可以减少许多攻击，但我们也努力确保在最坏的情况下，网络在持续无利可图的攻击中效率降低，但不会遭遇故障。

### 共识攻击

如前所述，Livepeer生态系统的共识由底层区块链平台（例如以太坊）提供。 51％攻击、Livepeer代币的双倍花费以及网络分支都需要与以太坊本身相同的资源和攻击成本。

Livepeer是一种基于股份的协议，虽然转码器具有参与工作验证过程和代币奖励分配过程的作用，但它们实际上不具有验证或接受其他转码器工作的作用。 没有链的概念，也没有先前区块的验证。 简单地存在经济激励来验证自己的工作并在轮到它时分配一部分自己的代币分配部分。 因此，在远程攻击、无任何问题和贿赂攻击等利益协议证明中看到的攻击不适用，因为没有机会尝试签署多个区块或尝试创建来自早期阶段的较长链。 然而，人们应该意识到，当底层区块链迁移到股权证明时，如果在Livepeer上执行它们的好处超过对以太坊本身的攻击成本，这些攻击确实可能会破坏Livepeer。

虽然依赖底层区块链的安全性对于防止共识攻击很有帮助，但仍然存在一类可能损害Livepeer网络的高质量和高效率攻击。

### DDoS攻击

Livepeer中的拒绝服务有两种方式：

1. 转码器可以尝试通过接受作业但拒绝转码来阻止或减慢Broadcaster将其编码流输出到网络。
2. Broadcaster可以通过拒绝发送段来阻止转码器执行它们认为已分配的作业。

这两种攻击都有成本，可以减轻，问题不大。

在第一种情况下，Transcoder必须付费才能在链上声明其可用性。 如果它们因为不做这项工作而不收取费用，那么它们就会把ETH扔掉。 Broadcaster可以重新提交作业并分配一个新的transcoder。 可扩展性的一个潜在选择是协议可以按优先级顺序识别多个有效的Transcoder，而不仅仅是一个，这样，Broadcaster可以在没有其他链交易的情况下继续操作。 此外，所有关于已接受任务的统计数据和转码/任务等的平均段数可以从链上数据计算，并且delegator将使用它作为它们的委派决策的输入。 表现不佳将失去你的角色。

在Broadcaster阻止transducer工作的情况下，这仅仅是容量规划计算。 转码节点可以维护其并发任务容量的记录，任务处于活动/不活动状态的可能性，并确保它始终认为它将具有其声明的工作容量。 简单地忽略或在拒绝发送段的节点上调用`EndJob()`几乎不会损坏Transducer。

### 无用或自我处理Transducer

如果Transcoder有足够的股权来维持它们的位置，它们理论上可以列出100％的`BlockRewardCut`，0％`FeeShare`，并收取高的`PricePerSegment`，这样它们就不会做任何工作，但可以收集它们的代币分配。 这可以通过`CompetitivenessTolerance`来阻止，但需要它们提供一些有效的工作。 另外，由于transducer参与协议所产生的交易成本，对于它们简单地将它们的代币放到与它们共享费用的有效transducer上比将其作为无用transducer的人更有利可图，后者不会收到任何值得注意的费用。

输出无效输出的行为不端的transducer的股份很快就会被削减到无法真正保住工作和接受任何工作的程度。

### Transcoder 恶意破坏

如果Broadcaster想要使协议编码的协议非常昂贵，它可以发送代码转换器非连续的段号。 这是因为transducer可以在单个交易中声明连续范围的段号，但是必须使许多交易在随机段号范围内声明工作。 这可以通过以下选项进行防御：

1. Transducer调用`EndJob()`并且不打扰工作或试图收取费用。 
2. 协议实现链解析或更好的段索赔编码，以减少与在单个呼叫中声明非连续段相关联的费用。
3. 简单地忽略段，永远不声明工作。

这种攻击对Broadcaster来说成本很高，因为它们必须有存款并在链上提交任务，以便首先分配给代码转换器。它们能够使transducer处境艰难并可能降低效率，但不会对网络造成损害。

### 链重组

当broadcaster向Livepeer Smart Contract 提交任务时，协议使用当前区块哈希来确定将为哪个transducer分配任务。在这种情况下，底层区块链的重组可能会引起混乱。 虽然这不是直接“攻击”，但transducer将在一秒内有效，然后在重组时将不再有效。 当检测到重组时，Broadcaster可以将数据流重定向到新的有效transducer，或者协议可以检测主链中包含的叔块，并且如果给定阈值内的叔块使其有效，则认为代码转换器是有效的。 

## 实时视频分发 ###########################################

本白皮书主要关注确保实时视频正确转码的经济激励和协议，这对于支持自适应码率流并到达每个设备是必要的。 但同样重要的是在整个网络中分发视频，以便实现高质量和低延迟的消费。 分销经济依赖于Bittorrent推广的TFT带宽计算，并通过SWAP [[13](#references)]等协议进行扩展。 作为简化，节点付费以请求一段视频，并且节点获得付费以服务一段视频。 如果一个节点已经有一个段并且可以将它提供给多个请求者，那么它是有利可图的。 我们将这种类型的节点称为中继节点。

当涉及到在网络中扮演不同角色的节点的带宽时，存在不同的激励。

* 消费者可能愿意交换上游带宽以将内容提供给其他消费者，以换取自己能够免费消费视频。 请参阅Webtorrent [[14](#references)]等系统。
* Broadcaster作为原始节点，可能想要为视频的消费收费，或者可能想要补贴带宽成本，以便每个人都可以免费访问它们的视频。
* Transducer和中继节点愿意为分发视频提供带宽，只要它是有利可图的。 这类似于传统CDN的作用。



使用段`Segments`作为流经网络的核心数据单元，可以使用ETH作为结算基础进行TFT的带宽计费。 我们从Swarm [[6](#references)]借用支票簿合同抽象作为一种通过链式结算传递的离线支付方法。 生态系统的未来发展，包括Raiden网络[[15](#references)]也可能允许支付渠道用于此目的。 由于代币传输对于协议是本机的，因此还可以将与内容相关联的定价直接嵌入到协议中。 Broadcaster可以直接收取它们的时间或内容，节点将通过支付更高的价格/段来选择这种价值转移，这些价格/段将转回Broadcaster。

需要注意的重要一点是，虽然可以使用带宽计费来运行中继节点，这些节点只是通过网络传输视频网段以增加容量，而不是CDN，但这些节点纯粹受到内容需求的激励， 不受新代币分配的激励。 事实上，Livepeer的输出可以插入传统的CDN（如Amazon S3，Cloudflare等）或分散的CDN（如IPFS或Swarm）。 开发用于视频段分发的这种点对点协议本身将是优化和改进性能的持续机会。

已经证明点对点CDN可以减少原始CDN服务器[[17](#references)]上80-98％的带宽需求，并且在分散网络中看到的代币机制可以使利益相关者对齐开发和维护现已存在的专有P2P CDN的开放版本。 PPSPP协议[[18](#references)]是开放实施的可行候选者，它专注于提供实时内容。

由于对Livepeer协议的加密经济学并不重要，因此本文不讨论细节，但是有兴趣的人可以[点此](https://github.com/livepeer/go-livepeer)关注其发展动态，并寻找未来的纯视频分发协议文档。 

## 使用案例 ###########################################

Livepeer项目关注的是去中心化一对多的直播视频广播（多播）。 这是最真实的媒体分发形式，因为它允许Broadcaster以第一手的方式直接与观众联系，不受改动、事后解释和编造影响。 它为每个人提供了一个发声的平台。 现有的集中式解决方案可能遭受审查，第三方对用户数据/关系/货币化的控制，以及围绕服务支付的低效成本结构。 以下是在Livepeer之上构建应用程序和服务的一些逻辑用例。

### 即用即付内容消费

通过融合进协议中的价值转移交易，现在Broadcaster可以直接向观众收取其直播的消费，而无需通过集中平台对信用卡、账户或对用户身份的控制。 这适用于教育（付费参加在线课程），活动（付费观看音乐会或现场体育赛事），娱乐（付费观看游戏玩家或表演者的直播）以及许多其他用例 - 同时保留 观众的隐私，并允许他们只支付购买他们直接向Broadcaster消费的东西。

### 自动缩放社交视频服务

当今构建消费者视频服务的挑战之一是扩展基础设施，以支持随着新用户的增加而对越来越多的数据流和越来越多的消费者的需求。 一个服务层可以轻松地让开发人员开始在Livepeer网络之上构建他们的视频解决方案，该网络将自动扩展以支持任意数量的数据流和观看者，这将是基础设施开发人员的一个受欢迎的解决方案，否则他们将不得不继续配置服务器，媒体许可服务器，以及有效管理资源以处理峰值。

### 无法审查的现场新闻

Twitter和Facebook等现有平台为大量受众提供了令人惊叹的实时视频解决方案，但它们也是在各种政治冲突情况下第一个被封锁或审查的平台。 使用像Livepeer这样的去中心化网络几乎不可能阻止实际上正在发生的事情。

### 带视频功能的DApps

去中心化应用程序（DApps）开始出现，主要由以太坊生态系统驱动。 然而，到目前为止，还没有一种可行的解决方案，可以在不使用集中式解决方案的情况下，在DApp中嵌入实时视频，或者根据WebRTC的限制来限制消费客户端的数量。通过将Livepeer引入架构，应用程序可以完全去中心化，但仍然包含大规模的实时视频，而且尽可能多的用户希望消费它。

## 总结 ###########################################

总之，Livepeer协议激励节点将其处理和带宽贡献给网络，以便为转码和分发实时视频服务。 通过Truebit协议之上的可放大扩展来解决工作验证，该扩展可以激励节点正确执行转码操作，以获得费用和代币分配，并保留其作为Transcoder的价值收益角色。网络的游戏化和虚假工作问题通过委托块奖励核算的委托证明经济学来解决。简单地将一个标记放入增值节点比在网络上支付费用以便在执行实际上没有实际需求的工作时分配给其他委托人更加经济合理。

最终结果是一个可扩展的、即用即付的网络，用于去中心化的实时视频广播 - 这是Livepeer试图填补的web3架构中的缺失层。

## 附录 ###########################################

### Livepeer协议参数引用

| 参数名称 | 描述 | 示例值 |
|----|------|---|
| `T` | 段长度，以秒为单位 | 2 秒 |
| `N` | 活跃Transcoder的数量 | 144 |
| `RoundLength` | 选择新一轮Transcoder之间的时间长度 | 1 天 |
| `InflationRate` | 目前每轮LPT的目标通胀率。 （按算法方式移动）。 | .04% (相当于每年15%) |
| `ParticipationRate` | 代币绑定与流动的目标百分比。 | 50% |
| `RoundLockAmount` | transducer在一轮结束时锁定一轮的这一百分比，以便委托人可以相应地审查和委派，而不必担心最后一刻的费率变化。 | 10% == 2.4 小时 |
| `UnbondingPeriod` | 进入无约束状态和提取资金的能力之间的时间。 | 1 个月 |
| `VerificationPeriod` | 提交工作声明后验证工作声明的截止日期。 这也是在去中心化存储解决方案中必须提供数据持久性接收的最短期限。 | 6 小时 |
| `VerificationRate` | 要验证的段的百分比。 | 1/500 |
| `FailedVerificationSlashAmount` | 在验证失败的情况下的削减百分比（超出潜在的允许故障阈值） | 5% |
| `MissedRewardSlashAmount` | 在缺少一个区块奖励回合的情况下的削减百分比（也许只在n次连续失误的情况下这样做） | 3% |
| `MissedVerificationSlashAmount` | Transcoder没有调用验证时的削减百分比 | 10% |
| `CompetitivenessTolerance` | 如果所有Transcoder始终可用并设置相同的价格和费用，那么它们将按照其利益的比例接收工作。 此参数设置％必须在此目标工作％内才有资格进行代币分配。 这样可以防止Transcoder相对于它们的股份做很少的工作。 | 90％（极端的例子。有100个Transcoder和100,000个段，这意味着如果我只做了100个段（我应该做的1000分的10％）就可以了）。 |
| `*SlashingThresholds` (TBD) | 占位符表示我们可能不会针对所有故障进行削减，只要它们超过故障率的某个阈值百分比。 | |
| `VerificationFailureThreshold` | 您在不被削减的情况下可以失败的验证百分比。 有用，因为Swarm / Truebit等外部依赖可能会导致偶发故障。 | 1% |
| `FinderFee` | 作为补偿的、发现者将收到的削减数量的百分比。 | 5% |
| `SlashingPeriod` | 在“验证期`VerificationPeriod`”完成后调用削减条件的截止日期。  | 1 小时 |

### Livepeer协议交易类型

| 交易 | 描述 |
|----|------|
| `Bond()` | 绑定股份对transducer的影响。 |
| `Unbond()` | 输入固定的 `UnbondingPeriod`的未绑定状态。 |
| `Transcoder()` | 声明你成为Transcoder的意图。 |
| `ResignAsTranscoder()` | 取消您成为transducer的意图。 |
| `TranscodeAvailability()` | 此transducer目前可以接受另一项工作。 它们在池中随机被分配新工作。 |
| `Job()` | 在链上提交转码任务。 |
| `EndJob()` | 结束工作以放弃转码责任。 |
| `Deposit()` | 提交链条上的存款，用于支付工作。 |
| `Withdraw()` | 退出存款和未绑定的股份。 |
| `ClaimWork()` | 结束转码工作并声明您可以证明您已经通过段范围和merkle root.take转码的段。 |
| `DistributeFees()` | Transcoder在验证后索要特定声明的费用。 |
| `Reward()` | 链上的所有验证是否会削减或分配代币分配。 只能由在当前轮次中活动的Transcoder调用，每轮一次。 |
| `Verify()` | Transducer提供段的转码声明，这些声明将与merkle证明一起进行验证，以便与merkle root进行比较 `ClaimWork()`。 明确调用Truebit执行验证。 |
| `InitializeRound()` | 在新一轮的起始块之后需要调用此事务一次以初始化新活动的Transcoder池。 |
| `UpdateDelegatorStake()` | 这允许delegator在前几轮申请他们的费用+代币分配。 它通过非绑定和绑定自动调用，但如果delegator想要在不改变状态的情况下更新它，它就可以作为故障保护。 |
| `*GovernanceTransactions()` | 待定  |

## 参考文献 ###########################################

1. 以太坊白皮书 - Vitalik Buterin - Ethereum Wiki - <https://github.com/ethereum/wiki/wiki/White-Paper>
2. 胖协议 - Joel Monegro - USV Blog - <http://www.usv.com/blog/fat-protocols>
3. 加密代币和协议创新的未来时代 - Albert Wenger - <http://continuations.com/post/148098927445/crypto-tokens-and-the-coming-age-of-protocol>
4. SectorCoins案例 - Eric Tang - <https://medium.com/@ericxtang/case-for-sectorcoins-b70a7c820c2d#.7892n4a57>
5. DPoS共识 - Daniel Larimer - <https://bitshares.org/technology/delegated-proof-of-stake-consensus/>
6. Truebit - Jason Teutsch 和 Christian Reitweisner - <https://people.cs.uchicago.edu/~teutsch/papers/truebit.pdf>
7. 交换、发誓和欺诈，群体激励系统 - viktor trón, aron fischer, dániel a. nagy, zsolt felföldi, nick johnson - <http://swarm-gateways.net/bzz:/theswarm.eth/ethersphere/orange-papers/1/sw%5E3.pdf>
8. Kademlia：基于XOR度量的点对点信息系统 - Petar Maymounkov 和 David Mazieres <https://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf>
9. Steem 白皮书 - Daniel Larimer, Ned Scott, Valentine Zavgorodnev, Benjamin Johnson, James Calfee, Michael Vandeberg - <https://steem.io/SteemWhitePaper.pdf>
10. “友善的幽灵” ：卡斯帕介绍 - Vlad Zamfir - <https://blog.ethereum.org/2015/08/01/introducing-casper-friendly-ghost/>
11. Tendermint 文档 - Jae Kwon 和 Ethan Buchman - <https://tendermint.com/docs>
12. Swarm - <http://swarm-gateways.net/bzz:/theswarm.eth/>
13. 在BitTorrent中通过激励措施建立稳健性 - Bram Cohen - <http://bittorrent.org/bittorrentecon.pdf>
14. WebTorrent - <https://webtorrent.io/>
15. 雷电网络  - <http://raiden.network/>
16. ERC20代币标准 - <https://github.com/ethereum/EIPs/issues/20>
17. Peer5利用观众的设备实现流媒体视频的P2P方法 - <https://techcrunch.com/2017/01/26/peer5-y-combinator/>
18. 点对点流媒体对等协议 - <https://tools.ietf.org/html/rfc7574>
19. 基于股权协议的通货膨胀与参与 - Doug Petkanics - <https://medium.com/@petkanics/inflation-and-participation-in-stake-based-token-protocols-1593688612bf>
