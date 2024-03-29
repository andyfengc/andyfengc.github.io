---
layout: post
title: Futures vs. Options
author: Andy Feng
---

期权和期货是金融衍生品，其诞生是商人为了货物保值，对冲风险的目的。

以期权为例，顾名思义是由“期”和“权”组成。“期”是指未来的某个时间，“权”是指“买卖标的物”的权力。即在未来的某个时间，以约定的价格买卖标的物的权力。其中，我们把“未来的某个时间”叫做行权日，“约定的价格”叫做行权价。当然世上没有免费的午餐，想要获得权力，就要有所付出，也就是购买期权！因此我们把期权的价格叫做“权力金”。

# 期货 Futures
## History
期货起源于19世纪初期的芝加哥，当时芝加哥是美国最大的谷物集散地，最初创建期货市场的目的是为了满足农民和商人的需要。当时，正处于美国农业发展的初期，农业生产面临的主要困难是交通不便、信息传递落后、仓库稀缺、价格频繁波动而且剧烈，从而导致农产品供求失衡。当时谷物的价格极不稳定，丰收之年农作物充斥市场时，价格便下跌，反之，当市场上谷物短缺时，价格又会上涨。时而景气时而萧条全靠天定，并且形成了周而复始的怪圈，这相当不利于买卖双方，同样也严重影响着美国农业的发展。

为了解决这个问题，1848年一群商人成立了Chicago Board of Trade（CBOT），该组织创造了称为“将运到”合约。这种合约允许农民在谷物交割前先卖出。换句话说，农民可以种植庄稼的同时签订合约，然后在收割后按签约时商量好的价格买卖谷物。这种交易可以让农民在芝加哥以外的地方存储粮食。合约的另一方则是成立芝加哥期货交易所的商人们。于是当时的买家和卖家开始签订合约，以确定自己能在指定日期按确定价格进行一定数量的谷物交易。。这样一来，对农民和商人来说，他们分别有长期供货和长期买货的需求，在9月份谷物交割前见面，根据双方对9月份谷物的供求的预期而达成一个一致的价格，就很有意义。换句话说，他们可以协商制定某种类型的期货合约，这个期货合约可以消除双方各自面临的因未来价格不确定而产生的风险。

## Introduction
期货指的是一种远期的“货物”合同。成交了这样的合同，实际上就是承诺在将来某一天买进或卖出一定量的“货物”。当然这样的“货物”可以是大豆、铜等实物商品，也可以是股指、外汇等金融产品。

![](/images/posts/20201109-option-2.jpg)

举个例子，如果你想买烧饼，但是要四个月后才买，但你不知道四个月后的价格会变成怎样。所以你先跟卖烧饼的签一个合同，锁定四个月后的烧饼成交价格，四个月后你必须以5块钱的价格买入烧饼。

![](/images/posts/20201109-option-3.jpg)

这样你就可以将烧饼价格控制在预期之内了，降低了四个月后买不到烧饼或者高价才能买到烧饼的风险。

同样，对卖烧饼的来说，也同样担心四个月后的烧饼行情和风险，想在现在就锁定有销路，并且达到预期收益，买卖双方都有意愿，就成交了。

![](/images/posts/20201109-option-4.jpg)

你有买烧饼的义务，对方也有卖烧饼的义务。期货是双向合约，双方都有义务履行合约。

如果四个月后，市面上的烧饼涨到8块钱，你用5块钱的价格买入，那就算赚了3块。

![](/images/posts/20201109-option-5.jpg)

但是如果烧饼跌到2块钱，你依然要以5块钱的价格买入，因为已经签了合同。

![](/images/posts/20201109-option-6.jpg)

## 定义
期货（Futures）与现货相对。期货是现在进行买卖，但是在将来进行交收或交割的标的物，这个标的物可以是某种商品例如黄金、原油、农产品，也可以是金融工具，还可以是金融指标。交收期货的日子可以是一星期之后，一个月之后，三个月之后，甚至一年之后。买卖期货的合同或者协议叫做期货合约。买卖期货的场所叫做期货市场。投资者可以对期货进行投资或投机。对期货的不恰当投机行为，例如无货沽空，可以导致金融市场的动荡。

1. 期货交易，也叫“合约交易”，也就是交易的是所谓的合约，通常指的是保证金交易，缴纳一份保证金就可以买卖期货合约。双方对合约报出价格，买方买的是合约，卖方卖的也是合约。
	> 进行买卖的时候，不需要支付全部金额，只要交出一定的比例（通常5%-10%）的金额作为履行担保就可以，这个一定的比例金额就是保证金。

1. 期货交易是双向的，也就是既可以做多也可以做空。
	> 我们既可以先买一张期货合约，在合约到期之前卖出平仓（或者到期时与卖方进行交割），也可以先卖出一张合约，在合约到期之前买进平仓（或者到期时交出实物或者通过结算现金进行交割。）就算手头没有一张合约，依然可以先卖出。

1. 期货交易因为具有较大的杠杆效应，所以风险大，交易者在进入市场之前，要做好充分的思想准备，特别是对资金有限的散户，更应该懂得止损的概念，否则当行情与总计的预测相反时，很可能血本无归。

## 期货分类：商品期货、金融期货
商品期货又分工业品（可细分为金属商品（贵金属与非贵金属商品）、能源商品）、农产品、其他商品等。

