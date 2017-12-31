---
layout: post
title:  "Access the Azure Resource Manager Virtual Machine resources using ARM APIs and the new Azure Portal"
date:   2016-12-06 05:21:00 +0530
categories: 
- Azure
- C#
- Microsoft
tags:
- ARM APIs
- Azure
- Azure Active Directory  
- Azure Resource Manager
- C#
---

<a href="https://www.codeproject.com/script/Articles/BlogFeedList.aspx?amid=4854419" rel="tag" style="display:none">CodeProject</a>

In an [earlier post]({{site.url}}/access-the-azure-resource-manager-virtual-machine-resources-using-arm-apis-part-1.html) , I explained how **Resource VMs** can be accessed using **ARM APIs**. We used the **classic** Azure portal along with the **native** application approach to implement this. 

We had to use the **classic** portal as the **new portal** was in **preview mode** and **Azure Active Directory (AAD)** was not fully integrated in the new portal.

Now that **AAD** is integrated in the **new portal**, I'll explain how we can achieve the same use case of accessing **ARM APIs** using this new Azure portal.

For the sake of this demo I have created a new VM as part of my Azure subscription.

![Azure Subscription with VM]({{site.url}}/static/img/blogs/1SubscriptionAndVM.jpg)

Next, we'll create a **native application** in the Azure Active Directory.

#### Step 1: Create Native application in AAD

In the Azure portal, navigate to `Azure Active Directory` and click on the `App Registrations` section.
Next click on the `Add` option at the top.

![Navigate to AAD App Registration]({{site.url}}/static/img/blogs/2CreateNativeApplication.jpg)

This will open up a new blade towards the right. 
As shown in the screenshot below provide a `Name` for the application, select the `Native` application option in the `Application Type` dropdown and provide a `Redirect URI` in the third input option. This URL need not be an actual endpoint, it just needs to be a valid URI.

![New application details]({{site.url}}/static/img/blogs/3NewAppForm.jpg)

Click on the `Create` button at the bottom and with this the new native application would be created.

![Newly created application listed]({{site.url}}/static/img/blogs/4NewlyCreatedApplicationListed.jpg)

Next, we'll delegate permission to this application to access the `Windows Azure Service Management APIs`. This will help us access the ARM APIs later on.

#### Step 2: Delegate permission to the Native application to access Azure Service Management APIs

Click on the newly created application, in this case `SKApp`, and click on the `Settings` option.
This will open up a blade on the right. Click on `Required permissions` option and then the `Add` option at the top.

![Native application delegate permissions option]({{site.url}}/static/img/blogs/5NativeApplicationDelegateOption.jpg)

Now for the `Select an API` option select the option `Windows Azure Service Management API` on the right.

![Delegation API enumerations]({{site.url}}/static/img/blogs/6SelectAzureMgmtAPI.jpg)

Once selected, grant `delegation rights` to the application to access this API as follows.

![Azure Service Mgmt API permission delegation]({{site.url}}/static/img/blogs/7DelegatePermission.jpg)

Once permission is granted, the assigned permission list would look like...

![Delegated permission listing]({{site.url}}/static/img/blogs/8DelegatePermissionResult.jpg)

#### Step 3: Create a Secret Key for this Native application

We'll now create a `secret key` and associate it with this application.
Click on the `Keys` section in `Settings` blade.

![Open Keys blade]({{site.url}}/static/img/blogs/9KeysBlade.jpg)

Add a `key description` and select an `expiry` as per your requirement.

![Key description and expiry]({{site.url}}/static/img/blogs/10SelectNameAndKeyExpiry.jpg)

Now click on the `Save` option at the top and a key would be generated.
Store this key **safely** and **securely** with you, as this would be required later on when we try to access the ARM APIs.

![Key generation]({{site.url}}/static/img/blogs/11KeyGenerated.jpg)

#### Step 4: Save the Application ID and Directory ID

Now that we have generated a `secret key` and saved it, we need to also save the `Application ID` of our **Native application** and `Directory ID` of the Active Directory instance.

You'll find the `Application ID` in the Native application details blade.

![Application ID]({{site.url}}/static/img/blogs/12ApplicationID.jpg)

The `Directory ID` also known as the `Tenant ID`, is the **unique id** for the **AAD** instance.
You will find it at the following location.

![Tenant ID]({{site.url}}/static/img/blogs/13TenantId.jpg)

In addition to these we’ll also require the following additional parameters.

**Service resource id**
The value of this id is `https://management.core.windows.net/`

**OAuth endpoint**
This url is used for authorization and its value is `https://login.microsoftonline.com/<Directory ID>/oauth2/authorize`
As shown above, the authorization url requires the `Directory ID`.

With this, we now have all the details to go ahead and implement a **REST client** to consume **ARM APIs**.

However, before we do that we need to carry out an additional step of granting rights to our native application on the subscription.
**Note:**
These rights can be granted at the **Subscription**, **Resource Group** or at the **Resource** level (in this case virtual machine level).

