---
layout: post
title: Compiler error CS1617 Invalid option 6 for langversion comes up in Visual Studio 2015 for .Net Framework 4.6
categories: 
- ASP.Net 
- Web
tags:
- .Net framework v4.5.2
- .Net framework v4.6
- ASP.Net MVC 5
- Visual Studio 2015
---

<p>I created an empty <strong>ASP.Net MVC 5</strong> project in <strong>Visual Studio 2015</strong> recently.<br />
This project targeted <strong>.Net Framework version 4.5.2</strong> by default. The project <strong>compiled successfully</strong> and ran as expected without any issues.</p>

<p>Later on I changed this from the project properties to target the latest <strong>.Net Framework v4.6</strong><br />
This time however, when I tried running the project it <strong>failed</strong> with the following error.</p>

{% highlight C# %}
C:\Program Files (x86)\IIS Express&gt; "C:\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe" /t:library /utf8output /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\ab86a7ca\9447cc05_9ac4d001\System.Web.Optimization.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\Microsoft.CSharp\v4.0_4.0.0.0__b03f5f7f11d50a3a\Microsoft.CSharp.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_32\System.EnterpriseServices\v4.0_4.0.0.0__b03f5f7f11d50a3a\System.EnterpriseServices.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.ServiceModel.Activities\v4.0_4.0.0.0__31bf3856ad364e35\System.ServiceModel.Activities.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\3a5fd52a\fe1de005_9ac4d001\System.Web.Mvc.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\4ec01510\6e387621_9ac4d001\mvc452.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.ComponentModel.DataAnnotations\v4.0_4.0.0.0__31bf3856ad364e35\System.ComponentModel.DataAnnotations.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Core\v4.0_4.0.0.0__b77a5c561934e089\System.Core.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\d21f1f2b\cf2dcf05_9ac4d001\System.Web.WebPages.Razor.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.Services\v4.0_4.0.0.0__b03f5f7f11d50a3a\System.Web.Services.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.WebPages.Deployment\v4.0_1.0.0.0__31bf3856ad364e35\System.Web.WebPages.Deployment.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.DynamicData\v4.0_4.0.0.0__31bf3856ad364e35\System.Web.DynamicData.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Configuration\v4.0_4.0.0.0__b03f5f7f11d50a3a\System.Configuration.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\Microsoft.VisualStudio.Web.PageInspector.Loader\v4.0_1.0.0.0__b03f5f7f11d50a3a\Microsoft.VisualStudio.Web.PageInspector.Loader.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_32\System.Web\v4.0_4.0.0.0__b03f5f7f11d50a3a\System.Web.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.ApplicationServices\v4.0_4.0.0.0__31bf3856ad364e35\System.Web.ApplicationServices.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.ServiceModel\v4.0_4.0.0.0__b77a5c561934e089\System.ServiceModel.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\5b520775\2a2b4106_9ac4d001\Microsoft.CodeDom.Providers.DotNetCompilerPlatform.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Data.DataSetExtensions\v4.0_4.0.0.0__b77a5c561934e089\System.Data.DataSetExtensions.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.Extensions\v4.0_4.0.0.0__31bf3856ad364e35\System.Web.Extensions.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\517d2319\377ff305_9ac4d001\Antlr3.Runtime.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\b132331e\aedfce05_9ac4d001\System.Web.WebPages.Deployment.dll" /R:"C:\Windows\Microsoft.NET\Framework\v4.0.30319\mscorlib.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.IdentityModel\v4.0_4.0.0.0__b77a5c561934e089\System.IdentityModel.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\Microsoft.Web.Infrastructure\v4.0_1.0.0.0__31bf3856ad364e35\Microsoft.Web.Infrastructure.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.ServiceModel.Web\v4.0_4.0.0.0__31bf3856ad364e35\System.ServiceModel.Web.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Xml.Linq\v4.0_4.0.0.0__b77a5c561934e089\System.Xml.Linq.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.WorkflowServices\v4.0_4.0.0.0__31bf3856ad364e35\System.WorkflowServices.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System\v4.0_4.0.0.0__b77a5c561934e089\System.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\0af4374b\7aafc905_9ac4d001\System.Web.Razor.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Xml\v4.0_4.0.0.0__b77a5c561934e089\System.Xml.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\b964c8b6\30c9f505_9ac4d001\WebGrease.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Drawing\v4.0_4.0.0.0__b03f5f7f11d50a3a\System.Drawing.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Runtime.Serialization\v4.0_4.0.0.0__b77a5c561934e089\System.Runtime.Serialization.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.ServiceModel.Activation\v4.0_4.0.0.0__31bf3856ad364e35\System.ServiceModel.Activation.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Web.WebPages.Deployment\v4.0_2.0.0.0__31bf3856ad364e35\System.Web.WebPages.Deployment.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\f5630b78\8d91ce05_9ac4d001\System.Web.Helpers.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\fe5a9f88\be06cf05_9ac4d001\System.Web.WebPages.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_32\System.Data\v4.0_4.0.0.0__b77a5c561934e089\System.Data.dll" /R:"C:\Windows\Microsoft.Net\assembly\GAC_MSIL\System.Activities\v4.0_4.0.0.0__31bf3856ad364e35\System.Activities.dll" /R:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\assembly\dl3\cc0337ba\8b692306_9ac4d001\Newtonsoft.Json.dll" /out:"C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\App_global.asax.y7q4zs_7.dll" /D:DEBUG /debug+ /optimize- /warnaserror /w:4 /nowarn:1659;1699;1701;612;618 /langversion:6 /nowarn:1659;1699;1701  "C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\App_global.asax.y7q4zs_7.0.cs" "C:\Users\ckurz\AppData\Local\Temp\Temporary ASP.NET Files\vs\af543f55\865ca3e2\App_global.asax.y7q4zs_7.1.cs"


Microsoft (R) Visual C# Compiler version 4.6.0081.0

for C# 5
Copyright (C) Microsoft Corporation. All rights reserved.



This compiler is provided as part of the Microsoft (R) .NET Framework, but only supports language versions up to C# 5, which is no longer the latest version. For compilers that support newer versions of the C# programming language, see http://go.microsoft.com/fwlink/?LinkID=533240

error CS1617: Invalid option '6' for /langversion; must be ISO-1, ISO-2, 3, 4, 5 or Default
{% endhighlight %}

<p>This was quite weird.<br />
After searching a bit on Google I finally found the following solution.<br />
In the web.config file there is a section <strong>system.codedom</strong> as follows...</p>

{% highlight xml %}
<system.codedom>
    <compilers>
      <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CSharp.CSharpCodeProvider, System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089">
        <providerOption name="CompilerVersion" value="v4.0" />
      </compiler>
      <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:14 /nowarn:41008 /define:_MYTYPE=\&amp;quot;Web\&amp;quot; /optionInfer+" />
    </compilers>
</system.codedom>
{% endhighlight %}

<p>Observe the value of the <strong>compiler type</strong> for the <strong>C#</strong> language.<br />
It <strong>needs to be changed</strong> from <code>Microsoft.CSharp.CSharpCodeProvider, System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089</code> to <code>Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35</code></p>

<p>In a nutshell,</p>

<blockquote>
  <ul>
  <li>for <strong>4&#46;5 and C#5</strong> => type=<strong>"Microsoft.CSharp.CSharpCodeProvider"</strong> </li>
  <li>for <strong>4&#46;6 and C#6</strong> => type=<strong>"Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider"</strong></li>
  </ul>
</blockquote>

<p>I'm not yet sure why this issue occurred in the first place, I'm guessing this <strong>could be a bug</strong> in <strong>Visual Studio 2015</strong>.</p>