金融期货主要是传统的金融商品（工具）如股指、利率、汇率等，各类期货交易包括期权交易等。目前金融期货大致占了期货市场交易量的80%。
>  金融期货自从本世纪70年代初问世以来，发展十分迅速，交易量呈"爆炸性"上升之势。金融期货主要包括利率期货、外汇期货和股票指数期货。

1. 商品期货

	农产品期货：如棉花、大豆、小麦、玉米、白糖、咖啡、猪腩、菜籽油、棕榈油。农产品期货是世界上最早上市的期货品种，期货市场最先产生于农产品市场，并且在期货市场产生之后的120多年中，农产品期货一度成为期货市场的主流。

	金属期货：如铜、铝、锡、铅、锌、镍、黄金、白银。

	能源期货：如原油、汽油、燃料油。新兴品种包括气温、二氧化碳排放配额、天然橡胶。

1. 金融期货

	股指期货：指以股票指数为标的物的期货合约。股票指数期货是目前金融期货市场最热门和发展最快的期货交易。股票指数期货不涉及股票本身的交割，其价格根据指数计算，合约以现金清算形式进行交割。如英国FTSE指数、德国DAX指数、东京日经平均指数、香港恒生指数、沪深300指数

	利率期货：所谓利率期货是指以债券类证券为标的物的期货合约, 它可以回避银行利率波动所引起的证券价格变动的风险。利率期货的种类繁多,分类方法也有多种。通常,按照合约标的的期限, 利率期货可分为短期利率期货和长期利率期货两大类

	外汇期货：外汇期货，又称为货币期货，是一种在最终交易日按照当时的汇率将一种货币兑换成另外一种货币的期货合约。指以汇率为标的物的期货合约。货币期货是适应各国从事对外贸易和金融业务的需要而产生的，目的是借此规避汇率风险。一般来说，两种货币中的一种货币为美元，这种情况下，期货价格将以“x美元每另一货币”的形式表现。一些货币的期货价格的表示形式可能与对应的外汇现货汇率的表示形式不同。

1. 期货合约：由期货交易所统一制定的、规定在将来某一特定的时间和地点交割一定数量和质量标的物的标准化合约。

	- 期货合约的商品品种、数量、质量、等级、交货时间、交货地点等条款都是既定的，是标准化的，唯一的变量是价格。

## Terminalogy

1. 保证金：是指期货交易者按照规定标准交纳的资金，用于结算和保证履约。

	> 保证金是交易所要求投资者为确保履约提供的财力担保，是投资者对其所持交易部位负责所表示的信誉，交存在其帐户上的一笔资金。按照性质不同，保证金分为交易保证金、结算保证金和追加保证金三种。交易保证金是指投资者在交易所专用结算帐户中确保履约的资金，是已被合约占用的保证金；结算保证金是投资者在交易所专用结算帐户中除去已在交易所占用的交易保证金后剩余部分。追加保证金是指如果投资者账户当日权益小于持仓保证金，意味着资金余额是负数，同时也意味着保证金不足。按照规定，期货经纪公司会通知帐户所有人在下一交易日开市之前将保证金补足。这被称为追加保证金。

1. 交割：是指期货合约到期时，根据期货交易所的规则和程序，交易双方通过该期货合约所载商品所有权的转移，了结到期末平仓合约的过程。

1. 套利：投机者或对冲者都可以使用的一种交易技术，即在某市场买进现货或期货商品，同时在另一个市场卖出相同或类似的商品，并希望两个交易会产生价差而获利。

1. 成交量：是指某一期货合约在当日交易期间所有成交合约的双边数量。

1. 开仓、持仓和平仓：期货交易中的买、卖行为，只要是新建头寸都叫开仓(开始买入或卖出期货合约的交易行为)。交易者手中持有的头寸，称为持仓。平仓是指交易者了结持仓的交易行为，了结的方式是针对持仓方向作相反的对冲买卖 - 指期货交易者买入或者卖出与其所持期货合约的品种、数量及交割月份相同但交易方向相反的期货合约，了结期货交易的行为。

1. 强制平仓：如果帐户所有人在下一交易日开市之前没有将保证金补足，按照规定，期货经纪公司可以对该帐户所有人的持仓实施部分或全部强制平仓，直至留存的保证金符合规定的要求。

1. 头寸(position)：是一种市场约定，即未进行对冲处理的买或卖的期货合约数量。对买进者，称处于多头头寸；对卖出者，称处于空头头寸。

# 期权 Options
期权（Option），是一种选择权，指是一种能在未来某一时间，以期权约定的价格，买入或卖出某种特定商品（如股票）的权利。期权的持有者可以在该项期权规定的时间内选择买或不买、卖或不卖，他可以实施该权利，也可以放弃该权利，而期权的出卖者则只负有期权合约规定的义务。这种权力在股票市场上是可以交易的，交易该权利的价格被称作权利金（premium）。

期权有几个关键的概念：

- 过期时间（expiration date），到了时间就过期。有期权的一方可以在到期前选择执行期权或放弃执行。
- 行权价（strike price），买卖双方按行权价执行。
- 权利金（premium），也就是期权的现金价值。期权的现金价值会随着时间以及股价波动。
- 类型。分为买入权利（CALL）和卖出权利（PUT）。CAll 又被称为看涨期权，PUT 被称为看跌期权。
- 标的。指目标期权。
- 期权有两方，买方 buyer(holder) 和卖方seller（writer）。买方花钱获得了是否执行option的权利，卖方收钱得到了被迫执行option的义务
- 我们把行权价（strike price）与当前股票的价格（stock price）相比较的，会出现两种情况，分别是比当前股票价高（Covered） 和 比当前股票价格低（Naked）。
- 开仓也叫建仓，是指投资者启动期权交易，在账户里建立头寸。投资者可以开仓买入看涨期权、买入看跌期权、卖出看涨期权与卖出看跌期权。
- 持仓是指期权投资者在开仓之后尚没有平仓的合约，也叫做未平仓合约或头寸。
- 平仓也叫对冲(offset)，是指投资者买入或者卖出与所持期权头寸相反的期权合约，了结权利或义务。
- 行权(execute)，指持仓的投资者选择执行权力，按行权价或者买入或者卖出股票

