# Linux学习笔记

## 0.目录

[TOC]



## 1.简介

Linux，全称GNU/Linux，是一套免费使用和自由传播的类UNIX操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年第一次释出，它主要受到Minix和Unix思想的启发，是一个基于POSIX和Unix的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的Unix工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。Linux有上百种不同的发行版，如基于社区开发的debian、archlinux，和基于商业开发的Red Hat Enterprise Linux、SUSE、oracle linux等。

## 2.系统目录结构

根目录为/，一切文件都是在这下面

bin  boot  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

- /bin ： 存放系统命令的目录，普通用户和超级用户都可以执行
- /sbin ： 存放和系统环境设置相关的命令，只有超级用户可以使用这些命令进行系统环境配置，但有些命令可以允许被普通用户查看
- /usr/bin ： usr(unix systemm  resourse) 存放系统命令的目录，系统用户和超级用户都可以执行（这些命令和系统启动无关），在单用户模式下不能执行
- /usr/sbin：存放根文件系统不必要的系统管理命令
- /boot：系统启动目录，保存系统相关的文件，如内核文件和启动引导(grub)
- /dev：设备文件保存位置（Linux中所有的硬件设备都以目录文件形式展示在Linuxd的/dev目录下）
- /etc：配置文件保存位置（默认）
- /home：普通用户的家目录
- /lib/：系统调用函数库保存位置
- /lost+found/：当系统意外崩溃或机器意外关机，而产生一些文件碎片放在此目录下。
- /media：挂载目录
- /mnt：挂载目录
- /misc：挂载目录
- /opt：第三方安装软件的保存位置。
- /proc：虚拟文件系统，该目录中的数据并不保存在硬盘，而是保存在内存中。主要保存系统的内核，进程，外部设备状态和网络状态。
- /sys：虚拟文件系统，保存在内存中，主要保存内核相关信息
- /root：超级用户的家目录
- /srv：服务数据目录。一些系统服务启动之后，可以在这个目录中保存所需要的数据 
- /tmp：系统存放临时文件的目录。
- /usr：(unix software resourse)系统软件资源目录
- /var：动态数据保存位置。主要保存缓存，日志以及软件运行产生的文件


## 3.基本命令

### 1.数据同步

> sync

将数据同步到硬盘



### 2.关机

**shutdown [-t seconds] [-rkhncfF] time [message]**

- -t seconds : 设定在几秒钟之后进行关机程序
- -k : 并不会真的关机，只是将警告讯息传送给所有只用者
- -r : 关机后重新开机
- -h : 关机后停机
- -n : 不采用正常程序来关机，用强迫的方式杀掉所有执行中的程序后自行关机
- -c : 取消目前已经进行中的关机动作
- -f : 关机时，不做 fcsk 动作(检查 Linux 档系统)
- -F : 关机时，强迫进行 fsck 动作
- time : 设定关机的时间
- message : 传送给所有使用者的警告讯息

### 3.重启

- reboot
- shutdown -r now

### 4.显示目录

> **ls**

-a：显示所有文件，包括隐藏文件

-l：显示所有文件，不包括隐藏文件

### 5.切换目录

> **cd**

cd /root：绝对路径切换目录

cd root：相对路径切换目录

cd ../root：相对路径切换到上一级的相对目录

cd ./root：相对路径切换到同级的相对目录

cd ~：切换回当前用户所在目录

### 6.当前所在目录

> pwd

显示当前所在目录

### 7.创建目录

> **makdir **

makdir test ：创建目录（不可以递归创建）

makdir -p test ：创建目录（递归创建）

makdir /root/test：绝对路径创建目录

makdir /test：相对路径创建目录

### 8.删除目录

> rmdir 

rmdir test ：删除目录（不可以递归删除）

rmdir -p test ：删除目录（递归删除）

### 9.复制目录

> cp

cp 原来的目录  新的目录

### 10.移除文件或目录

>  rm

-f:忽略不存在的文件，不会出现警告，强制删除

