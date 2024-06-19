### For All Linux Distro's
```bash
uname -a
cat /etc/issue
cat /proc/version
cat /etc/*release  #os-release
arch
```
### For Distro's
#### Debian
```bash
file /bin/bash #OS struct
dmesg | grep Linux #Debian
```
#### RedHat
```bash
rpm -q kernel #RedHat
```
# Exploit
内核提权最好再目标系统上编译
1. searchsploit
2. google search
3. [linuxkernelcves](https://www.linuxkernelcves.com/cves)
```bash
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"

strace ./your_binary # 跟踪提权文件使用情况
```