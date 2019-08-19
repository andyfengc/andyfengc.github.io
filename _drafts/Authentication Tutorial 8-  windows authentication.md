---
layout: post
title: ASP.NET Identity authentication 7 - windows authentication
author: Andy Feng
---

# typical windows form authorization #

	<system.web>
		<compilation debug="true" targetFramework="4.5.1" />
		<httpRuntime targetFramework="4.5.1" />
		<authentication mode="Forms">
			<forms loginUrl="~/Account/Login" timeout="2880">
				<credentials passwordFormat="Clear">
				<user name="user" password="secret"/>
				<user name="admin" password="secret" />
				</credentials>
			</forms>
		</authentication>
	</system.web>

here, use the loginUrl attribute to specify that unauthenticated requests should be redirected to the /Account/Login URL

	[HttpPost]
	public ActionResult Login(string username, string password, string returnUrl) {
		bool result = FormsAuthentication.Authenticate(username, password);
		if (result) {
			FormsAuthentication.SetAuthCookie(username, false);
			return Redirect(returnUrl ?? Url.Action("Index", "Admin"));
		} else {
			ModelState.AddModelError("", "Incorrect username or password");
			return View();
		}
	}

# References #