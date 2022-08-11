---
title: 'Restore-OneDrive (the PowerShell way)'
date: '2019-08-12T22:09:55+02:00'
aliases:
    - /blog/restore-onedrive-the-powershell-way/
tags: ["OneDrive","Microsoft 365"]
images: ["cover.jpg"]
description: "Recently I moved from OneDrive to OneDrive for Business (which is not automated task by the way…) and was hoping to have more control over my data..."
---
Recently I moved from OneDrive to OneDrive for Business (which is not automated task by the way…) and was hoping to have more control over my data and also email because I also migrated from Gmail to Exchange Online (that works great by the way).

I was not thinking about all the lab I’ve done in the tenant, so one day I got an email alert saying that multiple files where deleted from my OneDrive. Looked at the Recycle Bin in OneDrive and yes, there was plenty of deleted files… After some research I found the root cause, Retention Policy. Don’t mess with Retention Policies… 😉

![OneDrive](./OneDrive_logo.png)

To restore OneDrive, file by file you can select multiple files and then hit the Restore button in the OneDrive portal. But for 30.000 files? No… big no. At maximum I could restore about 700-1000 files at a time through the browser. And sometimes I had to wait to be able to restore more files. I need PowerShell!

OneDrive/SharePoint official Microsoft PowerShell module isn’t good for restore of OneDrive. But! The community had written the SharePointPnPPowerShellOnline module! (Install-Module SharePointPnPPowerShellOnline). Read more about the PnP PowerShell here: <https://docs.microsoft.com/en-us/powershell/sharepoint/sharepoint-pnp/sharepoint-pnp-cmdlets?view=sharepoint-ps>

So I started to write a PowerShell function to be able to restore all my files in the recycle bin. And here it is!

```powershell
function Restore-OneDrive
{
    Param(
    [Parameter(Position=0,
        HelpMessage="Name of the tenant. E.g. Contoso", 
        Mandatory=$True)]
        [string]$TenantName,

        [Parameter(Position=1,
        HelpMessage="UPN of the user you want to restore OneDrive content", 
        Mandatory=$True)]
        [string]$UserEmail,

        [Parameter(Position=2,
        HelpMessage="Restore all objects in specified users recycle bin", 
        Mandatory=$false)]
        [switch]$RestoreAll
    
    )

    if(Get-Module -Name SharePointPnPPowerShellOnline){
        Import-Module SharePointPnPPowerShellOnline
        # Trim username
        $User = $UserEmail
        $User = $User.Replace('@','_')
        $User = $User.Replace('.','_')
        $OneDriveUrl = "https://$TenantName-my.sharepoint.com/personal/$User"
        Write-Output "Connecting to $OneDriveUrl"
        Connect-PnPOnline -Url $OneDriveUrl -UseWebLogin -Verbose
        if(Get-PnPConnection){
            $Count = Get-PnPRecycleBinItem
            Write-Output "User $userEmail has $($Count.count) objects in OneDrive Recycle Bin"
            if($RestoreAll){
                Write-Verbose "Starting restore..."
                $RecycleBinItem = Get-PnPRecycleBinItem
                $i = 0
                foreach ($item in $RecycleBinItem)
                {
                    $i++
                    Write-Progress -Activity “Restoring OneDrive” -Status "Restore file $i of $($count.count)" -PercentComplete (($i / $Count.count)*100)
                    Restore-PnPRecycleBinItem -Identity $item -Force
                }
                Write-Output "Done with restore of $($Count.count) objects"
            }
            else{
                Write-Output "Filter restore will be in a future update..."
            }
        }
        else{
            Write-Output "You do not have permissions to specified users OneDrive, add your admin account as secondary admin"
        }
    }
    else{
        if(Test-Administrator){
        Install-Module SharePointPnPPowerShellOnline -SkipPublisherCheck -AllowClobber
        }
        else{
        Write-Output "Run the function with administrative rights"
        }
    }
}

function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    (New-Object Security.Principal.WindowsPrincipal $user).IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```