---
layout: post
title:  "Microsoft LAPS for Homelab"
date:   2023-09-07 00:01:01 -0500
categories: Active Directory; LAPS; Windows Server
---

I recently rolled out laps for some of my homelab servers. With LAPS enabled, I no longer have to keep a list of local administrator passwords for my Windows servers. It also prevents you from having one local administrative password across all your VMs.

# Prereqs
First, LAPS is a newer feature and only supported on newer OS versions. See: [Supported Platforms][sp]

This one is kind of obvious, but the VMs must be either Azure Active Directory or Active Directory joined (or hybrid).

DFL of 2016 or higher.

# Implementation

My devices are domain joined so I will be proceeding with the steps for that scenario.

1) First, I made sure my account was in the schema admins group.

2) Then I can run the schema extensions command from one of my 2019 or 2022 domain controllers

```powershell
Update-LapsADSchema -Verbose
```

3) I also created a test OU for this. I then created a LAPS GPO and applied it to the OU (make sure you move at least one test VM to that OU)

![2023-09-07(1).png][LAPS1]

4) Lastly, I had to grant permission for LAPS to reset the local passwords. In the PowerShell line below, "Laps Test" is the name of my test OU.

```powershell
Set-LapsADComputerSelfPermission -Identity "Laps Test"
```

# Testing and Verification
Now that everything is set up and has had time to sync, I can test it out and make sure I am rotating the local administrator password on one of my test servers.

On one of my domain controllers I ran the following command to get the password of my test server named "xcpconfigmgr"

```powershell
Get-LapsADPassword -Identity xcpconfigmgr -AsPlainText
```

And the output I got

![2023-09-07(2).png][LAPS2]

I can then test that password by logging in as the local admin account.

If you run into any issues, take a look at Event Viewer

![2023-09-07(3).png][LAPS3]

<!--URL Block-->
[sp]: https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-overview#windows-laps-supported-platforms-and-azure-ad-laps-preview-status

<!--Picture Block-->
[LAPS1]: /img/2023-09-07(1).png "GPO"
[LAPS2]: /img/2023-09-07(2).png "PowerShell output"
[LAPS3]: /img/2023-09-07(3).png "Event Viewer"