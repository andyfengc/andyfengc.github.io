---
layout: post
title: Tax - Personal Income Tax 
author: Andy Feng
---

本文讨论个人税务

# 个人所得税计算
首先，我们要需要填一张表和查一张表

1. 填表计算Total Income and Net Income

	![](/images/posts/20200106-tax-10.jpg)

1. 查表Tax Rate Brackets进行计算，譬如2009年BC省：

	![](/images/posts/2019200406-tax-8.png)

	基于前面计算出的Taxable Income后，对照第二张表，就可以知道你的税率。当然，由于本国的所得税法采用的是Progressive System，计算的方法有些特别。

	你可以把你整年的Taxable Income想象成一块大蛋糕，政府把这块蛋糕切成不同小块，然后每一块拿走一点点。第一小块称为Base Amount，大约是$9,300左右是不抽税的。然后第二小块，也就是$9,300 ~ $35,716中这块$26,416的部份，政府会抽走20.06%的税，也就是$5,299.05。

	由此类推下去，这$61,000中，有$12,457.70是被政府分走的，实际税率便是20.42%。下图是分块计算细节：

	![](/images/posts/20200106-tax-11.jpg)

计税例子，譬如2012年的联邦税和BC省税 ：

1. 联邦税：

		第一级：$42,707以下，交15％；阶梯总税6406.05
		第二级：$42,707－$85,414，交22％；阶梯总税9395.54
		第三级：$85,414－$132,406，交26％；阶梯总税12217.92
		第四级：$132,406以上，交29％。

1. 每个省的收入税略有不同，一般在联邦税的一半以下，BC省有5级：

		第一级 : $37,013以下，交5.06％；阶梯总税1872.86
		第二级 : $37,013－$74,028，交7.7％；阶梯总税2850.16
		第三级 : $74,028－$84,993，交10.5％；阶梯总税1151.33
		第四级 : $84,993－$103,205，交12.29％；阶梯总税2238.25
		第五级 : $103,205以上，交14.7％

1. 以一个典型的单身打工族为例，看看不同年薪在BC省需要交的收入税：

		3万年薪需交联邦税$4,500,省税$1,518,共$6,018；
		5万年薪需交联邦税$8,010,省税$2,873,共$10,883；
		7万年薪需交联邦税$12411,省税$4413,共$16824；
		10万年薪需交联邦税$19,594,省税$7,718,共$27,312；
		15万年薪需交联邦税$33,122,省税$14,990,共$48,112.

对于安省来说，除了Combine 联邦税和省税，安省还有一个附加税(surtax)，附加税在省税基础上增收。譬如通常提到的年收入超过22w，收53%的税，其实`联邦最高税阶33% + 安省最高税阶13.16% = 46.16%`，比53%少算的一部分就是增收的surtax
> 2019年，当年收入超过4740，收20% surtax；年收入超过6067，收 20%+36%=56% surtax。
> 当年收入超过22w时，含surtax的完整算法是：`联邦最高税阶33% + 安省最高税阶13.16% + 安省最高税阶13.16% x 56% = 53.53%`

![](/images/posts/20200402-tax-1.png)

# 报税清单checklist
报税前请对照下面清单，逐项检查资料是否准备齐全，不要遗漏。  

## 收入类资料清单准备：
各类薪酬收入，包括佣金、顾客支付的小费、津贴等收入凭证

请注意，所有的T表都是这样：开出T表的单位都是同时在给你的时候会给税务局一份，所以T表都不能遗漏，否则税务局都会来查的。必须要报。

1. 如果上年度报过税，上年度报税的税表存底和税务局的NOA。

1. 个人、配偶及子女的SIN卡、枫叶卡、Landing Paper等报税者及家人的个人信息，地址，出生日期，银行账号，联系电话，是不是公民，新登录日期

1. T4工资收入 Statement of Remuneration Paid - salary
	> 去年你为之工作过的每一个公司、机构寄来的T4表。这些公司、机构最迟在2月底前一般都会寄给你T4表，如果没收到，应该去询问；实在收不到，估算一下你挣得的金额，填入税表中，附上一张字条说明你没能取得或丢失那张收入凭单，并提供应该发出那张凭单的雇主的名称和地址
	> Employer's name(雇主名字)
	> 14 Employment Income （工资收入）
	> 16 Employee's CPP Contributions（所交养老金）
	> 18 Employee's EI Premiums （所交失业保险）
	> 22 Income Tax Deducted （所交税）
	> 24 EI Insurable Earnings（受保内的收入）
	> 所有BOX中的数据

