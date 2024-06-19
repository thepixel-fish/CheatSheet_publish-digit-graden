# Ps & reg & dir
```powershell
reg query HKEY_LOCAL_MACHINE\SOFTWARE
## 属于当前用户的 ##
Get-ItemProperty  "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname, DisplayVersion, Publisher, InstallDate | Format-Table –AutoSize #32位程序
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
## ##
Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime
Get-WmiObject -Class Win32_Product | Select-Object -Property Name > C:\InstalledSoftwareList.txt
Get-WindowsFeature | Where-Object {$_.InstallState -eq 'Installed'}

Get-Command <command> #like linux which
where <command>
```
# Wmic
```cmd
wmic product get name,version,vendor
wmic product get name,version /format:csv > C:\InstalledSoftware.csv
```
# Obtain a list of installed from remote
```powershell
Invoke-command -computer remote_pc_name {Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall* | Select-Object DisplayName, DisplayVersion, Publisher, InstallDate | Format-Table -AutoSize }
```
# Insecure GUI apps
```powershell
#Check the applications that are running from "TaskManager" and obtain list of applications that are running as Privileged user
#Open that particular application, using "open" feature enter the following
file://c:/windows/system32/cmd.exe 
```
# Exploit

## Vulnerable App
check it version then Google/searchsploit!