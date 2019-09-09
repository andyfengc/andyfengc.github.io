## Add login ##

add a new action method, modify with [Authorize]

Now, start application and try to access this method

![](/images/20160604-add-identity-10.png)

Modify Startup.cs

    public void Configuration(IAppBuilder app)
    {
		//configure the sign in cookie 
        app.UseCookieAuthentication(new CookieAuthenticationOptions()
        {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,  
                LoginPath = new PathString("/Account/Login"),  
                LogoutPath = new PathString("/Account/LogOff"),  
                ExpireTimeSpan = TimeSpan.FromMinutes(5.0),  
        });
    }

Here, we enable the application to use a cookie to store information for the signed in user and to use a cookie to temporarily store information about a user logging in with a third party login provider  

Add Login action method:

    public async Task<ActionResult> Login()
    {
        var db = new IdentityDbContext<User>("IdentityContext");
        var store = new UserStore<User>(db);
        var manager = new UserManager<User>(store);
        var signinManager = new SignInManager<User, string>(manager, HttpContext.GetOwinContext().Authentication);
        var email = "andy@gmail.com";
        var password = "Pass123";
        var result = await signinManager.PasswordSignInAsync(email, password, true, false);
        switch (result)
        {
            case SignInStatus.Success:
                return Content("You already signed in");
            case SignInStatus.Failure:
            case SignInStatus.LockedOut:
            default:
                return Content("sign in failed");
        }       
    }
