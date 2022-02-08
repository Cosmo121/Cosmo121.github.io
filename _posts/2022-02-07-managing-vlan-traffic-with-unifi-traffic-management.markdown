---
layout: post
title:  "Managing VLANs with Unifi Traffic Management"
date:   2022-02-07 21:41:07 -0500
categories: Unifi; Ubiquiti; VLAN
---

Quick guide on managing traffic restrictions easily in the new user interface in Unifi OS. This guide was made with Unifi Network version 7.0.20. Unifi changes their UI constantly. Keep that in mind if the screenshots do not align with your console.


### Traditional Way with Firewall Rules
Creating Accept and Drop rules in the Firewall rules section under Firewall & Security
![2022-02-07(1)](/img/2022-02-07(1).png "Firewall Rules")

### A Different Approach, with Traffic Management
Just like with the firewall section, in traffic management you will create rules. Traffic management rules can have a bit more logic in them and the UI may make it easier for you to validate what you are allowing/dropping.

![2022-02-07(3)](/img/2022-02-07(3).png "Traffic Management")

### Requirements
You will have to allow these two items, device identification and traffic identification. You may be able to get around the first one, you would just be limited on the type of rules you can create.

![2022-02-07(6)](/img/2022-02-07(6).png "Enable these two items")

### Example: Restricting a Security Camera's VLAN
Here I have four rules that restrict traffic to and from the VLAN for my security cameras. I am using Synology's Surveillance Station software (which I love), and only want the cameras to have visibility to my Synology NAS, which doesn't live on the same subnet.

![2022-02-07(2)](/img/2022-02-07(2).png "Simple Rules List")

Example of one of the rules that allows specific IPs to communicate with my NAS:
![2022-02-07(4)](/img/2022-02-07(4).png "Example Rules")


And just for kicks, you can block specific apps. You can even apply the rule to a specific device:
![2022-02-07(5)](/img/2022-02-07(5).png "Block the Facebooks")

### Documentation:
[UniFi Network Docs][unifi_network_docs]

[unifi_network_docs]: https://help.ui.com/hc/en-us/categories/200320654