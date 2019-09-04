---
title: Linux用户和组管理
date: 2019-09-03 16:06:09
tags: linux
categories: linux
---


# Linux用户和组管理

## Linux中用户和组相关文件

### /etc/passwd

 存储用户账户信息,存储格式为:

```
name:password:UID:GID:comment:directory:shell
```

- name：用户登录名；
- password：用户口令，用占位符x表示；
- UID：用户ID，用户登录时，系统根据UID，而非用户名来识别用户；
- GID：用户所属的主组ID；
- comment：用户的注释信息；
- directory：用户家目录的绝对路径；
- shell：用户的默认shell。

### /etc/shadow

存储用户密码信息

存储格式为：

```
登录名:$加密算法$salt$加密了的密码:最后一次更改密码的日期:密码最小期限:密码最大期限:密码警告时间段:密码禁用期:账户过期日期:保留字段
```

### /etc/group

存储用户组信息, 存储格式:

```
group_name:password:GID:user_list
```

- group_name：组名；
- password：用户组的口令，用占位符x表示，一般Linux用户组都没有口令；
- GID：组ID；
- user_list：用户列表，注意，这里列出的是以该组为附加组的用户列表，以此组为主组的用户没有列在此处。

## 用户类别和标识

### 用户类别

Linux中，用户分为两大类、三小类：
分别为**管理员**（一般为root）和**普通用户** 。
普通用户中，又划分为两类，分别为*系统用户*和*登录用户*。

**管理员**
**（超级用户root）**

可以操作系统中任意文件和命令，拥有最高的管理权限。

**普通用户**

又分为登录用户和系统用户：

- 登录用户:  一般为管理员手动添加的用户，默认仅拥有操作自身家目录中文件及目录的权限，以及进入与浏览相关目录文件的权限（如/etc、/var/log等），但没有创建、修改、删除等权限。

- 系统用户: 
  一般为系统安装后默认存在的，且默认情况下不能登录系统，它们的存在主要是为了满足系统进程对文件属主的需求。

  Tips：*在部署某些服务是，也可以手动添加某些系统用户。*

### 用户标识(UID)

Linux系统使用UID（User ID）来标识不同用户。
UID是16bits的二进制数字，所以换算成十进制，UID的范围是0~65535，Linux根据用户类别，对UID划分做了规定：

**管理员**
**UID为0**

Tips：当用户UID为0时，该用户就是管理员，所以不只root才是管理员，可以手动指定，但不建议。

**普通用户（1~65535）**

- 系统用户
  一般发行版为1\~499（CentOS7为1\~999）
- 登录用户
  一般发行版为500\~65535（CentOS7 为1000\~65535）

## 组类别和组标识

### 组类别

Linux对组有三种划分方法：

**用户类别**

- 管理员组
- 普通用户组（包括系统用户组和登录用户组）

**主组和附属组**

- 用户的基本组（主组: 
  用户必须有且只能有一个基本组。
- 用户的附加组 （附属组) : 用户可以有0个、1个或多个附加组。
  基本组和附加组就比如，每个人有一个用来安家的房子（基本组），还可以有N个用于投资的房子（附属组）。

**私有和公共组**

- 私有组: 每新建一个用户，如果不指定-g参数，都会自动创建一个和用户名同名的组，且组内只包含用户本身。
- 公共组: 组内可包含多个用户。

### 组标识(GID)

Linux系统使用GID（Group ID）来标识不同组。
GID的划分和UID相同。

## Linux用户和组管理命令

### 组管理

#### groupadd

新建组

```
groupadd [options] group
```

- -g GID：指定GID：默认是上一个组的GID+1
- -r：创建系统组

#### gourpmod

修改组

```
groupmod [options] GROUP
```

- -g GID：--gid GID：修改GID
- -n NEW_NAME，修改组名

#### groupdel

删除组

```
groupdel [options] GROUP
```

*tips: 当某user以某group为主组时，是无法使用groupdel命令删除该group的，但附加组不受影响。*

### 用户管理

#### useradd

新建用户

```
useradd [options] LOGIN
```

- u UID：--uid UID：指定UID，默认是上一个用户UID+1
- -g GROUP：--gid GROUP：指定用户的基本组，此组必须事先存在
- -G：--groups GROUP1,GROUP2...，指定用户的附加组，这些组必须事先存在
- -c COMMENT：--comment COMMENT：添加注释
- -d：--home HOME_DIR：指定用户家目录，通过复制/etc/skel并重命名实现的，指定的家目录路径如果事先存在，则不会为用户复制环境初始化配置文件（如.bashrc等）
- -s：--shell SHELL：指定用户默认shell，可用的所有shell列表存储在/etc/shells文件中
- -r：--system：创建系统用户

#### usermod

 修改用户

```
usermod [options] LOGIN
```

- -u UID：--uid UID：修改UID
- -g GROUP：--gid GROUP：修改用户的基本组，此组必须事先存在
- -G：--groups GROUP1,GROUP2...，修改用户的附加组，这些组须事先存在。
  注意，原来的附加组会被覆盖。
  如果只添加不覆盖，则配合使用-a选项。
- -a：--append：与-G一同使用，添加用户的附加组
- -c COMMENT：--comment COMMENT：修改注释
- -d：--home HOME_DIR：修改用户家目录
  用户原有的文件不会被转移至新位置。
  如果需要转移，则配合使用-m选项。
- -m：--move-home：只能与-d选项一同使用，用于将原来的家目录移动为新的家目录。
- -l：--login NEW_LOGIN：修改用户登录名
- -s：--shell SHELL：修改用户默认shell
- -L：--lock：锁定用户的密码，即禁止用户登录。
  其实就是在/etc/passwd文件中用户原来的密码字符串前添加一个“!”，使其不能匹配。
- -U：--unlock：解锁用户的密码

usermod的常用选项和useradd相同，只需注意-d和-G两个选项。

-G: 修改用户的附加组，这些组须事先存在。
注意，原来的附加组会被覆盖。

-d 和-m 的使用

- 不移动家目录

  例如只修改qinkai用户的家目录为/home/qinkai_newhome，不移动之前家目录的内容，则用-d选项

  *tips: 此目录须事先存在，否则只是更改了/etc/passwd中的记录，实际的目录是不会自动创建的*

- 修改家目录的同时，移动以前家目录的内容, 将-d和-m选项同时使用。

  *tips: 目标目录不要事先存在，否则和只用-d的效果是一样的*

#### userdel

 删除用户

```
userdel [options] LOGIN
```

- -r：删除用户时一并删除用户家目录

#### passwd

修改用户密码

```
passwd [options] [username]
```

passwd：不带任何选项：修改当前登录用户自己的密码

passwd USER：修改指定用户的密码，默认仅root用户有此权限

- -l：--lock：锁定用户
- -u：--unlock：解锁用户
- -d：--delete：清除用户密码
- -e：--expire DATE：过期期限（日期）
- -i：--inactive DAYS：非活动期限（时长）
- -n：--minimum DAYS：密码的最短使用期限
- -m：--maximum DAYS：密码的最长使用期限
- -w：--warning DAYS：警告期限

### 其他命令

#### su

登录切换

```
su [options...] [-] [user [args...]]
```

- \- 登录式切换：会通过重新读取目标用户的配置文件来重新初始化
- -c COMMAND:  仅以指定用户的身份运行此处指定的命令

#### whoami

查看当前登录的用户。