-r：递归删除目录

-i：互动删除目录，提示是否删除

` rm  -rf /`：禁止使用，删除全部文件

### 11.移动目录

> mv

-f：强制移动

-u：只替换已经更新过的文件

### 12.文件属性

1. 文件属性及权限图形说明：![img](D:%5CAlldata%5CData%5CNote%5CLinux%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets%5C435896-20190301143028104-680877119.png)
2. 

![img](D:%5CAlldata%5CData%5CNote%5CLinux%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets%5C435896-20190301143055719-17428972.png)



### 13.修改文件属性

1. 修改文件属组

    > chgrp [-R] 文件名
    
    -R：递归更改文件属组，在更改文件夹属组时，如果加上-R属性，该目录下所有文件属组都会发生改变。
    
2. 修改文件属主，也可以修改属组

    >chown -[R] 属主名 文件名
    >
    >chown -[R] 属主名：属组名 文件名

3. 修改文件9个属性

    > chmod -[R] xyz文件或目录

    文件属性有两种表示法，数字法，与字符法

    ` r:4 ` ` w:2 ` ` x:1 ` 

    ` owner=rwx=4+2+1=7 ` 

    ` group=rwx=4+2+1=7 ` 

    > chomd 777 filename

### 14.文件查看

1. cat：从第一行开始显示内容，用来读文章，或者读取配置文件都使用这个命令
2. tac：从最后一行开始读取，可以看出tac时cat的反写，即是倒着看这个文件。
3. nl：显示时顺序输出行号。
4. more：一页一页地查看文件内容。
    - 空格为向下翻页，上下键为上下翻行
5. less：与more类似，与more相比，优点是可以向前翻页。
    1. 翻页
        - pageDown：向下翻页
        - pageUp：向上翻页
    2. 查找文件
        - /char：向下查找字符
        - ?char：向上查找字符
        - n：继续搜寻下一个
        - N：继续搜寻上一个
    3. 退出命令：q
6. head：只看前几行。
    - 可使用 -n，查看所看行数
7. tail：只看后几行
    - 可使用 -n，查看所看行数
8. man cp：查询命令使用方法

### 15.链接

1. 硬链接

    > ln filename

    A--B，假设B是A的硬链接，那么他们两个指向了同一个文件。允许一个文件拥有多个路径，用户可以通过这种机制建立硬连接到一些重要文件上面，防止误删。

2. 软连接

    > ln -s filename
    
    类似于windows上面的快捷方式，删除源文件后，快捷方式也没法访问。

### 16.VIM编辑器

命令行模式：

- 向下左右箭头可以移动光标；
- 将光标移动到行尾
- 将光标移动到行头
- 将光标移动到页尾：shift+g;
- 将光标移动到页头：1+shift+g;
- 移动到目标行：n+shift+g;
- 快速向左右移动光标：ctrl+←/→键；
- 向左或者向右移动n个字符：n+←/→键；
- 删除光标所在行：dd;
- 删除光标所在行及之后的n-1行：dnd；
- 删除一个字母：x (相当于是del)；
- 删除一个字母：X （相当于是backspace）；
- 删除一个词：dw;（最好是放在单词的首字母处）
- 撤销上一步：u
- 向右移动一个单词：w;
- 向左移动一个单词：b;
- 复制当前光标所在行：yy;
- 箭头移动到目的行粘贴：p;
- 复制当前光标所在行及之后的n-1行：yny;
- 复制一个词：yw;
- 剪切当前光标所在位置到行尾: d+shift+4/d+$;
- 剪切当前光标所在位置到行首：d+shift+6/d+^;

编辑模式：

在命令行模式下进入编辑模式，需要按下i、I、a、A、o、O、r、R这些才能进入编辑模式，一般直接按下i就行；i是在当前光标前编辑，a是在当前光标后进行编辑，o是在当前光标的下一行进行编辑；退出编辑模式进入命令行模式需要按下esc键。

末行模式：

