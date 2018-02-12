---
layout: post
title: nopCommerce tutorial - (3) develop a plugin
author: Andy Feng
---

# Introduction #
There are two approaches to customize nopCommerce to fit our needs. Plugin can be used to extend the functionality of nopCommerce. Theme can be used to define the look and feel of pages and controls.

nopCommerce has some built-in plugins. For example, payment methods (such as PayPal), tax providers, shipping method computation methods (such as UPS, USP, FedEx), widgets. There are many different plugins including free anc commercial ones in nopCommerce website. We can search plugins which suit our needs. 

![](/images/posts/20180211-nop-21.png)

Basically, we create a class implements Nop.Core.Plugins.IPlugin interface to build our own plugin. nopCommerce also provides a BasePlugin class which already implements some IPlugin methods and simplify our development work. 

# Create a simple plugin #
Here are major steps how to create our own plugin and add it to nopCommerce.

1. Create a new project > windows classic library. As convention, please select project-dir>Plugins as the location.
 
	![](/images/posts/20180211-nop-1.png)

1. specify output dir to ..\..\Presentation\Nop.Web\Plugins\MyPlugins\

	![](/images/posts/20180211-nop-3.png)

1. Add reference > nop.core

	![](/images/posts/20180211-nop-2.png)
	
1. Create a plugin class, implements IPlugin interface

	![](/images/posts/20180211-nop-7.png)	

1. Add a plugin.json file

		````
		{
		  "Group": "User Plugins",
		  "FriendlyName": "Andy's Plugins",
		  "SystemName": "MyPlugins",
		  "Version": "1.00",
		  "SupportedVersions": [ "4.00" ],
		  "Author": "nopCommerce team",
		  "DisplayOrder": 1,
		  "FileName": "MyPlugins.dll",
		  "Description": "This plugin just for demo pupose"
		}
		```

1. make sure plugin.json always copy to compiled output directory: 

	right click plugin.json > properties > copy to output directory > always newer
 
	![](/images/posts/20180211-nop-11.png)	

	or 

	open MyPlugins.csproj, specify we want to copy plugin.json to output directory

	change 

		```
		  <ItemGroup>
		    <None Include="plugin.json" />
		  </ItemGroup>
		```

	to 

		```
		<ItemGroup>
		  <Content Include="plugin.json">
		    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
		  </Content>
		</ItemGroup>
		```

	Here is the explanation ([https://msdn.microsoft.com/library/0c6xyb66(v=vs.100).aspx](https://msdn.microsoft.com/library/0c6xyb66(v=vs.100).aspx)): 

	**None** - the file is not included in the project output group and is not compiled in the build process. An example is a text file that contains documentation, such as a Readme file.

	**Content** - The file is not compiled, but is included in the Content output group. For example, this setting is the default value for an .htm or other kind of Web file.

	**Compile** - The file is compiled into the build output. This setting is used for code files.

1. Build the new plugin project, we will find the output dlls at: project-dir\Presentation\Nop.Web\Plugins\

	![](/images/posts/20180211-nop-5.png)	

1. Rebuild and start the web project

	![](/images/posts/20180211-nop-4.png)

1. login as admin > administration > configuration > plugins > local plugins, we will find the new plugin

	![](/images/posts/20180211-nop-6.png)	

Also, nopCommerce provides some specific plugin interfaces derived from IPlugin. We should implement them to develop plugins with appearance and advanced logic. 

- External authentication methods - IExternalAuthenticationMethod interface
- Widgets - IWidgetPlugin interface
- Rxchange rate providers - IExchangeRateProvider interface
- Fiscount rules - IDiscountRequirementRule interface
- Payment methods - IPaymentMethod interface
- Shipping method computation methods - IShippingRateComputationMethod interface
- Tax providers - ITaxProvider interface
- Other plugins - IMiscPlugin interface

# References #
[http://docs.nopcommerce.com/pages/viewpage.action?pageId=1442493](http://docs.nopcommerce.com/pages/viewpage.action?pageId=1442493)
