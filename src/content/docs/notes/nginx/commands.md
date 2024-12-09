---
title: Nginx 常用命令
---

记录常用 Nginx 命令。

``` bash
# 查看 Nginx 状态
sudo systemctl status nginx
# 启动 Nginx
sudo systemctl start nginx
# 重启 Nginx
sudo systemctl restart nginx
# 停止 Nginx
sudo systemctl stop nginx
# 检查 Nginx 配置文件是否正确
sudo nginx -t
# 重新加载 Nginx 配置文件
sudo nginx -s reload
# 设置 Nginx 开机自启
sudo systemctl enable nginx
# 取消 Nginx 开机自启
sudo systemctl disable nginx
# 实时查看 Nginx 错误日志
sudo tail -f /var/log/nginx/error.log
# 实时查看 Nginx 访问日志
sudo tail -f /var/log/nginx/access.log
# 查看 Nginx 版本
sudo nginx -v
# 查看 Nginx 编译参数
sudo nginx -V
# 查看 Nginx 是否正在占用端口 80
sudo lsof -i:80
# 查看 Nginx 进程
ps aux | grep nginx
```
