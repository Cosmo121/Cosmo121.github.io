---
layout: post
title:  "Backing up the local Chia Database with Veeam"
date:   2022-01-16 14:23:07 -0500
categories: Chia; Veeam; Backups
---

### Intro
If you have been farming with the Chia full node client for long enough, you may have ran into the dreaded 'database corruption' problem. It can happen to both Linux and Windows users, and can cause you to lose DAYS of farming time. Personally, this has happened twice to me. Once was my fault, a system crash caused a reboot with the client open. But the second time I have no idea what happened. I looked at my sent partials and noticed quite a time gap. I spent the next two or three days re-syncing the entire blockchain database. There has got to be a better way!
<br/>
<br/>
<br/>
### The Better Way
There are lots of solutions to get your client synced up as quick as possible. Windows users are setting up restore points with the built-in Windows OS backup tools. Others are manually copy/pasting the database to a thumb drive or another disk every week or so. There is even a website where you can download the Chia blockchain (seemingly defeating the purpose of decentralization?). So let's take it to a bit of a higher level. Let's use an enterprise backup tool and set up an automated backup of our precious Chia database...
<br/>
<br/>
<br/>
### The Tools
The backup tool I am using may be enterprise level, but there is actually a community version that can be had for free. I am using Veeam Backup & Replication Community Edition. Setup documentation and download link here: [https://www.veeam.com/virtual-machine-backup-solution-free.html][veeam-link]
<br/>
<br/>
<br/>
### Create the Backup Job
Once you have a fully functioning instance of Veeam (including a service account with permissions on your Chia node!), we can proceed to set up our backup job

First, create a new job. Select Workstation as the Type. By default the mode will be Manged by Agent:
![2022-01-16(1)](/img/2022-01-16(1).png "Create a new job and select workstation")
<br/>
<br/>
Name our new job whatever you like:
![2022-01-16(2)](/img/2022-01-16(2).png "Name your new job")
<br/>
<br/>
Now select your Chia node. You can select it by choosing individual computer and then provide the host name or URL. You will also need to provide credentials that your Veeam instance can use to authenticate to your Chia node. This can be a domain account that has administrative priviledges or a local account that you have created:
![2022-01-16(3)](/img/2022-01-16(3).png "Provide the hostname or IP address of your chia node, and an admin account")
<br/>
<br/>
On the Backup Mode screen, we want to select File level backup, since we are only backing up the Chia database. You can however, backup the entire computer, just make you sure you aren't backing up your Chia plots:
![2022-01-16(4)](/img/2022-01-16(4).png "Choose File level backup")
<br/>
<br/>
Choose a specific file system object, and enter the path to your Chia database. If you have an older Chia node your file name may differ. So be sure to verify your own environment. I find it in good practice to backup the entire db folder, such as:$userprofile\.chia\mainnet\db\
![2022-01-16(5)](/img/2022-01-16(5).png "Enter your Chia database file path")
<br/>
<br/>
For the destination, you will want to choose one of your Veeam backup repositories. Creating these are outside the scope of this guide, but you should have set these up when you configured your Veeam instance. Alternatively, you may have configured an SMB share or someone other repo. If so, be sure to choose it. For my envrionment, I have a backup repo set up on a NAS device:
![2022-01-16(6)](/img/2022-01-16(6).png "Select where you will store the backups")
<br/>
<br/>
The Backup Server should autopopulate but if it does not, enter the FQDN (ex. VeeamServer.domain.com) or IP of your Veeam host:
![2022-01-16(7)](/img/2022-01-16(7).png "Enter the backup server FQDN or IP")
<br/>
<br/>
On the Storage screen, specify the backup repo you want to use. If you selected a different backup option in the previous screens, your choices will differ here. We will also choose a backup retention time. I am doing 7 days, but you may do shorter or longer:
![2022-01-16(8)](/img/2022-01-16(8).png "Select your backup repo and set your retention period")
<br/>
<br/>
We will not be configuring a backup cache, so the next screen can be skipped.
<br/>
<br/>
For the backup schedule, we can do backups nightly, or whenever you feel is best. Remember that we are only backing up the database (around 40 GB at time of writing) so the backup time period will be pretty short:
![2022-01-16(9)](/img/2022-01-16(9).png "Set your backup schedule")
<br/>
<br/>
Lastly is the summary screen. Review your settings and click Finish to complete backup job creation.
<br/>
<br/>
You can always edit the job after you create it. You can also go into the advanced settings and look at some of the other settings such as doing full period backups once a week:
![2022-01-16(10)](/img/2022-01-16(10).png "Take a look at the advanced settings options")
<br/>
<br/>
Another useful thing is Veeam's reporting features. You can set up email notifications:
![2022-01-16(11)](/img/2022-01-16(11).png "Veeam email notifications feature")
<br/>
<br/>
How a daily report looks. This can be useful if you multiple full nodes:
![2022-01-16(12)](/img/2022-01-16(12).png "Example Veeam job report")
<br/>
<br/>
### What would a restore look like?
So let's say the unthinkable happens, and the dreaded database corruption issue happens. What does the process of restoring your last known-good database look like? With Veeam it is super simple. First, close your Chia client so that it isn't trying to read/write to your database. Second, right click your Veeam agent on your Chia node and choose Restore -> Individual files:
![2022-01-16(13)](/img/2022-01-16(13).png "Open up the restore point menu in the Veeam agent")
<br/>
<br/>
The File Level Restore menu will open, showing you all the available restore points. Choose the last known-good restore point to begint he restore process. Once the file has been restored you can re-open the Chia client. Your client will begin syncing blocks that are newer than the restore point you chose:
![2022-01-16(14)](/img/2022-01-16(14).png "Select the last known-good restore point")
<br/>
<br/>

### Conclusion
We now have a nightly backup job running for our Chia database. If we have another database corruption issue, we have a couple very simple steps to follow to restore our database to a previous height. That means that as long as we are monitoring our client, we should only have to sync a days worth of work at the very most!



<!--URL Block-->
[veeam-link]: https://www.veeam.com/virtual-machine-backup-solution-free.html