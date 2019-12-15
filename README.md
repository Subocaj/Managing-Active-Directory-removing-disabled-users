# Managing-Active-Directory-removing-disabled-users
#Powershell script to remove membership from user in the Disabled Users OU.
#Import AD Module
IPMO act*
#Get your list
$userlist = Search-AdAccount -AccountDisabled -SearchBase “OU=DisabledUserAccounts,DC=hq,DC=corp,DC=pbs,DC=org”
#Makes magic happen
ForEach ($user in $userlist)
    {
      Set-aduser $user -add @{msExchHideFromAddressLists=“FALSE”}
       $groups = (Get-ADUser $user –Properties MemberOf | Select-Object MemberOf).MemberOf
      ForEach ($group in $groups)
        {
          Remove-ADGroupMember -Identity $group -Members $user -Confirm:$false
        }
    }
