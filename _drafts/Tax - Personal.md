---
layout: post
title: Tax - Personal income tax 
author: Andy Feng
---

## Marginal tax rate 边际税率
`边际税率`是指在不同等级的应纳税收入上，每增加一元的收入所增加的应纳税数额，也就是增加这部分收入所纳税额同增加收入之间的比例。

> 对于所得税来说，边际税率是每多增加1元钱的收入中税收所占的比例；对于遗产税来说，其边际税率就是每多增加1元钱的遗产中税收所占的比例；而对于公司所得税来说，其边际税率就是公司新增加1元钱利润中税收所占的比例。
> 通常情况下，边际税率采用累进税制，即不同收入区间，边际税率不同。另一种是比例税制，即收税总是按收入的固定比率。

以个人所得税为例，譬如免征额为800元，则800元以下的这部分所得额的边际税率为0；当所得额为 1000元时，增量200元，若收税10元，则边际税率为10元÷200元=5%。当所得额为1800时，增量1000元，税率10%，税额100元，边际税率10%。可见，个人所得税超额累进税率表中的每一级税率实际上就是相应级距所得额的边际税率。而应纳税额和全部应税所得额的比则是平均税率。当然，并非所有税种的边际税率都随着征税对象边际数额的增加而提高。

`平均税率`是相对于边际税率而言的，它指全部税额与全部收入之比。在比例税率条件下，边际税率等于平均税率。在累进税率条件下，边际税率往往要大于平均税率。

边际税率与平均税率是依据政府制定的税表计算出来的，即`名义税率`，没有考虑退税，部分政策免税，等等情况

`实际税率`是`名义税率`的对称，是实征税额占征税对象数额的比率。实际税率与名义税率可能相等，也可能不等。两个人收入完全相同，但是根据个人和家庭情况，实际征收的税可能不同，此时就属于名义税率相等但实际税率不同的情况。

## 个人所得税计算
首先，我们要需要填一张表和查一张表

1. 填表计算Net Income for Tax Purposes

	![](/images/posts/20200106-tax-10.jpg)

1. 查表Tax Rate Brackets，譬如2009年BC省：

	![](/images/posts/2019200406-tax-8.png)

	基于前面计算出的Taxable Income后，对照第二张表，就可以知道你的税率。当然，由于本国的所得税法采用的是Progressive System，计算的方法有些特别。

	你可以把你整年的Taxable Income想象成一块大蛋糕，政府把这块蛋糕切成不同小块，然后每一块拿走一点点。第一小块称为Base Amount，大约是$9,300左右是不抽税的。然后第二小块，也就是$9,300 ~ $35,716中这块$26,416的部份，政府会抽走20.06%的税，也就是$5,299.05。

	由此类推下去，这$61,000中，有$12,457.70是被政府分走的，实际税率便是20.42%。下图是分块计算细节：

	![](/images/posts/20200106-tax-11.jpg)

# 自雇

# taxable vs. non-taxable

# 税务居民和非税务居民
报税之前首先要确定是否加拿大的税务居民, 因为居民与非居民在税务上及福利方面都完全不同.

1. 个人报税表的第一页有这样一项: “Enter your province or territory of residence on December 31”. 这里的residence（税务居民）是什么涵义?

	> 凡是在涉及到税务的文件里面出现residence一词, 我们就不应该仅仅将它理解为一般意义上的“居住”, 它是一个税务上的专用词汇, 是税务上的“居住”概念, 或者说是哪儿的税务居民的意思。判断属于哪里的税务居民基本上就是根据居住联系(Residential ties), 具体来说由以下几方面来考虑: 首先是您的居所, 配偶和需抚养的未成年的孩子; 其次是社会及财务的联系, 例如银行帐号, 信用卡, 医疗卡, 驾驶执照等等. 如果您在某年12月31日虽然在中国, 但是如果您的家, 配偶和孩子在温哥华, 并且您还有其它社会的和财务的联系在这里, 那么您属于BC省的税务居民, 您应该在这一栏填上BC省。
	> 一般来说，在上一年度12月31日时，如果长住在哪个省份，第二年报税就在哪个省份报税


#References#
[Canadian income tax rates for individuals - current and previous years](https://www.canada.ca/en/revenue-agency/services/tax/individuals/frequently-asked-questions-individuals/canadian-income-tax-rates-individuals-current-previous-years.html)

[Tax packages for all years](https://www.canada.ca/en/revenue-agency/services/forms-publications/tax-packages-years.html)

[T1 form](https://www.canada.ca/content/dam/cra-arc/formspubs/pbg/5006-r/5006-r-18e.pdf)

[https://www.canada.ca/en/services/taxes/income-tax.html](https://www.canada.ca/en/services/taxes/income-tax.html)

[https://turbotax.intuit.ca/tips/who-can-benefit-from-the-home-buyers-tax-credit-5203](https://turbotax.intuit.ca/tips/who-can-benefit-from-the-home-buyers-tax-credit-5203)

[加拿大个人所得税计算方法专业解析](https://www.juwai.com/news/131471.htm)

[Tax Calculator - SimpleTax](https://simpletax.ca/calculator)

[9.2.4 Tax brackets and rates](https://www.canada.ca/en/financial-consumer-agency/services/financial-toolkit/taxes-quebec/taxes-quebec-2/5.html)