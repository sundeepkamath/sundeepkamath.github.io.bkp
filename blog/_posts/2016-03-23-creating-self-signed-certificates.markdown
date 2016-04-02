---
layout: post
title:  "Creating self-signed certificate using makecert.exe"
date:   2016-03-23 17:48:25 +0530
categories: Certificates
---
I've had to create self-signed certificates on quite a few occasions over the years.  
There are multiple scenarios when one might want to create these self-signed certificates.
Two of the most popular tools used for certificate generation are...  
1. **openssl** (on windows and linux)  
2. **makecert** (on windows)

I'll cover the usage of **makecert.exe** in this post.  

#### **Where to get makecert.exe**  

* [Windows SDK](https://dev.windows.com/en-us/downloads/sdk-archive)  
	IF you have Windows SDK installed, based on the version that you have installed you can find makecert.exe at one of the following locations...  
    
    |Version | Location |  
    |--------|--------|  
    |7|C:\Program Files (x86)\Microsoft SDKs\Windows\v7.1A\bin|  
    |8|C:\Program Files (x86)\Windows Kits\8.0\bin|  
    |8.1|C:\Program Files (x86)\Windows Kits\8.1\bin|  
    |10|C:\Program Files (x86)\Windows Kits\10.0\bin|  
    | | |  
        
    
* [Visual Studio](https://www.visualstudio.com) (if Visual Studio IDE is installed)  
    In case you are already using Visual studio you will find makecert.exe at one of the following locations...  

    | Version | Location |
    |--------|--------|
    |2015|C:\Program Files (x86)\Windows Kits\10.0\bin|
    |2013|C:\Program Files (x86)\Windows Kits\8.1\bin|
    |2010|C:\Program Files\Microsoft SDKs\Windows\v7.0A\bin\|
    |2008|C:\Program Files\Microsoft SDKs\Windows\v6.0A\bin\| 
    |||  
     

Usually certificates are generated for **enabling HTTPS** on the **web server**. The other reason is for **client authentication**.  
I'll cover both these cases  
    1. **server certificates**  
    2. **client certificates**  

We'll also create Root CA certificates for signing both these certificates.  

Say, suppose I have a company named **FunSoft** which is working on a new cloud service offering called **FunSoft Cloud Service**.  

#### **Root CA certificate:**  

{% highlight powershell %}
makecert.exe -r 
             -n "CN=FunSoft Root Authority,O=FunSoft,OU=Development,L=Pune,S=MH,C=IN" 
             -pe 
             -ss Root 
             -sr LocalMachine 
             -sky signature 
             -m 120 
             -a sha256 
             -len 2048 
{% endhighlight %}

Switch|Usage  
------|:----  
**r**|Mark the certificate as self-signed.  
**n**|Certificate subject name; starts with “CN=”. An example value is “CN=Test Certificate”.  
**pe**|Switch to mark the generated private key as exportable.  
**ss**|Certificate store name. Most common options are [AuthRoot/CA/My/Root]  
**sr**|Certificate store location. Valid options are [CurrentUser/LocalMachine]. Default to ‘CurrentUser’  
**sky**|Subject key type. Valid options are [signature/exchange/[integer]].  
**m**|Number of months for the certificate validity period.  
**a**|Signature algorithm. Valid options are [md5/sha1/sha256/sha384/sha512]. Default to ‘sha1’.  
**len**|Generated Key Length (Bits). An example value is 2048.   
|  



#### **Note:**  


|Abbreviation|Full form|Example|  
|------------|:---------|:-------|  
|C|Country|IN -> India|  
|S|State|MH-> Maharashtra|  
|L|Locality|Pune|  
|O|Organization|FunSoft|  
|OU|OrganizationalUnit|Development|  
|CN|Common Name|FunSoft Root Authority|    
||||  



You will also find this in the certificates snap-in at  
Certificates(Local Computer) **=>** Trusted Root Certification Authorities **=>** Certificates  

#### **Server certificate signed with Root CA**  

We will now create a server certificate signed with the Root CA certificate created above...  


{% highlight powershell %}
makecert -pe 
         -n "CN=*.funsoft.com" 
         -a sha256 
         -len 2048 
         -sky exchange 
         -eku 1.3.6.1.5.5.7.3.1 
         -sp "Microsoft RSA SChannel Cryptographic Provider" 
         -sy 12 
         -in "FunSoft Root Authority" 
         -is Root 
         -ir LocalMachine 
         -ss My 
         -sr LocalMachine 
         -m 13
         funSoftServerCert.cer 
{% endhighlight %}


|Switch|Usage|
|--------|:--------|
|**pe**|Switch to mark the generated private key as exportable.|
|**n**|Certificate subject name; starts with “CN=”. An example value is “CN=Test Certificate”.|
|**a**|Signature algorithm. Valid options are [md5/sha1/sha256/sha384/sha512]. Default to ‘sha1’.|
|**len**|Generated Key Length (Bits). An example value is 2048.|
|**sky**|Subject key type. Valid options are [signature/exchange/[integer]].|
|**eku**|Comma separated Enhanced Key Usage based on Microsoft’s Object IDs (OIDs)|
|**sp**|Subject’s CryptoAPI provider’s name|
|**sy**|Subject’s CryptoAPI provider’s type|
|**in**|Issuers certificate common name|
|**is**|Issuers certificate store name|
|**ir**|Issuers certificate store location|
|**ss**|Certificate store name. Most common options are [AuthRoot/CA/My/Root]|
|**sr**|Certificate store location. Valid options are [CurrentUser/LocalMachine]. Default to ‘CurrentUser’|
|**m**|Number of months for the certificate validity period.|  
|||  



#### **Note:**  

|EKU|OID|Use|
|:--------|:--------|:------|
|serverAuth|1.3.6.1.5.5.7.3.1|SSL/TLS Web Server Authentication|
|clientAuth|1.3.6.1.5.5.7.3.2|SSL/TLS Web Client Authentication|
|codeSigning|1.3.6.1.5.5.7.3.3|Code signing|
|emailProtection|1.3.6.1.5.5.7.3.4|E-mail Protection (S/MIME)| 
||||  
 


#### **Client certificate signed with Root CA**  

We can also create a client certificate for client authentication as follows...  


{% highlight powershell %}
makecert -pe 
         -n "CN=SUN" 
         -a sha256 
         -len 2048 
         -sky exchange 
         -eku 1.3.6.1.5.5.7.3.2 
         -sp "Microsoft RSA SChannel Cryptographic Provider" 
         -sy 12 
         -in "FunSoft Root Authority" 
         -is Root 
         -ir LocalMachine 
         -ss My 
         -sr LocalMachine 
         -m 13
         funSoftClientCert.cer 
{% endhighlight %}


Observe that the only value we have changed here is **eku** and **CN**.  


Now, one thing to note here is that you could issue client certificates with CN value scoped at  
* **per machine** or  
  The CN value can be the machine name (you could also have the machine FQDN if your machine is part of a domain).  
* **per user**  
  In this case you could have the user name in CN and set the **-sr** switch to **CurrentUser**.  
    
  


