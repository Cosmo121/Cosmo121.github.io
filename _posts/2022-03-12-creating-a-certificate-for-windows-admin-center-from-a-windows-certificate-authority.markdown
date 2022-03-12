---
layout: post
title:  "Creating a Certificate for Windows Admin Center from a Windows Certificate Authority"
date:   2022-03-12 17:22:00 -0500
categories: WindowsAdminCenter; WAC; Certificates
---

In my 2022-01-12 post I set up Windows Admin Center (WAC) on a gateway server and used a self-signed certificate for it. I also pushed that self-signed certificate out with Group Policy so that other clients could connect to it and see that it is a trusted site. This was a super simple way to set it up and worked just fine. However, it wouldn't be the best way to do it if we want to keep using WAC or were using WAC in an enterprise environment. I really like the toolsets within WAC so I plan on keeping it around for a while. With that in mind, lets identify and fix some of the negatives about the way we set it up.

1) Our self-signed certificate is only valid for 60 days (this is a limitation that Microsoft has configured within WAC)
<br/>

2) When we create a new certificate after the 60 day period, we need to push that new certificate out all over again

So, lets reconfigure it. Lets: 

1) Create a new certificate template with Windows Active Directory Certificate Services
<br/>

2) Have the gateway server where WAC resides request a new certificate using that template
<br/>

3) Configure WAC to use our new certificate (with a two year validity period!)

### Step 1 - Create a new certificate with Windows Active Directory Certificate Services

In AD CS, right-click on Certificate Templates and choose Manage

![2022-03-12(1)](/img/2022-03-12(1).png "Right-Click Certificate templates and choose Manager")


Right-click Web Server and choose Duplicate Template

![2022-03-12(2)](/img/2022-03-12(2).png "Right-click Web Server and choose Duplicate Template")


On the General tab, change the Template display name and Template name to something related to Windows Admin Center. You can also change the validity period here

![2022-03-12(3)](/img/2022-03-12(3).png "Change the template display name and template name")


On the Security tab, change the permissions for Authenticated Users to Full Control. Click OK

![2022-03-12(4)](/img/2022-03-12(4).png "Change the permissions for Authenticated Users to Full Control")


Back on the Certificate Templates snap-in, right-click Certificate Templates and choose New -> Certificate Template to Issue. Choose the template that you just created and click OK

![2022-03-12(5)](/img/2022-03-12(5).png "Right-click Certificate Templates and choose new certificate template to issue")


### Step 2 - Have the gateway server where WAC resides request a new certificate using that template

Your template will now appear in the Certificate Templates section. We can now go back to our WAC gateway server and launch the certlm.msc snap-in there. On our gateway server right-click Certificates in the Personal store and choose All Tasks -> Request New Certificate

![2022-03-12(6)](/img/2022-03-12(6).png "On our gateway server, request a new certificate")


The first screen in the Certificate Enrollment window will be to choose the enrollment policy. We can leave it as the default (Active Directory Enrollment Policy) and click Next. The next screen will show us all the certificates that are available to us. We should see our Windows Admin Center template here and also a yellow triangle saying that we need to provide more information in order to enroll a certificate from it. Click the blue text to launch Certificate Properties

![2022-03-12(7)](/img/2022-03-12(7).png "Our new template will become available and prompt us to provide more information")


In Certificate Properties, on the Subject tab, change the dropdown for Subject name to Common name. Type your gateway server's hostname and click add. In the Alternative name dropdown choose DNS and type your server's FQDN (serverName.domain.com). Be sure to click Add to move both values into the right-hand boxes.

![2022-03-12(8)](/img/2022-03-12(8).png "Provide values for subject name and alternative name")


On the General tab, type an appropriate name for the certificates friendly name. Click OK

![2022-03-12(9)](/img/2022-03-12(9).png "Type an appropriate friendly name for the certificate")


After we click OK we can see that the prompt that we need to provide more information is gone. We can now select our Windows Admin Center certificate and click Enroll. That is it! You should see a sucessful status message and also be able to see your new certificate in our gateway server's personal certificate store

![2022-03-12(10)](/img/2022-03-12(10).png "Success!")


Now that we have the certificate created, we need to copy the certificate's thumbprint. Double-click the certificate and go to the details tab. Make sure Show is set to <all> and scroll all the way to the bottom. Click on the second to last field, called Thumbprint. Copy the thumbprint, we will need it in the next step

![2022-03-12(11)](/img/2022-03-12(11).png "Copy the new certificate's thumbprint")


### Step 3 - Configure WAC to use our new certificate

On the gateway server, open Control Panel and click Change. Click Next and then Change. On the Configure Gateway Endpoint screen, select the second option of using an SSL certificate installed on this computer. In the text box paste the thumbprint we got from the previous step. Click Change

![2022-03-12(12)](/img/2022-03-12(12).png "Paste our new certificate thumbprint")


Now re-launch Windows Admin Center and check on the certificate. No more renewing every 60 days!

![2022-03-12(13)](/img/2022-03-12(13).png "Check out our new certificate!")