---
layout: post
title:  "Install and Use Exchange Online Module v2 with Modern Authentication"
date:   2022-04-23 17:22:00 -0500
categories: O365; Office 365; Exchange Online
---

Haven't you heard? Basic auth is dead. Or at least [dying][basic-auth-deprecation]. Not only should you switch to modern authentication for Exchange Online, but you should be using the Exchange Online PowerShell V2 Module. Per Microsoft, "The module contains a small set of exclusive Exchange Online PowerShell cmdlets that are optimized for bulk data retrieval scenarios (think: thousands and thousands of objects)". Also, older cmdlets still work. See [here][exo-v2-cmdlets] for the full list of EXO V2 cmdlets.

### Install or Update
```PowerShell
# Check to see if you have the EOM Module installed already
Import-Module ExchangeOnlineManagement; Get-Module ExchangeOnlineManagement

# If yes, update it
Update-Module -Name ExchangeOnlineManagement -Scope CurrentUser

# If no, install it
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser

# Confirm installed
Import-Module ExchangeOnlineManagement; Get-Module ExchangeOnlineManagement
```

### Connect
```PowerShell
Connect-ExchangeOnline -UserPrincipalName username@domain.com -ShowProgress $true
```
That's it. We are now connected to EXO using modern authentication. We are also using the latest module and can use the new Get-EXO cmdlets. Below are some basics for managing an O365 environment. Some are exclusive to EXO V2 and some are not. Enjoy!

### Microsoft 365 Groups
```PowerShell
# Get info about M365 Group
Get-UnifiedGroup -Identity "Legal Department" | Format-List

# Add member(s)
Add-UnifiedGroupLinks -Identity "Legal Department" -LinkType Members -Links chris@contoso.com,michelle@contoso.com -confirm

# Add owner(s)
Add-UnifiedGroupLinks -Identity "Legal Department" -LinkType Owners -Links chris@contoso.com,michelle@contoso.com -confirm

# Remove member(s)
Remove-UnifiedGroupLinks -Identity "People Leaders" -LinkType Members -Links laura@contoso.com,julia@contoso.com -confirm

# Remove owner(s)
Remove-UnifiedGroupLinks -Identity "People Leaders" -LinkType Owners -Links laura@contoso.com,julia@contoso.com -confirm

# Create new
New-UnifiedGroup -accesstype Private -AutoSubscribeNewMembers -DisplayName "Group Name" -EmailAddresses groupname@contoso.com -Owner julia@contoso.com -Members chris@contoso.com,michelle@contoso.com -notes "Requested by Susan Suzzy"

# Add authorized sender(s)
Set-UnifiedGroup -Identity  "Group Name" -AcceptMessagesOnlyFrom @{add="email@contoso.com"} -confirm

# Auto subscribe new members
Set-UnifiedGroup -Identity  "Group Name" -AutoSubscribeNewMembers:$true

# Hide from GAL
Set-UnifiedGroup -Identity  "Group Name" -HiddenFromAddressListsEnabled:$true
```
#### Microsoft 365 Group Common Commands and References
[Get-UnifiedGroup][Get-UnifiedGroup],
[Add-UnifiedGroupLinks][Add-UnifiedGroupLinks],
[Remove-UnifiedGroupLinks][Remove-UnifiedGroupLinks],
[New-UnifiedGroup][New-UnifiedGroup],
[Set-UnifiedGroup][Set-UnifiedGroup]

[Get-UnifiedGroup]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-unifiedgroup
[Add-UnifiedGroupLinks]:https://docs.microsoft.com/en-us/powershell/module/exchange/add-unifiedgrouplinks
[Remove-UnifiedGroupLinks]:https://docs.microsoft.com/en-us/powershell/module/exchange/remove-unifiedgrouplinks
[New-UnifiedGroup]:https://docs.microsoft.com/en-us/powershell/module/exchange/new-unifiedgroup
[Set-UnifiedGroup]:https://docs.microsoft.com/en-us/powershell/module/exchange/set-unifiedgroup

### Dynamic Distribution Lists
```PowerShell
# List all dynamic distros
Get-DynamicDistributionGroup

# Get info about a specific dynamic distro
Get-DynamicDistributionGroup -Identity "all-Company" | Format-List

# list all members of a dynamic distro
$DD = Get-DynamicDistributionGroup "all-Company"; Get-Recipient -RecipientPreviewFilter $DD.RecipientFilter -OrganizationalUnit $DD.RecipientContainer

# Change A DD filter
Set-DynamicDistributionGroup -Identity "all-Company" -RecipientFilter "((RecipientType -eq 'UserMailbox') -and (Office -eq 'Main Office'))"

# Pull all members
$DD = Get-DynamicDistributionGroup "all-Company"
Get-Recipient -ResultSize Unlimited -RecipientPreviewFilter $DD.RecipientFilter -OrganizationalUnit $DD.RecipientContainer | Format-Table Name,Primary*
```
#### Dynamic Distribution List Common Commands and References
[Get-DynamicDistributionGroup][Get-DynamicDistributionGroup],
[Set-DynamicDistributionGroup][Set-DynamicDistributionGroup]

