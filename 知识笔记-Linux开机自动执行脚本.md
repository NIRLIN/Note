# 知识笔记-Linux开机自动执行脚本

> 当前笔记在Ubuntu-20.04下完成

## 原因

在win11下安装Ubuntu后，电脑关机，导致docker关闭，每次都要重新打开，输入命令启动容器，太过麻烦。

是否有什么方法可以在Ubuntu启动时，自动执行一个脚本？

## 解决方案

Ubuntu在启动后会执行` /etc/profile`

```bash
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "${PS1-}" ]; then
  if [ "${BASH-}" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

if [ -d /etc/profile.d ]; then 
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

最后一个for循环中，会对/etc/profile.d文件夹下所有.sh结尾地文件进行执行，因此创建一个文件`docker-start.sh`。使其在开机后自动执行。文件内容如下：

```bash
for ((i=1; i<=10; i++))
do
    docker ps -q
    if [ $? -ne 0 ]; then
        echo "第$i次尝试启动docker"
        service docker start # 启动docker
        sleep 1 # 避免docker未启动时，便启动容器
    fi
done

echo "docker10次启动尝试已执行完成，即将尝试启动容器"
docker start $(docker ps -aq) # 遍历docker已关闭的容器，并重新启动
```

