---
layout: post
title: ASP.NET Core Tutorial (3) TagHelper
author: Andy Feng
---

# Introduction #
Tag Helper is server-side code to create and render HTML elements in Razor files. It is introduced in MVC 6. It is an alternative of HTML helper in MVC 5. 

There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more. We can also extend abstract TagHelper class to create customized TagHelper.

# Usage of TagHelper #

1. Used as server-side markup in Razor view e.gl 

		<bell-button></bell-button>

1. Used as server-side attribute for HTML element to change standard HTML elements.

	e.g. 

	Built-in TagHelper: `LabelTagHelper`

	in model:
		
		public class Movie
		{
		    public int ID { get; set; }
		    public string Title { get; set; }
		    public DateTime ReleaseDate { get; set; }
		    public string Genre { get; set; }
		    public decimal Price { get; set; }
		}
	
	in Razor view:

		<label asp-for="Movie.Title"></label>

	It generates the following HTML:

		<label for="Movie_Title">Title</label>

	Here, `asp-for` attribute is available by the For property in the LabelTagHelper.

# TagHelper vs HtmlHelper #
Please note that Htmlhelper is C# code mixed with HTML inside Razor views and sometimes hard to read and maintain. Whereas, TagHelper is written in C# entirely and can be rendered and inserted to Razor views. Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.

## sample 1 ##
- HtmlHelper

	Suppose we have a simple label html helper:

		@Html.Label("FirstName", "First Name:", new {@class="caption"})

	Here, `new {@class="caption"}` is an anonymous object used to represent attributes. Because `class` is a reserved keyword in C#, we have to use the `@` symbol to force C# to interpret "@class=" as a symbol (property name). Also, we lose intellisense. On the other hand, to a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign. 

- TagHelper equivalent

	using `LabelTagHelper`
	
		<label class="caption" asp-for="FirstName"></label>
	
	It generates 
	
		<label class="caption" for="FirstName">First Name</label>
	
	We can find TagHelpers looks like standard HTML and is used as a markup in Razor view.  It is friendly to front-end designer. 

## sample 2 ## 
- HtmlHelpers
	Suppose we have the Form portion of the `Views/Account/Register.cshtml` Razor view generated from the legacy ASP.NET 4.5.x MVC template included with Visual Studio 2015.

	![](/images/posts/20180512-taghelper-3.png)

	We can find most of the markup in the Register view is C#. 

- Tag Helpers equivalent:

	![](/images/posts/20180512-taghelper-4.png)

	The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.

Overall, TagHelper is server-side markup/attribute technique in contrast to Angular is client-side markup/attribute technique. Here, we suppose markup is dependent HTML tag and attribute is the attribute of a HTML tag.

# Develop a TagHelper - demo 1: Email TagHelper #
## Basic ##
We wanna create a tag helper of email and use it in razor view like: 

	<email>Support</email>

We hope the server use our email tag helper to convert that markup into the following:

	<a href="mailto:Support@contoso.com">Support@contoso.com</a>

Steps:

1. Create new TagHelper class inherits .NET built-in `agHelper` class or implements `ITagHelper` interface. Here, we name it as `EmailTagHelper`

		using Microsoft.AspNetCore.Razor.TagHelpers;
		using System.Threading.Tasks;
		
		namespace AuthoringTagHelpers.TagHelpers
		{
		    public class EmailTagHelper : TagHelper
		    {
		        public override void Process(TagHelperContext context, TagHelperOutput output)
		        {
		            output.TagName = "a";    // Replaces <email> with <a> tag
		        }
		    }
		}

