---
layout: post
title: Using display and editor templates to customize views for model in ASP.NET MVC
author: Andy Feng
categories: [C#, Web, ASP.NET, MVC, Razor]
---

## Introduction ##

In HTML helper, such as LabelFor(), TextBoxFor(), DropdownFor(), then display a model property in a fixed manner. For example, LabelFor() renders a model property name in a <label> tag and TextBoxFor() renders a textbox in which a model property is shown for editing. customization is not possible with the these HTML helpers.

When creating views, you can use DisplayFor(), DisplayForModel(), EditorFor(), and EditorForModel() templated helpers to create customized user interface. 

- DisplayFor()
- DisplayForModel()
- EditorFor()
- EditorForModel()

DisplayFor() helper displays a model property using a Display Template. A display template is simply a user interface template that is used to display a model property. If no custom display template is provided by developers, a default one is used.

DisplayForModel() helper is similar to DisplayFor() but displays the whole model (not just a single property) using a display template. 

EditorFor() helper displays a user interface for editing a model property. This user interface is called Editor Template. 

EditorForModel() helper displays the whole model for editing using a given editor template. All the helpers listed above pick a template based on the data type of a model property.

## Steps to create templated helpers, part1 - render a property ##

1. create DisplayTemplates or EditorTemplates folder inside Shared folder or Controller's folder

1. add partial views to in the folder and name it based on the name of related helper. By default, azor view engine will use the same name of template as the name of property for looking up template. We can also use different name. But need to to specify [UIHint("different template name")] for property to change template
	
		public class{
			[UIHint("AnotherTemplate")]
		    public DateTime BirthDate { get; set; }
		}

we create Shared/EditorForTemplates/AnotherTemplate.cshtml

1. code the view

e.g. we want to customize a DateTime textbox

	1. create a DateTime.cshtml file inside Shared/EditorForTemplates
	1. create the template:
		@model DateTime
		@Html.TextBox("", Model.HasValue ? Model.Value.ToString("dd/MM/yyyy") : string.Empty, new{@class="datePicker", @readonly="rendonly"})	
	1. create the view to invoke the template
		<script>
		     $(function () {
		         $(".datePicker").datepicker({
		            showOn: "button",
		            buttonImage: "/images/calendar-icon.png",
		            buttonImageOnly: true,
		            buttonText: "Select date"
		         });
		     });
		</script>

once the templates are created, all the model properties of DateTime type (because it is inside Shared folder) will start using the custom display template. 

e.g. customize another DateTime template

	@model DateTime
	 
	@{
	    List<SelectListItem> days = new List<SelectListItem>();
	    for (int i = 1; i <= 31;i++ )
	    {
	        days.Add(new SelectListItem() { Text = i.ToString(), Value = i.ToString(), Selected = (i == Model.Day ? true : false) });
	    }
	 
	    List<SelectListItem> months = new List<SelectListItem>();
	    for (int i = 1; i <= 12; i++)
	    {
	        months.Add(new SelectListItem() { Text = i.ToString(), Value = i.ToString(), Selected = (i == Model.Month ? true : false) });
	    }
	 
	    List<SelectListItem> years = new List<SelectListItem>();
	 
	    int prevYearCount = ViewBag.PreviousYearCount;
	    int nextYearCount = ViewBag.NextYearCount;
	 
	    for (int i = Model.Year - prevYearCount; i <= Model.Year + nextYearCount; i++)
	    {
	        years.Add(new SelectListItem() { Text = i.ToString(), Value = i.ToString(), Selected = (i == Model.Year ? true : false) });
	    }
	} 
	@Html.DropDownList("days", days)
	@Html.DropDownList("months", months)
	@Html.DropDownList("years", years)

it is like

![](/images/20151123-customize-datetime.png)

## Steps to create templated helpers, part2 - render a model ##

The DisplayFor() and EditorFor() helpers work for a single model property. If we need to render a user interface for the whole model. e.g. we have a model property that is of a complex type, such as Address. We can use the DisplayForModel() and EditorForModel() helpers. These helpers pick display and editor templates based on the model type. If no template is found, they use default template to render each property one by one inside the model.

e.g. render an address

1. create class

		public class Person
		{
		    public string Name { get; set; }
		    public List<Address> Addresses { get; set; }
		}
	
		// complex type	
		public class Address
		{
		    public string Street { get; set; }
		    public string PostalCode { get; set; }
		    public string City { get; set; }
		    public string Country { get; set; }
		}

2. create controller

		public class PersonController : Controller
		{
		    public ActionResult Index()
		    {
				Person person = new Person();
				// assign value to person
		        return View(person);
		    }
		}

1. create template for display address

		@model Models.Address
		//razor view to set the layout of display properties of address object
		<ul>
			<li>@Model.Street</li>
			<li>@Model.PostalCode</li>
			<li>@Model.City</li>
			<li>@Model.Country</li>
		</ul>

1. create view to call template to render		

		@model Models.Person
		@{
		    ViewBag.Title = "Person information";
		}
		<h2>Person</h2>
		<p>
		    @Html.LabelFor(model => model.Name)
		    @Html.EditorFor(model => model.Name)
		</p>		 
		<h2>Person Addresses</h2>
		// razor will call the template to render this object
		@Html.EditorFor(model => model.Addresses) 