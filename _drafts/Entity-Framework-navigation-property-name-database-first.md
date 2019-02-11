---
layout: post
title: Improve navigation property names in Entity Framework database first
author: Andy Feng
---

# Problem #
We have tables:

Address:

	create table [dbo].[Address] (
	    [Id] [int] not null identity,
	    [City] [nvarchar](max) null,
	    [Address1] [nvarchar](max) null,
	    [Address2] [nvarchar](max) null,
	    [ZipPostalCode] [nvarchar](max) null,
	    [PhoneNumber] [nvarchar](max) null,
	    primary key ([Id])
	);

Customer:

	create table [dbo].[Customer] (
	    [Id] [int] not null identity,
	    [CustomerGuid] [uniqueidentifier] not null,
	    [Username] [nvarchar](1000) null,
	    [BillingAddress_Id] [int] null,
	    [ShippingAddress_Id] [int] null,
	    primary key ([Id])
	);

foreign keys:

	alter table dbo.Customer add constraint FK_Customer_ShippingAddress foreign key (ShippingAddress_Id) references dbo.Address(Id)
	alter table dbo.Customer add constraint FK_Customer_BillingAddress foreign key (BillingAddress_Id) references dbo.Address(Id)

After database first reversing we got entities:

    public partial class Address
    {
        public Address()
        {
            this.Customers = new HashSet<Customer>();
            this.Customers1 = new HashSet<Customer>();
        }
    
        public int Id { get; set; }
        public string City { get; set; }
        public string Address1 { get; set; }
        public string Address2 { get; set; }

        public virtual ICollection<Customer> Customers { get; set; }
        public virtual ICollection<Customer> Customers1 { get; set; }
    }

and

    public partial class Customer
    {        
		public Customer()
        {
        }
    
        public int Id { get; set; }
        public System.Guid CustomerGuid { get; set; }
        public string Username { get; set; }
        public Nullable<int> BillingAddress_Id { get; set; }
        public Nullable<int> ShippingAddress_Id { get; set; }

        public virtual Address Address { get; set; }
        public virtual Address Address1 { get; set; }
    }
}

Please note that the navigation properties is not readful.

# Solution 1, buggy #
## Modify T4 template ##
First, we need to make sure all constraints following the naming convention: `FK_TableName_RelationName`. 

Modify `[project-name]Entity.tt` T4 template file

1. there is a class inside the file

		... 
		public class CodeStringGenerator
		{
		    ...
		    public CodeStringGenerator(CodeGenerationTools code, TypeMapper typeMapper, MetadataTools ef)
		    {
		        ...
		    }
			// add this
			public string GetPropertyNameForNavigationProperty(NavigationProperty navigationProperty)
			{
			    var ForeignKeyName = navigationProperty.RelationshipType.Name.Split('_');
			    var propertyName = ForeignKeyName[ForeignKeyName.Length-1] + navigationProperty.ToEndMember.Name;
			    return propertyName;
			}
			// add this
			public string NavigationProperty(NavigationProperty navigationProperty, string name)
			{
			    var endType = _typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType());
			    return string.Format(
			        CultureInfo.InvariantCulture,
			        "{0} {1} {2} {{ {3}get; {4}set; }}",
			        AccessibilityAndVirtual(Accessibility.ForProperty(navigationProperty)),
			        navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many ? ("ICollection<" + endType + ">") : endType,
			        name,
			        _code.SpaceAfter(Accessibility.ForGetter(navigationProperty)),
			        _code.SpaceAfter(Accessibility.ForSetter(navigationProperty)));
			}
		... 

	Next, we need to Change 2 parts. 

1. part 1, find the place where the constructor part and the navigation property part are being build up of the entity. In the constructor part (around line 50) we need to replace the existing code by calling the method GetPropertyNameForNavigationProperty and passing this into the escape method.

	from:

		        foreach (var navigationProperty in collectionNavigationProperties)
		        {
		#>
		        this.<#=code.Escape(navigationProperty)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
		<#
		        }
		
	to
		
		        foreach (var navigationProperty in collectionNavigationProperties)
		        {
		        var propName = codeStringGenerator.GetPropertyNameForNavigationProperty(navigationProperty);
		#>
		        this.<#=code.Escape(propName)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
		<#
		        }

