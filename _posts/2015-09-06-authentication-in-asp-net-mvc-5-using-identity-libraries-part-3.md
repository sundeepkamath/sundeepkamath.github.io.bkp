---
layout: post
title: Authentication in ASP.Net MVC 5 using Identity Libraries – Part 3
categories:
- ASP.Net
- Entity Framework
- SQL Server
- Web
tags:
- ASP.NET Identity
- ASP.NET MVC 5
- C#
- Cookie based authentication
---

My [earlier post][1] listed the steps to set up cookie based authentication in **ASP.Net MVC 5** project using **Identity** libraries.  
There was however, **hardcoded** username and password used for the authentication logic.  
I will replace the same with the new **membership** features in **ASP.Net Identity**, by validating the credentials against information stored in the **SQL database**.  

- Install a new **Nuget** package
  In order to store the user information in database we need to install another nuget package.  
  `Microsoft.AspNet.Identity.EntityFramework`  
  This can be installed from the Package explorer using  
  `Install-Package Microsoft.AspNet.Identity.EntityFramework`  
  
- This library uses **Entity Framework** to persist user data to **SQL Server**. 
  Update the connection string in the `web.config` file accordingly.  
  I'm using the **localdb** database and hence my `web.config` file has the following settings...  
  
{% highlight xml %}
<connectionStrings>
  <add name="DefaultConnection" 
       connectionString="Data Source=(LocalDb)\mssqllocaldb;AttachDbFilename=|DataDirectory|\ASPNetMVC5Identity.mdf;Initial Catalog=ASPNetMVC5Identity;Integrated Security=True" 
       providerName="System.Data.SqlClient" />
</connectionStrings>
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="mssqllocaldb" />
    </parameters>
  </defaultConnectionFactory>
  <providers>
    <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
  </providers>
</entityFramework>
{% endhighlight %}  
   
- Next we need to create a class to represent our user.  
  ASP.Net provides a class `IdentityUser` which is a default implementation for the `IUser` interface.  
  We can subclass the `IdentityUser` class and add any additional properties that we plan to have for the user.  
  For this, I have created a new class file `IdentityConfig.cs` under `App_Start` folder.  
  Add the following code to this class. I have added an additional property `Country` to the user class.  
  