1. Import TagHelper assembly to Razor view. To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the Views/_ViewImports.cshtml file for .NET core. `Views/_ViewImports.cshtml` file makes the Tag Helper available to all view files in the Views directory and sub-directories.

	![](/images/posts/20180512-taghelper-1.png)

		@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
		@addTagHelper *, AuthoringTagHelpers

	Another sample to import tag helpers:

	![](/images/posts/20180512-taghelper-2.png)

	Here, `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` loads fundamental TagHelpers assembly. `@addTagHelper *, AuthoringTagHelpers` loads the customized TagHelper assembly. We can also use fully qualified name (FQN) like:

		@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
		@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
	
	Here, wildcard syntax ("*") specifies that all Tag Helpers in the specified assembly (Microsoft.AspNetCore.Mvc.TagHelpers) will be available to every view file in the Views directory or sub-directory.

	Note that @removeTagHelper removes a Tag Helper that was previously added. 

	Also, we can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!"). e.g., Phone validation is disabled in the `<span>`. Here, the element and Tag Helper attributes are no longer displayed in a distinctive font.

		<!span asp-validation-for="Phone" class="text-danger"></!span>
	
1. Add the TagHelper to `Views/Home/Contact.cshtml` razor view

		@{
		    ViewData["Title"] = "Contact";
		}
		<h2>@ViewData["Title"].</h2>
		<h3>@ViewData["Message"]</h3>
		
		<address>
		    One Microsoft Way<br />
		    Redmond, WA 98052<br />
		    <abbr title="Phone">P:</abbr>
		    425.555.0100
		</address>
		
		<address>
		    <strong>Support:</strong><email>Support</email><br />
		    <strong>Marketing:</strong><email>Marketing</email>
		</address>

	Note `<email>Support</email>` portion

1. run the app and we will get

		<a>Support</a>
		<a>Marketing</a>

	Please note that the anchor tag missing href and not clickable. We'll fix it.

## Improve v1 ##
1. Update EmailTagHelper with the following

		[HtmlTargetElement("email", TagStructure = TagStructure.WithoutEndTag)] 
		public class EmailTagHelper : TagHelper
		{
		    private const string EmailDomain = "contoso.com";
		
		    // Can be passed via <email mail-to="..." />. 
		    // Pascal case gets translated into lower-kebab-case.
		    public string MailTo { get; set; }
		
		    public override void Process(TagHelperContext context, TagHelperOutput output)
		    {
		        output.TagName = "a";    // Replaces <email> with <a> tag
		
		        var address = MailTo + "@" + EmailDomain;
		        output.Attributes.SetAttribute("href", "mailto:" + address);
		        output.Content.SetContent(address);
		    }
		}

	Here, we specify TagStructure=TagStructure.WithoutEndTag to make the tag support self-closing

1. Update the markup in the `Views/Home/Contact.cshtml`:

		@{
		    ViewData["Title"] = "Contact Copy";
		}
		<h2>@ViewData["Title"].</h2>
		<h3>@ViewData["Message"]</h3>
		
		<address>
		    One Microsoft Way Copy Version <br />
		    Redmond, WA 98052-6399<br />
		    <abbr title="Phone">P:</abbr>
		    425.555.0100
		</address>
		
		<address>
		    <strong>Support:</strong><email mail-to="Support"></email><br />
		    <strong>Marketing:</strong><email mail-to="Marketing"></email>
		</address>

1. Run the app and it generates the correct links.

		<a href="Support@contoso.com">Support</a>
		<a href="Marketing@contoso.com">Marketing</a>

## Improve v2 ##
In this section, we'll write an asynchronous email helper.

	public class EmailTagHelper : TagHelper
	{
	    private const string EmailDomain = "contoso.com";
	    public override async Task ProcessAsync(TagHelperContext context, TagHelperOutput output)
	    {
	        output.TagName = "a";                                 // Replaces <email> with <a> tag
	        var content = await output.GetChildContentAsync();
	        var target = content.GetContent() + "@" + EmailDomain;
	        output.Attributes.SetAttribute("href", "mailto:" + target);
	        output.Content.SetContent(target);
	    }
	}