　　命令行模式进入末行模式的方式：

　　　　1.：

　　　	2./ 查找，/+被查找的单词，n是下一个，shift+n是指上一个；

　　　　3.? 查找，?+被查找的单词，n是上一个，shift+n是下一个；

　　存盘指令：w;

　　另存指令：w 文件名；

　　退出指令：q;

　　保存并退出指令：wq;

　　强制退出不存盘指令：q!;

　　替换：%s/old/new/g;

　　**显示行号：set nu;**

```bash
    　--------- 按键盘i键-------->输入模式 (按Esc键回到命令模式)  
      |        
   命令模式
      |

      -----------输入 ":" ------> 末行模式 (按Esc键回到命令模式) 

　　三种模式的主要功能：

　　命令模式：复制，粘贴，删除、移动光标、查找

　　编辑模式：编辑文本

　　末行模式：存盘、退出、替换、查找
```

### 17.账号管理

1. 添加用户

> useradd -选项 用户名



2. 删除用户

> userdel -选项 用户名

-r：可以在删除用户时将其目录页一起删除

3. 修改用户

> usermod

注：修改后目录实际看不出来改没改变，目录的实际地址已经发生了改变

4. 切换用户

> su usernaem

注：使用root用户切换到普通用户时，使用`exit`可以直接返回root用户,切换账户时不需要输入用户密码。

5. 设置用户密码

> passwd username

 6. 锁定账户

> passwd -l username

### 18.用户组管理

1. 创建一个用户组

    groupadd：创建一个用户组

    ​	-g：设置用户组id

2. 删除用户组

    groupdel：删除一个用户组

### 19.磁盘管理

1. df 

    -h：以M方式查看

2. du

    查看当前文件夹使用了多少

### 20.进程管理

1. ​    概念

    1. 在Linux中，每一个程序都是有自己的一个进程，每一个进程都有一个id号
    2. 每一个进程都会有一个父进程
    3. 进程有两种运行方式：前台运行和后台运行
    4. 一般的话，服务在后台运行，基本的程序在前台运行

2. 命令

    **ps**：查看系统中正在执行的各种进程信息

    - -a：显示当前终端的所有进程信息
    - -u：以用户的信息显示进程
    - -x：显示后台运行进程的参数

    ps -aux：查看所有进程

    |：管道符

    grep：查找文件中符合条件的字符

    ps -aux | grep mysql：查看mysql进程

3. ps -ef：可以查看父进程的信息

4. pstree 

    -p：显示父id

    -u：显示用户组

5. 终结进程

    kill -n

## 4.环境管理

### 1.rpm安装

1. 下载.rpm安装包

2. 检测版本信息

    rpm -qa |grep jdk

3. 安装：

    rpm -ivk filename

4. 环境变量

    在/etc/profile中设置环境变量

    JAVA_HOME：在/usr下找到安装信息

    注：使用`source /etc/profile`使配置生效

5. 卸载

    rpm -e --nodeps jdk

### 2.解压安装

1. 解压

   tar -zxvf filename

2. 使用启动项启动


### 3.yum在线安装

1. #### 设置存储库

   ```bash
   sudo yum install -y yum-utils
   ```
   
2. #### 设置存储库
   
   ```bash
   sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```
   
3. 安装*最新版本*的Docker Engine和容器

   ```bash
   yum install docker-ce docker-ce-cli containerd.io
   ```

4. 启动docker

   ```
   systemctl start docker
   ```

5. 通过运行`hello-world` 映像来验证是否正确安装了Docker Engine

   ```
   docker run hello-world
   ```



## 5.Docker

###  安装教程

https://blog.csdn.net/shana_8/article/details/105190368

### 1.安装

1.  设置存储库

   ```bash
   sudo yum install -y yum-utils
   ```

