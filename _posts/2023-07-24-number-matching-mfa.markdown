---
layout: post
title:  "Enable Number Matching for Azure MFA"
date:   2023-07-23 00:01:01 -0500
categories: Azure AD; MFA; Azure Active Directory
---

This is a short guide one how to enable number matching in Azure Active Directory. Number Matching is [Microsoft's latest step in combating MFA fatigue attacks][cisa-mfa].

### Enabling in Azure Portal
1) Log into portal.azure.com with your priviledged account and open Azure Active Directory.

2) On the left side, select Security

![2023-07-24(1).png][Security]

3) Then under Manage, click Authentication Methods

![2023-07-24(2).png][Security2]

4) Under Authentication Methods, choose Microsoft Authenticator

![2023-07-24(3).png][Security3]

5) Now comes the time to configure or re-configure the actual policy. What you have already set may differ from my screenshots. In my example I am creating forcing Microsoft Authenticator to a test Azure AD group. Note that the option to not use number matching is actually disabled, as Microsoft is already enforcing this.

![2023-07-24(4).png][Security4]

![2023-07-24(5).png][Security5]

### Testing
Now, with the test user you enabled the policy for, log out and back into something (Office 365, Azure, etc). You should be prompted with a randomly generated number on the application you are signing into. Additionally, you should receive a notification on your mobile device. This will include the text box where you enter the number, along with the geographical location of the sign-in and the application name. If you don't get prompted to MFA, you may need to wait a while or lower your token expiry length.

<!--URL Block-->
[cisa-mfa]: https://www.cisa.gov/sites/default/files/publications/fact-sheet-implement-number-matching-in-mfa-applications-508c.pdf

<!--Picture Block-->
[Security]: /img/2023-07-24(1).png "Manage"
[Security2]: /img/2023-07-24(2).png "Authentication Methods"
[Security3]: /img/2023-07-24(3).png "Microsoft Authenticator"
[Security4]: /img/2023-07-24(4).png "Microsoft Test Group"
[Security5]: /img/2023-07-24(5).png "Number Matching Enforced"