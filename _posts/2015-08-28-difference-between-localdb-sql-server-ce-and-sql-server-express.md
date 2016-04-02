---
layout: post
title: Difference between localDb, SQL Server CE and SQL Server Express
categories: 
- SQL
- SQL Server
tags:
- LocalDb
- SQL Server CE
- SQL Server Express
---

While the different versions of SQL server help cater to different needs of a developer, many a times they also end up creating a confusion for those who aren't aware of their respective roles.  
There are many articles that try explaining this difference, I've tried to consolidate these in one place for my reference.  
Hope it helps you all as well.

In a nutshell,

##### **SQL Server Express**  
* It is a **free** SQL Server edition, fully compatible with and easily upgradeable to higher SQL Server editions.
* It can be used for **learning, training and to run relatively small production database** (with less than 10GB of data).
* Upgrade from SQL Express to paid SQL Server editions is a matter of typing in a **license key** and **no installation** is required.
* SQL server express was initially introduced to provide the developers with a **small, simple, low-footprint installation**.
* This would require no configuration or administration, be run as a non-admin user etc.
  
##### **Why was there a need to introduce LocalDb?**  
- As SQL Server product matured more **complexity** got introduced. 
- It became **harder and harder** for SQL Express to be both **compatible** with other SQL Server editions and be **small/simple**.     
  The challenge is most visible in installation and configuration of SQL Express. 
- Hence LocalDb or SQL Server "Denali" was introduced. 
- It is a dedicated version of SQL Express for developers - **LocalDB** that **delivers the simplicity** and yet **is compatible** with other editions of SQL Server at the **API** level.

##### **LocalDb**  
- LocalDb is created specifically for developers.
- LocalDB uses the same **sqlservr.exe** as the regular SQL Express and other editions of SQL Server.  
  (By default it is located at `C:\Program Files\Microsoft SQL Server\110\LocalDB\Binn`) 
- It is **not a replacement** for SQL Server Express but an **addition** to the lineup.
- The application is using the **same client-side providers** (ADO.NET, ODBC etc.) to connect to it and operates on data using the same T-SQL language as provided by SQL Express.
- LocalDB is **installed once** on a machine (per major SQL Server version).    
  Multiple applications can start multiple LocalDB processes, but they are all started from the same sqlservr.exe executable file from the same disk location.
- LocalDB **doesn't create any database services**; LocalDB processes are started and stopped automatically when needed. 
- The application is just connecting to "`Data Source=(localdb)\v11.0;IntegratedSecurity=true`" and LocalDB process is started as a **child process** of the application.     
  A few minutes after the last connection to this process is closed the process shuts down.
- When an application uses any of the client-side providers (like ADO.NET, ODBC or PDO) to connect to `"Data Source=(localdb)\v11.0"`, the provider will first check if LocalDB instance for the current user is started.    
  If it's already started the application will connect to it.     
  Otherwise the LocalDB instance for the current user will be started and then the provider proceeds to connect to it.    
  Note that each user (Windows login) may have their own LocalDB instance that is isolated from instances of other users.
- LocalDB connections support **AttachDbFileName** property, which allows developers to specify a database file location.     
  LocalDB will attach the specified database file and the connection will be made to it.    
  For example:  
  Assume that a database file (**\*.MDF file with the corresponding \*.LDF file**) is stored at `"C:\MyData\Database1.mdf"`.    
  The developer can start working with it by simply using the connection string: `"Data Source=(localdb)\v11.0;Integrated Security=true;AttachDbFileName=C:\MyData\Database1.mdf"`.
  
##### **SQL Server CE vs LocalDb**  
- SQL Server CE, just like LocalDb is **small and simple** database, with a **lightweight installation**, and can directly connect to a database file.
- The **huge benefit of LocalDB** is that it's real SQL Server - it's a **special version of SQL Server Express**, but it basically supports everything that "real" SQL Server has - spatial data types, stored procedures etc.
- **SQL Server Compact Edition** on the other hand is a very much **scaled down version** - lot of features and datatypes aren't supported. It's **smaller** and more **agile**.
- **Execution mode:**  
  SQL Server Compact is an **in-proc** DLL, while LocalDB runs as a **separate process**.
- **Disk usage:**  
  All SQL Server Compact binaries amount to some **4MBs**, while LocalDB installation takes **140MBs**.
- **Features:**  
  SQL Server Compact offers **core RDBMS** functionality like **querying**, while LocalDB provides a much **richer set of features**, including **Stored Procedures, Geometry and Geography data types**, etc.
  
##### **References**  
- [Introducing LocalDB, an improved SQL Express](http://blogs.msdn.com/b/sqlexpress/archive/2011/07/12/introducing-localdb-a-better-sql-express.aspx)
- [Comparison of SQL Server Compact, SQLite, SQL Server Express and LocalDB](http://erikej.blogspot.in/2011/01/comparison-of-sql-server-compact-4-and.html)
  
  