> 期权是一个买卖双方制定的交易合约，合约中有「履约价」以及「到期日」。以看涨期权为例，交易这个合约时，买方付一笔权利金给卖方，于合约到期之前，买方可随时通知卖方，以履约价执行合约，卖方不得拒绝。 简单来说，若你想买一台车，与卖方谈好价格为50万元（履约价），付了订金1万元（权利金）；卖方保证帮你保留车子30天（到期日），30天之内若你决定购买，只要通知卖家，卖家必须以50万元卖给你；但若你决定不买，1万元的订金也不会退回。如此就是期权交易的基本概念。
> 期权可以分为看涨期权（买权，Call）及看跌期权（卖权，Put）。买入看涨期权的投资者，可于到期日前依照履约价，买入对应的资产；买入看跌期权的投资者，可于到期日前依照履约价，卖出对应的资产。
> 无论您打算投入多少资金，期权都能丰富您的交易策略。期权除了保险的功能，也能提供高倍的杠杆。许多绩优股的市价可能高不可攀，此时入门价格较低的期权，就提供了一个参与该股票波动的交易机会。 举例而言，若您想交易谷歌股票，但谷歌一股将近1000美元的高价非一般人可负荷，您可以考虑透过期权方式间接参与谷歌的市价波动。大多情况下，股票的价格变动，与期权价格的变动高度相关。

![](/images/posts/20201109-option-7.jpg)

举个例子

依然是和卖烧饼的签订一个合同，四个月之后以5块钱的价格买入烧饼。

与期货不同的是，你可以向卖方花5角钱的价格买个期权。

![](/images/posts/20201109-option-8.jpg)

因为你已经花了5角钱买了期权，所以卖烧饼的有四个月后卖给你烧饼的义务，而你，有选择买或不买的权利。

当烧饼涨到8块钱的时候，你可以用5元的价格买入。

![](/images/posts/20201109-option-9.jpg)

烧饼价格跌到2块了的时候你可以选择不买。

![](/images/posts/20201109-option-10.jpg)

通过这种方式，如果市场大跌了，买方可以不履行合约，只损失期权费，如果市场涨了，买方能按合同原价要求履行；如果市场跌了买家没履行合同，卖方能拿到期权费来补偿市场不景气造成的损失，如果市场涨了买家要求按原价履行合同，卖方也能拿到期权费来补偿低价出售的损失。

## 期权部位的了结方式有哪些？
期权的了结方式分平仓，行权和放弃三种。

1. 平仓

	期权的平仓方法与期货基本相同，都是将先前买进（卖出）的合约卖出（买进）。只不过，期权的报价是权利金。	
	> 期权买入者可以通过转让期权、对冲（卖一张期权，但是要交保证金，钱要等到对方行权，或者行权日过期后才能拿到）平仓来获利
	> 期权卖出者对冲来平仓来获利

2. 行权

	美式期权，期权买方(持有方)可在期权到期前的所有交易日规定时间下达行权期权指令，期权买卖双方的期权持仓在当日结算时转换成相应的股票持仓。
	> 根据股票市场涨跌情况，当合约价低于市场价时，期权买入者可以选择行权call，以低于市场价向对方买入股票，并立即股票市场上卖出股票来获利；当当合约价高于市场价时，期权买入者可以选择行权put，在股票市场买入股票，并立即以高于市场价向对方卖出股票来获利

3. 放弃

	放弃是指期权合约到期，买方放弃权利，卖方义务终结。
	> 期权卖方拿到权利金来获利

## 期权交易如何获利？
一般而言，期权交易的获利模式，主要来自下列：

1. 期权合约的标的物价值变动而获利

	若马上行权，由行权价与股票现价两者之间的价差所获得的收益。CALL的获利 = 现价 - 行权价；PUT的获利 = 行权价 - 现价。

	以看涨买车（即call）的情景为例，你付了1万元买入call合约后，虽然此订金不能退给卖家，但您可以转手这个合约。若你与卖家约定好的车子价位是50万，30天之内，车子的市价涨到了60万，这个能以50万买进车子的合约便显得更有价值，此时将合约转手，很可能卖到比1万元更好的价位。

1. 期权对应的资产价值变动

	期权合约增值而获利

	使期权履约后，能以更好的价位卖出对应资产而获利 以看涨买车的情景为例，车子于30天内从50万涨到60万。作为期权买方，你选择履约，以50万价格买入该车，并于市场上以60万卖出，则你能透过此价差获利。

1. 担任期权卖方

	收取权利金获利 以上述买车的情景为例，若你担任合约的卖家，买家付给你1万元的权利金，此1万元立即落袋成为你的收入。若接下来买家没有履约，你的纯收入是1万元。

影响期权价格的因素，有现价、时间、波动率

