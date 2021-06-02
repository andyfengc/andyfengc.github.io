---
layout: post
title: Finance - investment
author: Andy Feng
---

本文讨论加拿大各种投资，stock，ETF

# Stock
## Place order
美股下单方式包括基本的市价单和限价单，还有止损单，止损单加上限价、移动停损单、移动停损限价单等

1. 市价单 Market order：

	是以当时市场价格成交的订单，不需要自己设定价格，可以使得订单快速成交。

		市价单保证成交，但不保证成交价格，尤其是当市场快速变动的时候，或者对于交易不活跃的股票，市价单成交常常会以较高或较低价格执行；
		盘前或者盘后下的市价单，很有可能在开盘时，以较高或较低的价格被执行；
		盘前从美东时间8:10开始可以下市价单，在这之前只能下限价单。对应北京时间（冬令时）：22:10，（夏令时）：21:10。

	![](/images/posts/20210207-invest-1.png)

1. 限价单 Limit order：

	限价单（Limit Order）需要指定成交价格，只有达到指定价格或有更好价格时才会执行。限价单可以用于用更低的价格买入，用更高的价格卖出

		如果股票未达到或不超过限价，订单将不会被执行；
		股价方向确定后，会以相当快的速度发动走势(譬如庄家快速拉高)，但成交量和成交时间不确定，此时如果采用限价单，可能会错失行情；
		部分未成交的限价单可以进行撤单和改单，当改单或撤单过程中原订单还在生效，可能会出现修改过程中原订单全部成交导致无法撤单和改单的情况。

	![](/images/posts/20210207-invest-2.png)

1. 止损单（Stop Order或stop-loss order）

	指在订单中设置止损价格，需要输入一个指定的止损价,即触发价（Stop Price），一旦股价到达所设定的止损价，将会以市价单的方式成交。止损单是帮助客户的股票和期权持仓保护利润和限定损失的一种下单方式，如果是卖单，当股价跌破目标价位时自动卖出；如果是买单，但股价超过目标价位时买入。

	> 止损单可视作市价单的一个包装，止当系统检测达到触发价后自动提交市价单，并立即执行。
	> 能保证100%成交;

	![](/images/posts/20210207-invest-3.png)

	止损单分为卖单和买单。

	1. 卖单用于卖出止损，而非卖出止盈。
		
		> 例子：您以$10买入100股的XYZ，你希望将最大损失控制在100美元，因此您创建了一个$9.10的止损价，以及一个$9的限价，一旦XYZ的股价下跌至$9.10的时候，您持有的100股XYZ将以$9的限价形式被触发。

	1. 买单用于买入止损，而非买入持有。譬如你做空一支股票，期待股价下跌。当股价并未下跌反而上涨时，就可以执行stop order买入该股票停止做空。
	
1. 限价止损单(Stop Limit Order)

	需要客户输入2个价格：一个指定的止损价,即触发价(Stop price),和指定的限价(Limit Price)，一旦股价达到设置的止损价，将以限价单的方式下单。

	> 止损单可视作限价单的一个包装。止当系统检测达到触发价后自动提交限价单;触发后的下单和普通下单一样，如果一直没有撮合，当天收盘后会自动撤销。
	> 不一定能保证100%成交;

	![](/images/posts/20210207-invest-4.png)

	操作提示
	> 当买入止损时，限价要大于止损价。
	> 
	> 当卖出止损时，限价要小于止损价。

1. 移动止损市价单(价差&百分比)Trailing Stop order
	
	移动止损市价单(Trailing Stop order)简称移动止损单，就是不限定止损价格本身，而是可以设置止损价格和市场价格之间的差价。差价的设置可以用金额，也可以用市价的百分比表示。
	> Questrade 只支持设置价差(trailing)
	
	![](/images/posts/20210207-invest-5.png)

	优势：在跟踪止损单的止损价是自动调整的。当市场价格向有利的方向变化时，止损价格会以客户设置的差价(金额或百分比)为间距随着市场价格的变化而变化。
	
	说明及注意事项
	
	· 能保证100%成交;跟踪止损是系统检测达到触发价后自动帮投资者提交市价单并立即成交;
		
	· 客户使用这种订单，可以限制最大可能的损失，而不对收益的最大值做任何限制，在上了保险的同时让利润最大化。

	· 当“卖出”一个跟踪止损单时，市场价格上升时，止损价格也会随之上升;但是当市场价格下降时，止损价格会保持不变。老虎证券股票学院介绍，当股价触及止损价格时，将会向交易所传送一个市价定单。
	
	· 对于追踪止损“买入”单，在市场价格下降时，止损价格会随之下降;但是当市场价格上升时，止损价格会保持不变。

1. 移动止损限价单(价差&百分比)Trailing Stop limit order
	
	移动止损限价单Trailing Stop limit order类似Trailing Stop order，只是当触发价后自动提交限价单Limit order

	![](/images/posts/20210207-invest-6.png)

	· 不一定能保证100%成交;已触发不代表订单成交。跟踪止损只是系统检测达到触发价后自动帮投资者提交限价单;触发后的下单和普通下单一样，如果一直没有撮合，当天收盘后会自动撤销;
	
	· 跟踪止损单触发产生真实订单后，真实订单和跟踪止损单将没有任何实际关联，删除跟踪止损单单并不会对真实订单产生任何影响;

