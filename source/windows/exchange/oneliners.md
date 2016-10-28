# Exchange Powershell Quick Commands

List all messages out from a specific domain within a specific time::

```powershell
  Get-MessageTrackingLog -Server "servername" -EventID "SEND" -Start "27/07/2015 06:00:00" -End "27/07/2015 07:00:00" -ResultSize unlimited | where{$_.sender -like "*@contoso.com"}
```

Or you could output it to a text file::

```powershell
  Get-MessageTrackingLog -Server "servername" -EventID "SEND" -Start "27/07/2015 06:00:00" -End "27/07/2015 07:00:00" -ResultSize unlimited | where{$_.sender -like "*@contoso.com"} | tee contoso.txt
```

List all of the distribution groups for a given domain::

```powershell
  Get-DistributionGroup | where{$_.primarysmtpaddress -like "*@contoso.com"} | select name, primarysmtpaddress
```

List all members of that distribution group::

```powershell
  Get-DistributionGroupMember -Identity "Name of Dist Group" | select -primarysmtpaddress
```


Set SCL rating of a domain and reject messages at the threshold::

```powershell
  Get-Mailbox -OrganizationalUnit contoso.com | Set-Mailbox -SCLRejectEnabled $true -SCLRejectThreshold 5
```


List the size of a mailbox and the number of messages::

```powershell
  $array = @()
  get-mailbox -identity *@contoso.com -resultsize unlimited | foreach-object {
  $dname=$_.DisplayName
  $email = $_.primarysmtpaddress
  $size=(Get-MailboxStatistics -identity $_).totalitemsize
  $count=(Get-MailboxStatistics -identity $_).itemcount
  $email = $email.Local + "@" + $email.Domain
  $object = New-Object -TypeName PSObject
  $object | Add-Member -Name 'Email' -MemberType Noteproperty -Value $email
  $object | Add-Member -Name 'Count' -MemberType Noteproperty -Value $count
  $object | Add-Member -Name 'Size' -MemberType Noteproperty -Value $size
  $array += $object

  }
  $array | export-csv C:\contoso.com.csv -notypeinformation  
```