- 现价: 期权价格跟现价比，如果越赚钱，期权价格越贵
- 剩余时间：距离行权日越远，期权价格越贵。随着时间的衰减，期权价格也会下跌。
- 股价越贵，期权价格越贵 
- 波动率：价格波动越剧烈，期权价格越贵。期权的一个重要作用就是保值对冲风险。波动率越大说明股票未来的收益越存在不确定性，风险也越大。因此期权作为风险对冲的工具，其价值就会伴随波动率水涨船高。临近财报日，股票受财报影响会剧烈波动，call/put期权价格会暴涨。

# 计算利润
期权的价值，由“内在价值”与“外在价值”构成。

内在价值是指：若马上行权，由行权价与股票现价两者之间的价差所获得的收益。

`CALL的内在价值 = 现价 - 行权价`；

如果call strike price < stock price 而且 option price + strike price + fee < stock price, 稳赚

> 例如股票现价是$18，option strike是$17，option价格$0.5，17+0.5<18，那么我们可以买进option，要求立即行权，拿到股票后马上按18卖掉，赚 18-17-0.5=0.5 的差价

`PUT的内在价值 = 行权价 - 现价`。

如果strike price > stock price，而且 strike price > stock price + option price + fee，稳赚

> 例如股票现价$18, option strike是$19，option price是$0.5, 19 > 18+0.5，那么我们可以买进option，要求立即行权把股票按19卖给对方，然后从市场上再按18买回来，赚19-18-0.5=0.5 的差价 

这里需要注意，内在价值不会是负数，当计算结果为负数时，我们会说该期权内在价值为0，也即没有内在价值。

> 例如京东当前股价 $25，现在有一张行权价为 $28 的CALL，根据CALL内在价值的计算公式可以得出，该期权内在价值为0。这不难理解，因为一旦选择行权，意味着你将以 $28的价格买入股票。而现价为 $25，行权会使得你的买入成本上升 $3/股，显然作为一个理性投资者，是不会如此不明智地这样使用“权力”。而一旦权力无法使投资者获利，那么期权也将变得没有价值。
> 我们把内在价值为0的期权称为`价外期权`，反之就是`价内期权`，我们总是想要价内期权。价外期权与价内期权是可以相互转换的，这取决于股价的变化，若股价一路上涨，直至现价大于行权价时，期权就由价外转为了价内（拥有了内在价值）。另一种特殊情况是，现价 = 行权价，我们称其为平价期权。

外在价值：就是时间与波动率

# 优缺点及风险
投资者使用期权有两个主要原因：投机和对冲。

投机

　　你可以将投机定义为赌某一证券的未来走势。期权的优势在于，你并不限于仅在市场上涨的时候能够赚钱(拥有call option)，在市场下跌甚至横盘的时候你同样可以赚钱（拥有put option）。

对冲

　　期权的另一个功能是对冲。我们可以把期权当成一份保单(insurance policy)。就像你的住房保险或汽车保险一样，期权可以用来对你的投资进行保险。期权的批评者认为，如果你对自己的选股没有把握，需要用期权来进行保护，那么你根本就不应该做这笔投资。但另一方面，毫无疑问对冲策略是有用的，尤其对于大型机构而言。对于个人投资者也是有用的。举个例子，你想要抓住一只大牛股后面的行情，但又希望降低风险。使用期权的话，你可以用低成本的方式控制住下跌的风险(拥有put option)，同时抓住整轮上升行情(已经囤好股票)。


期权合约的价值会随着时间流逝而减少，此特性使期权不适合做为一个长期投资的工具。若您是想长抱一档股票，直接买入股票是最适切的作法。

对于期权买家而言

1. 只要支付一定的权利金，便能得到于到期日之前执行合约（行权）的权利。若预测正确，无论是行权后卖出资产，或是于正确的时间点将合约转手，都可能获利。若预测错误，损失的也只有权利金。买入期权若操作适当，其风险可能比直接持有资产来得小。

1. 期权买家的风险虽然只有权利金损失，但有许多因素可能增加需要付出的权利金。期权合约在许多程度上类似一个保险合约，保险合约的期限愈长，要付出的保费必然更高。若只需要保一天，亦只需要付出一天的保费。若买家买入一个到期日长的合约，也必须付出高额的权利金。

对于期权卖家而言

1. 卖出期权的瞬间便可直接获得权利金，这也是期权卖家的最大收益。期权卖家面对的风险一般比买家来得大，因买家有权利于到期日前执行合约，而卖家不得拒绝买家的行权要求。

1. 此外，若买家行权时，卖家账户中没有相对应的股票，卖家可能会被迫借入该股票来满足买家的行权要求。因此期权卖家一般不建议无经验的初学者担任。

1. 期权权利金会受到「期权到期日」以及「对应的股票价格」的影响。到期日愈长的期权合约，以及愈好的对应股票价格，都会提高权利金。卖方承担的风险愈大，其权利金通常也愈高。

## 期权的分类
期权分成看涨(做多)期权CALL和看跌(做空)期权PUT。相应的，你可以买入或卖出这两种期权，所以一共有 4 种操作：

![](/images/posts/20201109-option-1.png)

- 买入 看涨（做多）期权（Long Call）
- 卖出 看涨（做多）期权（Sell Call）
- 买入 看跌（做空）期权（Long Put）
- 卖出 看跌（做空）期权（Sell Put）

