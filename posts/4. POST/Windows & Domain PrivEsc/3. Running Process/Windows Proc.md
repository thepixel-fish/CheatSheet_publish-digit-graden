- 高权限用户的进程是看不到的
# cmd
```powershell
tasklist
tasklist /SVC #which is running.
wmic process list full
```
# powershell
```powershell
Get-Process
Get-Process -Name 'Notepad'
# Details
Get-Process -Id 1234 | Format-List *
Get-Process notepad | Format-List *
Get-Process notepad | Select-Object Id, CPU, StartTime, Path
(Get-Process -Name 'Calculator').Path
```
# Mount
```powershell
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object ‘Display Name’, ‘Start Mode’, Path
Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"} 
```
