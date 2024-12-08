---
title: Linux 常用命令
---

## 文件与目录操作

``` bash
# 列出当前目录内容
ls
# 切换目录
cd <directory>
# 显示当前工作路径
pwd
# 创建目录
mkdir <directory>
# 删除文件或目录
rm -rf <directory|file>
# 复制文件或目录
cp <source> <destination>
# 移动或重命名文件
mv <source> <destination>
```

## 文件编辑

``` bash
# 使用 Nano 编辑文件
nano <file>
# 使用 Vim 编辑文件
vim <file>
```

## 权限管理

``` bash
# 修改文件权限
chmod <permissions> <file>
# 修改文件所属用户和组
chown <owner>:<group> <file>
```

## 进程管理

``` bash
# 查看所有进程
ps -aux
# 终止指定进程
kill <PID>
# 实时显示系统资源使用情况
top
```

## 网络操作
``` bash
# 测试主机连通性
ping <host>
# 通过 SSH 连接到远程主机
ssh <user>@<host>
# 通过 SSH 复制文件
scp <source> <destination>
```

## 系统管理
``` bash
# 启动服务
sudo systemctl start <service>
# 停止服务
sudo systemctl stop <service>
# 重启服务
sudo systemctl restart <service>
# 查看服务状态
sudo systemctl status <service>
# 查看磁盘使用情况
df -h
# 查看内存使用情况
free -h
```

## 包管理（CentOS）
``` bash
# 安装软件包
yum install <package>
# 卸载软件包
yum remove <package>
# 更新系统
yum update
```

## 用户管理
``` bash
# 添加用户
adduser <username>
# 修改用户密码
passwd <username>
# 将用户添加到组
usermod -aG <group> <username>
```

## 上传本地文件到服务器

``` bash
scp -r /path/to/your_project username@your_server_ip:/var/www/your_project_directory
```

- -r：递归复制整个文件夹。
- /path/to/your_project/dist：本地 dist 文件夹的路径。
- username@your_server_ip：服务器的用户名和 IP 地址，替换为实际信息。
- /var/www/docs：目标服务器上的目录。
