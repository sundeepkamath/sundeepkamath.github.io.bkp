---
layout: post
title: Setting up Entity Framework connection string in web.config
categories:
- ASP.Net
- Entity Framework 
- SQL
- SQL Server
tags:
- LocalDb
- SQL Server CE
- SQL Server Express
- SQL Server
- Entity Framework
- Connection String
---

While I explain how the **connection string** and **database provider** information can be set in the **web.config** for **Entity Framework**, I'll be assuming that you are aware of the differences between **localdb, Sql Server Express** and **Sql Server Compact Edition**.  
If not you may checkout my earlier article [here]({{site.url}}/difference-between-localdb-sql-server-ce-and-sql-server-express.html).

When you add the nuget package for Entity Framework in your project (I tried this out in Visual Studio 2015 for ASP.Net MVC 5), by default it adds support for the localdb data provider in the web.config file as follows...  

{% highlight xml %}
<configuration>
	<entityFramework>
		<defaultConnectionFactory type="System.Data.Entity.Infrastructure.localdbConnectionFactory, EntityFramework">
			<parameters>
				<parameter value="mssqllocaldb" />
			</parameters>
		</defaultConnectionFactory>
		<providers>
			<provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
		</providers>
	</entityFramework>
</configuration>
{% endhighlight %}

The `<parameter value="mssqllocaldb" />` here represents the default instance of SQL Server 2014.  
For SQL Server 2012 we would have to replace this with `<parameter value="v11.0" />`  

This represents the **implicit** way to declare the connection string.  
We can however, declare this **explicitly** using...

{% highlight xml %}
<configuration>
	<configSections>
		<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false"/>
	</configSections>
	<connectionStrings>
		<add name="DefaultConnection" connectionString="Data Source=(LocalDB)\mssqllocaldb;Integrated Security=True;MultipleActiveResultSets=True" 
										providerName="System.Data.SqlClient"/>
	</connectionStrings>
	<entityFramework>
		<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
			<parameters>
				<parameter value="mssqllocaldb"/>
			</parameters>
		</defaultConnectionFactory>
		<providers>
			<provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer"/>
		</providers>
	</entityFramework>
</configuration>
{% endhighlight %}

If we wish to connect to a localdb database using file name we can use  
`Data Source=(LocalDB)\mssqllocaldb;AttachDbFileName=|DataDirectory|\DatabaseFileName.mdf;InitialCatalog=DatabaseName;Integrated Security=True;MultipleActiveResultSets=True`

This connection string can be then accessed from with the Database context class as follows...

{% highlight C# %}
    public class TestContext : DbContext
    {
        public TestContext() : base("name=DefaultConnection")
        {

        }

        
    }
{% endhighlight %}

 
As can be seen in the code snippet above the name of the connection string is passed as a parameter in the constructor of the base DbContext class.

On similar lines, the connection string for  

- SQL Server Express database in default instance

{% highlight xml %}
<add name="ConnectionStringName"
    providerName="System.Data.SqlClient"
    connectionString="Data Source=.\SQLEXPRESS;Initial Catalog=DatabaseName;Integrated Security=True;MultipleActiveResultSets=True"/>
{% endhighlight %}

- SQL Server Express database in a .mdf file in the `App_Data` folder

{% highlight xml %}
<add name="ConnectionStringName"
    providerName="System.Data.SqlClient"
    connectionString="Data Source=.\SQLEXPRESS;AttachDbFileName=|DataDirectory|\DatabaseFileName.mdf;Integrated Security=True;User Instance=True;MultipleActiveResultSets=True" />
{% endhighlight %}

- SQL Server (Full Edition) database using default SQL Server instance

{% highlight xml %}
<add name="ConnectionStringName"
    providerName="System.Data.SqlClient"
    connectionString="Data Source=ServerName;Initial Catalog=DatabaseName;Integrated Security=False;User Id=userid;Password=password;MultipleActiveResultSets=True" />
{% endhighlight %}
 
- SQL Server (Full Edition) database using named SQL Server instance

{% highlight xml %}
<add name="ConnectionStringName"
    providerName="System.Data.SqlClient"
    connectionString="Data Source=ServerName\InstanceName;Initial Catalog=DatabaseName;Integrated Security=True;MultipleActiveResultSets=True" />
{% endhighlight %}

- SQL Server CE

{% highlight xml %}
<add name="ConnectionStringName"
    providerName="System.Data.SqlServerCe.4.0"
    connectionString="Data Source=|DataDirectory|\DatabaseFileName.sdf" />
{% endhighlight %}

##### **Note:**
- The **AttachDbFileName** setting is typically used for database files that you keep in the `App_Data` folder.   
- The `App_Data` folder is a **relatively secure** place to **store database files** that you want to keep in the web application folder structure.  
- ASP.NET will not serve contents of the `App_Data` folder in response to Web requests, which helps to maintain the security of the data in database files kept in this folder.  
- ASP.NET automatically substitutes the file path to the `App_Data` folder for the `|DataDirectory|` connection-string variable when it opens a connection to the database.  
- This ensures that the path to your database remains current if the application is moved to a different directory.  
- If you don't use the `|DataDirectory|` connection string variable, you have to provide the **full physical path** to the **database file**.


##### **References**
- [SQL Server Connection strings for ASP.Net Web Applications][1]
- [SQL Server Connections strings for LocalDb][2]

[1]:https://msdn.microsoft.com/en-us/library/jj653752(v=vs.110).aspx
[2]:https://msdn.microsoft.com/en-us/library/hh510202(v=sql.110).aspx
