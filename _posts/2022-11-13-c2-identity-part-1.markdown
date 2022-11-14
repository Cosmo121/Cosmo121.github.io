---
layout: post
title:  "Synology C2 Identity Integration with Active Directory"
date:   2022-11-13 20:28:00 -0500
categories: Synology; Synology C2; Active Directory
---

Synology just sent out an email that their [C2 Identity service][c2-identity] can now sync with Active Directory. Let's give it a try!

## It's free (for 250 users) - Synology Marketing Materials

![2022-11-13][def2]

![2022-11-13(2)][def]

## Installing the Agent

Once you sign up for the service with your Synology account and pick a domain name, you can download the C2 Identity AD Sync application.

![2022-11-13(3)][def3]

Run throught the install wizard.

![2022-11-13(4)][def4]

![2022-11-13(5)][def5]

![2022-11-13(6)][def6]

![2022-11-13(7)][def7]

## Agent Setup

This is what I am getting on first launch, after the install completes

![2022-11-13(8)][def8]

A reboot does not help. But whoops, turns out is it that you just need to make sure you right-click "Run as administrator". After doing so, you will be prompted for your C2 Identity connect key, which you can copy/paste from your C2 Identity dashboard.

![2022-11-13(9)][def9]

After submitting your connect key, you are prompted to approve the connection in the C2 Identity admin portal

![2022-11-13(10)][def10]

Back in the admin portal, you will see the Approve option, along with the FQDN of your domain controller

![2022-11-13(11)][def11]

Once approved, the directory will automatically start syncing

![2022-11-13(12)][def12]

![2022-11-13(13)][def13]

I'm going to let this sync run and i'll be back to explore more of what Synology's C2 Identity Active Directory integration has to offer.

[def]: /img/2022-11-13(2).png "Synology Marketing Materials"
[def2]: /img/2022-11-13.png "Synology Marketing Materials"
[def3]: /img/2022-11-13(3).png "Download the application"
[def4]: /img/2022-11-13(4).png "Application Install Wizard"
[def5]: /img/2022-11-13(5).png "Application Install Wizard"
[def6]: /img/2022-11-13(6).png "Application Install Wizard"
[def7]: /img/2022-11-13(7).png "Application Install Wizard"
[def8]: /img/2022-11-13(8).png "Launch Error"
[def9]: /img/2022-11-13(9).png "Identity connect key"
[def10]: /img/2022-11-13(10).png "Approval required"
[def11]: /img/2022-11-13(11).png "Approve in Identity admin portal"
[def12]: /img/2022-11-13(12).png "Success!"
[def13]: /img/2022-11-13(13).png "Directory sync"

[c2-identity]: https://c2.synology.com/en-global/identity/overview