2.  设置存储库

   ```bash
   sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. 安装最新版本的Docker Engine和容器

   ```bash
   yum install docker-ce docker-ce-cli containerd.io
   ```

4. 启动docker

   ```
   systemctl start docker
   ```

5. 通过运行`hello-world` 映像来验证是否正确安装了Docker Engine

   ```
   docker run hello-world
   ```

### 2.简介

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

**docker将运行环境一同打包，然后发布到服务器上**

优势：

- 更快速的应用交付和部署
- 更便捷的升级和扩缩容
- 更简单的系统运维
- 更高效的计算资源运行

组成

- 镜像
- 容器
- 仓库

### 3.配置阿里云镜像加速

1. 阿里云地址https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

2. mkdir -p /etc/docker

3. vim /etc/docker/daemon.json

   ```bash
   {
     "registry-mirrors": ["https://5gf89wyj.mirror.aliyuncs.com"]
   }
   ```

   

4. systemctl daemon-reload

5. systemctl restart docker

### 4.docker常用命令

#### 1.帮助命令

1. docker version
2. docker info
3. docker help

#### 2.镜像命令

1. `docker images`：列出本地主机上面所有的镜像

   - -a：列出本地所有镜像
   - -q：只显示镜像id
   - --digests：显示镜像摘要信息
   - --no-trunc：显示完整镜像信息

2. docker search imagename [options]：搜索镜像

   ​	options:

   - --no-trunc：显示完整镜像信息
   - es-s：列出收藏数不小于指定数的镜像
   - --automated：只列出automated build类型的镜像

3. docker pull imagename:tag

   tag ：版本号

4. docker rmi imagename

   -f：强制删除

   1. 删除单个

      docker rmi -f imageid

   2. 删除多个

      docker rmi -f imageid1 imageid2

   3. 删除全部

      docker rmi -f $(docker image -qa)

#### 3.容器命令

1. 新建并启动容器

   docker run [options] imagename 

   options说明：

   - --name="容器新名字"：为容器指定一个名称
   - -d：后台运行容器，并返回容器id，即启动守护式容器
   - **-i：以交互模式启动容器，通常与-t同时使用**
   - **-t：为容器重新分配一个伪输入终端，通常与-i同时使用**
   - -P：随机端口映射
   - -p：指定端口映射，有以下四种
     - ip:hostPort:containerPort
     - ip::containerPort
     - hostPort:containerPort
     - containerPort

2. 列出当前所有**正在运行**的容器

   命令：docker ps [options]

   options说明：

   - -a：列出当前正在运行的容器+历史上运行过的容器
   - -l：显示最近创建的容器
   - -n：显示最近n个创建的容器 docker ps -n 3
   - **-q：静默模式，只显示容器编号**
   - --no-trunc：不截断输出

3. 退出容器

   1. exit：容器停止退出
   2. ctrl+P+Q：容器不停止退出

4. 启动容器

   docker start 容器id或者容器名

5. 重启容器

   docker restart 容器id或者容器名

6. 停止容器

   docker stop 容器id或者容器名

7. 强制停止容器

   docker kill 容器id或者容器名

8. 删除已停止容器

   docker rm 容器id

   一次性删除多个容器

   1. docker rm -f $(docker ps -a -q )
   2. docker -ps  -a -q |xargs docker rm

9. **重点**

   1. **启动守护式容器**

      1. docker run -d 容器名

   2. 查看容器日志

      docker logs -f -t --tail 容器id

      1. -t：加入时间戳
      2. -f：跟随最新的日志打印
      3. --tail：数字，显示最后多少条

   3. 查看容器内运行的进程

      docker top 容器id

   4. 进入正在运行的容器并以命令行交互

      1. docker exec -it 容器id bashShell
      2. 重新进入：docker attach 容器id
      3. 两者区别
         1. attach：直接进入容器启动命令的终端，不会启动新的进程
         2. exec：是在容器中打开新的终端，并启动新的进程

   5. 结果

      docker有隔离机制，每一次启动tomcat时，都是启动一个新的tomcat，之前修改的tomcat是另一份容器，开始时webapps之所以是空的，是为了让我们放自己的项目





