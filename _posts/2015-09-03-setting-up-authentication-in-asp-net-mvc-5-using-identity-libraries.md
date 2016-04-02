---
layout: post
title: Setting up authentication in ASP.Net MVC 5 using Identity libraries
categories:
- ASP.Net
tags:
- ASP.NET Identity
- ASP.NET MVC 5
- Authentication
- Cookie based authentication
---

I've been working on this side project of mine developed in **ASP.Net MVC 5**.  
While developing the project, I had started with an **empty MVC project template**.  
As a result of this, the default plumbing with regards to authentication etc was not part of this project.  
I was now trying to figure out a way to add authentication support to it on the lines of **forms authentication** in ASP.Net web projects.   

I found that starting from ASP.Net MVC 5, ASP.Net provides a set of **Identity libraries** that can be used to set up authentication.
  
I came across an excellent post on this topic by **Ben Foster**, whereby he has explained the process of adding **cookie based authentication** to an ASP.Net MVC application in a step-by-step manner.  

It's a two part post and I highly encourage you to check it out here  

1. [ASP.NET Identity Stripped Bare - MVC Part 1][1]  
2. [ASP.NET Identity Stripped Bare - MVC Part 2][2]  


[1]: http://benfoster.io/blog/aspnet-identity-stripped-bare-mvc-part-1
[2]: http://benfoster.io/blog/aspnet-identity-stripped-bare-mvc-part-2