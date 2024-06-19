# Tips
**尝试anonymous:anonymous登录**
**别忘了，ftp也可以爆破下，或者一些其他的默认密码**
# ftp
```bash
ftp 172.16.0.0
ftp -A 172.16.0.0

wget -m ftp://anonymous:anonymous@10.10.10.98 #Donwload all
wget --no-passive-ftp --mirror 'ftp://anonymous:anonymous@192.168.117.110'  #下载所有，强制主动模式
wget --ftp-user= --ftp-password= --level=20 ftp://ip:port/* -r #指定端口，递归
#command perp to upload/download
binary
type
mget #get all dir
```
# Connect via Browser
```bash
ftp://172.16.0.0
```
# CrackMapExec
```bash
crackmapexec ftp 172.16.0.0
crackmap exec ftp 172.16.0.0 -u 'a' -p ''
crackmapexec ftp 172.16.0.0 -u 'anonymous' -p '''

# FTP Default wordlists: 
/usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt
#⬆️format -> <user>:<pass> 如果作词表要注意里面不是单纯的密码
# hydra -c 用来添加此类格式的文件!
```
# Nmap NSE
```bash
ls -lh /usr/share/nmap/scripts/ | grep ftp
nmap -script=ftp* -p21 -Pn -sV -oN ftp_enum 172.16.0.0
```