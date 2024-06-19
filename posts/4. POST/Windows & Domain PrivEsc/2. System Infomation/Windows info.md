目前没有内核提权啥的，还不怎么用的上
```powershell
systeminfo
Get-ComputerInfo
Get-ComputerInfo -Property "*version"
Get-ComputerInfo -Property "*version", "os*" | select WindowsCurrentVersion, WindowsVersion, OsName, OsBuildNumber, OsHotFixes, OsArchitecture | fl

$PSVersionTable
#Obtaining Systems Enviorment Variables:
Get-ChildItem -Path Env:
```