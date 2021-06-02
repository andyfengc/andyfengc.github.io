---
layout: post
title: .NET c# Attribute
author: Andy Feng
---

# Introduction ##
`Attribute` is a reusable element can be applied to a variety of targets i.e. classes, structs, methods, constructors, and more. `Attribute` adds additional declarative information for a target.

Attributes are metadata; they are compiled into the assembly at compile-time and do not change during runtime. As such, any parameters you pass into an attribute must be constants; literals, constant variables, compiler defines, etc.

In C#, `attributes` are classes that inherit from the `Attribute` base class. Any class that inherits from Attribute can be used as a sort of "tag" on other pieces of code. For instance, there is an attribute called `ObsoleteAttribute`. This is used to signal that code is obsolete and shouldn't be used anymore. You can place this attribute on a class by using square brackets.

	[Obsolete]
	public class MyClass
	{	
	}

Moreover, When marking a class obsolete, it's a good idea to provide some information as to why it's obsolete, and/or what to use instead. Do this by passing a string parameter to the Obsolete attribute.

	[Obsolete("ThisClass is obsolete. Use ThisClass2 instead.")]
	public class ThisClass
	{	
	}

> The string is being passed as an argument to an ObsoleteAttribute constructor, just like `var attr = new ObsoleteAttribute("some string")`

Please note Attributes in the .NET base class library like `ObsoleteAttribute` trigger certain behaviors within the compiler. Any attribute you create acts only as metadata, and doesn't result in any code within the attribute class being executed. 

Two ways to use Attribute:

- At compiling time, used by compiler. e.g. [Obsolete]
- At runtime, we need to use reflection. e.g.

		TypeInfo typeInfo = typeof(MyClass).GetTypeInfo();
		var attrs = typeInfo.GetCustomAttributes(); // get a collection of 	Attribute objects
		foreach(var attr in attrs)
		    Console.WriteLine("Attribute on MyClass: " + attr.GetType().Name);

# Create custom attribute 
Steps:

1. The custom attribute class should be derived from `System.Attribute` class
1. The Attribute name should  suffixed by "Attribute".
1. Set the probable targets with the AttributeUsage attribute.

	> using the “AttributeUsage” and “AttributeTargets” you can restrict the attribute to a particular section like class , method , property etc. 
	
1. Implement the class constructor and write-accessible properties.

e.g.

	// the custom attribute can be assigned to a class and its members
	[AttributeUsage(
		AttributeTargets.Class |
		AttributeTargets.Constructor |
		AttributeTargets.Field |
		AttributeTargets.Method |
		AttributeTargets.Property,
		AllowMultiple = true)]
	public class GotchaAttribute : Attribute
	{
	    public GotchaAttribute(string param1, string param2) {       
	    }
	}

Then we can use as 

	[Gotcha(param1: "param1 value", param2: "param2 value")]
	public class SomeOtherClass
	{	
	}

> Each attribute must have at least one constructor. The positional parameters should be passed through the constructor. 

# Demo
Create attributes:

	[AttributeUsage(AttributeTargets.Class | AttributeTargets.Property)]
    public class HelpAttribute : Attribute
    {
        public HelpAttribute(string s)
        {
            this.Company = s;
        }
        public string Company { get; set; }
    }

    public class CheckAttribute : Attribute
    {
        private bool Val = false;
        public CheckAttribute(bool val)
        {
            Val = val;
        }
        public bool status
        {
            get { return Val; }
        }
    }

Create class:

	[Check(false)]
    [Help("HCL Technology from class")]
    public class HelpClass
    {
        public HelpClass(string name, string country)
        {
            this.EmpName = name;
            this.Country = country;
        }

        public string Details()
        {
            //string str = EmpName + "-" + Country;
            //return str;
            string str = null;
            Type type = this.GetType();
            CheckAttribute[] attrib = (CheckAttribute[])type.GetCustomAttributes(typeof(CheckAttribute), false);
            if (attrib[0].status == true)
            {
                str = EmpName + "-" + Country;
            }
            else
            {
                str = "Hi " + EmpName;
            }
            return str;
        }
        [Help("HCL Technology from property")]
        public string EmpName { get; set; }
        private string Country;
    }

Here we use `HelpAttribute` to decorate `HelpClass` and its property separately. 

    // attribute
    HelpClass help = new HelpClass("Vidya Vrat", "India");
    System.Console.WriteLine("Result:{0}", help.Details());

    // way1, get attribute from class
    MemberInfo info = typeof(HelpClass);
    var attributes = info.GetCustomAttributes(typeof(HelpAttribute), false);
    foreach (var attribute in attributes)
    {
        HelpAttribute a = (HelpAttribute)attribute;
        System.Console.WriteLine("Company: {0}", a.Company);
    }

    // way2, get attribute from class
    //MemberInfo info = typeof(HelpClass);
    //var attributes = info.GetCustomAttributes(true);
    //foreach (var attribute in attributes)
    //{
    //    HelpAttribute a = attribute as HelpAttribute;
    //    if (a != null) System.Console.WriteLine("Company: {0}", a.Company);
    //}

    // way1, get attribute from property
    foreach (var propertyInfo in help.GetType().GetProperties())
    {
        var attributes = propertyInfo.GetCustomAttributes(typeof(HelpAttribute), true);
        foreach (var attribute in attributes)
        {
            HelpAttribute a = (HelpAttribute)attribute;
            System.Console.WriteLine("Company: {0}", a.Company);
        }
    }

Result:

![](/images/posts/20201223-attribute-1.png)

# Reference #
[Attributes](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/attributes)

[Writing Custom Attributes](https://docs.microsoft.com/en-us/dotnet/standard/attributes/writing-custom-attributes)
