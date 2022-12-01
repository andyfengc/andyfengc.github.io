---
layout: post
title: Electric DIY tutorial - build outlet
author: Andy Feng
---

# Introduction
300年前, 轧制和挤压尚未诞生, 还是用锻造制备线坯,当年的测量工具也很粗糙, 在这情况下以拉制的次数作为线材粗细的标志。线坯为0号，每拉一次增加一号。因为有众多作坊, 其工艺又各不相同, 于是出现了众多的线号标准，当时也没有统一的度量标准。经过300年的淘汰, 兼并和变迁, 有的国家就改为直径计量,但英国和美国, 线号表示法沿用至今。现今常使用的是美国线规(AWG),伯明翰线规(BWG)和英帝国标准线规(SWG)。

# AWG
AWG（American wire gauge）美国线规，是一种区分导线直径的标准，又被称为 Brown&Sharpe 线规。这种标准化线规系统于1857年起在美国开始使用。AWG前面的数值（如24AWG、26AWG）表示导线形成最后直径前所要经过的孔的数量，数值越大，导线经过的孔就越多，导线的直径也就越小。粗导线具有更好的物理强度和更低的电阻，但是导线越粗，制作电缆需要的铜就越多，这会导致电缆更沉、更难以安装、价格也更贵。电缆设计的挑战在于使用尽可能小直径的导线（减小成本和安装复杂性），而同时保证在必要电压和频率之下实现导线的最大容量。

下图是AWG线规的孔的大小。

![](/images/posts/20220504-wire-1.jpg)

AWG线径对照表
> 其中，4/0表示0000，3/0表示000，2/0表示00，1/0表示0。例如，常用的电话线直径为26AWG，约为0.4mm

![](/images/posts/20220504-wire-2.png)

![](/images/posts/20220504-wire-3.png)

据说有一个函数可以算出

	AWG = -19.93156857*log(A) - 9.73724
	有2点说明:
	(1)上式的log是以10为底的对数，结果取整即为对应的AWG号数。
	(2)A为导线直径，的单位是英寸。
	如：2平方的导线，直径是1.596mm，也就是0.06284inch
	将这个0.06284带入上面的A处，得AWG=14.2156
	取整得，AWG=14。

下表是美国线规（AWG）与中国线规的对照表

![](/images/posts/20220504-wire-4.png)

AWG前面带个数字，代表的是线的粗细。不过我们并不能以AWG前面的数字算出直径，而是通过查表获得。
> 10AWG 600V 105℃，表示该线束***大承载电压600V，***大承受温度105摄氏度，10AWG表示该线束的直径是2.59mm。

我们是先确定要使用通过多大电流的线，再选择合适的AWG。
> 例如，假设我们要驱动一个电机，该电机的***大电流是3A，为保证在使用过程中电线不被烧断，我们需要使用正常可以通过6A的电线，那么通过查表，就可以得出，使用15AWG的线就可以，当然你也可以使用0~14AWG的线，不过，考虑到成本(电线按照米计算价格，线越粗越贵)，我们一般就选择15AWG的线了。

# Demo实例
电线尺寸通常使用American Wire Gauge (AWG)系统，Wire gauge 指电线的物理尺寸，通常指直径。电线gauge数字越小，电线直径越大。
> 常见电线尺寸包括 include 14-, 12-, 10-, 8-, 6-, and 2-gauge

电线尺寸也表明了支持多大电流

Amperage Capacities for Standard Non-Metallic (NM) Cable

![](/images/posts/20220504-wire-5.jpg)

![](/images/posts/20220504-wire-6.jpg)

白色，14 gauge，最大支持15amp；14/2最常见，14/3用在220v

黄色，12 gauge，最大支持20amp；12/2用在厨房，卫生间，冰箱

橘色，10 gauge，支持30amp；10/3用于热水器，空调，dryer等

黑色，8 gauge，支持45 amp，8/3用在stove；6 gauge支持60 amp，用于sub panel

# Manufacturer
Romex®是由Southwire制造的非金属（NM）建筑电线的品牌名称，被专业电工和DIY专家广泛使用。 Romex用于许多常见的家用电器项目，如插座和灯。

“NM”称号指电线的护套 一个30密耳厚的PVC护套 将单根电线绑在一起 。 NM不是指电线本身，导线是铜的，是金属。 NM也不是指导线周围的夹克; 这些夹克由颜色编码的PVC（聚氯乙烯）制成 - 黑色，白色和红色。

相反，NM属于




# References
[How to Install and Wire a Sub Panel](https://www.youtube.com/watch?v=XVqQ7feTPck)

[How To Wire A Main Electrical Panel - Start To Finish! NEATLY And VERY DETAILED](https://www.youtube.com/watch?v=hEDto-bnHKw)

[How To Add a New Circuit Breaker to a Main or Sub Panel](https://www.youtube.com/watch?v=zpIIYWhCFgo)