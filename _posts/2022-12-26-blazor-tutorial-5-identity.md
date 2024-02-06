---
layout: post
title: Blazor tutorial 5 - Identity
author: Andy Feng
---

# Introduction
ASP.NET Core Identity is a membership system. It allows us to create, read, update and delete users. It supports user registration, authentication, authorization, password recovery, two-factor authentication. It also supports external login providers like Microsoft, Facebook, Google and etc.

# Add identity 
right click blazor web project > add new scafforded item > identity

![](/images/posts/20230223-asp.net-identity-1.png)

![](/images/posts/20230223-asp.net-identity-2.png)

![](/images/posts/20230223-asp.net-identity-3.png)

Then identity scaffold was generated under Areas/Identity folder

![](/images/posts/20230223-asp.net-identity-4.png)

Add identity migration support:

Package Manager Console > enter command > generate tables

`Add-Migration IdentitySupport`

`Update-Database`

![](/images/posts/20230223-asp.net-identity-5.png)

# How to use Identity
Blazor server app provides `AuthenticationStateProvider` service that helps us to find-out user’s authentication state data from HttpContext.User.

This service is used by `CascadingAuthenticationState` and `AuthorizeView` component to get the authentication states.

In the code of building component UI, we should use the 2 encapsulated components instead of directly use `AuthenticationStateProvider ` because component isn't notified automatically if the underlying authentication state data changes.

## `AuthenticationStateProvider` service
The AuthenticationStateProvider service is a built-in service in the Blazor server app that helps you to obtain the authentication state data from HttpContext.User.

	@page "/"  
	@using Microsoft.AspNetCore.Components.Authorization  
	@inject AuthenticationStateProvider AuthenticationStateProvider  

	<p>
	    @userAuthenticated  
	</p>
	  
	@code{  
	    string userAuthenticated;  
	    protected override async Task OnInitializedAsync()  
	    {  
	        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();  
	        var user = authState.User;  
	        if (user.Identity.IsAuthenticated)  
	        {  
	            userAuthenticated = $"{ user.Identity.Name} is authenticated.";  
	        }  
	        else  
	        {  
	            userAuthenticated = "The user is NOT authenticated.";  
	        }  
	    }  
	}  

## `<AuthorizeView>` component
In Blazor we use AuthorizeView component to show or hide UI elements depending on whether the user is authorized to see it.

	<AuthorizeView>
	    <Authorized>
	        This content is displayed only if the user is Authorized
	    </Authorized>
	    <NotAuthorized>
	        This content is displayed if the user is Not Authorized
	    </NotAuthorized>
	</AuthorizeView>

This component supports both role-based and policy-based authorization. 

For role-based authorization, use the Roles parameter.

	<AuthorizeView Roles="administrator, manager">
	    <p>Displayed if the logged in user is in administrator or manager role</p>
	</AuthorizeView>

For policy-based authorization, use the Policy parameter:.

	<AuthorizeView Policy="admin-policy">
	    <p>Displayed if the logged in user staisfies admin-policy</p>
	</AuthorizeView>

## `<CascadingAuthenticationState>` component
it is used to wrap up this entire component. It can share the Authentication state within the complete application.

e.g.

	<CascadingAuthenticationState>
	    <Router AppAssembly="@typeof(Program).Assembly">
	        <Found Context="routeData">
	            <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
	        </Found>
	        <NotFound>
	            <LayoutView Layout="@typeof(MainLayout)">
	                <p>Sorry, there's nothing at this address.</p>
	            </LayoutView>
	        </NotFound>
	    </Router>
	</CascadingAuthenticationState>

## [Authorize] attribute and `<AuthorizeRouteView>` component
1. We use `[Authorize]` attribute to protect routable components (i.e components with @page directive). We reach these components via the router and authorization is performed while being routed to these components.

	> `AuthorizeView` component is used to protect parts of a page of child components
	> `[Authorize]` attribute is used for the complete routable components, not the part
	
		e.g. AccountPage.razor
		
		@page "/accountpage"
		@page "/accountpage/{actionparameter}/{idparameter:int}"
		@attribute [Authorize]
		@inherits AccountComponentBase
		
		...
		@code {
		    ...
		}

1. [Authorize] attribute also supports role-based and policy-based authorization. For role-based authorization, use the Roles parameter:

		@page "/"
		@attribute [Authorize(Roles = "administrator, manager")]
		
		<p>Only users in administrator or manager role are allowed access</p>

	For policy-based authorization, use the Policy parameter:
	
		@page "/"
		@attribute [Authorize(Policy = "admin-policy")]
		
		<p>Only users who satisfy admin-policy are allowed access</p>
		
1. When use [Authorize] attribute, we must also use `<AuthorizeRouteView>` component to replace <RouteView> component

	`<RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />`
	
	to 
	
	`<AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />`

## Customize unauthorized content
If access is not allowed, "Not authorized" is displayed by default. However, we can customize this unauthorized content.

![](/images/posts/20230223-asp.net-identity-6.jpg)

	<CascadingAuthenticationState>
	    <Router AppAssembly="@typeof(Program).Assembly">
	        <Found Context="routeData">
	            <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
	                <NotAuthorized>
	                    <p>Sorry, you're not authorized to reach this page.</p>
	                    <p>You may need to log in as a different user.</p>
	                </NotAuthorized>
	            </AuthorizeRouteView>
	        </Found>
	        <NotFound>
	            <LayoutView Layout="@typeof(MainLayout)">
	                <p>Sorry, there's nothing at this address.</p>
	            </LayoutView>
	        </NotFound>
	    </Router>
	</CascadingAuthenticationState>

# Get Authentication and authorization state data
Use Cascading AuthenticationState parameter to get the state data.

e.g.

	public class EditEmployeeBase : ComponentBase
	{
	    [CascadingParameter]
	    private Task<AuthenticationState> authenticationStateTask { get; set; }
	
	    [Inject]
	    public NavigationManager NavigationManager { get; set; }
	
	    protected async override Task OnInitializedAsync()
	    {
	        var authenticationState = await authenticationStateTask;
	
	        if (!authenticationState.User.Identity.IsAuthenticated)
	        {
	            string returnUrl = WebUtility.UrlEncode($"/editEmployee/{Id}");
	            NavigationManager.NavigateTo($"/identity/account/login?returnUrl={returnUrl}");
	        }
	
	        // rest of the code
	    }
	}

Check if authenticated user is in a specific role

	if (authenticationState.User.IsInRole("Administrator"))
	{
	    // Execute Admin logic
	}

Check if authenticated user satisfies a specific policy

	public class EditEmployeeBase : ComponentBase
	{
	    [CascadingParameter]
	    private Task<AuthenticationState> authenticationStateTask { get; set; }
	
	    [Inject]
	    private IAuthorizationService AuthorizationService { get; set; }
	
	    protected async override Task OnInitializedAsync()
	    {
	        var user = (await authenticationStateTask).User;
	
	        if ((await AuthorizationService.AuthorizeAsync(user, "admin-policy"))
	        .Succeeded)
	        {
	            // Execute code specific to admin-policy
	        }
	    }
	}

# Reference
[ASP.NET core identity setup in blazor application](https://www.pragimtech.com/blog/blazor/asp.net-core-identity-setup-in-blazor-application/)