1. RRSP - Schedule 7
	> RRSP deduction limit for 2012(2012年RRSP的限制数额)
	> contribution amount(RRSP投入数额)
	> 关于RRSP的购买额度，可以参考2011年的Notice of Assessment

1. T4A自雇收入 - commision
	> your name(你的名字), time from: to (从什么时间到什么时间), final year of business(yes or no)? (是不是最后一年), business name(商业名称), main commodity(主要产品), business address(公司地址), business number(联系电话)
	> 研究费收入、学术研究基金及助学金，读书奖学金T4A, 填在box 105，报税但不需要缴税 
	> 20 self-employed commission （自雇收入）
	> 22 income tax deducted （所交税)
	> 28 other income （其他收入）

2. T4E失业保险收入 Statement of Employment Insurance and Other Benefits
	> 如果你去年领EI，会收到T4E表
	> 14 total benefit paid（所有福利所得）
	> 15 regular and other benefits （一般和其他福利）
	> 17 employer/support measures（雇主支持）
	> 20 taxable tuition assistance （雇主学费支持）
	> 21 non-taxable tuition assist （不用扣税的学费支持）
	> 22 income tax deducted（所交税）

1. 养老金OAS T4A (OAS),  退休福利 CPP T4A(P) Statement of Pension, Retirement, Annuity and Other Income
	> 耆老福利金、政府及私人机构退休金
	> 老人的Old Age Security & CPP

1. 公司雇员除底薪外还有Commissions收入，要准备雇主签字的T2200表，汇总整理所有要申报的费用收据。

1. T3 股票红利，资本收入
	> 32 taxable amount of dividends(分红收入)
	> 21 capital gains(投资收入)

3. T5 Statement of Investment Income 利息收入
	> 银行利息，股息收益，投资收益等，通常每年2 月份银行等金融机构会邮寄给客户
	> 这个银行每年会给你一个T5表的，而且银行是一式三联，一份交税务局，一份给你，一份它自己留底，所以你报税的时候不能忘记这个要报，否则税务局都会来查的
	> 11 Taxable amount of dividends(分红收入)
	> 13 Interest from Canadian sources(利息收入)
	> 18 capital gain dividends(投资分红收入)
	> 14 other income Canadian sources(其他收入)
	> Student loan interest paid of Income tax credit （学生贷款利息）,可以参考National student loans service centre 寄来的信，第2页最后一行的数据。

1. 投资房产的收入（如果买卖自住房产，则不必申报收入）、房租收入、物业租金收入
	> address of property(房屋地址)
	> gross rents income(房租收入)
	> personal rate(自用百分率)
	> 注解：1. 按卧室房间数进行费用分摊。例子：整栋房有8间房，出租4间，自住4间。自用百分率为50%; 2.按租用面积进行费用分摊。 例子：整栋房除开公用面积（厨房，卫生间）面积100平方，出租50平方，自用百分率为50%。分租面积不应超过40%

1. 生意收入

1. （自雇者）财务报表financial statement（for self-employed）

1. 国内外投资收入：
	> 股票（为投资证券所贷款的利息支出单据、买卖交割凭单或Trading Summary）
	> 房产出租及出售的记录（如果买卖自住房产，则不必申报收入）
	> 你有存款或资金户口的银行或其它投资机构寄来的T5表，T3表，上面有利息收入，股息所得

1. T4RSP - RRSP 提款收据
	> 注册退休储蓄计划收入

1. Federal unused tuition, education and textbook amounts and unused Ontario tuition and education amounts (没有用掉的联邦，安省学费教育额度) - Schedule 11
	可以参考Notice of Assessment第一页中的第2段和第3段中的数据。

1. T5007 福利金收入表Statement of Benefits
	> 社会福利以及Workers' compensation
	> 10 救济金总额

1. RC62, 2016年起不用报税
	> UCCB 收入，牛奶金的一部分
	> 10 福利金总金额

1. 抚养收入。这个只限于是配偶间的，比如夫妻离婚后，孩子和母亲一起住，那么父亲给的抚养前妻的费用是前妻要报税的，而父亲给前妻抚养孩子的费用其前妻不需要报税。

1. 其他收入（现金，偶然性收入）比如帮朋友搬家或者接机拿的报酬。

1. 海外收入 Foreign income（如美国Form1040、W8、中国个人所得税纳税申报表和完税证明单等）

## 支出和抵扣类资料清单准备：
所有报税发票单据（Receipts），需准备发票单据，虽无需随报税表递交，但报税发票单据需保存完好，以便将来税局查税时可以方便提交。