> 看涨期权call option（购权）: 看涨期权是一个权利。该权利使持有人（买入方）在合约到期前任何时候，有权以事先约定的价格，买入对应的股票，而不管当时股票的价格是多少。为了获得这个权利，需要支付一定的费用，这个费用叫权利金（premium）。如果买入一种期权，获得的是一种可以选择买或不买的权利，这种期权就是“看涨期权”；
> 看跌期权put option（沽权）：看跌期权是一个权利。该权利使持有人（买入方）在合约到期前任何时候，有权以事先约定的价格，卖出对应的股票，而不管当时股票价格是多少。为了获得这个权利，需要支付一定费用。如果买入一种期权，获得的是一种可以选择卖或者不卖的权利，这种期权就是“看跌期权”。
> 到期日（Expiration）指期权将在这一天到期，在这一天会被自动行权（买入/卖出股票）。按期权履约的方式可以分欧式期权与美式期权。欧式期权必须持有至到期，是不能提前执行的。美式期权可以看做附有提前执行权利的欧式期权，即可以在到期日前的任一交易日行权。
> 看涨：买入看涨期权long call 或者 卖出看跌期权short put都是看涨
> 看跌：买入看跌期权long put 或者卖出看涨期权short call都是看跌

作为期权买方，两个买入期权option的例子。

1. 买入看涨期权LONG CALL。买入call，如果未来价格比合约价格高就赚了差价

	当你买入CALL时，你付出权利金，得到了在行权日当天，以行权价买入股票的权力；
	
	这里补充一个小知识：
	
	每张期权合约，控制100股的股票。一旦选择行权，那么你将买入/卖出 100股股票。如果你有 N 张期权同时行权，那么你将买入/卖出 100 x N 股股票。
	
	为了便于大家理解，我们拿Facebook举例：
	
	假设Facebook当天 $120/股，你预期会持续上涨，于是你买入行权价尽可能低的call，譬如买一张30天后到期的CALL，行权价为 $110。一个月后由 $120/股 -> $125/股，此时你选择行权，我们来看一下会发生了什么？
	
	如前文所述，你将以 $110 的行权价买入100股 Facebook，而股票现价为 $125，于是你获得了（125 - 110）* 100 = $1500 的收益。
	
	当然啦，以上只是一个简化的情形。现在我们继续来深挖细节，还记得我们提到过，“你付出权利金，获得了权力。”这里假设期权的价格为 $5/股，那么你真正获得的利润为：1500 - 5 * 100 = 1000元。

	分析：

	> 买call的人常常是投机者。多数情况下，买call的人并不是因为现在没钱（如果没钱还买call，到行权日钱不够的话，岂不是也是100%的损失?），高于市价买call的多数情况是卖空股票的止损单以及上破阻力位追涨的技术型买家，也就是多数是投机者，他们为 股市优质投资者的价值投资 做出了降低成本的贡献。

1. 买入看跌期权LONG PUT。买入put，如果未来价格比合约价格低就赚了差价
	
	当你买入PUT时，你付出权利金，得到了在行权日当天，以行权价卖出股票的权力。
	
	接着之前的Facebook例子往下说：
	
	Facebook来到了 $125/股，你预期股价阶段性到顶，会进行一波回调，于是你买入一张30天后到期的PUT，行权价为 $130。一个月后股价由 $125/股 -> $122/股，此时你选择行权，我们再来看一下会发生了什么？
	
	你将以 $130 的行权价卖出100股 Facebook，而股票现价为 $122，于是你获得了（130 - 122）* 100 = $800 的收益。同样我们假设期权价格还是 $5/股，那么你真正获得的利润为：800 - 5 * 100 = 300元。

	分析：

	> 买put就是买了一个将来某一天（多数是一个月，时间太久的put都是有价无市，太久的call也是有价无市，1个月的成交比较活跃）以某个价格卖股票的权利，这一天之前我们就应该买入股票了
	> 买put相当于做多的止损单，即上个保险，也就是说如果我们看多某只股票，并且大量买入了。那么就买个put作为保险。万一将来如我们预料股票大涨了，那么就放弃行权；如果该股票大跌，我们可以行权按合约价卖掉股票。
	> 买高价put是庄家做空股票的常用手段，比如facebook现在是高位$120，庄家买$120的put，然后做空让股票跌下来，还能通过执行权力按$120卖出股票

作为期权卖方，两个卖出期权option的例子

1. 卖出看涨期权 sell call。卖出call，如果未来价格比合约价格低就赚了权利金

	卖出CALL，作为卖方你获得权利金；此时对面的买方得到了行权日当天，以行权价买入股票的权利，如果买方行权，你就得卖给他股票

	假设Facebook当天 $120/股，你预期会持续上涨，于是你卖出 行权价尽可能高的call，譬如卖掉一张30天后到期的CALL，行权价为 $130，假设期权的价格为 $5/股。一个月后股票由 $120/股 -> $110/股

	此时，对面的买方有两个选择

	一个是放弃行权，那么你就获得权利金 5 * 100  = $500。

	另一个是买方按$130刀买入你的股票，此事基本不可能，因为买方市场价目前跌到$110，他基本不会行权也就是高价买股票的。如果他真的选择行权，如果你有100股，那么你的利润是 5 x 100 = $500 ；如果你没有股票，从市场上买100股给他就可以了，此时你的利润就是权利金 5 x 100 + (130-110) x 100 = 2500

	分析：

	> 卖call相当于卖出了个权利，对方能以call的合约价跟我们买股票。多数情况下，买正股并持有+卖高价call是一种低风险组合。因为直接卖call风险太大，别人行权的话你不得不买入股票卖给对方，所以卖call多是和正股配合，正股长期持有。比如今天facebook是120一股，我买facebook股票的同时卖下月130的call，卖价5元，那么相当于我今天买入一股Facebook的成本就是115元了。如果下个月facebook没有触及130，就白赚5元权利金。对于长期投资者来讲，持仓成本降低了，只要有持股，以后每月都可以卖一次call。
	> 卖call也就是你随时准备好足够多的股票卖给别人，如果没法筹够足够多的股票，不要超卖call，卖call的股票数量不要超过持有的股票数。
	> 卖call在牛市的时候风险巨大，因为call是期权买方的看涨单。如果市场涨过合约价，期权买方大量买我们的股票，我们需要准备足够多的股票去卖给别人，如果事先没有足够的股票，弄不好会破产：因为市场已经涨很多了，我们需要高价买入大量股票，然后低价也就是合约价卖给别人。

