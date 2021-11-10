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

## 文件查找

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