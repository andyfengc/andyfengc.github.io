---
layout: post
title: Domain application tutorial
author: Andy Feng
---

## Outline ##
0. Prepare github pages hosting using jekyll or hexo, add CNAME file

1. Apply a domain `andyfeng.ga` at freenom

2.1. Use freenom's DNS parsing service to add DNS parsing support to convert domain server(freenom) to hosting server(github)

	- use default name servers
	- add A record
	- add CNAME

2.2 Use godaddy's DNS parsing service

	- add an Off-site DNS in godaddy
	- add A record in godaddy
	- add CNAME in godaddy
	- login current domain provider(freenom), modify its name servers(ns) to godaddy's

3. Add email server under the new domain, email format `name@andyfeng.ga`

4. Add SSL support by cloudflare
	- login current domain provider, modify name servers(ns) to cloudflare's
	- we can also use cloudflase free dns service to add A record and CNAME

5. all solutions:

	- DNS Procedure 1 (simplest):
	user -> andyfeng.ga -> domain/name servers/a/cname/mx(freenom) -> github pages -> user
	
	- DNS Procedure 2:
	user -> andyfeng.ga -> domain(freenom) -> name servers/a/cname/mx(godaddy) -> github pages -> user
	
	- DNS Procedure 3(complexest):	
	user -> andyfeng.ga -> domain(freenom) -> name servers/a/cname/mx(cloudflare, ssl) -> github pages -> user
	
	- DNS Procedure 3(ssl simplest):
	user -> andyfeng.ga -> domain(freenom) -> name servers/a/cname/mx(cloudflare, ssl) -> github pages -> user

Note:
- domain在哪里，就在哪里配置name servers (ns)
- ns是谁的，就在谁那里修改添加A记录、CNAME，添加MX等
- domain和ns可以分开，也可以在一个服务器上。 e.g. 注册Godaddy的域名，其域名服务器在墙外，然后使用国内免费的DNSPOD配置A记录/CNAME/MX等域名解析，最后在Godaddy处自定义name servers（域名服务器）为DNSPOD的ns
- 技术架构：github pages + jekyll + godaddy + cloudflare + qq企业邮箱
- 编辑器：记事本或任何支持markdown的编辑器

## 0. Prepare hosting via github pages ##
`znhbjbond.github.io`

then add CNAME file in github page project, file content:
`
andyfeng.ga
`

## 1. Apply a free domain ##
Currently, we have following free top domain options.

> search "site:.tk" in baidu at August, 2017, counts as follows:
> 
> .TK, 28k
> 
> .ML, 381k
> 
> .GA, 356k
> 
> .GQ, 145k
> 
> .CF, 279k