1. 卖出看跌期权 sell put。卖出put，如果未来价格比合约价格低就赚了权利金

	卖出put，作为卖方你获得权利金；此时对面的买方得到了行权日当天，以行权价卖出股票的权利，如果买方行权，你就得买他的股票

	下面这个例子基本不现实，很少人买高位的put：

	假设Facebook当天 $120/股，你预期会下降，于是你卖出行权价尽可能高的put以便赚更多差价，譬如卖掉一张30天后到期的put，行权价为 $130，假设期权的价格为 $5/股。一个月后股票由 $120/股 -> $110/股

	此时，对面的买方有两个选择

	一个是放弃行权，那么你就获得权利金 5 * 100  = $500。

	另一个是买方按$130刀卖给你的股票，此事基本不可能，因为买方当时是希望$130卖给你股票的，市场价目前跌到$110，他基本不会行权也就是赔钱卖给你股票。如果他真的选择行权，你可以拿到$110 x 100的股票然后立刻在股市上卖掉，那么你的利润是 5 x 100 + (130-110) x 100 = 2500

	另一个例子：

	假设Facebook当天 $120/股，你预期会下降，于是你卖出行权价尽可能低的put，譬如卖掉一张30天后到期的put，行权价为 $100，假设期权的价格为 $5/股。一个月后股票由 $120/股 -> $110/股

	此时，对面的买方有两个选择

	一个是放弃行权，那么你就获得权利金 5 * 100  = $500。

	另一个是买方按$100刀卖给你的股票，此事基本不可能，因为目前市场价才跌到$110，如果买方不可能花110买入股票并按合约价100卖给你，他基本不会行权也就是赔钱卖给你股票。如果他真的选择行权，你可以拿到$100 x 100的股票然后立刻在股市上按$110卖掉，那么你的利润是 5 x 100 + (110-100) x 100 = 1500

	分析：

	> 卖put相当于卖出了个权利，对方能以put的合约价卖给我们股票。卖低价put收股票也是价值投资者常用的手段，比如我看中了facebook股票打算入，现在facebook的股价是120块，但我觉得会跌很惨到95块，也就是说那么可以卖出下个月95块行权的put，如果跌到95块，也就是买入目标价，执行义务买入就是了。如果真的跌的比95还低，我们至少还有期权费作为补偿。
	> 卖put就是你要随时准备足够的钱买入这个股票，如果资金不足，不要超卖put，卖put的合约价x股数不要超过将来收购股票的现金能力。
	> 卖put有一定风险，特别是市场熔断的时候风险巨大；因为put是期权买方的止损单。如果市场跌破合约价，期权买方大量卖给我们股票，我们需要准备足够的钱去买人家的股票，弄不好会破产。2008年香港破产的土豪很多，他们都是死在了卖put上面。简单说一下。长和代码是1，绝对蓝筹，2008年大概是150块，很多土豪觉得，跌能跌多少啊，我卖60块的put不是白赚权利金么（当时成交最活跃的在60元，跌到60不到5PE，历史上没出现过）？事实上，很多超级大蓝筹的低价put都是被过分卖出，结果到了2008年股票一路跌下来，持有put的买家开始执行合约止损，也就是卖股票给土豪们，跌到60块该土豪们执行买入股票义务了，其实他们根本没那么多钱买入啊，早已卖出超过自己资产数倍的权利了，于是破产了。其实长和股价最低才跌到50多，如果土豪们能在60块时候接下来所有买入股票的义务，持有等待反弹，就是大赚了。

对于期权卖方来讲：收了人家的权利金，需要承担义务

1. 期权是权利，非实物所有权，不是真正拥有股票

1. 期权是消耗性资产 wasting asset，有效期越长越值钱；越临近有效期，股票走势越明朗，期权越不值钱

1. option的买卖单位是contract（合约），每手合约的价格等于当时的bid 或 ask 价格乘以100.

1. option的交易费，也就是给券商的佣金（commission），一般比股票交易费贵。现在市场上的交易商收取option交易费最便宜的是[Interactive brokers （美国盈透证券，简称IB盈透）](https://www.e-investingguide.com/ibopenaccount)

1. 由于option的升值（或跌值）空间也很大。买多的话（call），一支暴涨（跌）20%的股票的近期option可能跟着就升值（或跌值）100% 或更多。 普通股票涨或跌1%， 如果是近期option call大概可能跟着涨或跌3% 到4% 左右， 远期option却可能没有涨跌。近期option 就是expiration date（到期日） 比较靠近的， 比如现在到本月底或下月；远期option 就是expiration date 比较靠远的，比如现在到半年后。

1.  option是有期限的， 到期就自动过期（expiration date）， 价值变为0。 所以如果option的到期日越短， 它的时间价值（time value)就可能越低。 option的到期日越长， 时间价值就越高。 这里说的Time value 越高， option的升值的可能性就越高。比如现在买40天后的 YGE strike 14 Nov 2013 call， 这只option的价格就是接近零。这是因为在短期49天，所剩时间不多， 股价要从当前的7.7 左右升到14 基本是不可能的， 所以option的价格甚至就会低到0。而如果现在买 YGE strike 15 Mar 2014 call， 这只option还有163天到期。现在的价格是$0.55。所以买卖option还必须了解清楚过期日expiration date还剩几天。

