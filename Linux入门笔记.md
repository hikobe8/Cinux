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