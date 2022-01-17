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
There are lots of solutions to get your client synced up as quick as possible. Windows users are setting up restore points with the built-in Windows OS backup tools. Others are manually copy/pasting the database to a thumb drive or another disk every week or so. There is even a website where you can download the Chia blockchain (defeating the purpose of decentralization).