## 期权有几个关键的概念：

- 底层证券(underlying stock) ，即标的物，也就是具体某只股票
- 合约到期日（expiration date），到了时间就过期。有期权的一方可以在到期时选择执行期权或放弃执行。
- 行权价（strike price），买卖双方按该价格执行合约。
- 权利金（premium），也就是期权的现金价值。期权的现金价值会随着时间以及股价波动。
- 期权行权（exercise）：指作为期权买方，期权到期 行使权力。也就是说，期权买方可以以期权约定的价格，向期权卖方买入股票，此时，如果期权卖方刚好有相应股票，就自动卖给期权买方；如果期权卖方没有该股票，券商会自动帮他卖空该股票，也就是让期权卖方自动拥有该股票，
- 期权指派（assignment）：指作为期权卖方，接到买方的指令后，必须履行的义务

1. spread：bid, ask的差价。
	option的bid ask spread有时候很大，如果不熟悉的话千万要小心，否则很容易亏损。
	
	比如：
	
	bid 0.15（买方出价， 也就是你的卖出价）
	
	ask 0.25（卖方出价， 也就是你的买入价）,
	
	spread（区间） 就是10美分。 （0.25-0.15=0.1 ）。
	
	ask是你的买入价是0.25， 而此时如果买入后立即卖出， 却只能卖到bid 0.15这个价格。 所以 买option 必须持有一段时间 等到 最新bid 价格高于你买入的ask价格之后卖出才会盈利， 否则 就会亏损。与stock相比， option的bid ask spread区间大很多， 所以option的风险也很大。

# FAQ
## 股指期货 vs. 股票
1. 期货合约有到期日，不能无限期持有。

	股票买入后可以一直持有，正常情况下股票数量不会减少。但股指期货都有固定的到期日，到期就要摘牌。因此交易股指期货不能象买卖股票一样，交易后就不管了，必须注意合约到期日，以决定是提前了结头寸，还是等待合约到期（好在股指期货是现金结算交割，不需要实际交割股票），或者将头寸转到下一个月。

1. 期货合约是保证金交易，必须每天结算

	股指期货合约采用保证金交易，一般只要付出合约面值约10-15%的资金就可以买卖一张合约，这一方面提高了盈利的空间，但另一方面也带来了风险，因此必须每日结算盈亏。买入股票后在卖出以前，账面盈亏都是不结算的。但股指期货不同，交易后每天要按照结算价对持有在手的合约进行结算，账面盈利可以提走，但账面亏损第二天开盘前必须补足（即追加保证金）。而且由于是保证金交易，亏损额甚至可能超过你的投资本金，这一点和股票交易不同。

1. 期货合约可以卖空

	股指期货合约可以十分方便地卖空，等价格回落后再买回。股票融券交易也可以卖空，但难度相对较大。当然一旦卖空后价格不跌反涨，投资者会面临损失。

1. 市场的流动性较高。

	有研究表明，指数期货市场的流动性明显高于股票现货市场。如在1991年，FTSE-100指数期货交易量就已达850亿英镑。

1. 股指期货实行现金交割方式

	期指市场虽然是建立在股票市场基础之上的衍生市场，但期指交割以现金形式进行，即在交割时只计算盈亏而不转移实物，在期指合约的交割期投资者完全不必购买或者抛出相应的股票来履行合约义务，这就避免了在交割期股票市场出现“挤市”的现象。

1. 专注于根据宏观经济资料

	一般说来，股指期货市场是专注于根据宏观经济资料进行的买卖，而现货市场则专注于根据个别公司状况进行的买卖。

## 股票 vs. 期权 vs. 期货
股票：指买卖双方立即履行票据买卖

期货：是指买卖双方约定在未来某一特定时间按照约定的价格在交易所进行交收标的物的合约。

期权：指卖方必须按在未来某一特定时间按照约定的价格在交易所进行交收标的物的合约。

![](/images/posts/20201109-option-11.jpg)

1.权利和义务不同

	期权是单向合约，是卖方的单向。程大叔买了X公司的股票期权，就取得了在到期日有权买入或者卖出X公司股票的权利，而不必承担义务。x公司拿了期权保证金，则有义务在到期日将股票按合约价卖给买家，卖方有履行合约的义务。
	
	期货合同则是双向合约，交易双方都要承担期货合约到期交割的义务。比如说，程大叔买入了一份3个月大豆期货，约定价格为3800元/吨
	
	如果3个月后的价格为4000元/吨，则以3800元买入，赚了；
	
	如果3个月后的价格为3500元/吨，则以3800元买入，亏了。

	这里一个例子：
	
	期货：跟卖车的签订一个协约，一年后你必须以1w买入这辆车。 你有买车的义务，对方有卖车的义务。如果这车价格涨到2w了那你以1w买入，算是赚了1w。但是如果车价格跌到5000了，你仍然要以1w买入，以为已经签订了协约。
	
	期权：跟卖车的签订一个协约，一年以后你可以以1w元买入这辆车。这里与期货不同之处在于这里卖车的有义务卖给你车，但是你一年后可以选择买也可以选择不买。所以你只有买入的权利而没有买入的义务。 如果这车价格跌至5000时，你可以选择不买，但是如果车涨到2w时，卖车老板一定要以1w卖给你。 这时候你会不会感觉这这样对于卖车的来说不公平？所以这时你就要先付给卖车老板一点钱（premium ）来补偿他。