{% highlight C# %}
public class AppUser : IdentityUser
{
  public string Country { get; set; }
}
{% endhighlight %}

- ASP.Net provides an inbuilt `IdentityDbContext<TUser>` to interface with Entity Framework.  
  However, it is recommended that you create your own Entity Framework `DbContext`.  
  To do this, create a new `IdentityModel.cs` class file under the `Models` folder.  
  Add the following code to it...
  
{% highlight C# %}
public class AppDbContext : IdentityDbContext<AppUser>
{
  public AppDbContext()
      : base("DefaultConnection")
  {

  }

  public static AppDbContext Create()
  {
      return new AppDbContext();
  }
}
{% endhighlight %}

**Note:**  
The `Create` method has been added as an alternative to **Dependency Injection(DI)**.  
If you are using **DI** libraries like **Ninject**, you may handle these instantiation in an appropriate manner.    

- The ASP.NET Identity `UserManager` class is used to manage users.  
  Example:  
  Registering new users, validating credentials and loading user information.  
  It is not concerned with how user information is stored.  
  For this it relies on a `UserStore` (which in our case uses Entity Framework).  
  There are also implementations available for **Azure Table Storage, RavenDB** and **MongoDB** to name a few.  

- We'll add our own `UserManager` class by subclassing the `UserManager<TUser>` as follows...
  
{% highlight C# %}
public class AppUserManager : UserManager<AppUser>
{
    public AppUserManager(IUserStore<AppUser> store)
        : base(store)
    {
    }

    public static AppUserManager Create(IdentityFactoryOptions<AppUserManager> options, IOwinContext context)
    {
        var userManager = new AppUserManager(new UserStore<AppUser>(context.Get<AppDbContext>()));
        // Configure validation logic for usernames
        userManager.UserValidator = new UserValidator<AppUser>(userManager)
        {
            AllowOnlyAlphanumericUserNames = false,
            RequireUniqueEmail = true
        };

        // Configure validation logic for passwords
        userManager.PasswordValidator = new PasswordValidator
        {
            RequiredLength = 6,
            //RequireNonLetterOrDigit = true,
            //RequireDigit = true,
            //RequireLowercase = true,
            //RequireUppercase = true,
        };

        // Configure user lockout defaults
        userManager.UserLockoutEnabledByDefault = true;
        userManager.DefaultAccountLockoutTimeSpan = TimeSpan.FromMinutes(5);
        userManager.MaxFailedAccessAttemptsBeforeLockout = 5;

        
        return userManager;
    }
    
}
{% endhighlight %}

- We'll now make the `UserManager<AppUser>` instance accessible from `AuthController`.
  To do this add the following to the `AuthController` class...
  
{% highlight C# %}
public class AuthController : Controller
{
    private  AppUserManager _userManager;

    public AuthController()
    {

    }
    public AuthController(AppUserManager userManager)
    {
        UserManager = userManager;
    }

    public AppUserManager UserManager
    {
        get
        {
            return _userManager ?? HttpContext.GetOwinContext().GetUserManager<AppUserManager>();
        }
        private set
        {
            _userManager = value;
        }
     }
 // Rest of the code    
}
{% endhighlight %}
  
- We also want to make sure that we **dispose** the underlying Entity Framework `DbContext` at the end of the request.  
  To do this we override the `Dispose` method in the `AuthController` as...
  
{% highlight C# %}
protected override void Dispose(bool disposing)
{
    if (disposing && UserManager != null)
    {
        UserManager.Dispose();
    }
    base.Dispose(disposing);
}
{% endhighlight %}

- We can now replace the **hardcoded** authentication logic in the `Login` action of  the `AuthController` as follows...   

{% highlight C# %}
[HttpPost]
public async Task<ActionResult> Login(LoginModel model)
{
    if (!ModelState.IsValid)
    {
        return View();
    }

    var user = await UserManager.FindAsync(model.Email, model.Password);

    if (user != null)
    {
        await SignIn(user);

        return Redirect(GetRedirectUrl(model.ReturnUrl));
    }

    // In case user authentication fails.
    ModelState.AddModelError("", "Invalid email or password");
    return View();
}

private async Task SignIn(AppUser user)
{
    var identity = await UserManager.CreateIdentityAsync(
                                        user, DefaultAuthenticationTypes.ApplicationCookie);

    GetAuthenticationManager().SignIn(identity);
}

private IAuthenticationManager GetAuthenticationManager()
{
    var ctx = Request.GetOwinContext();
    var authManager = ctx.Authentication;

    return authManager;
}

private string GetRedirectUrl(string returnUrl)
{
    if (string.IsNullOrEmpty(returnUrl) || !Url.IsLocalUrl(returnUrl))
    {
        return Url.Action("index", "home");
    }

    return returnUrl;
}

{% endhighlight %}  


We try to achieve the following here...  
1. First we attempt to find a user with the provided credentials using `UserManager.FindAsync`.
2. If the user exists we create a **claims identity** for the user that can be passed to `AuthenticationManager`. This will include any custom claims that you've stored.
3. Finally we sign in the user using the cookie authentication middleware `SignIn(identity)`.  


- With this the logic to login the user is complete.  
  We now need a way to **register** the user.  
  We'll first create a view model to register the user, say `RegisterModel`.

{% highlight C# %}
public class RegisterModel
{
    [Required]
    [DataType(DataType.EmailAddress)]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Required]
    public string Country { get; set; }
}
{% endhighlight %}

- We can now add Register actions to the `AuthController` as...  

{% highlight C# %}
public ActionResult Register()
{
    return View();
}

[HttpPost]
public async Task<ActionResult> Register(RegisterModel model)
{
    if (!ModelState.IsValid)
    {
        return View();
    }

    var user = new AppUser
    {
        UserName = model.Email,
        Email = model.Email,
        Country = model.Country
    };

    var result = await UserManager.CreateAsync(user, model.Password);

    if (result.Succeeded)
    {
        await SignIn(user);
        return RedirectToAction("index", "home");
    }

    foreach (var error in result.Errors)
    {
        ModelState.AddModelError("", error);
    }

    return View();
}
{% endhighlight %}  


- To create the user we call `UserManager.CreateAsync` passing our `AppUser` instance and the user password.  
  The ASP.NET Identity library will take care of **hashing** and **storing** this securely.  

- Finally, we create a Register view as...  

{% highlight C# %}
@model ASPNetMVC5Identity.Models.RegisterModel
@{
    ViewBag.Title = "Register";
}

<h2>Register</h2>

@Html.ValidationSummary(false)

@using (Html.BeginForm())
{
    @Html.EditorForModel()
    <p>
        <button type="submit">Register</button>
    </p>
}
{% endhighlight %}

- With this we can now run the application and verify the end-to-end flow.  
- For handling the claims related information we can use our own `ClaimsIdentityFactory` as follows...  
  This can be added in the `IdentityConfig.cs` file.  
  
{% highlight C# %}
public class AppUserClaimsIdentityFactory : ClaimsIdentityFactory<AppUser>
{

    public override async Task<ClaimsIdentity> CreateAsync(UserManager<AppUser, string> manager, AppUser user, string authenticationType)
    {
        var identity = await base.CreateAsync(manager, user, authenticationType);
        identity.AddClaim(new Claim(ClaimTypes.Country, user.Country));

        return identity;
    }
}
{% endhighlight %}

- Now we need to add a reference to an instance of the above `ClaimsIdentityFactory` in our `UserManager` class as follows...

{% highlight C# %}
public class AppUserManager : UserManager<AppUser>
{
    public AppUserManager(IUserStore<AppUser> store)
        : base(store)
    {
    }

    public static AppUserManager Create(IdentityFactoryOptions<AppUserManager> options, IOwinContext context)
    {
        //Earlier Code

        userManager.ClaimsIdentityFactory = new AppUserClaimsIdentityFactory();
        
        return userManager;
    }
}
{% endhighlight %}

With this we come to the end of this 3 part series.  
Hope this benefits everyone.  

[1]: {{site.url}}/authentication-in-asp-net-mvc-5-using-identity-libraries-part-2.html