Granting rights at **Subscription** or **Resource Group** level would get **inherited** by all resources under them.

#### Step 5: Grant permission to the Native application at the subscription level
To grant rights, drill down to the relevant subscription on which we wish to assign the rights to the application.

![Grant Subscriptions rights]({{site.url}}/static/img/blogs/14GrantRightsAtSubscriptionLevel.jpg)

Click on the `Add` option as shown above, and select the `Reader` role option.

![Select Reader role]({{site.url}}/static/img/blogs/15SelectReaderRole.jpg)

Once done, select the native application we created earlier by typing its name in the `Add Users` input box.
Select the application once it shows up and click on the `Select` button below.

![Select application to assign role]({{site.url}}/static/img/blogs/16SelectApplicationToAssignRole.jpg)

Thats it!! We are done.
Now we can proceed towards consuming the **ARM Rest APIs** using a **REST Client**.

#### Step 6:  Consume the ARM API using the REST client and dependent parameters

Lets create a simple **C#.Net Console** application to implement this.
You'll need to install the NuGet package for `Microsoft.IdentityModel.Clients.ActiveDirectory`. At the time of this post I used `v3.13.8`.

We'll create a class `AzureAD.cs` to authenticate our user and get an **access token** which can later be used to call the ARM based REST APIs.

{% highlight C# %}
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace AzureNewPortalResourceVMIntegration
{
    public class AzureAD
    {
        internal static string Authenticate(string authority, 
            string serviceResourceId, string clientId, string secretKey)
        {
            AuthenticationContext authContext = new AuthenticationContext(authority, true);
            AuthenticationResult authResult = null;

            try
            {
                authResult = authContext
                    .AcquireTokenAsync(serviceResourceId, 
                                new ClientCredential(clientId, secretKey)).Result;

                if (authResult != null)
                    Console.WriteLine("Auth Token: {0}", authResult.AccessToken);
                else
                    throw new Exception("Azure AD authentication failed.");

            }
            catch (AdalException adEx)
            {
                Console.WriteLine("Exception: {0}", adEx);
            }

            return authResult.AccessToken;
        }
    }
}
{% endhighlight %}

We’ll also need a class to place REST API calls using this access token. We’ll create a class `AzureRestClient.cs` for this.

{% highlight C# %}
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace AzureNewPortalResourceVMIntegration
{
    internal class AzureRestClient
    {
        internal static async Task<string> ExecuteRequest(string accessToken, string url)
        {
            string serviceBaseAddress = url;
            string responseMessage = null;

            HttpClient httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = 
                new AuthenticationHeaderValue("Bearer", accessToken);

            HttpResponseMessage response = 
                await httpClient.GetAsync(serviceBaseAddress);

            if (response.IsSuccessStatusCode)
            {
                responseMessage = await response.Content.ReadAsStringAsync();
                return responseMessage;
            }
            else
            {
                responseMessage = await response.Content.ReadAsStringAsync();

                if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                {
                    return "Access denied.";
                }
                return responseMessage;
            }
        }
    }
}
{% endhighlight %}

With this we can now fetch details of our VM `SK-Lin-1` as follows…

{% highlight C# %}
using System;

namespace AzureNewPortalResourceVMIntegration
{
    class Program
    {
        static void Main(string[] args)
        {
            
            string tenantId = "<Enter Directory Id>";
            string clientId = "<Enter Application Id>";
            string secretKey = "<Enter Secret Key>";
            string subscriptionId = "<Enter Subscription Id>";
            string resourceGroupName = "<Enter Resource group name>";
            string virtualMachineName = "<Enter Virtual Machine name>";

            string oauthUrl = "https://login.microsoftonline.com/{0}/oauth2/authorize";
            string serviceResourceId = "https://management.core.windows.net/";

            string authority = string.Format(oauthUrl, tenantId);

            string vmDetailsURL = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}?api-version=2015-06-15";

            string accessToken = AzureAD.Authenticate(authority, serviceResourceId, clientId, secretKey);

            if (accessToken != null)
            {
                AzureRestClient.ExecuteRequest(accessToken, vmDetailsURL)
                     .ContinueWith((task) =>
                     {
                         Console.WriteLine(task.Result);
                     });

            }
            Console.ReadLine();
        }
    }
}
{% endhighlight %}

For more details on the **REST API** used to fetch VM details refer to the [Azure reference guide](https://msdn.microsoft.com/en-us/library/azure/mt163682.aspx).

Once you get the response, you can parse the **JSON result** and extract the relevant data from it.
You may also download the source code for this from my [github repository](https://github.com/sundeepkamath/AzureNewPortalResourceVMIntegration).

Do explore the **Azure Reference documentation** which lists a number of other APIs that can be used. In all cases the code remains the same, only the REST API URL and its dependent parameters would change.

Hope this was useful!

**References:**   

* [Tom FitzMacken's post](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)  

 