# Brokers
## Questrade

## Wealthsimple 
[Wealthsimple](https://www.wealthsimple.com/en-ca/)是加拿大的理财平台，有如下产品

![](/images/posts/20210215-invest-1.jpg)

1. Invest: 每年收取0.4-0.5% management fee的理财产品

	![](/images/posts/20210215-invest-2.jpg)

1. Cash: 现金存款账户，相当于高息的chequing account, 会发一张黑金色的debit card 

	![](/images/posts/20210215-invest-4.jpg)

1. Trade: 免交易费的在线证券交易平台
	
	![](/images/posts/20210215-invest-3.jpg)

1. Crypt: 电子货币投资平台

1. Tax: 在线免费报税平台SimpleTax

## Interactive Brokers（盈透证券）
[https://www.canadianrewards.org/2020/12/interactive-brokers.html](https://www.canadianrewards.org/2020/12/interactive-brokers.html)

## Tiger Brokers（老虎证券）
[https://www.canadianrewards.org/2020/12/tiger-brokers.html](https://www.canadianrewards.org/2020/12/tiger-brokers.html)

# ETF
## ARK
ARK投资是美国市场的投资公司之一，其创始人和基金经理人是凯瑟琳.伍德(Catherine Wood)女士。Catherine Wood拥有40年的投资经验，在2014年创办ARK投资公司，专注于发掘和投资那些具备颠覆性创新能力的新兴企业

ARK基金主要包括[5只主动管理型ETF](https://ark-funds.com/active-etfs)和[2只被动型ETF](https://ark-funds.com/index-etfs)：

![](/images/posts/20210215-invest-5.jpg)

1. **ARKK**：ARK Innovation ETF（ARK创新ETF），ARKK投资的公司包括那些依赖或受益于新产品或服务的开发，与DNA技术领域有关的科学研究的技术进步的公司，能源，自动化和制造领域的工业创新公司，共享技术，基础设施和服务（下一代互联网）公司以及金融科技创新公司。ARKK是一个主动管理的ETF，通过将其资产的至少65％投资于国内和国际创新投资主题相关的公司来寻求资本的长期增长。可以认为是其他四只etf的合体。

1. **ARKQ**：Autonomous Technology & Robotics ETF（自动技术与机器人ETF），ARKQ投资的公司专注于并有望从新产品或服务的开发，技术改进以及与能源，材料和运输，自动化和制造等相关领域的科学研究的进步中受益匪浅 。ARKQ是一个主动管理的ETF，它在正常情况下将其资产的至少80％投资于与该基金的投资主题相关的自主技术和机器人公司的国内外股票证券。投资领域报包括：1）无人驾驶运输（Autonomous Transportation）；2）机器人与自动化；3）3D打印；4）能源储备；5）太空探索

1. **ARKW**：Next Generation Internet ETF（下一代互联网ETF），ARKW是一个主动管理的ETF，它在正常情况下将其资产的至少80％投资于下一代互联网，投资于国内和美国交易所买卖的外国股本证券。ARKW投资专注于：1）云计算与网络安全；2）电子商务；3）大数据与人工智能（AI）；4）移动技术与物联网；5）社交平台；6）区块链和P2P，比特币

1. **ARKG**：Genomic Revolution ETF（基因组革命ETF），通过将技术和科学发展以及基因组学的进步纳入其业务范围，ARKG投资的公司专注于并有望从延长和提高人类及其他生活的质量中受益匪浅。 ARKG将集中于医疗保健行业中任何行业公司，主要是生物技术行业公司。ARKG是主动管理的ETF，它通过将其资产的至少80％投资于多个行业的公司的国内外股票证券，以寻求资本的长期增长，投资行业包括医疗保健，信息技术，材料， 与基金的基因组学革命主题相关的能源和消费者行业公司。

1. **ARKF**：Fintech Innovation ETF（金融科技创新ETF），一家主动管理的交易所买卖基金（ETF），旨在寻求资本的长期增长。它力求通过将资产的80%以上投资于国内和国外的金融科技（Fintech）公司。ARK认为该金融产品包括但不限于以下业务平台： 1）交易创新；2）区块链技术；3）Risk Transformation；4）Frictionless Funding Platforms；5）Customer Facing Platforms；6）New Intermediaries

ARKW每年的费用是0.76%，ARKK是0.75%，比普通ETF偏贵。如果你对投资基金感兴趣，期望能够获得较高回报，且能承受较高的风险，那么我推荐你考虑ARK旗下的基金，尤其是ARKW,ARKK, ARKG。

![](/images/posts/20210215-invest-6.jpg)

1. **PRNT**: 3D打印

1. **IZRL**: 以色列创新科技

加拿大版本Emerge ARK与ARK合作, 2019年10月推出， 有[5个产品](https://emergecm.ca/emergearketfs/) 

![](/images/posts/20210215-invest-7.jpg)
 
1. EARK: Emerge ARK Global Disruptive Innovation ETF，创新etf -> ARRK

1. EAGB: Emerge ARK Genomics & Biotech ETF， 基因生物科技 -> ARKG

1. EAAI: Emerge ARK AI & Big Data ETF， 人工智能和大数据 -> ARKW

1. EAUT: Emerge ARK Autonomous Tech and Robotics ETF， 自动化和机器人 -> ARKQ

1. EAFT: Emerge ARK Fintech Innovation ETF， 金融科技 -> ARKF

## ARK vs. Emerge ARK
![](/images/posts/20210215-invest-8.png)

1. Emerge ARK 交易量小，每天仅2万股左右

2. Emerge ARK 回报差距比ARK差很多，差20-50%不等

1. Emerge ARK旗下所有产品的管理费0.8%，但总费用Mgmt. Expense Ratio MER据说1.7%。但原版ARKK MER只有0.75%，不到其一半

# FAQ
## Stop order vs. Limit order
Stop order（stop-loss order）和limit order最大的差别是, Limit order在你下单后你的券商是马上将这个订单推送到市场。但是stop order不是，提交stop order后，这个订单在券商这里，

拿下跌止损来说，比如当前股价是15，你希望如果股价下跌，最多损失5刀每股，你设定stop order price是10，只有当股票大跌的时候并且跌穿10刀的时候，这时候券商会将订单推向市场，成为一个market order, 以低于10刀的价格尽快卖出。但是券商无法保证卖出价格是多少。如果从10.01刀直接跌到8刀，那么将以8刀的价格成交。

## 止损单stop order vs. 限价止损单stop limit order
stop order（stop-loss order）会以市价单的方式保证订单可以尽可能快的成交，但不保证成交的价格。

stop limit order则会以限价单(Limit)形式挂出以保证成交的价格等于或好于客户所设定的限价价格，但是不保证一定会成交。

说明及注意事项
> 如果是止损单，则一定可以成交，尽管可能卖的很低;
> 
> 对于限价止损单，当价格快速下落到限价以下，这个下单就没用了，损失就会一直扩大下去。

# References
[Questrade Order types](https://questrade-support.secure.force.com/mylearning/view/h/Investing/Order+types)

[美股订单类型](http://www.laohuzhengquan.net/hangqing-4689.html)

[让你赚更多钱的美股下单方式](http://www.laohuzhengquan.net/chaomeigu-454.html)

[如何设置Trailing Stop移动止损](https://www.3dfn.com/%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AEtrailing-stop%E7%A7%BB%E5%8A%A8%E6%AD%A2%E6%8D%9F/)

[Wealthsimple Trade 简介](https://www.canadianrewards.org/2021/01/wealthsimple-trade.html)

[加拿大炒股0手续费软件Wealthsimple Trade怎么样？](https://www.infosqc.com/2020/06/17/1206/)

[用WealthSimple Trade购买股票的心得分享](https://www.infosqc.com/2020/07/29/1421/)

[Wealthsimple Cash 简介](https://www.canadianrewards.org/2020/01/wealthsimple-cash.html)

[Wealthsimple Crypto免费交易比特币和以太坊？真的免费吗？](https://www.youtube.com/watch?v=uKKIdgwF_PA)

[加拿大的Robinhood（罗宾汉）？炒股无手续费券商？适合什么人用？](https://www.youtube.com/watch?v=PVXoOR2doDo)

[万物皆可ETF：一文带你玩转ETF基金](https://cn.investing.com/analysis/article-200456797)

[加拿大股民现金放哪？如何保证资金安全？三种方法解析！](https://www.youtube.com/watch?v=MI0MWlldDIU&t=208s)

[如何用加币炒美股？如何加元美元便宜换汇？如何划算的加币美刀互换？](https://www.youtube.com/watch?v=IGL5sHmkql8)

[加国银行高利率账户汇总](https://www.canadianrewards.org/2015/05/56.html)

[加国银行账户开户奖励汇总](https://www.canadianrewards.org/2018/05/201805.html)

[那些年，我们一起去的加油站](https://www.canadianrewards.org/2017/11/201711.html)

[加拿大信用卡之新移民开户办卡指南](https://www.canadianrewards.org/2016/08/aug-2016-by.html)

[如何无损操作加元美元换汇](https://www.canadianrewards.org/2017/03/blog-post_22.html)

[加拿大信用卡之我见 - FAQ](https://www.canadianrewards.org/2015/01/faq.html)

[加拿大信用卡之留学生开户办卡注意事项](https://www.canadianrewards.org/2016/09/canada-student-guide.html)

[加拿大银行账户之我见](https://www.canadianrewards.org/2015/01/blog-post_3.html)

[理财窍门 如何用房屋贷款利息减税[zt]](http://www.depqc.com/thread-25710-1-1.html)