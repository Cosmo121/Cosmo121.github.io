---
layout: post
title:  "Testing Action1's Patch Management Solution"
date:   2025-11-08 00:00:02 -0500
categories: Action1; Patch Management; MDM
---

I'm currently testing a bunch of Windows server and workstation automation tools. All of these have at least some component of self-hosting, and can help with endpoint discovery, patching, hardware/software asset management and so much more. This week, I came across [Action1][action1mainpage].

### Sign-up and Setup
Action1 has a pretty generous free tier; you can manage up to 200 endpoints for free. Additionally, the sign up process, downloads and documentation are free and open, no sales demos or calls needed. Check out the full details of the free tier here: [https://www.action1.com/free-edition/][freeurl]

After creating an account, you are brought to an empty dashboard. Simply download and install the relevant agent for your OS (Windows or MacOS) and you will start seeing your dashboard populate with data. I have to say, it is quite a nice layout:

![act1_1][act1_1]

### Automating Agent Deployment
For SMBs, you won't want to be manuallly installing the Action1 agent on all your endpoints. Instead, you will want to use Action1 Deployer. The requirements for the deployer are pretty basic, check them out here: [https://www.action1.com/documentation/action1-deployer-recommended/][deployerdocs]. You will also need to set up a service account that will have admin permissions on the endpoints you plan on managing. This service account needs:

![act1_2][act1_2]

As recommended, I created a service account specific to Action1. I then added that service account to the AD group that I have added as an administrator on all workstations.

To run the deployer, I spun up a Server 2022 VM and launched the Action1 Deployer installer. After installation, the deployer launches a cmd window and prompts for your service account and its credentials. This is where I ran into my first issue. For the life of me, I could not get it to accept the service account's password. After trying everything I could think of, I ran across a [Reddit][redditpost] post of someone with the same issue. Apparently, it has to do with some password characters? Well, a reset of my service accounts password to...something else...fixed the issue, and I was on to the next speed bump. And that was that the Action1 service would not start. This one I didn't need Reddit for; it was because my service account didn't have permissions to start the service on my Deployer server. Maybe Action1 assumes that you will give it administrative permissions, or maybe I completely missed that requirement (I still can't find it), but adding the account as an admin on the server fixed the issue.

![act1_3][act1_3]

Now that the Deployer service is humming along, back in the Action1 console it is connected and ready for configuration. This configuration is pretty basic; what do you want it to push the agent to? Add in your OUs and exlude/include anything you want:

![act1_4][act1_4]

### Push some Updates
Now that the agent is installed on some workstations, I pushed a simple Microsoft Edge update to test:

![act1_5][act1_5]

![act1_6][act1_6]

![act1_7][act1_7]

![act1_8][act1_8]

Super simple. Select the update, set who gets it, schedule it, and watch it go. All in, it took about 10 mins for the update to hit the workstation and start installing.

### Push Some Apps
Now let's push a third-party application out. Here I am installing 7-Zip on a Windows 11 workstation:

![act1_9][act1_9]

![act1_10][act1_10]

![act1_11][act1_11]

![act1_12][act1_12]

![act1_13][act1_13]

![act1_14][act1_14]



[//]: # Pictures below
[act1_1]: /img/act1_1.png "Action1 Dashboard"
[act1_2]: /img/act1_2.png "Service Account Requirements"
[act1_3]: /img/act1_3.png "Deployer Account Setup"
[act1_4]: /img/act1_4.png "Agent Deployment Setup"
[act1_5]: /img/act1_5.png "Pushing an update"
[act1_6]: /img/act1_6.png "Pushing an update"
[act1_7]: /img/act1_7.png "Pushing an update"
[act1_8]: /img/act1_8.png "Pushing an update"
[act1_9]: /img/act1_9.png "Pushing an application"
[act1_10]: /img/act1_10.png "Pushing an application"
[act1_11]: /img/act1_11.png "Pushing an application"
[act1_12]: /img/act1_12.png "Pushing an application"
[act1_13]: /img/act1_13.png "Pushing an application"
[act1_14]: /img/act1_14.png "Pushing an application"

Just like with the Edge update, the third-party app push was simple and quick.

## Wrap-up
In just a few hours I was able to set up Action1 and its automated agent deployment tool. I really like the modern user interface of Action1's dashboard. It seems to have plenty of items in its toolbox and some decent reporting capabilities. I also like the option of using the Action1 deployer to ensure that any newly added domain devices will get the agent installed. This could be a really great add for SMBs who don't want to dive into another MDM tools licensing cost(Intune, JAMF, etc).


[//]: # Links below
[action1mainpage]: https://www.action1.com/
[redditpost]: https://www.reddit.com/r/Action1/comments/1iy130e/active_directory_deployer_password_character_issue/
[freeurl]: https://www.action1.com/free-edition/
[deployerdocs]: https://www.action1.com/documentation/action1-deployer-recommended/