# 202308nh
## Vorbereiten des Hybrid Join
### Hardwareschlüssel auslesen
https://learn.microsoft.com/de-de/mem/autopilot/add-devices


### One Drive Auto Logon
Mindestens  Windows 10 1709

Registrierung:
 [HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\OneDrive] “SilentAccountConfig”=dword:00000001
 Moderne Authentisierung aktivieren
 [HKEY_CURRENT_USER\SOFTWARE\Microsoft\OneDrive] “EnableADAL”=dword:00000001
 
 Ondrive SilentSetup
 C:\Windows\SysWOW64\OneDriveSetup.exe /silent
 
 Downloads:
 
 Content Prep Tool
 https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool
 
 
 ## Package Manager WinGet
 https://learn.microsoft.com/de-de/windows/package-manager/winget/

 
 ## Company Portal App
 https://learn.microsoft.com/de-de/mem/intune/apps/store-apps-company-portal-app
 # Update
 
 https://learn.microsoft.com/de-de/windows/deployment/update/waas-configure-wufb
 
 ##Sicherheit
 
 ### bedingter Zugriff
 
 https://learn.microsoft.com/de-de/mem/intune/protect/conditional-access-intune-common-ways-use


## Richtlinien erstellen

https://learn.microsoft.com/de-de/mem/intune/protect/create-conditional-access-intune

##Anpassungen für Citrix
	https://docs.citrix.com/en-us/citrix-endpoint-management/prepare-to-enroll-devices-and-deliver-resources.html



 
 
 Funktion zum Zurücksetzen fehlerhafter WSUS einstellungen
 
 function Clear-WsusSettings {
 
    [cmdletbinding(SupportsShouldProcess=$true)]
    param ()
    $ErrorActionPreference = 'SilentlyContinue'
    Remove-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate' -Force -Name WUServer
    Remove-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate' -Force -Name TargetGroup
    Remove-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate' -Force -Name WUStatusServer
    Remove-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate' -Force -Name TargetGroupEnable
    Set-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate\AU' -Value 0 -Force -Name UseWUServer
    Set-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate\AU' -Value 0 -Force -Name NoAutoUpdate
    Set-ItemProperty 'HKLM:\Software\Policies\Microsoft\Windows\WindowsUpdate'    -Value 0 -force -Name DisableWindowsUpdateAccess
    Restart-Service -Name wuauserv
}
 

##Intune Powershell Scripts


Set-Location C:\Temp
Clear-Host

 

#Install the module
Install-Module -Name Microsoft.Graph.Intune -AllowClobber -Verbose -Force

 

#Connect and change the scheme
Connect-MSGraph -ForceInteractive
Update-MSGraphEnvironment -SchemaVersion beta
Connect-MSGraph

#Which group do you want to check?
$groupName = "AutoPilot Geräte"

 

$Group = Get-AADGroup -Filter "displayname eq '$GroupName'"

####Config Start####

Write-host "Azure Active Directory Group: $($Group.displayName)" -ForegroundColor Green

#Apps
$AllAssignedApps = Get-IntuneMobileApp -Filter "isAssigned eq true" -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Apps found: $($AllAssignedApps.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllAssignedApps) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Compliance
$AllDeviceCompliance = Get-IntuneDeviceCompliancePolicy -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Compliance policies found: $($AllDeviceCompliance.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllDeviceCompliance) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Configuration
$AllDeviceConfig = Get-IntuneDeviceConfigurationPolicy -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Configurations found: $($AllDeviceConfig.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllDeviceConfig) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Configuration Powershell Scripts
$Resource = "deviceManagement/deviceManagementScripts"
$graphApiVersion = "Beta"
$uri = "https://graph.microsoft.com/$graphApiVersion/$($Resource)?`$expand=groupAssignments"
$DMS = Invoke-MSGraphRequest -HttpMethod GET -Url $uri
$AllDeviceConfigScripts = $DMS.value | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Configurations Powershell Scripts found: $($AllDeviceConfigScripts.DisplayName.Count)" -ForegroundColor cyan

Foreach ($Config in $AllDeviceConfigScripts) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Administrative templates
$Resource = "deviceManagement/groupPolicyConfigurations"
$graphApiVersion = "Beta"
$uri = "https://graph.microsoft.com/$graphApiVersion/$($Resource)?`$expand=Assignments"
$ADMT = Invoke-MSGraphRequest -HttpMethod GET -Url $uri
$AllADMT = $ADMT.value | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Administrative Templates found: $($AllADMT.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllADMT) {

Write-host $Config.displayName -ForegroundColor Yellow

 

}

####Config End####


{
$Groups = Get-AADGroup | Get-MSGraphAllPages

####Config Start ####


Foreach ($Group in $Groups) {
Write-host "Azure Active Directory Group Name: $($Group.displayName)" -ForegroundColor Green

#Apps
$AllAssignedApps = Get-IntuneMobileApp -Filter "isAssigned eq true" -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Apps found: $($AllAssignedApps.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllAssignedApps) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Compliance
$AllDeviceCompliance = Get-IntuneDeviceCompliancePolicy -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Compliance policies found: $($AllDeviceCompliance.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllDeviceCompliance) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Configuration
$AllDeviceConfig = Get-IntuneDeviceConfigurationPolicy -Select id, displayName, lastModifiedDateTime, assignments -Expand assignments | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Configurations found: $($AllDeviceConfig.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllDeviceConfig) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Device Configuration Powershell Scripts
$Resource = "deviceManagement/deviceManagementScripts"
$graphApiVersion = "Beta"
$uri = "https://graph.microsoft.com/$graphApiVersion/$($Resource)?`$expand=groupAssignments"
$DMS = Invoke-MSGraphRequest -HttpMethod GET -Url $uri
$AllDeviceConfigScripts = $DMS.value | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Configurations Powershell Scripts found: $($AllDeviceConfigScripts.DisplayName.Count)" -ForegroundColor cyan

Foreach ($Config in $AllDeviceConfigScripts) {

Write-host $Config.displayName -ForegroundColor Yellow

}

#Administrative templates
$Resource = "deviceManagement/groupPolicyConfigurations"
$graphApiVersion = "Beta"
$uri = "https://graph.microsoft.com/$graphApiVersion/$($Resource)?`$expand=Assignments"
$ADMT = Invoke-MSGraphRequest -HttpMethod GET -Url $uri
$AllADMT = $ADMT.value | Where-Object {$_.assignments -match $Group.id}
Write-host "Number of Device Administrative Templates found: $($AllADMT.DisplayName.Count)" -ForegroundColor cyan
Foreach ($Config in $AllADMT) {

Write-host $Config.displayName -ForegroundColor Yellow

}

}

####Config End####
}


