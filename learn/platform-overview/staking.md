---
说明：了解如何在Avalanche上通过验证或委托来完成权益质押
---

# 权益质押

权益质押是通过锁定代币来支持网络，同时获取回报的过程（回报可以是更多使用网络的权利、货币偿付等）。权益质押的概念是由 Peercoin 的 Sunny King 和 Scott Nadal [首次正式提出的]（https://web.archive.org/web/20160306084128/https://peercoin.net/assets/paper/peercoin-paper.pdf）。

### 权益证明（proof-of-stake）如何工作？


为抵御[女巫攻击]（https://support.avalabs.org/en/articles/4064853-what-is-a-sybil-attack），去中心化网络务须要求以稀缺资源来偿付网络影响力。这使得攻击者通过在网络上获取充分影响力来危害网络安全的代价高得无法企及。在工作量证明(PoW)系统中，稀缺资源就是算力。在Avalanche上，稀缺资源则是本地代币 [AVAX]（../../# avalanche-avax-token）。为了让节点能够[验证]（http://support.avalabs.org/en/articles/4064704-what-is-a-blockchain-validator）Avalanche 上的区块链，必须以 AVAX 进行质押。

## Avalanche 上的权益质押参数

验证器完成对[主网]的验证之后（http://support.avalabs.org/en/articles/4135650-what-is-the-primary-network），验证器即收回其质押出去的 AVAX 代币。它可能会因帮助维护网络安全而获得报酬。只有在验证期间响应足够积极且验证达到足够的准确度，验证器才会获得[验证奖励]（http://support.avalabs.org/en/articles/4587396-what-are-validator-staking-rewards）。阅读[Avalanche 代币白皮书]（https://files.avalabs.org/papers/token.pdf），了解有关 AVAX 和权益质押机制的更多信息。

{% hint style="warning" %}
只要满足了所有如下参数，权益质押报酬就会在质押期满时汇往您的钱包地址。
{% endhint %}

* 验证方质押的最低金额为2,000 AVAX
* 委托方委托的最低金额为25 AVAX
* 质押资金投入验证的最短时间为2周
* 质押资金投入验证的最长时间为1年
* 质押资金投入委托的最短时间是2周
* 质押资金投入委托的最长时间为1年
* 委托费率最低为2％
* 验证方的最高权重\（其质押权益+其受托的质押权益\）为3e6 AVAX与验证方质押金额5倍这两个值之间的最小值。譬如，如果您质押2,000 AVAX成为验证方，那么您的节点所能接受的委托的总金额\（而非每个委托人的委托金额）只有8000 AVAX
* 验证方验证正确且在线时间达到60%或以上才能获得报酬

## 验证器

**验证器** 维护 Avalanche 的安全运行，创建新的区块/顶点，并进行交易处理。为了达至共识，验证器反复对彼此进行采样。给定的验证器其被采样概率与其质押权益额成正比。

给验证器集添加节点时，要确认：

* 节点ID
* 验证启停时间
* 质押 AVAX 金额
* 接收报酬的地址
* 委托费率\（请参见下文\）

{% hint style="info" %}
验证方进行权益质押的最低金额为2,000 AVAX。
{% endhint %}

{% hint style="danger" %}
请注意，一旦发起交易将节点添加为验证器，参数便无法变更。 **权益质押不能提前撤销，质押金额、节点ID或报酬接收地址均无法变更。**请确保您在下面的API调用中所使用的值是正确的。如果不确定，请通过[Discord](https://chat.avax.network)寻求帮助，或浏览我们的[开发者常见问题解答]（http://support.avalabs.org/en/collections/2618154-developer-faq）。
{% endhint %}

### 运行验证器 <a id="running-a-validator"></a>

如果验证程器正在运行，务必使节点连接完好，以确保报酬的获取，这一点很重要。请参阅[此处信息](http://support.avalabs.org/en/articles/4594192-networking-setup)。

发起交易添加验证器时，质押代币和交易费用将从您控制的地址扣除。验证结束时，质押资金将返回到其源地址。如能计取报酬，报酬将汇往您添加自身为验证方时所指定的地址。

#### API调用许可设置 <a id="allow-api-calls"></a>

要从远程计算机对节点进行API调用，请在API端口\（默认为“ 9650” \）为流量设置为许可 , 并用参数运行节点 `--http-host=`

用不上的 API 应该用命令行参数实施禁用。将所处网络配置为API端口仅允许从受信机器\（例如您的个人电脑）访问。

#### 为什么我的正常运行时间很短？ <a id="why-is-my-uptime-low"></a>

Avalanche 上的每个验证器都会跟踪其他验证器的正常运行时间。您可以通过调用 `info.peers` 来查看节点具有的连接以及每个连接的正常运行时间。**这只是单一节点的衡量结果**。其他节点可能会对您节点的正常运行时间的认知有所不同。仅仅因为一个节点认为您的正常运行时间短，并不意味着您不会因权益质押获得回报。

节点未连接到另一节点的原因可能是NAT穿越失败，以及您的节点没有开启NAT穿越 `--public-ip=[NODE'S PUBLIC IP]`。 将来, 我们将添加更好的监视功能，从而更加便捷地核验节点连接是否完好。

#### 机密管理 <a id="secret-management"></a>

对于验证节点唯一需要保守的机密就是它的质押秘钥（Staking Key），即确定节点ID的TLS密钥。首次启动节点时，会创建质押密钥并放置在 `$HOME/.avalanchego/staking/staker.key`。 应将此文件\(和 `staker.crt`\) 备份到安全的地方。质押密钥丢失的话，由于节点将启用新的ID，因而可能危及验证报酬的获取。

验证节点无需放置 AVAX 资金。实际上，最好 **不要** 在节点上放置大量资金。几乎所有的资金都应放置在某些“冷”地址上，这些地址的私钥不应保存在任何计算机上。

#### 监视 <a id="monitoring"></a>

参照本教程学习如何监视节点的正常运行时间以及其总体健康状况等。

{% page-ref page="../../build/tutorials/nodes-and-staking/setting-up-node-monitoring.md" %}

## 委托方

委托方是代币持有者，他想参与权益质押，但选择以权益委托形式认定既有验证节点为受托方。

将权益委托给验证方时，应确认：

* 受托节点的ID
* 权益质押委托的启/停时间（须处于在验证方验证时段之内）
* 质押委托的 AVAX 额度
* 接收报酬的地址

{% hint style="info" %}
委托方委托的最小金额为25 AVAX。
{% endhint %}

{% hint style="danger" %}
请注意，一旦发起交易将权益质押委托给受托方，参数将无法变更。 **不得提前撤出质押，质押金额、节点ID或接收报酬的地址均无法变更。** 如果不确定，请通过[Discord]（https://chat.avax.network）寻求帮助。或浏览我们的[开发者常见问题解答]（http://support.avalabs.org/en/collections/2618154-developer-faq）。
{% endhint %}

### 委托方报酬 <a id="delegator-rewards"></a>

如果代币委托给的验证方准确度和响应能力足够高，委托完成后会获得报酬。委托方所获报酬计算方法与验证方计酬函数一致。不过，受委托的验证方会（根据验证方的委托费率）提留一部分您的报酬。

发起交易将代币委托出去时，质押的代币和交易费用将从您所控制的地址扣除。委托结束时，质押的代币将返回到您的地址。如有报酬计取，报酬将汇往发起代币委托时指定的地址。

