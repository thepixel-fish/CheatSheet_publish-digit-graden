# PowerView
[PowerView](https://powersploit.readthedocs.io/en/latest/Recon/)
```powershell

有时候域中find.All方法被禁用。
powerview就会失效。

Import-Module .\PowerView.ps1
Get-NetDomain #同GetCurrentDomain
```
# Nmap
域中使用-sT扫描
```bash
sudo nmap -sT -iL domain -oN domain_nmap -Pn
```
# Rubeus

- https://github.com/GhostPack/Rubeus

Review the opsec notes before compiling the program in visual studio. 

## Pass the key (PTK):

## Using the ticket on a Windows target: 

```powershell
Rubeus.exe ptt /ticket:<ticket_kirbi_file>
```