1. professional due 各种专业职业协会会费，工会会费。如工程师会费，会计师会费等。

1. 专业考试费用

1. Child care expenses. 支付托儿费用的凭证/收据，托儿机构（或个人）的名字，SIN卡号
	> 如Daycare, fitness, summer...等，自己带孩子去上钢琴，画画课的不行。
	> 7岁以下孩子每年最多8000抵税，7岁以上是5000元。

1. 本人及家人支付医疗费的收据（药费、牙科、配眼镜等正式收据，此费用必须超过净所得3%的部分才可扣抵），包括购买医疗保险的收据

1. RRSP/Pension供款收据 contribution receipts。
	> 金融机构会出具或者邮寄给客户

1. 如果没房子，支付房租的收据

1. 如果有出租房屋，含投资房或自住房分租，需要收集所有费用支出的收据

	1. 房屋地址
	
	1. 地税Property tax。地税是每年都要定期交给政府的，属于当期费用（current cost） 可以用来抵减房租收入。投资房100%能报；如果你把自住房的部分空间分租给了别人，那么可以根据分租面积占整个住宅的比例，申请部分的房产税抵扣。
	
	1. 管理行政费用Management and administration fees including condo fees。物业管理费Maintenance（CONDO或townhouse）、出租中介费、出租公司管理费，都可以抵扣租金收入。如果请人帮您看房，例如请物业管理公司或地产经纪代替打理出租房，代替招揽租客的托管费可以抵。房东请全职工人管理出租房，付给工人的工资、CPP、EI、保险等费用都可以抵。但是，房东亲历亲为自己管理的则不能计算在内。投资房100%；自住房分租按比例，出租部分在35%-40%
		> 如果你的spouse or parents 没有income 或者income比较低，而他们在帮助你打理你的rental property 或帮你做bookkeeping, 你可以考虑给他们“发工资”。这样的话你可以claim Management and Admin expenses, 而你的spouse or parents 需要claim income, 从而以次达到income splitting的目的。
		> the amount of Management and Admin expenses 必须要是fair market value 并且reasonable。也就是说如果你去市场上雇用一个外人来做同样的工作，你会付多少钱。
	
	1. 房屋保险费Insurance expenses。这里要注意的是，如果您的保险是每年一付而保险生效期不是由1月1日开始的话，则只能减掉属于当前日历年的那部分保险费用；如果保险是买一次可以保两年的，注意只能抵当年的这部分保费，剩余的第二年再抵。为出租物业所购买的各项保险，基本都可获得税收抵扣，比如火灾保险、失窃保险、洪灾保险、业主险等。如果你还请他人协助打理租赁事务，雇工的医疗保险及补偿保险费用亦可从中扣除。投资房100%；自住房分租按比例
	
	1. 利息和银行费用 Interest & Bank Charges(Mortgage interest, LOC interest and Bank fees) 。用来购买或者装修物业的贷款利息支出可以用来抵扣收入。在申请贷款过程中所产生的相关费用，例如评估费，保险证明，中介费等，这部分费用可以分5年去扣减（每年20%）。有些出租物业会在归还租金保证金的时候加上利息，这部份的利息支出也可以用作抵扣收入。投资房100%；自住房分租按比例
	
	1. 土地转让税(Land Transfer)是一次性的，只在买房交接时才会有，因此归入购房成本（adjusted cost base），投资房才能报
	
	1. 法律及专业服务费用Legal, accounting, and other professional fees，因出租而发生的，支付给律师、会计及房地产管理公司、房产经纪及其他专业人士的费用，也可视做运营支出，一并在税收抵扣中进行减免。
	
	1. 物业维修保养费用 Maintenance and repairs，这里的费用包括人工与材料，但是业主自己的人工费用不能计算在内。具体来说，雇佣工人维修房屋可以用来抵税，例如：房东雇人修房子通渠换炉头换马桶而发生的人工费和材料费可以抵。但是要注意，如果是房东自己动手维修的话，那么只有材料费可以抵，自己的人工费不能计算在内。出租屋的维修过程中所产生的各项支出可以在费用发生的当年，全额进行税款抵扣。
	
	1. 工资及福利支出Salaries, wages, and benefits ，如果业主有聘请员工来管理或维修出租物业，工资支出可以用来抵扣收入，但是业主不能给自己开工资
	
	1. 广告费Advertisement & promotion expenses including welcome gift to tenants。为出租房屋做广告所产生的广告费用可以抵扣
	
	1. 办公费用Office expenses。如果你在家进行办公，处理房产租赁事务，这部分费用也可从应纳税款中抵扣减免。
	
	1. 水电气暖电话费Utilities if paid by landlord，如果租赁合同中写明由业主提供水，电，煤气，上网，电视，电话这些费用，这部分费用可以用来抵扣收入；自住房分租按比例
	
	1. 其他开支Other expenses 。其他与房屋出租相关的费用，如清洁费、园艺管理，剪草（house）、公寓物业管理费，以及家庭办公室费用都可以用来抵扣收入
	
	1. Capital assets depreciation expenses 
	
	1. 房屋的折旧 Capital Cost Allowance 也能用于抵扣租金收入，但是不建议使用，因为将来卖房时候还要算回来。
	
	1. 汽车费用Motor vehicle expenses，这部分的费用需要满足一定的条件才能用来抵扣收入，CRA对这个比较敏感，若非必要（譬如房子多，租期短，租客问题多，而且离自己家较远，没车就没法管理），否则就不要报这个费用。当业主只拥有一处出租物业的时候，以下三个条件必须全部满足：1）出租物业与业主居所在同一个区域；2）出租物业的维修和保养大部分或全部由业主负责；3）由因运输工具和材料而产生的汽车费用。当只拥有一处出租物业的情况下，因收租而产生的汽车费用不能进行抵扣收入。当业主拥有两处或以上并且处于至少两个不同的地点的物业的情况下，因收租，监管维修或管理租务所产生的汽车费用可以用来抵扣收入
	
	1. 差旅费Travel，因收租，监管维修或管理租务所产生的旅行费用（包括从家里出发到达出租物业所产生的旅费）可以用来抵扣收入，标准与汽车相关费用一样，但是住宿费不能用来抵扣。

