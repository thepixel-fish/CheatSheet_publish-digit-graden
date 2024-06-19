- 通常运行在windows上的Web服务，也会导致提权(一般服务账号都有SeImpersonate)
- 我们可能会发现一个仅对本地主机（登录到主机时）可访问的脆弱服务(**监听回环地址127.0.0.1 和 ::1**)
- - Username and hostname 
- Group memberships of the current user
- Existing users and groups 
- Operating system, version and architecture 
- Network information 
- Installed applications - Running processes
# Query Ready!
先点开嗷
[HackTricks - Windows PrivEsc](https://book.hacktricks.xyz/windows-hardening/checklist-windows-privilege-escalation)
[PayloadAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation/)
# Situation Awareness

## Whoami
```powershell
whoami /all
whoami /groups
hostname
```

## All user && groups
```powershell
net user # /domin
net localgroup # /domain
Get-LocalGroup
Get-LocalGroupMember <group_name>
```

## Machine Info
```powershell
systeminfo
Get-ComputerInfo -Property "*version", "os*" | select WindowsCurrentVersion, WindowsVersion, OsName, OsBuildNumber, OsHotFixes, OsArchitecture | fl
$PSVersionTable
#Obtaining Systems Enviorment Variables:
Get-ChildItem -Path Env / set / dir env:

```

## Network
```powershell
ipconfig /all
netstat -ano

net share
Get-SmbShare -ComputerName #domain
#save crediential
cmdkey /list
```

## Process
```powershell
tasklist /SVC
tasklist /v /fi "username eq system" #check system procs
Get-Process | where {$_.ProcessName -notlike "svchost*"} | ft ProcessName, Id

wmic process list full
```
### Script -- Check Perm of Procs Binary
```powershell
for /f "tokens=2 delims='='" %%x in ('wmic process list full^|find /i "executablepath"^|find /i /v "system32"^|find ":"') do (
   	for /f eol^=^"^ delims^=^" %%z in ('echo %%x') do (
   		icacls "%%z" 
2>nul | findstr /i "(F) (M) (W) :\\" | findstr /i ":\\ everyone authenticated users todos %username%" && echo.
   	)
)
#DLL Hijack
for /f "tokens=2 delims='='" %%x in ('wmic process list full^|find /i "executablepath"^|find /i /v 
"system32"^|find ":"') do for /f eol^=^"^ delims^=^" %%y in ('echo %%x') do (
	icacls "%%~dpy\" 2>nul | findstr /i "(F) (M) (W) :\\" | findstr /i ":\\ everyone authenticated users 
todos %username%" && echo.
)
```

## Installed
App exploit
```powershell
wmic product get name
Get-WmiObject -Class Win32_Product |  select Name, Version
Get-Command <command>
where <command>
```

## Schedule Task
找寻可以替换的定时任务二进制文件
```powershell
schtasks /query /fo LIST /v
Get-ScheduledTask
```
## Services
Unquote / weak perm binary / Path blank Abuse / DLL path abuse or binary 
### General Check
```powershell
wmic service list brief
wmic serviceget name,displayname,pathname,startmode
sc qc  {$service_name} # *_PATH_* || *_START_*
sc query

icacls
Get-ACL
Get-Service
```
### Check Modifiable Binary
```powershell
# Modifiable Binary
#
for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> %temp%\perm.txt
for /f eol^=^"^ delims^=^" %a in (%temp%\perm.txt) do cmd.exe /c icacls "%a" 2>nul | findstr "(M) (F) :\"
sc query state= all | findstr "SERVICE_NAME:" >> C:\Temp\Servicenames.txt
FOR /F "tokens=2 delims= " %i in (C:\Temp\Servicenames.txt) DO @echo %i >> C:\Temp\services.txt
FOR /F %i in (C:\Temp\services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> C:\Temp\path.txt
```
### Unquote Service Path
```powershell
#cmd
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
#Not only Auto Service
wmic service get name,displayname,pathname,startmode | findstr /i /v "C:\\Windows\\system32\\" |findstr /i /v """
# Reg Auto-Run Binary
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```
### Weak Registry Modify Permissions
```powershell
reg query hklm\System\CurrentControlSet\Services /s /v imagepath #Get the binary paths of the services

#Try to write every service with its current content (Write Perm Check)
for /f %a in ('reg query hklm\system\currentcontrolset\services') do del %temp%\reg.hiv 2>nul & reg save %a %temp%\reg.hiv 2>nul && reg restore %a %temp%\reg.hiv 2>nul && echo You can modify %a
get-acl HKLM:\System\CurrentControlSet\services\* | Format-List * | findstr /i "<Username> Users Path Everyone"

.\accesschk.exe /accepteula "<current_user>" -kvuqsw hklm\System\CurrentControlSet\services
# Check Group Access of service
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
```
### ps1 -- Check Function.
```powershell
# PowerUp.ps1 --> Modifiable Binary
Get-ModifiableServiceFile
Get-UnquotedService
.\SharpUp.exe audit
```
## Interesting File
```powershell
tree /F #显示包含文件
tree /A #使用标准ASCII字符绘树状图
dir /S C:\path\to\directory

findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
#history
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```