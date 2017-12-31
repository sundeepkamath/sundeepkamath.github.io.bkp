---
layout: post
title:  "Developing a .Net core console application on Linux"
date:   2016-05-27 09:11:00 +0530
categories: 
- .Net Core
- Linux
tags:
- .Net Core
- Linux
- C#
- Ubuntu
---

The latest version of .Net also known as **.Net core** is now **cross platform** with support for **Linux** and **Mac OSX** in addition to **Windows**. I've been planning to try this out from some time now, and with the latest version of **.Net core RC2** out I decided to give this a try.

I'll be covering the steps to develop and run a simple **.Net core console application** that prints a simple Hello World on the screen.

I tried this out on **Ubuntu 14.04 LTS** distribution. However, the steps are almost similar on any other flavour of Linux. Most of it also holds true for OSX too.

* Firstly download the latest version of **Visual Studio Code**.
	* This is the latest cross platform editor from Microsoft. 
	* It runs on Windows, Linux and Mac.
	* You may download the same from [here](http://visualstudio.com).
* Next you need to install the **C# extension** for Visual Studio Code.
	* This provides syntax highlighting, intellisense, debugging support etc.
	* You may download the same from [here](http://https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* Install **.Net core libraries** on the platform of your choice. 
	* This is the new set of cross platform library that includes the .Net framework (with reduced footprint) as well as the CLR.
	* This can be downloaded from [here](https://www.microsoft.com/net/core).

With this, we can now open the Linux terminal and check if the .Net core is installed properly.
To do this, run the following command.
{% highlight powershell %}
dotnet --info
{% endhighlight %}
For .Net core RC2 installed on Ubuntu 14.04, the output would be as follows...
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/netfirst$ dotnet --info
.NET Command Line Tools (1.0.0-preview1-002702)

Product Information:
 Version:     1.0.0-preview1-002702
 Commit Sha:  6cde21225e

Runtime Environment:
 OS Name:     ubuntu
 OS Version:  14.04
 OS Platform: Linux
 RID:         ubuntu.14.04-x64
{% endhighlight %}

You can also check the options available with the new **dotnet command line tool**.
{% highlight powershell %}
dotnet --help
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/netfirst$ dotnet --help
.NET Command Line Tools (1.0.0-preview1-002702)
Usage: dotnet [common-options] [command] [arguments]

Arguments:
  [command]     The command to execute
  [arguments]   Arguments to pass to the command

Common Options (passed before the command):
  -v|--verbose  Enable verbose output
  --version     Display .NET CLI Version Number
  --info        Display .NET CLI Info

Common Commands:
  new           Initialize a basic .NET project
  restore       Restore dependencies specified in the .NET project
  build         Builds a .NET project
  publish       Publishes a .NET project for deployment (including the runtime)
  run           Compiles and immediately executes a .NET project
  test          Runs unit tests using the test runner specified in the project
  pack          Creates a NuGet package
{% endhighlight %}

We can now proceed towards developing the cross platform console application.
Create a new folder to create your application and navigate  to it.
{% highlight powershell %}
mkdir HelloWorldApp
cd HelloWorldApp
{% endhighlight %}

Use the dotnet commandline tool to create the console application.
{% highlight powershell %}
dotnet new
{% endhighlight %}
This will create the following files in the project folder
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ dotnet new
Created new C# project in /home/sundeep/Documents/dev/HelloWorldApp.
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ ls
Program.cs  project.json
{% endhighlight %}

The **Program.cs** file is similar to the file that we had in the earlier .Net frameworks.
It has the **main method** which serves as the **entry point** for the console application.
{% highlight C# %}
using System;

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
{% endhighlight %}
The **project.json** file is similar to the **csproj** file that were part of the earlier .Net projects.
{% highlight javascript %}
{
  "version": "1.0.0-*",
  "buildOptions": {
    "emitEntryPoint": true
  },
  "dependencies": {
    "Microsoft.NETCore.App": {
      "type": "platform",
      "version": "1.0.0-rc2-3002702"
    }
  },
  "frameworks": {
    "netcoreapp1.0": {
      "imports": "dnxcore50"
    }
  }
}
{% endhighlight %}

This has **configuration details** like the .Net core framework version being used, the dependent assemblies being used etc.
The dependent/referenced assemblies are all **Nuget packages** which are downloaded on the fly from **Nuget respositories**. (This is somewhat similar to the Maven respository system that we have in Java)
It is essential that these are downloaded before we can run the application.
To do this run the following on the  terminal...
{% highlight powershell %}
dotnet restore
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ ls
Program.cs  project.json
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ dotnet restore
log  : Restoring packages for /home/sundeep/Documents/dev/HelloWorldApp/project.json...
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethostresolver/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethostresolver/index.json 1052ms
info :   GET https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethost/index.json
info :   OK https://api.nuget.org/v3-flatcontainer/microsoft.netcore.dotnethost/index.json 1043ms
info : Committing restore...
log  : Writing lock file to disk. Path: /home/sundeep/Documents/dev/HelloWorldApp/project.lock.json
log  : /home/sundeep/Documents/dev/HelloWorldApp/project.json
log  : Restore completed in 12204ms.

NuGet Config files used:
    /home/sundeep/.nuget/NuGet/NuGet.Config

Feeds used:
    https://api.nuget.org/v3/index.json
{% endhighlight %}

{% highlight powershell %}
dotnet build
{% endhighlight %}
Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ dotnet build
Project HelloWorldApp (.NETCoreApp,Version=v1.0) will be compiled because expected outputs are missing
Compiling HelloWorldApp for .NETCoreApp,Version=v1.0

Compilation succeeded.
    0 Warning(s)
    0 Error(s)

Time elapsed 00:00:05.9934574
{% endhighlight %}
Now that the compilation has succeeded, we may now **run** the console application using 
{% highlight powershell %}
dotnet run
{% endhighlight %}

Output:
{% highlight powershell %}
sundeep@sundeep-XPS-L501X:~/Documents/dev/HelloWorldApp$ dotnet run
Project HelloWorldApp (.NETCoreApp,Version=v1.0) was previously compiled. Skipping compilation.
Hello World!
{% endhighlight %}

With this we have now successfully created a console application on Linux using the new **cross platform .Net core libraries**.

Hope this was useful!