1. 工作性质支出

1. 自雇人士或者有生意收入者，汇总整理所有收入及支出凭证（营业项目、地址、收支单据、收支损益表）
	> 自雇者的餐费、油费、礼品费
	> 在家办公支出的费用，home office费用
	> 行车里程记录及费用

1. 除底薪外有Commissions收入者，汇总整理所有要申报的费用收据

1. 离异者支付给配偶的赡养费凭据

	配偶的经济资助而发生的合法费用(如：律师费)，可以抵销个人的收入；
	子女经济资助而发生的合法费用(适合单亲父母的情况)可以抵税

1. 因为工作调动等原因形成的搬家费用
	> 40公里以上的搬家可以

1.  Support for a child, spouse or common-law partner (离婚人士给小孩和原配偶的赡养费)
	> 抚养孩子的不可以减税，抚养前妻，前夫的可以减税

1. 医药收据Medical-type receipts。本人及家人支付医疗费的收据，包括购买医疗保险的收据

1. 各类捐款（慈善捐款、政治捐款、教堂奉献）的收据（指有政府注册号码的正式收据）Charitable donation receipts

	> 2003年起，慈善捐款要求捐款收据一定要有税号(tax shelter number)
	> 捐款如果今年不想抵扣，可以先不报，单据留着，以后报没问题。

1. 投资支出和损失loss，例如保险箱花费收据Receipts for safety deposit box charges，利息开销Interest expenses

1. 投资借款利息(Refinance, HELOC)和其他服务费用。如会计师，律师费用等

1. 往年资本损失，比如去年股票亏损

1. 各类捐款donation（慈善捐款、政治捐款、教堂奉献等）的收据

1. 照顾父母（父母必须是有加拿大身份的才可以，在中国的不行）

1. 残障，老人支持费

1. 支付财产税的收据

1. T2202A - 学费和教育费用收据 tuition and education receipts T2202A。
	> 本人、配偶及子女在学院或大学里Full-time 或者 Part-time学习，学校会寄T2202A表用于申报学费，或是海外大学的TL11A。可以在学校网站上通过个人账户下载，或学校寄。学费可以往后推，但是在产生学费的那一年需要先报进去，存在那里。必须是正规注册的学校，纳税人所读的学校必须是经过加拿大人力资源部认可的，有T2202A表的才可以；纳税人所读的课程必须是高等教育。这里大部分的学院和大学提供的都属于高等教育课程。特别要强调的是高中不属于高等教育，所以学费不能作为报税用途；纳税人必须年满16岁；纳税人所交的学费大于100刀；
	> eligible tuition fee （当年所交可以抵扣税的学费）
	> number of part-time months(B) （多少个月兼职读书）
	> number of full-time months(C) （多少个月全职读书）
	> unused federal tuition and education amounts（剩余联邦学费和教育数额）
	> Ontario unused tuition and amount(剩余安省学费数额)