[Get-DynamicDistributionGroup]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-dynamicdistributiongroup
[Set-DynamicDistributionGroup]:https://docs.microsoft.com/en-us/powershell/module/exchange/set-dynamicdistributiongroup


### Calendar Permissions
```PowerShell
# View current permissions for user's calendar
Get-MailboxFolderPermission -Identity user@domain.com:\Calendar | ft Identity,FolderName,User,AccessRights,SharingPermissionFlags

# Set user to have Editor permissions to user's calendar | Note that Delegate will be able to do everything except view private events
# Note that -SendNotificationToUser is true and the person will get an email to accept the permissions
Set-MailboxFolderPermission -Identity user@domain.com:\Calendar -User user@domain.com -AccessRights Editor -SharingPermissionFlags Delegate -SendNotificationToUser $true

# Remove permissions
Remove-MailboxFolderPermission -Identity user@domain.com:\Calendar -User user@domain.com
```
#### Calendar Permissions Common Commands and References
[Get-MailboxFolderPermissions][Get-MailboxFolderPermissions],
[Set-MailboxFolderPermission][Set-MailboxFolderPermission],
[Remove-MailboxFolderPermission][Remove-MailboxFolderPermission]

[Get-MailboxFolderPermissions]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-mailboxfolderpermission
[Set-MailboxFolderPermission]:https://docs.microsoft.com/en-us/powershell/module/exchange/set-mailboxfolderpermission
[Remove-MailboxFolderPermission]:https://docs.microsoft.com/en-us/powershell/module/exchange/remove-mailboxfolderpermission

### Cleanup a Mailbox
```PowerShell
# Simple mailbox check for size
Get-EXOMailboxStatistics -Identity john@contoso.com | Format-Table Name,DeletedItemCount,ItemCount,TotalDeletedItemSize,TotalItemSize

# Another query for mailbox statistics
Get-EXOMailboxFolderStatistics mailboxname@contoso.com -FolderScope RecoverableItems | Format-Table Name,FolderAndSubfolderSize,ItemsInFolderAndSubfolders

# Check mailbox retention for deleted items
Get-EXOMailbox mailboxName@contoso.com | Format-Table RetainDeletedItemsFor
# Set the mailbox retention
Set-Mailbox mailboxName@contoso.com -RetainDeletedItemsFor 30

# Check ELC status
Get-EXOMailbox mailboxName@contoso.com | Format-Table ElcProcessingDisabled
# Set ELC
Set-Mailbox mailboxName@contoso.com -ElcProcessingDisabled $true


# Clean out mailbox by searching for a phrase with a wildcard
$mbx = Get-EXOMailbox mailboxName@contoso.com;
Do {
$result = Search-Mailbox -Identity $mbx.Identity -SearchQuery 'subject:"Phrase with wildcard*"' -DeleteContent -force -WarningAction Silentlycontinue;
$result | Out-file c:\temp\mailsearch.log -append;
write-Host "Search result for username: " + $result.resultitemscount -ForegroundColor Green;
} Until ($result.resultitemscount -eq 0)

# Clean out mailbox by searching for items from a particular sender, AND a date
$inputbox = "mailboxName"
$mbx = Get-EXOMailbox $inputbox;
Do {
$result = Search-Mailbox -Identity $mbx.Identity -SearchQuery 'from:"someone@contoso.com" AND received:"02/20/2020"' -deletecontent -force -WarningAction Silentlycontinue;
write-Host "Search result for " $inputbox ": " + $result.resultitemscount -ForegroundColor Green;
 } Until ($result.resultitemscount -eq 0)
```
#### Mailbox Cleanup Commands and References
[Get-EXOMailboxStatistics][Get-EXOMailboxStatistics],
[Get-EXOMailboxFolderStatistics][Get-EXOMailboxFolderStatistics],
[Search-Mailbox][Search-Mailbox],
[Get-EXOMailbox][Get-EXOMailbox],
[Set-Mailbox][Set-Mailbox]

[Get-EXOMailboxStatistics]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-exomailboxstatistics
[Get-EXOMailboxFolderStatistics]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-exomailboxfolderstatistics
[Search-Mailbox]:https://docs.microsoft.com/en-us/powershell/module/exchange/search-mailbox
[Get-EXOMailbox]:https://docs.microsoft.com/en-us/powershell/module/exchange/get-exomailbox
[Set-Mailbox]:https://docs.microsoft.com/en-us/powershell/module/exchange/set-mailbox


[basic-auth-deprecation]: https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/deprecation-of-basic-authentication-exchange-online#when-will-this-change-take-place
[exo-v2-cmdlets]: https://docs.microsoft.com/en-us/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps#how-the-exo-v2-module-works