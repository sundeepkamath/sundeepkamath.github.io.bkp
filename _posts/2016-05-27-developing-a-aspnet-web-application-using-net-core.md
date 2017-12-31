---
layout: post
title:  "Developing a ASP.Net web application using .Net core"
date:   2016-05-27 16:03:00 +0530
categories: 
- .Net Core
- Linux
- ASP.Net
tags:
- .Net Core
- Linux
- Asp.Net Core
---

In my [earlier post](http://sundeepkamath.in/2016/05/27/developing-a-net-core-console-application-on-linux) we had seen how we can create a **console application** using the new **.Net core** libraries.
Moving further, we'll now see how we can use the same .Net core cross platform libraries to develop and run a basic Hello World **web application** on **Linux**(I'll be using **Ubuntu 14.04** in this example).

We had already installed **Visual Studio Code** and **.Net core libraries** in the earlier post.
We need to now install the following...  

* **Node.js** and **npm**  
    * Follow install instruction [here](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)  
* Install **Yeoman, bower, grunt** and **gulp** using...  
`sudo npm install -g yo bower grunt-cli gulp`
* Next install the **ASP.Net generator** using...  
`sudo npm install -g generator-aspnet`

With this we are almost ready to create the new web application.

Lets create a new folder for our web application.
{% highlight powershell %}
mkdir HelloWorldApp
cd HelloWorldApp
{% endhighlight %}

We'll now use **Yeoman**, to generate the basic structure of the web application.
{% highlight powershell %}
yo aspnet
{% endhighlight %}

Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ yo aspnet

     _-----_
    |       |    .--------------------------.
    |--(o)--|    |      Welcome to the      |
   `---------´   |  marvellous ASP.NET Core |
    ( _´U`_ )    |      1.0 generator!      |
    /___A___\    '--------------------------'
     |  ~  |     
   __'.___.'__   
 ´   `  |° ´ Y ` 

? What type of application do you want to create? (Use arrow keys)
❯ Empty Web Application 
  Console Application 
  Web Application 
  Web Application Basic [without Membership and Authorization] 
  Web API Application 
  Nancy ASP.NET Application 
  Class Library 
  Unit test project (xUnit.net) 
{% endhighlight %}
For the sake of our example here, we'll go ahead with the **Empty Web Application** option.
It will ask us to name this application. Provide some name for the web application and press enter.

Listing contents of this directory will show the following files...
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp/HelloWorldApp$ ls
Dockerfile  Program.cs  project.json  Properties  README.md  Startup.cs  web.config  wwwroot
{% endhighlight %}

Just like the earlier post example of console application, the **Program.cs** file has the main method which serves as the **entry point** for this web application.
*Contents of Program.cs*
{% highlight C# %}
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace HelloWorldApp
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseKestrel()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
{% endhighlight %}

The Startup class, which is part of **Startup.cs** class is invoked by this main method.
This class by default returns Hello World as part of the response.
*Contents of Startup.cs file.*
{% highlight C# %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;

namespace HelloWorldApp
{
    public class Startup
    {
        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public void ConfigureServices(IServiceCollection services)
        {
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app)
        {
            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("Hello World!");
            });
        }
    }
}
{% endhighlight %}

The **project.json** file is the **configuration file** for the web application, the contents of which are as follows..
{% highlight javascript %}
{
  "dependencies": {
    "Microsoft.NETCore.App": {
      "version": "1.0.0-rc2-3002702",
      "type": "platform"
    },
    "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0-rc2-final",
    "Microsoft.AspNetCore.Server.Kestrel": "1.0.0-rc2-final"
  },

  "tools": {
    "Microsoft.AspNetCore.Server.IISIntegration.Tools": {
      "version": "1.0.0-preview1-final",
      "imports": "portable-net45+win8+dnxcore50"
    }
  },

  "frameworks": {
    "netcoreapp1.0": {
      "imports": [
        "dotnet5.6",
        "dnxcore50",
        "portable-net45+win8"
      ]
    }
  },

  "buildOptions": {
    "emitEntryPoint": true,
    "preserveCompilationContext": true
  },

  "runtimeOptions": {
    "gcServer": true
  },

  "publishOptions": {
    "include": [
      "wwwroot",
      "web.config"
    ]
  },

  "scripts": {
    "postpublish": [ "dotnet publish-iis --publish-folder %publish:OutputPath% --framework %publish:FullTargetFramework%" ]
  },

  "tooling": {
    "defaultNamespace": "HelloWorldApp"
  }
}
{% endhighlight %}

Just like the console application covered in the earlier post, this file has all details related to the **.Net core libraries version** being used, the **Nuget package dependencies, tooling** used etc.
We need to use the **dotnet command line tools** to restore these packages.
{% highlight powershell %}
dotnet restore
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp/HelloWorldApp$ dotnet restore
log  : Restoring packages for /home/sundeep/Documents/dev/HelloWorldApp/HelloWorldApp/project.json...
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethostresolver/index.json
info :   CACHE https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethost/index.json
log  : Restoring packages for tool 'Microsoft.AspNetCore.Server.IISIntegration.Tools' in /home/sundeep/Documents/dev/HelloWorldApp/HelloWorldApp/project.json...
info : Committing restore...
log  : Lock file has not changed. Skipping lock file write. Path: /home/sundeep/Documents/dev/HelloWorldApp/HelloWorldApp/project.lock.json
log  : /home/sundeep/Documents/dev/HelloWorldApp/HelloWorldApp/project.json
log  : Restore completed in 1587ms.

NuGet Config files used:
    /home/sundeep/.nuget/NuGet/NuGet.Config

Feeds used:
    https://api.nuget.org/v3/index.json
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp/HelloWorldApp$ 
{% endhighlight %}
Next we can build  the web application using
{% highlight powershell %}
dotnet build
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp/HelloWorldApp$ dotnet build
Project HelloWorldApp (.NETCoreApp,Version=v1.0) will be compiled because expected outputs are missing
Compiling HelloWorldApp for .NETCoreApp,Version=v1.0

Compilation succeeded.
    0 Warning(s)
    0 Error(s)

Time elapsed 00:00:06.0706350
{% endhighlight %}
If this is successfull, we can now run the asp.net application using
{% highlight powershell %}
dotnet run
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp/HelloWorldApp$ dotnet run
Project HelloWorldApp (.NETCoreApp,Version=v1.0) was previously compiled. Skipping compilation.
Hosting environment: Production
Content root path: /home/sundeep/Documents/dev/HelloWorldApp/HelloWorldApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
{% endhighlight %}

This uses the cross platform **kestrel web server** to host the application.
You can now open a browser of your choice and enter the url `http://localhost:5000`
This will bring up Hello World on to the browser output screen.

While there are a lot of concepts and details behind the steps that we have done above, this was supposed to be a **quickstart** to just get you up and running asp.net web application on Linux.

You may check out the official documentation of ASP.Net core [here](https://docs.asp.net/en/latest/)

Hope this was useful!

