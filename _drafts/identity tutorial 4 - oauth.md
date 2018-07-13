## add auth2 support ##

- create a mvc project, use individual authentication (form authentication)
- 
- apply google oauth key

	- login google developer console
	
	- select or create a new project
	![](/images/20160602-google-oauth-api-0.9.png)

	- select credentials > create a new oAuth client id
	![](/images/20160602-google-oauth-api-1.png)

	- fill authorized url, 
		- e.g. 
		- authorized url: http://localhost:8382
		- authorized redirect url: http://localhost:60184/signin-google (must be hostname/signin-google)
	![](/images/20160602-google-oauth-api-2.png)
	
	- get client id and client secret pair

	- select overview > social api > google+ api > click enable

	![](/images/20160602-google-oauth-api-3.1.png)

	![](/images/20160602-google-oauth-api-3.2.png)
	
- update startup.auth.cs, uncomment google api code with 

	![](/images/20160602-google-oauth-api-3.png)

- start mvc project, select signin > use another service to login
- 
- ![](/images/20160602-google-oauth-api-4.png) 

	enter google credentials, authorize its permission

**detailed steps in at**: [http://www.asp.net/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](http://www.asp.net/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

# External authentication

# References
[https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0)

[https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)