1. 履约保证不同

	在期权交易中，买方最大的风险只限于已经支付的权利金，所以不需要支付履约保证金。而卖方面临较大风险，因而必须缴纳保证金作为履约担保。

	而在期货交易中，期货合约的买卖双方都要交纳一定比例的保证金

3.盈亏的特点不同

	期权交易中，买方的收益随市场价格的波动而波动，其最大亏损只限于购买期权的权利金；卖方的亏损随着市场价格的波动而波动，最大收益（即买方的最大损失）是权利金。

例子：

A:现在你有100万，有一个房子售价也是100万。如果你直接拿100万去买房，双方一手交钱一手交房，过户以后这房价涨跌都跟卖房的没关系了。你认为下个月房价会涨，你用100万买了1套房，你现在就可以把房租出去收租了，这就是股票。

B:你跟卖方约定下个月把房款交齐，现在先交10万定金，卖房的说这样得多收一点钱，因为有物业水电什么的，于是约定1个月以后房价为100.5万，卖房的到期可以多卖5千块。你认为下个月房价会涨，于是你用100万定下了10套房，这就是期货。

C:你出1万块，一个月以后还是按100万的房价买房，但这1万不算在内，到期卖房的可以得101万。你认为下个月房价会涨，于是你用100万定下了100套房的购买权，这就是期权。

假如房价下个月涨到了110万，A只赚了10万，但B就赚了9.5×10=95万，C就赚了(10w增值-1w定金）×100=900万。同样是100万，股票只赚了10%，期货赚了95%，期权赚了900%！

假如房价下个月跌到了99万，A只亏了1万，但B就亏了1.5×10=15万。那C呢？C只有在房价涨到101万才能保本，否则都是亏的，甚至房价保持不变，到期也是亏光了。假如房价两天以后暴跌到90万，A亏了10万，但A可以不卖房等价格回升。而B此时就亏光了定金了。但卖方是按100.5万卖的，B的情况你还是得到期按这个价买，为了保证卖方的利益，你必须给卖方追加5千定金。C则不必按100万买房了，卖方也不会找你再要什么钱，到期你不买最多也就赔了这1万。假如房价继续暴跌到80万，B就还得追加10万，否则合同取消，之前交的定金被没收赔给卖方，你还得赔出10万给卖方。这就是期货的强行平仓。

总结一下：如果预期价格会大幅变动，就要买期权。如果预期价格只会小幅波动，就买期货。如果预期价格不会有什么变化，那就买股票，还有分红。

期权合约的价值会随着时间流逝而减少，此特性使期权不适合做为一个长期投资的工具。若您是想长抱一档股票，直接买入股票是最适切的作法。

# FAQ
跟着大多数人，是肯定赔钱的，一方面要合理配置，该空仓就空仓，该做多就做多，该做空就做空；另一方面要适时止损，适时买option加保险

周一option异动概率比较大，因为上个option在周五刚刚过期，同时周一股票比较容易拉涨或拉低

# Reference
[期货与期权的主要区别与联系？](https://www.zhihu.com/question/26695034)

[期货知识入门](http://futures.jrj.com.cn/focus/futureszs/?to=pc)

[期权基础入门：看涨，看跌，期权链及行权和指派 video](https://powerupgammas.com/option-basics-call-option-put-option-exercise-assignment/)

[期权与期货的区别](https://www.guosen.com.cn/gxzq/ggqq/xzqq13.jsp#:~:text=%E6%9C%9F%E6%9D%83%E6%98%AF%E4%B8%80%E7%A7%8D%E5%8F%AF,%E6%94%B6%E6%A0%87%E7%9A%84%E7%89%A9%E7%9A%84%E5%90%88%E7%BA%A6%E3%80%82&text=%E4%B9%B0%E6%96%B9%E6%9C%89%E4%BB%A5%E5%90%88%E7%BA%A6%E8%A7%84%E5%AE%9A,%E5%8D%96%E6%96%B9%E5%88%99%E8%A2%AB%E5%8A%A8%E5%B1%A5%E8%A1%8C%E4%B9%89%E5%8A%A1%E3%80%82)

[通过期权成交量找出投机机会](https://www.tradesmax.com/component/k2/item/4649-option)

[做期权的小伙伴不得不了解的神秘的异动期权（unusual options activity）](https://q.futunn.com/nnq/detail?id=104369328422916)

[trade-alert.com](https://trade-alert.com/services_individual/)

[聊一聊卖出看涨期权和买入看跌期权的区别](https://zhuanlan.zhihu.com/p/37948354)

[股票做空是个什么概念？](https://www.zhihu.com/question/19716709)

[美股期权学习笔记（一）](https://blog.devtang.com/2020/02/08/option-learning-note/)

[如何理解期权 Sell Put？](https://www.zhihu.com/question/20984976)

[期权入门篇](https://zhuanlan.zhihu.com/p/29658293)