1. 学生贷款利息

3. 其他减税证明，如消防义工或救援工作人员

### retired
1. 交通月票（周票需连续四张才可以）2017年起取消

1. 小朋友艺术/体育类课程费用 2017年起取消

# FAQ
1. 几个常见报税知识

	business income 填T2125表，100%报税

	rental income 租金收入填T776表，100%报税；

	rental property sold 投资房出售，股票、基金有收入或loss，算capital gain，其中50%的利润，按本人当年税率缴税，填schedule 3

	借refinance或HELOC的钱去投资，利息100%抵税，直接抵T4，填T1 > Carry charges and interest expenses，要准备证据

	投资房A refinance出的钱买了投资方B，refinance的利息是抵A还是B呀？B, 因为是投资B的成本
	> 譬如150w的自住房，剩余贷款30w，最多能拿出120w的HELOC，如果用其中30w的HELOC当首付买第2套投资房，总价90w，贷款60w，则 30w HELOC + 60w mortgage 的利息都能用于第2套投资房抵税

	interest利息的50%，按本人当年税率缴税， 填schedule 4
	
1. 公司如果提供车辆，低息贷款，停车场等都可能算为Company Benefits，需要申报的；但公司提供的学费，如果学习与工作有关的话，不算利益，不必申报。

1. T5 银行利息，是joint account，上面写两个人的名字，比如400的利息，是每人报200呢，还是一个人报400，另外一人不报?
	税局的规定是，是按照各自存入的比率来申报，联名账户的钱来源于谁，就应该由谁来报税, 而不是随意指定一方。但是CRA查的不严，可以简单的50/50，或者为了减税由低收入者100%申报，收入高那个不报

1. 出租物业的损失能报税吗？

	有两种情况：

	Uncollectible rent : 出租了，租客跑了，没收到钱。可以用来抵扣个税，方法是填报T776表，准备租客欠租证据，譬如打官司律师费，给租客的通知信，法院受理证据等

	Rental losses ： 重新装新（需要提供HST的收据），利息成本高负现金流（需要提供贷款Summary）。

	空置损失：找新房客的空置期的养房开销可以。能不能抵扣的前提是看你是不是incur to earn rental income. 只要能证明空置期间你有主动积极的招租，比如你有邀请租房经纪人，有广告招租等。如果你空置是为了省税，譬如空置了半年没找到租客，是不合理的，很难证明是incur to earn rental income， 那就不能抵扣。
	> 如果全年真正在出租房子，没有故意空置，所有开支都有单据，包括uncollectible rent都有证据，那么rental loss可以抵扣家庭收入，填报T776即可
	> rental loss可以抵消其他收入. 但vacant land除外，比如你有一块地租给农民入不敷出产生rental loss，这种情况不能抵消其他收入

	另外。
	1. 对出租房来说，折旧造成的亏损部分不能抵税。即如果你的出租房收益为零，报税软件不会把折旧成本再去抵扣收入。
	1. 投资房买卖产生的利润或者损失才叫资本利得capital gain。如果有利润，只有一半算收入需要纳税。如果有损失，总额一半可以抵消其它capital gain产生的利润。租金收入或损失是个人其它收入，利润是百分百应税的，损失当然也是百分百抵个税

#References#
[Canadian income tax rates for individuals - current and previous years](https://www.canada.ca/en/revenue-agency/services/tax/individuals/frequently-asked-questions-individuals/canadian-income-tax-rates-individuals-current-previous-years.html)

[Tax packages for all years](https://www.canada.ca/en/revenue-agency/services/forms-publications/tax-packages-years.html)

[T1 form](https://www.canada.ca/content/dam/cra-arc/formspubs/pbg/5006-r/5006-r-18e.pdf)

[https://www.canada.ca/en/services/taxes/income-tax.html](https://www.canada.ca/en/services/taxes/income-tax.html)

[https://turbotax.intuit.ca/tips/who-can-benefit-from-the-home-buyers-tax-credit-5203](https://turbotax.intuit.ca/tips/who-can-benefit-from-the-home-buyers-tax-credit-5203)

[加拿大个人所得税计算方法专业解析](https://www.juwai.com/news/131471.htm)

[Tax Calculator - SimpleTax](https://simpletax.ca/calculator)

[9.2.4 Tax brackets and rates](https://www.canada.ca/en/financial-consumer-agency/services/financial-toolkit/taxes-quebec/taxes-quebec-2/5.html)