1. part 2. In the NavigationProperties part (around line 90) we also need to replace the code with the following.

	from:
	
		<#
		        foreach (var navigationProperty in navigationProperties)
		        {
		            if (navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many)
		            {
		#>
		    [System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
		<#
		            }
		#>
		    <#=codeStringGenerator.NavigationProperty(navigationProperty)#>
		<#
		        }
		    }
		#>

	to	
	
		<#
		        foreach (var navigationProperty in navigationProperties)
		        {
		            if (navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many)
		            {
		#>
		    [System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
		<#
		            }
		            var propName = codeStringGenerator.GetPropertyNameForNavigationProperty(navigationProperty);
		#>
		    <#=codeStringGenerator.NavigationProperty(navigationProperty, propName)#>
		<#
		        }
		    }
		#>

## Re-generate models from database ##
Update the edmx model from database and save. 

- Double click on edmx file under Solution.
- Right click on it.
- Select "Update Model from Database...".
- Click on the "Refresh" tab.
- Click on the Finish button.
- Your edmx now should be updated with the latest database changes.

we got

    public partial class Address
    {
        public Address()
        {
            this.BillingAddressCustomer = new HashSet<Customer>();
            this.ShippingAddressCustomer = new HashSet<Customer>();
        }
    
        public int Id { get; set; }
        public string City { get; set; }
        public string Address1 { get; set; }
        public string Address2 { get; set; }
    
        public virtual ICollection<Customer> BillingAddressCustomer { get; set; }
        public virtual ICollection<Customer> ShippingAddressCustomer { get; set; }
    }

and

    public partial class Customer
    {
        public Customer()
        {
        }
    
        public int Id { get; set; }
        public string Username { get; set; }
        public Nullable<int> BillingAddress_Id { get; set; }
        public Nullable<int> ShippingAddress_Id { get; set; }

        public virtual Address BillingAddressAddress { get; set; }
        public virtual Address ShippingAddressAddress { get; set; }
    }

that is more read friendly.

# Solution 2, buggy #
Please note we can always debug the `GetPropertyNameForNavigationProperty()` method and play a little with the naming of the property.

Modify `[project-name]Entity.tt` T4 template file

