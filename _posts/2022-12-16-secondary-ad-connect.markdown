---
layout: post
title:  "Setting up a Secondary Azure AD Connect Server in Staging Mode"
date:   2022-12-16 20:28:00 -0500
categories: Azure AD; Azure Active Directory; Active Directory
---

If you are already using Azure Active Directory Connect to sync your on-premise Active Directory to Azure AD, then you should set up a secondary server for a backup. This second server can run in 'staging' mode, which means it can still be active but it will not sync any changes with Azure AD. If your primary sync server were to run into issues, you can easily flip your second sync server into active mode.

## Agent Install

Download the latest agent on a second domain controller: [Download Link][mcsft]

Launch the installer, accept the license terms and click continue

![2022-12-16][adc1]

Clicking customize will show you some advanced settings. For this example, I am going to stick with the express settings

![2022-12-16][adc2]

Enter the credentials for an Azure AD global admin account

![2022-12-16][adc3]

Enter the credentials of an AD enterprise admin account

![2022-12-16][adc4]

On the final screen, leave the "Start the synchronization process..." option unchecked

![2022-12-16][adc5]

## Configuring Staging Mode

Reopen Azure AD Connect, and choose Configure

![2022-12-16][adc6]

Select the Configure staging mode task and click Next

![2022-12-16][adc7]

Check "Enable staging mode" and click Next

![2022-12-16][adc8]

On the final screen, this time you do want to check the option to start the sync process. Microsoft's documentation explains why: "*It is recommended to leave the sync process on for the server in Staging Mode, so if it becomes active, it will quickly take over and won't have to do a large sync to catch up to the current state of the AD/Azure AD sync.*"

![2022-12-16][adc9]

Done!

![2022-12-16][adc10]

[mcsft]: https://www.microsoft.com/en-us/download/details.aspx?id=47594

[adc1]: /img/2022-12-16(1).png "Azure AD Connect Installer"
[adc2]: /img/2022-12-16(2).png "Azure AD Connect Installer"
[adc3]: /img/2022-12-16(3).png "Azure AD Connect Installer"
[adc4]: /img/2022-12-16(4).png "Azure AD Connect Installer"
[adc5]: /img/2022-12-16(5).png "Azure AD Connect Installer"
[adc6]: /img/2022-12-16(6).png "Azure AD Connect Installer"
[adc7]: /img/2022-12-16(7).png "Azure AD Connect Installer"
[adc8]: /img/2022-12-16(8).png "Azure AD Connect Installer"
[adc9]: /img/2022-12-16(9).png "Azure AD Connect Installer"
[adc10]: /img/2022-12-16(10).png "Azure AD Connect Installer"