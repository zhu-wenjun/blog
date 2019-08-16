---
title: "向Linux系统添加用户"
date: 2019-09-18T14:40:30+08:00
draft: false
---

如果需要向Linux文件系统中添加1个新的用户，需要修改的文件有3个：/etc/passwd、/etc/shadow和/etc/group。<br>
下面分别介绍这3个文件的含义。

### 1. /etc/passwd

/etc/passwd是一个文本文件，它包含了Linux系统所包含的用户信息，每个用户信息占一行，每行包含7段信息，每段信息之间用冒号`:`隔开，这7段信息分别为<br>

`username:password:UID:GID:comment:homedir:shell`

 段号 |   段名  | 含义|
:----:|:-------:|-----|
  1   | username| 用户名。名称中不能包含大写字母|
  2   | passwd  | 用户密码。x表示密码存储在/etc/shadow文件中 |
  3   | UID     | 用户ID|
  4   | GID     | 用户所在组的组ID。组ID存储在/etc/group文件中 |
  5   | comment | 注释段（可选填），分为5个部分，每个部分用逗号`,`隔开，分别为：<br>Full Name,Room Number,Work Phone,Home Phone,Other |
  6   | homedir | 用户登陆的home目录。root用户的home目录为/root, nufront用户的目录为/home/nufront|
  7   | shell   | 用户登陆后使用的shell解释器|

<br>/etc/passwd对所有用户均具有读权限，对超级用户具有写权限。比如我们用`ls -l`就能列出当前目录下文件所属的用户名和组名，用`ls -n`
就能将用户名和组名转成UID和GID来显示，这个转换映射就是基于/etc/passwd里的配置。<br>

```shell
nufront@nufront ~$ ls -l
total 4
-rwxrwxrwx    1 root     root           841 Sep 18  2019 sta0off.sh
-rwxrwxrwx    1 root     root          2066 Sep 18  2019 sta0on.sh
nufront@nufront ~$ ls -n
total 4
-rwxrwxrwx    1 0        0              841 Sep 18  2019 sta0off.sh
-rwxrwxrwx    1 0        0             2066 Sep 18  2019 sta0on.sh
```

<br>root用户和nufront用户在/etc/passwd中的示例如下

```shell
nufront@nufront ~$ cat /etc/passwd | grep -E 'root|nufront' 
root: x:0:0:root:/root:/bin/sh
nufront: x:1003:1002:Linux User,,,:/home/nufront:/bin/sh
```
<br>

### 2. /etc/shadow

/etc/shadow是一个存储用户密码的文件，/etc/shadow的每行共分为9个字段，它的username字段对应/etc/passwd里的username字段。<br>
/etc/shadow每行的9个字段格式如下：

`username:passwd:last:may:must:warn:expire:disable:reserved`

 段号 |   段名  | 含义|
:----:|:-------:|-----|
  1   | username| 用户的登陆名，名称中不能包含大写字母|
  2   | passwd  | 加密的密码|
  3   | last    | 上次修改密码距现在的天数(从1970.1.1开始算) |
  4   | may     | 下一次修改密码间隔最少的天数。0表示没有限制 |
  5   | must    | 下一次修改密码间隔最多的天数。99999表示没有限制 |
  6   | warn    | 密码到期之前多少天警告用户 |
  7   | expire  | 密码过期之后多少天禁用用户 |
  8   | disable | 用户被禁用的天数(从1970.1.1开始算)。如果为0，则该用户永久可用 |
  9   | reserved| 保留字段 |


<br>root用户和nufront用户在/etc/shadow中的示例如下

```shell
nufront@nufront ~$ cat /etc/shadow | grep -E 'root|nufront'
root:plDlN6FUrXGXs:17010:0:99999:7:::
nufront:xQDFAL5oFHgbg:16979:0:99999:7:::
```
<br>

### 3. /etc/group
/etc/group是用户组配置文件，/etc/group的每行共分为4个字段，它的GID字段对应/etc/passwd里的GID字段。<br>
/etc/group每行的4个字段格式如下：

`groupname:gpasswd:GID:userlist`

 段号 |   段名   | 含义|
:----:|:--------:|-----|
  1   | groupname| 用户组名 |
  2   | gpasswd  | 组密码, x表示密码存储在/etc/gshadow文件中|
  3   | GID      | 组ID(和/etc/passwd的GID关联) |
  4   | userlist | 附加组用户的用户名列表 |

<br>root用户和nufront用户在/etc/group中的示例如下

```shell
nufront@nufront ~$ cat /etc/group | grep -E 'root|nufront'
root: x:0:
nufront: x:1002
```

<br>
用whoami和id命令查看root用户和nufront用户的UID和GID
```shell
Welcome to Nufront
nufront login: root
Password:
root@nufront ~$ whoami
root
root@nufront ~$ id
uid=0(root) gid=0(root) groups=0(root),10(wheel)
```
```shell
Welcome to Nufront
nufront login: nufront
Password:
nufront@nufront ~$ whoami
nufront
nufront@nufront ~$ id
uid=1003(nufront) gid=1002(nufront) groups=1002(nufront)
```