1. there is a class inside the file

		... 
		public class CodeStringGenerator
		{
		    ...
		    public CodeStringGenerator(CodeGenerationTools code, TypeMapper typeMapper, MetadataTools ef)
		    {
		        ...
		    }
			// add this
			public string GetPropertyNameForNavigationProperty(NavigationProperty navigationProperty, string entityname = "")
			{
			    var ForeignKeyName = navigationProperty.RelationshipType.Name.Split('_');
			    var propertyName = "";
			
			    if (ForeignKeyName[ForeignKeyName.Length-1] != entityname){
			        var prepender = (ForeignKeyName[ForeignKeyName.Length-1].EndsWith(entityname)) ? ReplaceLastOccurrence(ForeignKeyName[ForeignKeyName.Length-1], entityname, "") :  ForeignKeyName[ForeignKeyName.Length-1];
			        propertyName = prepender + navigationProperty.ToEndMember.Name;
			    }
			    else {
			        propertyName = navigationProperty.ToEndMember.Name;
			    }
			
			    return propertyName;
			}
			// add this
			public string NavigationProperty(NavigationProperty navigationProperty, string name)
			{
			    var endType = _typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType());
			
			    var truname = name;
			
			    if(navigationProperty.ToEndMember.RelationshipMultiplicity != RelationshipMultiplicity.Many){
					if(navigationProperty.FromEndMember.RelationshipMultiplicity== RelationshipMultiplicity.One &&
						(navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.One
						|| navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.ZeroOrOne))
					{}
			        else if(name.Split(endType.ToArray<char>()).Length > 1){
			            truname = ReplaceLastOccurrence(name, endType, "");
			        }
			    }
			
			    return string.Format(
			        CultureInfo.InvariantCulture,
			        "{0} {1} {2} {{ {3}get; {4}set; }}",
			        AccessibilityAndVirtual(Accessibility.ForProperty(navigationProperty)),
			        navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many ? ("ICollection<" + endType + ">") : endType,
			        truname,
			        _code.SpaceAfter(Accessibility.ForGetter(navigationProperty)),
			        _code.SpaceAfter(Accessibility.ForSetter(navigationProperty)));
			}
			public static string ReplaceLastOccurrence(string Source, string Find, string Replace)
			{
			        int place = Source.LastIndexOf(Find);
			
			        if(place == -1)
			           return Source;
			
			        string result = Source.Remove(place, Find.Length).Insert(place, Replace);
			        return result;
			}
			
		    public string Property(EdmProperty edmProperty)
		    {
		... 

1. part 1, find the place where the constructor part and the navigation property part are being build up of the entity. In the constructor part (around line 50) we need to replace the existing code .

	from:

		        foreach (var navigationProperty in collectionNavigationProperties)
		        {
		#>
		        this.<#=code.Escape(navigationProperty)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
		<#
		        }
		
	to
		
		        foreach (var navigationProperty in collectionNavigationProperties)
		        {
		        var propName = codeStringGenerator.GetPropertyNameForNavigationProperty(navigationProperty, entity.Name);
		#>
		        this.<#=code.Escape(propName)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
		<#
		        }

1. part 2. In the NavigationProperties part (around line 90) we also need to replace the code with the following.

	from:
	
		<#
		        foreach (var navigationProperty in navigationProperties)
		        {
		            if (navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many)
		            {
		#>
		    [System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
		<#
		            }
		#>
		    <#=codeStringGenerator.NavigationProperty(navigationProperty)#>
		<#
		        }
		    }
		#>

	to	
	
		<#
		        foreach (var navigationProperty in navigationProperties)
		        {
		            if (navigationProperty.ToEndMember.RelationshipMultiplicity == RelationshipMultiplicity.Many)
		            {
		#>
		    [System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
		<#
		            }
		            var propName = codeStringGenerator.GetPropertyNameForNavigationProperty(navigationProperty, entity.Name);
		#>
		    <#=codeStringGenerator.NavigationProperty(navigationProperty, propName)#>
		<#
		        }
		    }
		#>

will get

    public partial class Address
    {
        public Address()
        {
            this.BillingAddress = new HashSet<Customer>();
            this.ShippingAddress = new HashSet<Customer>();
        }
    
        public int Id { get; set; }
        public string City { get; set; }
        public string Address1 { get; set; }
        public string Address2 { get; set; }
    
        public virtual ICollection<Customer> BillingAddress { get; set; }
        public virtual ICollection<Customer> ShippingAddress { get; set; }
    }

and

    public partial class Customer
    {
        public Customer()
        {
        }
    
        public int Id { get; set; }
        public string Username { get; set; }
        public Nullable<int> BillingAddress_Id { get; set; }
        public Nullable<int> ShippingAddress_Id { get; set; }

        public virtual Address BillingAddressAddress { get; set; }
        public virtual Address ShippingAddressAddres { get; set; }
    }

# Solution 3 #
Although previous solution can generate meaningful navigation property, it has issue to access database.

Therefore, we don't take those solutions and just add some hints(intellisense) to the  navigation properties. 

modify model.tt

`#40`, from

	        foreach (var navigationProperty in collectionNavigationProperties)
	        {		
	#>
	        this.<#=code.Escape(navigationProperty)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
	<#
	        }


to

	        foreach (var navigationProperty in collectionNavigationProperties)
	        {		
	#>
			/// <summary>
			/// RelationshipName: <#=code.Escape(navigationProperty.RelationshipType.Name)#>
			/// </summary>
	        this.<#=code.Escape(navigationProperty)#> = new HashSet<<#=typeMapper.GetTypeName(navigationProperty.ToEndMember.GetEntityType())#>>();
	<#
	        }

`#90`, from 

	[System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
	<#
	            }
	#>
	    <#=codeStringGenerator.NavigationProperty(navigationProperty)#>
	<#
	        }
	    }

to 
	
	[System.Diagnostics.CodeAnalysis.SuppressMessage("Microsoft.Usage", "CA2227:CollectionPropertiesShouldBeReadOnly")]
	<#
	            }
	#>
	    /// <summary>
	    /// RelationshipName: <#=code.Escape(navigationProperty.RelationshipType.Name)#>
	    /// </summary>
	    <#=codeStringGenerator.NavigationProperty(navigationProperty)#>
	<#
	        }
	    }

## Result ##: 

In database we have FK constraint name like: 

![](/images/posts/20190203-ef-1.png)

when we start typing a property name, we get a tooltip like this:

![](/images/posts/20190203-ef-2.png)

## More improvements ##
1. Open edmx model in GUI
1. open Entity Types > entity name
1. right-click on the Navigation Property i.e. Address1 and select "Properties" you can then change the name of the navigation property name to whatever you like i.e. BillingAddress:

![](/images/posts/20190203-ef-4.png)


# References #

[Improve navigation property names when reverse engineering a database](https://stackoverflow.com/questions/12937193/improve-navigation-property-names-when-reverse-engineering-a-database)