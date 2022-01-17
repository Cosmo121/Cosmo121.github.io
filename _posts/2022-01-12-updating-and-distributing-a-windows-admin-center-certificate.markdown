---
layout: post
title:  "Updating and distributing a Windows Admin Center certificate"
date:   2022-01-12 22:26:07 -0500
categories: Windows Admin Center; Certificates
---

### Intro
Today I am going to show you how to renew your Windows Admin Center certificate, as well as how to distribute that certificate with Group Policy. For those that do not know, Windows Admin center is an awesome tool that allows you to manage, configure and troubleshoot Windows servers and PCs. The best part, in my opinion, is that not only is it completely free, but you can run it in a browser window on any PC.
<br/>
<br/>
<br/>

### Problem Statement
The issue we are trying to solve today is that while Windows Admin Center works great on our gateway server that we stood up, but any other endpoint accessing this gateway gets a cert error.
<br/>
<br/>
![2022-01-12(1)](/img/2022-01-12(1).png "Certificate working correctly on local host")
##### Certificate working correctly on local host
<br/>
<br/>
![2022-01-12(2)](/img/2022-01-12(2).png "Certificate error when connecting to the WAC on another server")
##### Certificate error when connecting to the WAC on another server
<br/>
<br/>

### Why is this happening?
This is happening because of the way we set up Windows Admin Center. When installing the application we have the option to select an SSL certificate that we (the administrator) provide. This would be the ideal way to configure Windows Admin Center, but it would require us to use a certificate from either a public certificate authority or create one in an internal certificate authority. Now in my lab environment, I don’t want to pay for a public SSL certificate, and I don’t have an internal CA. But I can still solve this problem by using a self-signed certificate and then distributing it to our endpoints with Group Policy.
<br/>
<br/>
<br/>

### Self-signed Certificate
First thing we have to do is verify that we have a valid Windows Admin Center self-signed certificate. Now, it would be pretty obvious if we didn’t, as we would be getting a cert error even on our Windows Admin Center gateway. But let’s check our certificate and verify its validity period.
<br/>
<br/>
![2022-01-12(3)](/img/2022-01-12(3).png "Confirm the certificate is valid")
##### Confirm the certificate is valid
<br/>
<br/>
![2022-01-12(4)](/img/2022-01-12(4).png "Check certlm.msc for the published Windows Admin Center certificate")
##### Check certlm.msc for the published Windows Admin Center certificate
<br/>
<br/>


### Renewing Windows Admin Center Self-signed Certificate
If we do need to renew the certificate, it is super simple. We just need to launch the Windows Admin Center installer and select Change. This will give us the option to change out the certificate or create a new one.
<br/>
<br/>
![2022-01-12(5)](/img/2022-01-12(5).png "In the installer options, you can create a new certificate or view the thumbprint of the current one")
##### In the installer options, you can create a new certificate or view the thumbprint of the current one
<br/>
<br/>

### Exporting our Self-signed Certificate
Now that we have verified our certificate is valid, we need to export it so that we can distribute it to our other endpoints that will be using the Windows Admin Center on our gateway server.
<br/>
<br/>
![2022-01-12(6)](/img/2022-01-12(6).png "Right-click the WAC certificate, under All Tasks, choose Export")
##### Right-click the WAC certificate, under All Tasks, choose Export
<br/>
<br/>
On the next screen, choose to export the private key, leave the default format options checked, set a password, and then select a file name/location to export it. Once exported, move it to your domain controller where we will be creating the GPO.
<br/>
<br/>
![2022-01-12(7)](/img/2022-01-12(7).png "The exported certificate")
##### The exported certificate
<br/>
<br/>

### Creating the Group Policy Object to Distribute the Certificate
Now we need to create the GPO that will push our certificate to our endpoints. First we will connect to one of our domain controllers and open up the Group Policy Management Editor.
<!--image-->
<br/>
<br/>
<br/>
### Verify our Certificate has been Distributed to our Clients
Now that our GPO is created, we need to verify it has made it to our endpoints. First thing we can do is perform a manual Group Policy update on our client to save some time waiting for a sync. We can then open view our Trust Root Certificate store on the client to verify the Windows Admin Center certificate exists. Lastly, we will launch the Windows Admin Center in the browser and confirm we don’t have any errors.
<br/>
<br/>
![2022-01-12(8)](/img/2022-01-12(8).png "Right click the OU that will house your GPO and select Create a GPO...")
##### Right click the OU that will house your GPO and select Create a GPO. I named mine "Client Certificates"
<br/>
<br/>
![2022-01-12(9)](/img/2022-01-12(9).png "In the Group Policy Editor, look for Trust Root Certificate Authorities")
##### In the Group Policy Editor, look for Trust Root Certificate Authorities
<br/>
<br/>
![2022-01-12(10)](/img/2022-01-12(10).png "Right-click, choose Import, and browse to your exported WAC certificate")
##### Right-click, choose Import, and browse to your exported WAC certificate Note: You may need to change the file type filter to All Files
<br/>
<br/>
![2022-01-12(11)](/img/2022-01-12(11).png "Type in the password you chose earlier. Leave everything else as the defaults")
##### Type in the password you chose earlier. Leave everything else as the defaults
<br/>
<br/>
![2022-01-12(12)](/img/2022-01-12(12).png "Make sure the Trust Root Certificate store is selected")
##### Make sure the Trust Root Certificate store is selected
<br/>
<br/>
That is it. Click Finish and you will see the certificate show up in the policy you created.
<br/>
<br/>
Now to verify what we have done, go to a machine that is in the OU where you applied the GPO. Run GPUpdate /Force to kick off a group policy scan (this is done automatically at a variable time period, but kicking it off manually will save some time). Once the group policy update completes, launch certlm.msc and verify the Windows Admin Center certificate is now showing in the Trusted Root Certificate store.
<br/>
<br/>
![2022-01-12(13)](/img/2022-01-12(13).png "Open certlm.msc and verifiy the certificate is now there")
##### Open certlm.msc and verifiy the certificate is now there
<br/>
<br/>
Lastly, open a web browser on the machine and navigate to your WAC URL. Instead of the certificate warning we got previously, you should get a log-in prompt. Enter your credentials and look at our secure connection!
<br/>
<br/>
![2022-01-12(14)](/img/2022-01-12(14).png "Look, a padlock instead of a certificate error!")
##### Look, a padlock instead of a certificate error!
<br/>
<br/>
Missed a step? I have a full video on this process linked below.
<br/>
<br/>
<br/>
### References:
Microsoft - [Updating WAC certificate][updating-wac-certificate]
<br/>
Microsoft - [Distribute Certificates with Group Policy][distribute-certificates-with-group-policy]


Video for the visual learners: [Updating and distributing your Windows Admin Center Certificate][WAC]

[WAC]: https://youtu.be/OW2uxlbVIZs
[updating-wac-certificate]: https://docs.microsoft.com/en-us/windows-server/manage/windows-admin-center/deploy/install#updating-the-certificate-used-by-windows-admin-center
[distribute-certificates-with-group-policy]: https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/distribute-certificates-to-client-computers-by-using-group-policy