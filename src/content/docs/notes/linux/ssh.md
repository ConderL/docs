---
title: ssh 常见问题
---

# SSH 登录问题排查

#### 1. 编辑 SSH 配置文件
``` bash
sudo nano /etc/ssh/sshd_config
```

#### 2. 重启 SSH 服务
``` bash
sudo systemctl restart sshd
```

#### 3. 检查 SSH 服务状态
``` bash
sudo systemctl status sshd
```

#### 4. 启动 SSH 服务
``` bash
sudo systemctl start sshd
```

#### 5. 设置 SSH 服务开机启动
``` bash
sudo systemctl enable sshd
```

#### 6. 修改 root 用户密码
``` bash
sudo passwd root
```

#### 7. 检查防火墙规则（CentOS/RHEL/Fedora）
``` bash
sudo firewall-cmd --permanent --zone=public --add-service=ssh
sudo firewall-cmd --reload
```

#### 8. 检查防火墙规则（Ubuntu/Debian）
``` bash
sudo ufw allow ssh
sudo ufw reload
```

#### 9. 查看 SSH 日志（Ubuntu/Debian）
``` bash
sudo tail -f /var/log/auth.log
```

#### 10. 查看 SSH 日志（CentOS/RHEL）
``` bash
sudo tail -f /var/log/secure
```

#### 11. 检查 root 用户是否被锁定
``` bash
sudo passwd -S root
```

#### 12. 解锁 root 用户
``` bash
sudo passwd -u root
```