# Develop a TagHelper - demo 2: bold TagHelper #
1. Create a `BoldTagHelper` class

	using Microsoft.AspNetCore.Razor.TagHelpers;
	
	namespace AuthoringTagHelpers.TagHelpers
	{
	    [HtmlTargetElement("bold")]
		[HtmlTargetElement(Attributes = "bold")]
	    public class BoldTagHelper : TagHelper
	    {
	        public override void Process(TagHelperContext context, TagHelperOutput output)
	        {
	            output.Attributes.RemoveAll("bold");
	            output.PreContent.SetHtmlContent("<strong>");
	            output.PostContent.SetHtmlContent("</strong>");
	        }
	    }
	}

	Please note:

	- Here `[HtmlTargetElement(Attributes = "bold")]` specifies that any HTML element that contains an HTML attribute named "bold" will match, and the Process() override method in the class will run. 
	- Here `[HtmlTargetElement("bold")]` specified an HTML element named "bold" trigger Process() method.
	- Process method removes the "bold" attribute and surrounds the containing markup with <strong></strong>.

1. Import taghelper assembly to the view. see above

1. Add a `bold` attribute to `About.cshtml` razor view.

		@{
		    ViewData["Title"] = "About";
		}
		<h2>@ViewData["Title"].</h2>
		<h3>@ViewData["Message"]</h3>
		
		<p bold>I am bold</p>
		<bold>I am bold too.</bold>
		
# Develop a TagHelper - demo 3: Email TagHelper #
Support model

1. Create a model class
		
		using System;		
		namespace AuthoringTagHelpers.Models
		{
		    public class WebsiteContext
		    {
		        public Version Version { get; set; }
		        public int CopyrightYear { get; set; }
		        public bool Approved { get; set; }
		        public int TagsToShow { get; set; }
		    }
		}

1. Add TagHelper class `WebsiteInformationTagHelper`

		using System;
		using AuthoringTagHelpers.Models;
		using Microsoft.AspNetCore.Razor.TagHelpers;
		
		namespace AuthoringTagHelpers.TagHelpers
		{
			[HtmlTargetElement("Website-Information")]
		    public class WebsiteInformationTagHelper : TagHelper
		    {
		        public WebsiteContext Info { get; set; }
		
		      public override void Process(TagHelperContext context, TagHelperOutput output)
		      {
		         output.TagName = "section";
		         output.Content.SetHtmlContent(
		$@"<ul><li><strong>Version:</strong> {Info.Version}</li>
		<li><strong>Copyright Year:</strong> {Info.CopyrightYear}</li>
		<li><strong>Approved:</strong> {Info.Approved}</li>
		<li><strong>Number of tags to show:</strong> {Info.TagsToShow}</li></ul>");
		         output.TagMode = TagMode.StartTagAndEndTag;
		      }
		   }
		}

	Note:

	1. The TagHelper includes a property of WebsiteContext
	1. By convention, tag helper translates Pascal-cased C# class names (`WebsiteInformation`) and properties for tag helpers into lower kebab case (`website-information`). Here we specify the tag name is `Website-Information`
	
1. Add the tag helper to razor view

		@using AuthoringTagHelpers.Models
		@{
		    ViewData["Title"] = "About";
		}
		<h2>@ViewData["Title"].</h2>
		<h3>@ViewData["Message"]</h3>
		
		<p bold>Use this area to provide additional information.</p>
		
		<bold> Is this bold?</bold>
		
		<h3> web site info </h3>
		<website-information info="new WebsiteContext {
		                                    Version = new Version(1, 3),
		                                    CopyrightYear = 1638,
		                                    Approved = true,
		                                    TagsToShow = 131 }" />

	or
	
	update the controller:

		public IActionResult Index(bool approved = false)
		{
		    return View(new WebsiteContext
		    {
		        Approved = approved,
		        CopyrightYear = 2015,
		        Version = new Version(1, 3, 3, 7),
		        TagsToShow = 20
		    });
		}

	update the razor view:

		@using AuthoringTagHelpers.Models
		@model WebsiteContext
		
		@{
		    ViewData["Title"] = "Home Page";
		}
		
		<div>
		    <h3>Information about our website (outdated):</h3>
		    <website-information info=@Model />
		</div>

# Reference #
[https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-2.1#tag-helpers-compared-to-html-helpers](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-2.1#tag-helpers-compared-to-html-helpers)

[https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/authoring?view=aspnetcore-2.1](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/authoring?view=aspnetcore-2.1)