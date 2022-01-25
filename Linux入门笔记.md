## 用户管理

### adduser 和 useradd 的区别是什么
* #### useradd 
只创建用户，不会创建用户密码和工作目录，创建完了需要使用 passwd <username> 去设置新用户的密码。
* #### adduser 
在创建用户的同时，会创建工作目录和密码（提示你设置），做这一系列的操作。其实 useradd、userdel 这类操作更像是一种命令，执行完了就返回。而 adduser 更像是一种程序，需要你输入、确定等一系列操作。

### 删除用户 userdel

### 将其他用户添加到sudo用户组
使用 usermod 命令可以为用户添加用户组，同样使用该命令你必需有 root 权限，你可以直接使用 root 用户为其它用户添加用户组，或者用其它已经在 sudo 用户组的用户使用 sudo 命令获取权限来执行该命令。
```
sudo usermod -G sudo <username>
```
## 文件管理

### 修改文件权限

#### chmod <权限二进制数对应的十进制数> 文件名
```
# 777 = rwxrwxrwx
chmod 777 test.sh
# 600 = rw-------
chmod 600 hello.txt
```

### 更改文件所属用户，用户组
* #### chown 修改所属用户
```
# chown 用户 目录或文件名
chown hikobe8 ~/test
```
* #### chogrp 修改所属用户组
```
# chgrp 组 目录或文件名
chgrp hikobe8_group ~/test
```

##环境变量配置

Shell注意事项
**1. Shell 中的赋值操作，= 两边不可以输入空格，否则会报错。**
**2. 并不是任何形式的变量名都是可用的，变量名只能是英文字母、数字或者下划线，且不能以数字作为开头。**
```
# 正确的赋值
tmp=hikobe8

# 错误的赋值
tmp = hikobe8
```

按变量的生存周期来划分，Linux环境变量可分为两类：

1. 永久的：需要修改配置文件，变量永久生效；

2. 临时的：使用 export 命令行声明即可，变量在关闭 shell 时失效。

永久的环境变量也分为了两种：
1. 用户目录下的```.profile(Mac OS 现在大多数为.zprofile)```，这个文件只对当前用户生效，切换用户环境变量就无法生效了。
2. ```/etc/profile或者/etc/zprofile```

可以使用 unset 命令删除一个环境变量

如何让设置的环境变量生效?
```
source .bashrc(.zprofile)
#source 可以用.替代
. ./.zprofile
```

### 文件查找

* locate 快而全
  使用 locate 命令查找文件也不会遍历硬盘，它通过查询 /var/lib/mlocate/mlocate.db 数据库来检索信息。不过这个数据库也不是实时更新的，系统会使用定时任务每天自动执行 updatedb 命令来更新数据库。所以有时候你刚添加的文件，它可能会找不到，需要手动执行一次 updatedb 命令（在我们的环境中必须先执行一次该命令）
* which 小而精
which 本身是 Shell 内建的一个命令，我们通常使用 which 来确定是否安装了某个指定的程序，因为它只从 PATH 环境变量指定的路径中去搜索命令并且返回第一个搜索到的结果。
* find 精而细


练习1, 查找/etc目录下所有以.list结尾的文件

```
sudo locate /etc/*.list
#如果报错, sudo locate /etc/\*.list
```
练习2
寻找文件

有一个非常重要的文件（sources.list）但是你忘了它在哪了，你依稀记得它在 /etc/ 目录下，现在要你把这个文件找出来，然后设置成自己（shiyanlou 用户）可以访问，但是其他用户并不能访问。

目标

1. 找到 sources.list 文件
2. 把文件所有者改为自己（shiyanlou）
3. 把权限修改为仅仅只有自己可读可写

```
find /etc/ -name sources.list
sudo chown shiyanlou /etc/apt/sources.list
sudo chmod 600 /etc/apt/sources/list
```

### 文件的打包，压缩

**zip**

使用zip打包压缩文件

```
# -r 表示递归打包子目录的全部内容
# -q表示安静模式没有输出信息
# -1, 设置压缩级别 -[1-9]，1 表示最快压缩但体积大，9 表示体积最小但耗时最久
# -o 为打包后的文件名，随后为要打包的目录或文件 
zip -r -q -1 -o <dst.zip> <src>
```

创建加密zip包
```
zip -r -e -o <dst.zip> <src>
```

使用unzip解压缩

```
# -d 解压缩到指定文件夹
unzip -q <dst.zip> -d <dstDir>
# -l 不解压，只是查看压缩包的内容
unzip -l <dst.zip>
# 指定参数类型 -O(英文字母大写的O)
unzip -O GBK 中文压缩文件.zip
```
**tar**
tar为Linux中比较常用的打包命令，可以结合gzip,xz等压缩工具使用，就可以成为一个便捷的打包压缩工具。

使用tar打包文件
```
#打包为归档文件, -c表示创建,create, -f指定打包后的文件名
tar -c -f <dst.tar> <srcDir>
#-c -f可以写在一起，但是注意的是一定是-cf，因为f后面会跟着对应的打包后的文件名，一般的写法都是-cf之类的。
tar -cf <dst.tar> <srcDir>
```

使用tar打包压缩文件

```
# -z 使用gzip压缩， -v显示打包过程信息
tar -czvf <dst.tar> <srcDir>
```

tar解压缩

```
#-x 解包, -z使用gzip, -C 指定解压路径，请注意这个路径一定要是已存在的目录
tar -zxvf <dst.tar> -C <existedDir>
```

