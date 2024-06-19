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