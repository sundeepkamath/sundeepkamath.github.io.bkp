---
layout: post
title: Authentication in ASP.Net MVC 5 using Identity Libraries - Part 1
categories:
- ASP.Net
- Web
tags:
- ASP.NET Identity
- ASP.NET MVC 5
- C#
- Cookie based authentication
---

In my [earlier post][1] I had referred to the post by **Ben Foster** on using ASP.Net Identity libraries to add authentication support.  

I'm planning to try the same using an ASP.Net MVC 5 web application.  
I'll be covering the same on my blog as a 3 part series, this being the first part.

The aim is to add **cookie based authentication** on the lines of ASP.Net **forms based authentication**.  
We'll achieve this using the **ASP.Net Identity** libraries.  

With this background, lets begin...  
  
- Create an empty ASP.Net MVC 5 project.  
  - Go to `File -> New -> Project` in Visual Studio. 
    (**Note:** I'm using Visual Studio 2015)  
  - Select the `ASP.Net Web Application` project
  - On the ASP.Net template dialog select the `Empty` template.
  - In the `add folders and core references` section select the `MVC` option.  
  
  <img src="/static/img/blogs/ASPNetProjTemplate.jpg" class="img-responsive" alt="MVC 5 Empty template"/>

- This will create the basic ASP.Net MVC 5 web project.  
- To perform **cookie based authentication** we need to  install the following **2 Nuget** packages
  - **Microsoft.Owin.Host.SystemWeb**
     - This package enables the OWIN middleware to **hook into the IIS request pipeline**.
     - Install this using  
       `Install-Package Microsoft.Owin.Host.SystemWeb`       
  - **Microsoft.Owin.Security.Cookies**  
     - This package **enables cookie based authentication**.  
     - Install this using  
       `Install-Package Microsoft.Owin.Security.Cookies`
       
- It's now time to initialize the **OWIN identity** components.  
- Add a `Startup.cs` class to the project.  
- Add the `Configuration` method which accepts the `IAppBuilder` instance as a parameter as follows...

{% highlight C# %}
using Microsoft.Owin;
using Microsoft.Owin.Security.Cookies;
using Owin;

namespace ASPNetMVC5Identity
{
    public class Startup  
    {
        public void Configuration(IAppBuilder app)
        {
            
        }
    }
}
{% endhighlight %}

- Add another class file `Startup.Auth.cs` under `App_Start` folder.
- Create a partial class `Startup` in this file.
- To add cookie based authentication we can now add...


{% highlight C# %}
using Microsoft.Owin;
using Microsoft.Owin.Security.Cookies;
using Owin;

namespace ASPNetMVC5Identity
{
    public partial class Startup
    {
        public void ConfigureAuth(IAppBuilder app)
        {
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = "ApplicationCookie",
                LoginPath = new PathString("/auth/login")
            });
        }
    }
}
{% endhighlight %}  

- Here the `UseCookieAuthentication` extension tells ASP.Net Identity framework to use cookie based authentication.  
  - **AuthenticationType**  
    This is a string value that identifies the cookie.  
    **Note:** If we install `Microsoft.AspNet.Identity` Nuget package we can use the constant `DefaultAuthenticationTypes.ApplicationCookie` which has the same value as `ApplicationCookie`.
  - **LoginPath**  
    This is set to the path to which the browser should be redirected, when your application returns an unauthorized response (HTTP 401).  
    In our case we have an `AuthController` with a `Login` action.

- In the `Startup` class in the root folder place a call to the `ConfigureAuth` method as follows...  

{% highlight C# %}
namespace ASPNetMVC5Identity
{
    //To initialize the OWIN identity components we need to add a Startup class to the project.
    //This class should have a method Configuration that takes an OWIN IAppBuilder instance as a parameter. 
    public partial class Startup
    {
        //This class will be automatically located and initialized by the OWIN host
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
{% endhighlight %}  

- Before we add the authentication logic let us first create a basic resource for which we plan to have an authorized access.
- Lets add the following controller

{% highlight C# %}
public class HomeController : Controller
{
    // GET: Home
    public ActionResult Index()
    {
        return View();
    }
}
{% endhighlight %}  

- and the corresponding view as...


{% highlight C# %}
@{
    ViewBag.Title = "Index";
}

<h2>This my HomeControllers Index view page!!</h2>  
<h4>You need to be logged in to view this page.</h4>
{% endhighlight %}  


- The `RouteConfig` class in RouteConfig.cs file under `App_Start` folder has the following default route...

{% highlight C# %} 
public class RouteConfig
{
    public static void RegisterRoutes(RouteCollection routes)
    {
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
        );
    }
}
{% endhighlight %}  


- Hence if we now run the web application it will display this HomeController's index page as follows...  

<img src="/static/img/blogs/HomeController.jpg" class="img-responsive" alt="HomeController's index page"/>  

  

- We'll now create the `AuthController`.  
- This will have the `Login` action to which the user would be re-directed when they need to log in.  

{% highlight C# %}  
public class AuthController : Controller
{
    // GET: Auth
    public ActionResult Login()
    {
        return View();
    }
}
{% endhighlight %}  


- and the corresponding view as...

{% highlight C# %} 
@{
    ViewBag.Title = "Log In";
}

<h2>Log In</h2>
{% endhighlight %}   


- Now lets decorate the `HomeController` with the `[Authorize]` attribute as we want only authorized users to be able to access it.

{% highlight C# %} 
[Authorize]
public class HomeController : Controller
{
    // GET: Home
    public ActionResult Index()
    {
        return View();
    }
}
{% endhighlight %}   


- Now run the application.
  You'll observe that when the Default route is requested which is `http://localhost:51910/` instead of opening up the `Index` view of the `HomeController` it gets redirected to `Login` action of the `AuthController`.  
- The redirected url now shows up as...
  `http://localhost:51910/auth/login?ReturnUrl=%2F`
  
- Also, the `Login` view of the `AuthController` shows up in the browser.  
<img src="/static/img/blogs/AuthController.jpg" class="img-responsive" alt="AuthController's Login page"/>  
  
  

Next we'll cover the actual log in logic in [Part 2][2] of this series.

[1]: {{site.url}}/setting-up-authentication-in-asp-net-mvc-5-using-identity-libraries.html
[2]: {{site.url}}/authentication-in-asp-net-mvc-5-using-identity-libraries-part-2.html