> **zip, tar小tips**
> * zip：
打包 ：zip something.zip something （目录请加 -r 参数）
解包：unzip something.zip
指定路径：-d 参数
>* tar：
打包：tar -cf something.tar something
解包：tar -xf something.tar
压缩：-z 使用gzip
指定路径：-C 参数

### 磁盘管理

**dd**

```
# dd命令用于转换和复制文件，dd在复制时可以处理数据

# 输出到文件test, if=输入, of=输出, bs(block size)块大小，默认为Byte, count块的个数
dd if=/dev/stdin of=test bs=10 count=1
# 输出到终端
dd if=/dev/stdin of=/std/stdout bs=10 count=1
# 读取输入并且转换为大写然后输出到test文件
dd if=/dev/stdin of=test bs=10 count=1 conv=ucase
```
#### 使用dd命令创建虚拟镜像文件
```
# 从 /dev/zero 设备创建一个容量为 256M 的空文件
dd of=/dev/stdin of=test bs=10 count=1
# 使用 mkfs 命令格式化磁盘
sudo mkfs.ext4 virtual.img
```
```
#小练习，找出目录下最大的十个文件或目录
du -ah |sort -n -r|head -n 10
```

## 帮助命令

**type**
可以使用type命令来区分当前命令是内建还是外部，或者是否是别名
```
type cd
type vim
type ls
```

## 定时任务

**crontab**

crontab 命令从输入设备读取指令，并将其存放于 crontab 文件中，以供之后读取和执行。通常，crontab 储存的指令被守护进程激活，crond 为其守护进程，crond 常常在后台运行，每一分钟会检查一次是否有预定的作业需要执行。

通过 crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell 脚本。时间间隔的单位可以是分钟、小时、日、月、周的任意组合。

这里我们看一看 crontab 的格式：
```
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
```

```
#启动crontab
sudo cron －f &
#添加一个任务
crontab -e
#查看添加的任务
crontab -l
#删除任务
crontab -r
```
### 使用crontab每分钟创建一个文件
```
#启动crontab
sudo cron －f &
#添加一个任务
crontab -e
*/1 * * * * touch /home/shiyanlou/$(date +\%Y\%m\%d\%H\%M\%S)
```

### 创建系统级定时任务

每个用户使用```crontab -e```添加计划任务，都会在 /var/spool/cron/crontabs 中添加一个该用户自己的任务文档，这样目的是为了隔离。
如果要创建系统级别的定时任务，需要root权限，编辑/etc/crontab即可。
**cron 服务监测时间最小单位是分钟，所以 cron 会每分钟去读取一次 /etc/crontab 与 /var/spool/cron/crontabs 里面的內容。**

```ll /etc/ | grep cron```下面的目录解释

1. /etc/cron.daily，目录下的脚本会每天执行一次，在每天的 6 点 25 分时运行；
1. /etc/cron.hourly，目录下的脚本会每个小时执行一次，在每小时的 17 分钟时运行；
1. /etc/cron.monthly，目录下的脚本会每月执行一次，在每月 1 号的 6 点 52 分时运行；
1. /etc/cron.weekly，目录下的脚本会每周执行一次，在每周第七天的 6 点 47 分时运行；

**小练习**

**创建定时任务每天三点备份/var/log/alternatives.log 到 用户目录/tmp/， 备份文件名为年-月-日**

```
mkdir ~/tmp
sudo cron -f &
crontab -e # 添加任务
0 3 * * * sudo rm /home/shiyanlou/tmp/*
0 3 * * * sudo cp /var/log/alternatives.log /home/user/tmp/$(date +%Y-%m-%d)
 ```

## 命令控制顺序

* ; 简单的顺序执行，无论上一个命令的结果如何都会继续进行
* && 当上一条命令结果为0的时候执行&&之后的命令
* || 当上一条命令结果不为0的时候执行||之后的命令

**$?获取上一条命令的结果，0或非0**

```
sudo apt-get update;sudo apt-get install cowsay;cowsay -f head-in hello
#如果已安装cowsay执行&&后的命令，没有安装则没有输出
which cowsay>/dev/null && cowsay -f head-in hello
#如果已安装cowsay执行&&后的命令，没有安装的话输出 not installed
which cowsay>/dev/null && echo "installed" || echo "not installed"
```

## 管道
**上一条命令的输出作为下一条命令的输入**
cut 命令，打印每一行的某一字段

```
#打印 /etc/passwd 文件中以 : 为分隔符的第 1 个字段和第 6 个字段分别表示用户名和其家目录
cut /etc/passwd -d ':' -f 1,6
```

**wc 命令用于统计并输出一个文件中行、单词和字节的数目，比如输出**

```
# 行数
wc -l /etc/passwd
# 单词数
wc -w /etc/passwd
# 字节数
wc -c /etc/passwd
# 字符数
wc -m /etc/passwd
# 最长行字节数
wc -L /etc/passwd
```

**sort 排序命令**

```
# 默认为字典排序：
cat /etc/passwd | sort

# 反转排序：
cat /etc/passwd | sort -r

# 按特定字段排序：
cat /etc/passwd | sort -t':' -k 3

# 按特定字段，数字排序
cat /etc/passwd | sort -t':' -k 3 -n
```
**history 命令查看最近执行过的命令（实际为读取 ${SHELL}_history 文件，.zsh_history 文件）**

```
history
```