Navigage to [Freenom](http://www.freenom.com/en/index.html?lang=en)

Enter desired domain name, click search

![](/images/posts/20170829-free-domain-1.png)
![](/images/posts/20170829-free-domain-2.png)

period can select maximum 12 month for free domain
![](/images/posts/20170829-free-domain-3.png)

Enter email address, click verify
![](/images/posts/20170829-free-domain-4.png)

Next, go to email inbox, click link to fill in registration form, set password
![](/images/posts/20170829-free-domain-5.png)
![](/images/posts/20170829-free-domain-6.png)
![](/images/posts/20170829-free-domain-7.png)

Register successfully
![](/images/posts/20170829-free-domain-8.png)

## Renew free domain ##
Login [Freenom](http://www.freenom.com/en/index.html?lang=en) via credentials

![](/images/posts/20170829-free-domain-9.png)

Services > Renew domains, click renew. Please be note that renewal is only available before 14 days of expiration date

![](/images/posts/20170829-free-domain-10.png)

## 1. Apply a commecial domain ##
compare prices at [Domain Name Price and Availability](https://www.domcomp.com/)

Note:
- 在GoDaddy上购买域名相比国内的域名服务商如万网/阿里云的好处是，不需要各种审核，而且价格便宜，还有优惠券能打折
- 不建议购买GoDaddy的虚拟主机服务，价格一般而且很容易被墙
- 域名第一年的价格很便宜，但第二年域名到期后还想继续用价格就升上去了，大部分域名服务商都是如此。
- GoDaddy支持支付宝、银联卡和VISA等各种卡支付
- godaddy 10 years domain: andyfengc.com, about rmb 595, $99
- 注册域名过户方便，用于域名交易等。
- 注册域名解析生效快，其他网站域名解析可能要一两天，Godaddy只要几分钟就行了。
 
## 2. DNS solution way1: use freedom free dns parsing  ##
1. login Freenom > Go to My Domains > Manage Domain > Management Tools > Nameservers > Use default nameservers

1.  My Domains > Manage Domain > Manage Freenom DNS. Here, we can enter a variety of different DNS records, including A, MX and CNAME records.

	![](/images/posts/20170830-free-domain-25.png)

1. add A records, points to

	192.30.252.153
	192.30.252.154

1. add CNAME, points to

	znhbjbond.github.io

	![](/images/posts/20170830-free-domain-26.png)

1. Note:

	- You can use the 'name' field blank if you want to assign a DNS record (like A record) to your entire domain
	- For MX records, you need to add a priority.	
	- It will take normally up to 30 minutes before name server changes are distributed within our DNS servers.

## 2. DNS solution way2: use godaddy free dns parsing ##
在DNS概念里，A记录用来将域名指向另一个服务器的ip地址；CNAME可以叫做alias，用来将域名指向另一个服务器进行做进一步解析。
在godaddy的服务商的配置面板里，添加一条规则可以选择对@生效或www生效，@指所有域名，e.g. *.domainname.com，www只是www.domainname.com

1. login goddy > manage domain

	![](/images/posts/20170830-free-domain-1.png)

1. dns > add dna hosting 

	![](/images/posts/20170830-free-domain-2.png)
	
	enter a domain name > next to add a domain
	
	![](/images/posts/20170830-free-domain-3.png)
	
	![](/images/posts/20170830-free-domain-4.png)
	
	Manage dns to view the new domain
	
	![](/images/posts/20170830-free-domain-5.png)

1. manage domain, add A record and CNAME

	in godday, update CNAME to your hosting name,  "host" = www and "points to" = znhbjbond.github.io

	![](/images/posts/20170830-free-domain-9.png)

	create two A record that point your custom domain to the following IP addresses, "host" = @:
	
	192.30.252.153
	192.30.252.154

1. login freenom. Then, we update our free domain of Freenom's name servers with Godday's name servers. It represents that everytime when user connects this domain, user use godaddy's name servers to parse dns.

	My Domains > Click on Manage Domain > Click on Management Tools > Click on Nameservers > Enter your customer nameservers (minimum 2, maximum 5)

	![](/images/posts/20170830-free-domain-6.png)

	![](/images/posts/20170830-free-domain-7.png)

	select user custom name servers, copy and paste godaddy's name servers.

	![](/images/posts/20170830-free-domain-8.png)

	It will take normally up to 30 minutes before name server changes are distributed within our DNS servers.

1. Save changes and patiently wait 5-30 minutes for your DNS settings to update

## 3. Add email server ##
Register a free enterprise email server such as qq mail. It requires a wechat account to register

![](/images/posts/20170830-free-domain-19.png)

Then, add a administrator account. Please be note that everytime to manage email server as administrator, we need original wechat account to login.

Go back to godaddy, add below mx servers:

![](/images/posts/20170830-free-domain-22.png)
> 记录值：mxbiz1.qq.com 优先级：5
> 
> 记录值：mxbiz2.qq.com 优先级：10

Then, check email server status

![](/images/posts/20170830-free-domain-23.png)

Create a new email account
![](/images/posts/20170830-free-domain-20.png)

Send an email to this new email account and it succeed to receive emails
![](/images/posts/20170830-free-domain-21.png)

## 4. Add SSL ##
Although GitHub Page supports https, but if we are using 3rd domain, we have to add 3rd SSL certificate. However, github pages only supports static html pages and doesn't support 3rd certificate. A solution is use the dynamic ssl certificate via Cloudflare.

Typically, a SSL certificate costs $400+ but Couldflare is free. 
register Cloudflare account at [https://www.cloudflare.com/](https://www.cloudflare.com/)

enter your existing domain > scan > wait 60s > continue

![](/images/posts/20170830-free-domain-10.png)

![](/images/posts/20170830-free-domain-11.png)

verify domain information > continue

![](/images/posts/20170830-free-domain-12.png)

select free plan > continue, 

![](/images/posts/20170830-free-domain-13.png)

![](/images/posts/20170830-free-domain-14.png)

Specify ssl mode as full

![](/images/posts/20170830-free-domain-24.png)

in the control panel > page rules > add a rule to force all http requests to https

![](/images/posts/20170830-free-domain-15.png)

![](/images/posts/20170830-free-domain-17.png)

please copy previous two name servers (ns). Then, go back to original domains server, update the name servers to cloudflare servers.

brenda.ns.cloudflare.com
todd.ns.cloudflare.com

![](/images/posts/20170830-free-domain-16.png)

Again, original domain server might spend 5-30 minutes to take effect.

So far, all configuration succeeded and whenever we navigate to http://andyfeng.ga or https://andyfeng.ga, we got ssl certificate.

![](/images/posts/20170830-free-domain-17.png)

## References ##

[Customizing GitHub Pages](https://help.github.com/articles/setting-up-an-apex-domain/)

Install dig

[ftp](ftp://ftp.nominum.com/pub/isc/bind9/)
[mirror](http://www.bind9.net/download)

![](http://andrewsturges.com/blog/jekyll/tutorial/2014/11/06/github-and-godaddy.html)

![](https://medium.com/@LovettLovett/github-pages-godaddy-f0318c2f25a)

[腾讯企业邮](http://service.exmail.qq.com/cgi-bin/help?subtype=1&id=10000&no=1001500)