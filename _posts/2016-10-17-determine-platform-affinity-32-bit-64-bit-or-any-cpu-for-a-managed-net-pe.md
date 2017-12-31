---
layout: post
title:  "Determine platform affinity 32 bit, 64 bit or Any CPU for a managed .Net PE"
date:   2016-10-17 16:36:00 +0530
categories: 
- .Net
- CodeProject
tags:
- .Net
- Any CPU
- CorFlags  
- platform affinity
- x64
- x86
---

In my [earlier post]({{site.url}}/dealing-with-32-bit-64-bit-and-any-cpu-compilation-options-in-net.html), I discussed how **PEs** in **.Net** can target different platforms and the subtle differences between them.
In this post I'll discuss how one can determine the **platform (architecture)** that a managed .Net PE targets.

For this, we'll use a tool called `CorFlags` which come automatically installed with **Visual Studio IDE**. We'll use the **Visual Studio Developer Command Prompt** to use this tool.

**Syntax:**
`CorFlags.exe assembly [options]`

For more details on the options refer to the [msdn link](https://msdn.microsoft.com/en-us/library/ms164699&#40;v=vs.110&#41;.aspx).

Now, based on the way in which the PE has been compiled we can determine the **platform affinity** as follows...

|CPU Architecture|PE|32BITREQ|32BITPREF|
|:---|:---|:---|:---|
|x86(32-bit)|PE32|1|0|
|x64(64-bit)|PE32+|0|0|
|Any CPU|PE32|0|0|
|Any CPU 32-bit prefered|PE32|0|1|

The default case in **Visual Studio IDE** is `Any CPU` with **32-bit preferred**.
Hence, if we run `CorFlags` tool in this case we'll get...

{% highlight powershell %}
E:\Dev\Projects\TestApp\TestApp\bin\Debug>corflags TestApp.exe
Microsoft (R) .NET Framework CorFlags Conversion Tool.  Version  4.6.1055.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Version   : v4.0.30319
CLR Header: 2.5
PE        : PE32
CorFlags  : 0x20003
ILONLY    : 1
32BITREQ  : 0
32BITPREF : 1
Signed    : 0
{% endhighlight %}

As can be seen above, this is the case whereby the `exe` targets the **default** option of `Any CPU` with **32-bit preferred**.

Similarly, as shown in the the table, we can determine the platform affinity for other managed dlls too.

Hope this was useful!

**Abbreviations**:

  **PE** : Process Executables (exe's and dll's)

**References:**

* [MSDN CorFlags.exe](https://msdn.microsoft.com/en-us/library/ms164699&#40;v=vs.110&#41;.aspx)
* [Code Project - Which Platform to Target your .NET Applications?](http://www.codeproject.com/Tips/1098551/Which-platform-to-target-your-NET-applications)
* [SO-How to find if a native DLL file is compiled as x64 or x86?](http://stackoverflow.com/questions/480696/how-to-find-if-a-native-dll-file-is-compiled-as-x64-or-x86/2418287#2418287)



