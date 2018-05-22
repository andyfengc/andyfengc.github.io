## Add login ##

add a new action method, modify with [Authorize]

Now, start application and try to access this method

![](/images/20160604-add-identity-10.png)

Modify Startup.cs

    public void Configuration(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions()
        {
            AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie
        });
    }

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

## configure webapi ##

Install Microsoft.AspNet.WebApi.Owin library