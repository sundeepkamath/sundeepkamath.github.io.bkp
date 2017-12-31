---
layout: post
title:  "Dealing with 32-bit, 64-bit and Any CPU compilation options in .Net"
date:   2016-10-16 11:07:00 +0530
categories: 
- .Net
tags:
- .Net
- 32-bit
- 64-bit
- Any CPU
- architecture
- x64
- x86
---

<a href="https://www.codeproject.com/script/Articles/BlogFeedList.aspx?amid=4854419" rel="tag" style="display:none">CodeProject</a>

Ever wondered why Visual Studio IDE provides 3 options for compilation - `x86`, `x64` and `Any CPU` ?
While most are aware of these options, many still lack clarity on the significance of these options (including me earlier in my career). So, as part of this article I hope to clear some of these doubts.

Windows OS has evolved over the years, right to from the time it started out as a **16-bit** OS, then transitioned to **32-bit** (`x86`) and more recently to **64-bit** (`x64`).

**Why would you use a 64-bit OS over 32-bit?**
To understand this, lets understand the capabilities of both **32-bit** and **64-bit** CPUs.

* **32-bit CPU**
	* Address pointer size is **32 bits** -> can access **2^32 (4,294,967,296)** discrete addresses.
	* This allows a program to make a data structure in memory up to **4 GB** in size.

* **64 bit CPU** 
	* Address pointer size is **64 bits** -> can access **2^64 (18,446,744,073,709,551,616)** discrete addresses.
	* This allows a program to make a data structure in memory up to **16 Exabytes** in size.

* **Main advantages (64-bit over 32-bit)**
	* Process on a **64-bit** CPU can work with a larger set of data compared to **32-bit** CPU (only constrained by physical memory).
	* A **64 bit** integer makes arithmetic or logical operations using **64 bit** types such as **C#'s** `long` faster than one implemented as two **32 bit** operations. 

* **To summarize, for all practical purposes...**
	* Applications that use large amounts of memory, like Image and video editing software, 3D rendering utilities, and video games will make better use of a **64-bit architecture** and operating system, especially if the machine has **8** or even **16 GB** of **RAM** that can be divided among the applications that need it.


**Understanding the behaviour of 32-bit, 64-bit and Any CPU compiled PE files:**  
 
  * **On a 32-bit machine:**  
	* **Any CPU**: runs as a **32-bit** process, can load **Any CPU** and `x86` assemblies, will get `BadImageFormatException` if it tries to load an `x64` assembly.
	* **Any CPU-32-bit preferred (default)**: same as **Any CPU**.
	* **x86**: same as **Any CPU**.
	* **x64**: `BadImageFormatException` always.

  * **On a 64-bit machine:**  
	* **Any CPU**: runs as a **64-bit** process, can load **Any CPU** and `x64` assemblies, will get `BadImageFormatException` if it tries to load an `x86` assembly.
	* **Any CPU-32-bit preferred (default)**: runs as a **32-bit** process, can load **Any CPU** and `x86` assemblies, will get `BadImageFormatException` if it tries to load an `x64` assembly.
	* **x86**: runs as a **32-bit** process, can load **Any CPU** and `x86` assemblies, will get `BadImageFormatException` if it tries to load an `x64` assembly.
	* **x64**: same as **Any CPU**.

**Any CPU-32-bit preferred**
This is a new option introduced starting with **.NET 4.5** and **Visual Studio 11**. It is a new subtype of **Any CPU** and is also the default compilation option in the **IDE**.

You can check this in the project properties as shown in the screenshot below ...

![Visual Studio project properties default compilation option]({{site.url}}/static/img/blogs/AnyCPU32bitpreferred.jpg)

**How 32-bit PEs work on 64-bit architectures ?**  

  * Generally, only **32-bit** PEs can be loaded into a **32-bit** process, and only **64-bit** PEs can be loaded into a **64 bit** process.  
  * When a **32-bit** PE is launched on a **64-bit** OS, it runs in the **"WOW”** **(Windows-32 on Windows-64)** to present an *illusion* of a **32-bit** operating system to the process.  

**Why was the Any CPU compilation option introduced?**  

  * Now before **64-bit** Windows OS was introduced there was just the **32-bit** PEs (of course, we are not considering **16-bit** OS in this discussion) and hence during compilation these PEs were marked as **32-bit** and there was no ambiguity.  
  * With the introduction of **64-bit** Windows OS, a decision had to be made - should the binaries be marked as **32-bit** or **64-bit** by default?  
  * Technically speaking, managed binaries have no *hard CPU dependency* - they could be either **32** or **64 bit**.
  * However, there was a way required to reuse .Net libraries from both **32-bit** and **64-bit** processes. For this the **OS loader** support was extended to support **architecture-neutral** PE files **(Any CPU)**.  
  * In case of **Any CPU** PEs the **OS loader** decides how to initialize the process. On **64-bit** OS's they are run as **64-bit** processes, and on **32-bit** OS's they are run as **32-bit** processes.


Hope this post helped clarify a few things!!
I'll also touch upon how to identify the **architecture affinity** of a PE in a future post.


**Abbreviations**:

  **PE** : Process Executables (exe's and dll's)

**References**  

  * [What AnyCPU Really Means As Of .NET 4.5 and Visual Studio 11](http://blogs.microsoft.co.il/sasha/2012/04/04/what-anycpu-really-means-as-of-net-45-and-visual-studio-11/)
  * [AnyCPU Exes are usually more trouble than they’re worth](https://blogs.msdn.microsoft.com/rmbyers/2009/06/09/anycpu-exes-are-usually-more-trouble-than-theyre-worth/)
  * [SO-What are the advantages of a 64-bit processor?](http://stackoverflow.com/questions/607322/what-are-the-advantages-of-a-64-bit-processor)
  * [SO-C# compiling for 32/64 bit, or for any cpu?](http://stackoverflow.com/questions/5229768/c-sharp-compiling-for-32-64-bit-or-for-any-cpu)
  * [SO-What does the Visual Studio “Any CPU” target mean?](http://stackoverflow.com/questions/516730/what-does-the-visual-studio-any-cpu-target-mean)