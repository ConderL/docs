---
title: Podman
---

# Nuxt 项目配置

## 安装 podman 或 docker

```bash
sudo dnf install -y epel-release
sudo dnf install -y podman
podman --version
sudo systemctl enable --now podman.socket
```

## 镜像加速器地址

```link
https://cba6e3a6963e4f439e361d983145a9ec.mirror.swr.myhuaweicloud.com
```

## 配置镜像加速

`nano /etc/containers/registries.conf`

```ini
[registries.search]
registries = [
  "docker.io",
  "registry.cn-hangzhou.aliyuncs.com"
]
```

## 安装 jenkins

```bash
podman pull cba6e3a6963e4f439e361d983145a9ec.mirror.swr.myhuaweicloud.com/library/jenkins/jenkins
```

```bash
podman run -d \
  -p 9090:8080 \
  -p 50000:50000 \
  --name jenkins \
  18627ef39e36
```

在这个命令中：

- -p 9090:8080 表示将主机的 9090 端口映射到容器的 8080 端口。
- -p 50000:50000 保持不变，用于节点通信。
- --name 别名
- image id

有时候会遇到不能访问的莫名其妙的问题，可以尝试重启服务器

```bash
sudo reboot
```

## 启动 jenkins

```bash
podman start jenkins
```

## 检查日志

```bash
podman logs jenkins
```

podman 和 docker 跟服务器是隔离的，有些东西需要在里面再装一下
比如要生成公钥配置到 github 上需要在 jenkins 服务器里面生成公钥

## 查看 docker 镜像

```bash
podman ps (docker ps)
```

## 进入 jenkins 容器

```bash
podman exec -it --user root <jenkins_container_id_or_name> /bin/bash
```

- `-it`：这是两个选项的组合：
- `-i（interactive）`：保持标准输入打开，使你能够输入命令。
- `-t（tty）`：为这个会话分配一个伪终端，以便能提供一个命令行界面。这样的话，命令行会更易读和操作。
- `--user root`：指定执行命令的用户为 root，即以 root 权限进入容器。
- `<jenkins_container_id_or_name>`：这是容器的 ID，Podman 会通过这个 ID 找到并在对应的容器中执行命令。
- `/bin/bash`：这是在容器中要执行的命令。在这种情况下，/bin/bash 表示你希望进入容器的 Bash 命令行，以便进行进一步操作

## 创建目录并生成公钥

```bash
mkdir -p /var/lib/jenkins/.ssh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f /var/lib/jenkins/.ssh/id_rsa
```

- `-t rsa`：指定密钥类型为 RSA。
- `-b 4096`：生成 4096 位的密钥。
- `-C "your_email@example.com"`：这里填入你的邮箱地址作为注释，便于识别

## 确保访问权限

```bash
chmod 700 /var/lib/jenkins/.ssh
chmod 600 /var/lib/jenkins/.ssh/id_rsa
```

## 查看公钥

```bash
cat ~/.ssh/id_rsa.pub
```

## 验证链接，会生成私钥，配置到 jenkins 上

```bash
ssh -T git@github.com
```

## 切换用户

```bash
su - jenkins
```
