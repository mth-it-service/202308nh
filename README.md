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
 


