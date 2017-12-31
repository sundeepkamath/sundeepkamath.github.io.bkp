---
layout: post
title:  "Access the Azure Resource Manager Virtual Machine resources using ARM APIs (Part 1)"
date:   2016-07-21 16:23:00 +0530
categories: 
- Azure
tags:
- ARM API
- Azure
- Azure Active Directory
- Azure Resource Manager
- Cloud
- Virtual Machines
---

In the [last post]({{site.url}}/access-azure-classic-virtual-machine-resources-with-asm-apis.html) I explained how we can access the classic **Azure Virtual Machines** using **Azure Service Management (ASM) APIs**. 

In this post we'll see how we can access the new **Azure Resource Manager (ARM)** based Virtual Machines. This is also the **recommended** deployment model by **Microsoft** moving forward, with indications that the older **Classic** deployment model would be **deprecated** in the near future.

The documentation for the new **ARM based APIs** continue to be at the [Azure reference link](https://msdn.microsoft.com/en-us/library/azure/mt420159.aspx) as mentioned in the [last post]({{site.url}}/access-azure-classic-virtual-machine-resources-with-asm-apis.html).

In case you need a basic understanding of the **difference** between **Azure Service Management deployment model** and **Azure Resource Manager deployment models** then refer to my [earlier post]({{site.url}}/virtual-machine-deployment-models-in-azure/).

##### Step 1: Deploy a Resource VM in Azure
For the sake of this post, I'll assume that you already have an Azure account and have deployed a resource VM (Virtual Machine based on the Resource Manager deployment model). 

You would need to use the [new azure portal](http://portal.azure.com) to deploy the **resource VM**.
This **resource VM** would be part of a **Resource Group**.

Now unlike, the **classic VMs** whereby a **client certificate** needs to be generated for **user identity** and **authentication**, for **ARM model**, **Azure Active Directory (AAD)** is used for **authentication** and **Role based access control (RBAC)**.

I have created a resource virtual machine called **SK-AVLSC1** in my azure account. Notice that this is part of a resource group called **SKGroup**.

![Resource VM Portal View]({{site.url}}/static/img/blogs/ARM-VM.jpg)

Once done, the next step would require configuring the **Azure Active Directory (AAD)**.

##### Step 2: Configuring the Azure Active Directory (AAD)
The **Azure Active Directory (AAD)** is currently available only in the **classic** Azure portal.
If you are using the **new** Azure portal you can open the AAD as follows...

![Open Active Directory from new portal]({{site.url}}/static/img/blogs/OpenAADFromNewPortal.jpg)

This will take you to the **classic** Azure portal as **AAD** is not yet supported on the **new** portal at present.
If you are using the classic portal, you can open **AAD** as follows...

![Open Active Directory from the classic portal]({{site.url}}/static/img/blogs/OpenAADFromClassicPortal.jpg)

You may choose to create a new instance of **AAD** or use the default instance.
I'll be using the default **AAD** instance.

Configuring the **AAD** is a 3 step process  
    a. Create a user in **AAD**
    b. Assign role based access to the new user  
    c. Register your client application with **AAD**  

###### Step 2.a: Create a user in AAD
Click on the **AAD** instance and you'll find the following screen...

![AAD Home screen]({{site.url}}/static/img/blogs/AADHomeScreen.jpg)

Click on the `Users` tab.
This will show the list of all the existing users. (I have blurred the user data here for privacy/security reasons).
Click on the `ADD USER` option towards the bottom of the screen.

![Add User option on AAD Home screen]({{site.url}}/static/img/blogs/AADUsersHomePage.jpg)

This will open up a wizard as follows...

![Add User Wizard page 1]({{site.url}}/static/img/blogs/AddUser1.jpg)

As shown, select the `New user in your organization` option, give a `user name` and click on the next arrow.

![User details]({{site.url}}/static/img/blogs/AddUser2.jpg)

Provide the `first name`, `last name` details of the user and provide the least privileged role of `User` to this user. Later on, I'll show you how **ARM** provides a way to configure access control at a granular level on the Azure virtual machines.
Click on the next arrow.

![Final page on the user wizard]({{site.url}}/static/img/blogs/UserAddFinal.jpg)

Copy this password, logout from the azure portal and then re-login in the context of this newly created user.
You'll have to provide the temporary password. At this time it will ask you to provide a new password.

![Temporary password sign in]({{site.url}}/static/img/blogs/TempPasswdSignIn.jpg)

Add this new password and login.
It will show a screen as follows...

![No subscriptions found]({{site.url}}/static/img/blogs/NoSubsFoundScreen.jpg)

This is because we have created a user with **minimal privileges** (remember `user` role assigned while creating the user) but are **yet to assign** it rights to view/modify any resources in Azure.

###### Step 2.b: Assign role based access to the new user
We'll now have to **authorize** this newly created user.
Azure provides a number of roles out of the box like `Reader`, `Owner` etc.
In our case, we need minimal rights for the user, just enough to fetch details of a virtual machine.
So we will go ahead and assign `Reader` role to our user at Azure subscription level.

**Note:**
Roles can be assigned at various levels including subscription, resource group, virtual machine level etc.
Roles assigned at subscription level will also be applied for all resources under it.
This holds equally true for resource groups.

In order to apply the role at subscription level we'll have to go to the `Subscriptions` section on the Azure portal.

![Select subscription to assign role]({{site.url}}/static/img/blogs/ProvideRole1.jpg)

As shown, select the subscription in which your virtual machine has been deployed and click on the `Access` icon as shown in the above screenshot.
This will bring up the following screen...

![Select Reader role]({{site.url}}/static/img/blogs/ProvideRole2.jpg)

As shown select the role to be applied ... in this case `Reader` role.
Thereafter, select the newly created user as follows...

![Select newly created user]({{site.url}}/static/img/blogs/ProvideRole3.jpg)

Click on the select button at the bottom and with this we have now authorized our newly created user to access all resources under this subscription with the `Reader` role.

You can now verify this by signing out from the portal and sign in again in the context of this newly created user.
This time around you'll be able to view all the resources for which user was authorized.

In the next part of this series we'll look at how we can create an **application** to access these **ARM APIs** with our newly created **AAD user** and also register this application with **AAD**.

Hope this was useful!
