---
layout: post
title:  "Access Azure Classic Virtual Machine resources with ASM APIs"
date:   2016-07-20 04:26:00 +0530
categories: 
- Azure
tags:
- ASM API
- Azure
- Automation
- C#
- Classic VM
- Client Certificate
- Cloud Service
- make-cert
---

**Azure Classic Virtual Machines** as we know are based on the **Azure Service Management deployment model**.
Please refer to my [earlier post]({{site.url}}/virtual-machine-deployment-models-in-azure.html) for details on this.

In this post I'll be explaining the steps to use the **Azure Service Management (ASM) REST APIs** to discover as well as fetch details of Classic VMs.
Microsoft has a well documented list of Azure APIs at this [link](https://msdn.microsoft.com/en-us/library/azure/mt420159.aspx).
In addition to the **ASM REST APIs**, this reference also lists the APIs for **ARM REST APIs** and **Azure SDK**. I'll be covering the last 2 in future blog posts.  

##### Step 1: Deploy a classic VM in Azure
For the sake of this post, I'll assume that you already have an Azure account and have deployed a classic VM. 
You may use either the [older Azure classic portal](http://manage.windowsazure.com/) or the [newer Azure portal](http://portal.azure.com) to deploy the classic VM.
This would create both the **classic virtual machine** and a **cloud service** which acts as its container.
I have created a classic virtual machine called **SKClassicVM** in my azure account.  

![Classic VM Portal View]({{site.url}}/static/img/blogs/ClassicVMPortalView.jpg)

The cloud service created for the same is as follows...  

![Cloud Service Portal View]({{site.url}}/static/img/blogs/CloudServicePortalView.jpg)

Once done, the next step would require generating a client certificate for authentication.

##### Step 2: Generating a client certificate
In order to fetch details of the classic VMs via the ASM REST APIs we would need a way to **authenticate** and for this purpose the **client certificate** is required.
I'll show how a client certificate can be generated for this purpose using the **makecert.exe** tool. You may also use the **openssl** utility for the same.

Firstly, create a Root certificate.
{% highlight powershell %}
makecert.exe -r 
			 -n "CN=SK Root Authority,O=FunSoft,OU=Development,L=Pune,S=MH,C=IN"
             -pe 
             -ss Root 
             -sr CurrentUser 
             -sky signature 
             -m 12 
             -a sha256 
             -len 2048 
{% endhighlight %}
Secondly, generate the client certificate and use the above Root certificate to sign it.
{% highlight powershell %}
makecert -pe 
		 -n "CN=Sundeep" 
         -a sha256 
         -len 2048 
         -sky exchange 
         -eku 1.3.6.1.5.5.7.3.2 
         -sp "Microsoft RSA SChannel Cryptographic Provider" 
         -sy 12 
         -in "SK Root Authority" 
         -is Root 
         -ir CurrentUser 
         -ss My 
         -sr CurrentUser 
         -m 13 
         SundeepClientCert.cer 
{% endhighlight %}
This will generate a client certificate by the name **SundeepClientCert.cer** in the **My** certificate store of the Current user.
**Note:**
For more details on the usage of **makecert.exe** you may refer to my [earlier post]({{site.url}}/static/img/blogs/creating-self-signed-certificates/).

##### Step 3: Associate the client certificate with your Azure subscription
Now that the client certificate is ready, we need to now associate it with the Azure subscription.
This is required so that when we try to access the **ASM REST APIs** we can furnish this **client certificate** to **identify** ourselves.
Also note, that you might have **multiple subscriptions** associated with your Azure account, so associate this certificate with only those subscriptions whose resources (virtual machines, storage etc) you wish to access using the **ASM APIs**.

To associate the client certificate you need to go to the [classic Azure portal](http://manage.windowsazure.com).
Go to the `Settings section -> Management Certificates` tab as shown below and click on the `upload` button towards the bottom.

![Azure settings page to upload certificate]({{site.url}}/static/img/blogs/CertificateAssociation.jpg)

This will pop up a upload certificate dialog. 
Select your **client certificate** and select the **subscription** in the drop down below with which you wish to associate the certificate.

![Upload certificate dialog]({{site.url}}/static/img/blogs/UploadCertificate.jpg)

If you wish to associate this certificate with multiple subscriptions, repeat this step and associate the certificate with each subscription.


Once you are done with this the associations would be displayed on the grid on the `Management Certificates` tab.
With this we are now ready for the next step.

##### Step 4: Prepare the data required to call the ASM REST API
I'll be using a **C#.Net Console application** to consume the ASM REST API.
For the sake of this post, I'll be looking to fetch details of the classic VM shown in Step 1.
The Azure Reference documentation has an API [Get Deployment](https://msdn.microsoft.com/en-us/library/azure/ee460804.aspx) which provides the parameters required to call this API.
There are 2 APIs listed on this link of which we'll need to use the following API.
`https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/<deployment-slot>`

Here,  

|Parameter|Value|
|:----:|:----|
|subscription-id|This would be the subscription id of your subscription. You'll find this in the portal.|
|cloudservice-name|This would be the name of the cloud service in which the classic VM is hosted.|
|deployment-slot|This value would be **staging** or **production** based on your deployment|

Now, in the console application, we can provide values for the parameters in the `App.config` file as follows.

{% highlight C# %}  
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="subscriptionId" value="<subscription-id>"/>
    <add key="apiVersion" value="2014-02-01"/>
    <add key="certificateThumbprint" value="E6F9B4D8553364B55A99ADF7C29C852F86EAFEA8"/>
    <add key="cloudServiceName" value="SKClassicVM4685"/>
    <add key="classicDeploymentDetailURL" 
         value="https://management.core.windows.net/{0}/services/hostedservices/{1}/deploymentslots/production"/>
  </appSettings>
</configuration>
{% endhighlight %}
**Note:**
I have not included the actual `subscriptionId` value for security reasons, but you will have to include it here.
The `apiVersion` value is usually provided on the Azure reference documentation for each of the APIs. Use the one documented there.

The `certificateThumbprint` value can be obtained as follows.
Open the **SundeepClientCert.cer** certificate file by double clicking on it, go to the details tab and click on the `thumbprint` field.

![Thumbprint value in Certificate]({{site.url}}/static/img/blogs/CertificateThumbprint.jpg)  

This will display the thumbprint value. Copy it and then format it to remove the spaces in between.
This would now look like an alphanumeric value as `E6F9B4D8553364B55A99ADF7C29C852F86EAFEA8`. (This value would probably be different in your case.)
Update the same in the `App.config` file as shown above.
With this we now have all the parameters ready for our API consumption.

##### Step 5: Consume the ASM API using the client certificate and other parameters

The main method in Program.cs would consume the deployment API as follows...

{% highlight C# %}
using System;
using System.Configuration;

namespace AzureClassicVMIntegration
{
    class Program
    {
        static void Main(string[] args)
        {
            //This would get the client certificate from the certificate store of current user.
            var clientCertificate = CertificateStore.GetManagementCertificateFromStore(
                    ConfigurationManager.AppSettings[Constants.CertificateThumbprint]);

            //This prepares the url for getting the deployment details of classic VM
            string url = string.Format(ConfigurationManager.AppSettings[Constants.ClassicDeploymentDetailsUrl],
                                        ConfigurationManager.AppSettings[Constants.SubscriptionId],
                                        ConfigurationManager.AppSettings[Constants.CloudServiceName]);
            
            //We pass the url along with the API version and the client certificate to get the deployment details.
            //Client certificate is used to authenticate the request.
            string response = AzureRestClient.executeRequest(url,
                                ConfigurationManager.AppSettings[Constants.ApiVersion],
                                clientCertificate);

            Console.WriteLine(response);
        }
    }
}
{% endhighlight %}

The code for the most part is self explanatory.  
* Fetch the client certificate from the certificate store using the certificate thumbprint.  
* Prepare the Classic VM deployment url using the various parameters.  
* Call the REST API with the client certificate and capture the response.  

**Note:**  
* The response of ASM APIs is xml.
* The response of ARM APIs is json.

The code snippet to fetch the client certificate from the certificate store is as follows...
{% highlight C# %}
using System;
using System.Security.Cryptography.X509Certificates;

namespace AzureClassicVMIntegration
{
    public class CertificateStore
    {
        public static X509Certificate GetManagementCertificateFromStore(string certificateThumbprint)
        {
            X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            certStore.Open(OpenFlags.ReadOnly);

            X509Certificate2Collection certCollection =
                certStore.Certificates.Find(X509FindType.FindByThumbprint, certificateThumbprint, false);

            certStore.Close();

            if (certCollection.Count == 0)
            {
                throw new InvalidOperationException("The certificate with thumbprint {0} was not found in the My store of the current user");
            }

            return certCollection[0];
        }
    }
}
{% endhighlight %}

Finally, the code for the REST client used to consume the API is

{% highlight C# %}
using System.IO;
using System.Net;
using System.Security.Cryptography.X509Certificates;

namespace AzureClassicVMIntegration
{
    public class AzureRestClient
    {
        public static string executeRequest(string url, string apiVersion, X509Certificate clientCertificate)
        {
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(url);

            request.Headers.Add("x-ms-version", apiVersion);
            request.ContentType = "application/xml";
            request.ClientCertificates.Add(clientCertificate);

            WebResponse response = request.GetResponse();
            string responseContent = string.Empty;

            using (Stream responseStream = response.GetResponseStream())
                using (StreamReader sr = new StreamReader(responseStream))
                {
                    responseContent = sr.ReadToEnd();
                }

            return responseContent;
        }
    }
}
{% endhighlight %}
Once you get the response, you can parse the xml and extract the relevant data from it.
You may also download the source code for this from my [github repository](https://github.com/sundeepkamath/AzureClassicVMIntegration). 

In this post I have just shown one instance of how you can consume the ASM based REST APIs for classic VMs. 
Do explore the Azure Reference documentation which lists a number of other APIs that can be used. In all cases the code remains the same, only the REST API url and its dependent parameters would change.

Hope this was useful!


